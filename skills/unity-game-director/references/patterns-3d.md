# Unity 3D 패턴

## 3D 플레이어 이동 (CharacterController)
```csharp
[RequireComponent(typeof(CharacterController))]
public class PlayerController3D : MonoBehaviour
{
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float jumpHeight = 1.5f;
    [SerializeField] private float gravity = -9.81f;
    [SerializeField] private Transform cameraTransform;

    private CharacterController cc;
    private Vector3 velocity;

    void Awake() => cc = GetComponent<CharacterController>();

    void Update()
    {
        // 카메라 기준 이동 방향
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        Vector3 camForward = Vector3.ProjectOnPlane(cameraTransform.forward, Vector3.up).normalized;
        Vector3 camRight = Vector3.ProjectOnPlane(cameraTransform.right, Vector3.up).normalized;
        Vector3 move = (camForward * v + camRight * h);

        if (move.magnitude > 1f) move.Normalize();
        cc.Move(move * moveSpeed * Time.deltaTime);

        // 중력 & 점프
        if (cc.isGrounded && velocity.y < 0) velocity.y = -2f;
        if (Input.GetButtonDown("Jump") && cc.isGrounded)
            velocity.y = Mathf.Sqrt(jumpHeight * -2f * gravity);

        velocity.y += gravity * Time.deltaTime;
        cc.Move(velocity * Time.deltaTime);

        // 이동 방향으로 회전
        if (move.sqrMagnitude > 0.01f)
            transform.rotation = Quaternion.Slerp(transform.rotation,
                Quaternion.LookRotation(move), 10f * Time.deltaTime);
    }
}
```

## 3인칭 카메라 (Cinemachine 없이)
```csharp
public class ThirdPersonCamera : MonoBehaviour
{
    [SerializeField] private Transform target;
    [SerializeField] private float distance = 5f;
    [SerializeField] private float height = 2f;
    [SerializeField] private float sensitivity = 3f;
    [SerializeField] private float minY = -20f, maxY = 60f;

    private float rotX, rotY;

    void LateUpdate()
    {
        rotX += Input.GetAxis("Mouse X") * sensitivity;
        rotY -= Input.GetAxis("Mouse Y") * sensitivity;
        rotY = Mathf.Clamp(rotY, minY, maxY);

        Quaternion rotation = Quaternion.Euler(rotY, rotX, 0);
        Vector3 offset = rotation * new Vector3(0, height, -distance);
        transform.position = target.position + offset;
        transform.LookAt(target.position + Vector3.up * height * 0.5f);
    }
}
```

## 1인칭 카메라 + 이동
```csharp
[RequireComponent(typeof(CharacterController))]
public class FirstPersonController : MonoBehaviour
{
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float mouseSensitivity = 2f;
    [SerializeField] private float jumpHeight = 1.2f;
    [SerializeField] private Camera playerCamera;

    private CharacterController cc;
    private float cameraPitch;
    private Vector3 velocity;
    private readonly float gravity = -9.81f;

    void Awake()
    {
        cc = GetComponent<CharacterController>();
        Cursor.lockState = CursorLockMode.Locked;
    }

    void Update()
    {
        // 마우스 시점
        float mouseX = Input.GetAxis("Mouse X") * mouseSensitivity;
        float mouseY = Input.GetAxis("Mouse Y") * mouseSensitivity;
        cameraPitch = Mathf.Clamp(cameraPitch - mouseY, -80f, 80f);
        playerCamera.transform.localRotation = Quaternion.Euler(cameraPitch, 0, 0);
        transform.Rotate(Vector3.up * mouseX);

        // 이동
        Vector3 move = transform.right * Input.GetAxis("Horizontal")
                     + transform.forward * Input.GetAxis("Vertical");
        if (move.magnitude > 1f) move.Normalize();
        cc.Move(move * moveSpeed * Time.deltaTime);

        // 중력 & 점프
        if (cc.isGrounded && velocity.y < 0) velocity.y = -2f;
        if (Input.GetButtonDown("Jump") && cc.isGrounded)
            velocity.y = Mathf.Sqrt(jumpHeight * -2f * gravity);
        velocity.y += gravity * Time.deltaTime;
        cc.Move(velocity * Time.deltaTime);
    }
}
```

## 3D 적 AI (NavMesh 순찰 + 추격)
```csharp
using UnityEngine.AI;

[RequireComponent(typeof(NavMeshAgent))]
public class EnemyAI3D : MonoBehaviour
{
    [SerializeField] private Transform[] patrolPoints;
    [SerializeField] private float detectionRange = 10f;
    [SerializeField] private float attackRange = 2f;
    [SerializeField] private float attackCooldown = 1.5f;

    private NavMeshAgent agent;
    private Transform player;
    private int patrolIndex;
    private float attackTimer;

    enum State { Patrol, Chase, Attack }
    private State state = State.Patrol;

    void Awake()
    {
        agent = GetComponent<NavMeshAgent>();
        player = GameObject.FindWithTag("Player")?.transform;
    }

    void Update()
    {
        if (player == null) return;
        float dist = Vector3.Distance(transform.position, player.position);
        attackTimer -= Time.deltaTime;

        switch (state)
        {
            case State.Patrol:
                Patrol();
                if (dist < detectionRange) state = State.Chase;
                break;
            case State.Chase:
                agent.SetDestination(player.position);
                if (dist <= attackRange) state = State.Attack;
                else if (dist > detectionRange * 1.5f) state = State.Patrol;
                break;
            case State.Attack:
                agent.SetDestination(transform.position); // 멈춤
                transform.LookAt(player);
                if (attackTimer <= 0f) { Attack(); attackTimer = attackCooldown; }
                if (dist > attackRange) state = State.Chase;
                break;
        }
    }

    void Patrol()
    {
        if (patrolPoints.Length == 0) return;
        if (agent.remainingDistance < 0.5f)
            patrolIndex = (patrolIndex + 1) % patrolPoints.Length;
        agent.SetDestination(patrolPoints[patrolIndex].position);
    }

    void Attack()
    {
        // 데미지 처리: EventBus.Publish("EnemyAttack", damage);
    }
}
```

## 3D 물리 기반 상호작용
```csharp
// 잡기/던지기 인터랙션
public class GrabInteraction : MonoBehaviour
{
    [SerializeField] private float grabRange = 2f;
    [SerializeField] private float throwForce = 10f;
    [SerializeField] private Transform holdPoint;

    private Rigidbody heldObject;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.E))
        {
            if (heldObject == null) TryGrab();
            else Drop();
        }
        if (Input.GetKeyDown(KeyCode.Mouse0) && heldObject != null)
            Throw();
    }

    void TryGrab()
    {
        if (Physics.Raycast(Camera.main.transform.position,
            Camera.main.transform.forward, out RaycastHit hit, grabRange))
        {
            if (hit.rigidbody != null)
            {
                heldObject = hit.rigidbody;
                heldObject.isKinematic = true;
            }
        }
    }

    void Drop()
    {
        heldObject.isKinematic = false;
        heldObject = null;
    }

    void Throw()
    {
        heldObject.isKinematic = false;
        heldObject.AddForce(Camera.main.transform.forward * throwForce, ForceMode.Impulse);
        heldObject = null;
    }

    void LateUpdate()
    {
        if (heldObject != null)
            heldObject.transform.position = Vector3.Lerp(
                heldObject.transform.position, holdPoint.position, 15f * Time.deltaTime);
    }
}
```

## 3D 플레이스홀더 아트 생성 (코드로)
```csharp
// 런타임에 색상 입힌 Unity 프리미티브로 플레이스홀더 생성
public static class PlaceholderFactory3D
{
    public static GameObject CreateCharacter(Color color, string name = "Character_ph")
    {
        var root = new GameObject(name);

        // 몸통
        var body = GameObject.CreatePrimitive(PrimitiveType.Capsule);
        body.transform.SetParent(root.transform);
        body.transform.localPosition = Vector3.up;
        SetColor(body, color);

        // 머리
        var head = GameObject.CreatePrimitive(PrimitiveType.Sphere);
        head.transform.SetParent(root.transform);
        head.transform.localPosition = new Vector3(0, 2.2f, 0);
        head.transform.localScale = Vector3.one * 0.6f;
        SetColor(head, color * 1.2f);

        return root;
    }

    public static GameObject CreateProp(PrimitiveType shape, Color color,
        Vector3 scale, string name = "Prop_ph")
    {
        var obj = GameObject.CreatePrimitive(shape);
        obj.name = name;
        obj.transform.localScale = scale;
        SetColor(obj, color);
        return obj;
    }

    private static void SetColor(GameObject obj, Color color)
    {
        var mat = new Material(Shader.Find("Universal Render Pipeline/Lit"));
        mat.color = color;
        obj.GetComponent<Renderer>().material = mat;
        // Placeholder — replace with final art asset
    }
}
```

## Scene 구조 (3D 탑다운 예시)
```
Scene: GameplayScene
├── [Environment]
│   ├── Ground (MeshRenderer + MeshCollider)
│   ├── Walls
│   └── Props
├── [Characters]
│   ├── Player (CharacterController + PlayerController3D)
│   └── Enemies (NavMeshAgent + EnemyAI3D)
├── [Camera]
│   └── CameraRig (ThirdPersonCamera or Cinemachine Virtual Camera)
├── [UI]
│   └── Canvas (Screen Space - Overlay)
├── [Managers]
│   ├── GameManager
│   ├── SpawnManager
│   └── AudioManager
└── [Lighting]
    ├── Directional Light
    └── ReflectionProbe
```

## 3D 프로젝트 폴더 추가 구조
```
Assets/
├── Art/
│   ├── Models/          # .fbx, .obj 3D 모델
│   │   └── Placeholders/  # 프리미티브 기반 플레이스홀더 프리팹
│   ├── Textures/        # Albedo, Normal, Metallic 맵
│   ├── Materials/
│   └── Animations/      # Animator Controller, Animation Clips
├── Scripts/
│   └── Gameplay/
│       ├── Player/
│       ├── Enemy/
│       └── Camera/
```
