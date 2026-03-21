# Unity C# Patterns

## GameManager (Singleton)
```csharp
public class GameManager : MonoBehaviour
{
    public static GameManager Instance { get; private set; }

    public enum GameState { Menu, Playing, Paused, GameOver }
    public GameState State { get; private set; }

    void Awake()
    {
        if (Instance != null) { Destroy(gameObject); return; }
        Instance = this;
        DontDestroyOnLoad(gameObject);
    }

    public void SetState(GameState newState)
    {
        State = newState;
        OnStateChanged?.Invoke(newState);
    }

    public event System.Action<GameState> OnStateChanged;
}
```

## Event Bus (decoupled communication)
```csharp
public static class EventBus
{
    private static Dictionary<string, System.Action<object>> listeners = new();

    public static void Subscribe(string eventName, System.Action<object> callback)
    {
        if (!listeners.ContainsKey(eventName)) listeners[eventName] = null;
        listeners[eventName] += callback;
    }

    public static void Unsubscribe(string eventName, System.Action<object> callback)
    {
        if (listeners.ContainsKey(eventName)) listeners[eventName] -= callback;
    }

    public static void Publish(string eventName, object data = null)
    {
        if (listeners.ContainsKey(eventName)) listeners[eventName]?.Invoke(data);
    }
}
// Usage: EventBus.Publish("PlayerDied"); EventBus.Subscribe("PlayerDied", OnPlayerDied);
```

## Simple State Machine
```csharp
public class StateMachine
{
    private IState currentState;

    public void ChangeState(IState newState)
    {
        currentState?.Exit();
        currentState = newState;
        currentState.Enter();
    }

    public void Update() => currentState?.Update();
}

public interface IState
{
    void Enter();
    void Update();
    void Exit();
}
```

## Object Pool
```csharp
public class ObjectPool : MonoBehaviour
{
    [SerializeField] private GameObject prefab;
    [SerializeField] private int initialSize = 10;
    private Queue<GameObject> pool = new();

    void Start()
    {
        for (int i = 0; i < initialSize; i++) CreateNew();
    }

    private void CreateNew()
    {
        var obj = Instantiate(prefab);
        obj.SetActive(false);
        pool.Enqueue(obj);
    }

    public GameObject Get()
    {
        if (pool.Count == 0) CreateNew();
        var obj = pool.Dequeue();
        obj.SetActive(true);
        return obj;
    }

    public void Return(GameObject obj)
    {
        obj.SetActive(false);
        pool.Enqueue(obj);
    }
}
```

## ScriptableObject — Game Config
```csharp
[CreateAssetMenu(fileName = "GameConfig", menuName = "Config/GameConfig")]
public class GameConfig : ScriptableObject
{
    [Header("Player")]
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    public int maxHealth = 3;

    [Header("Game")]
    public float levelTimeLimit = 120f;
}
// Reference in MonoBehaviour: [SerializeField] private GameConfig config;
```

## SceneLoader
```csharp
public class SceneLoader : MonoBehaviour
{
    public static SceneLoader Instance { get; private set; }

    void Awake()
    {
        if (Instance != null) { Destroy(gameObject); return; }
        Instance = this;
        DontDestroyOnLoad(gameObject);
    }

    public void Load(string sceneName) => StartCoroutine(LoadAsync(sceneName));

    private IEnumerator LoadAsync(string sceneName)
    {
        var op = UnityEngine.SceneManagement.SceneManager.LoadSceneAsync(sceneName);
        while (!op.isDone) yield return null;
    }
}
```

## Procedural Placeholder Sprite
```csharp
// Creates a colored rectangle sprite at runtime — use for placeholder art
public static Sprite CreatePlaceholderSprite(int width, int height, Color color)
{
    var tex = new Texture2D(width, height);
    var pixels = new Color[width * height];
    for (int i = 0; i < pixels.Length; i++) pixels[i] = color;
    tex.SetPixels(pixels);
    tex.Apply();
    return Sprite.Create(tex, new Rect(0, 0, width, height), new Vector2(0.5f, 0.5f), 100f);
}
```

## Input (New Input System)
```csharp
// Requires: Window > Package Manager > Input System
public class PlayerInput : MonoBehaviour
{
    private InputActions actions;

    void Awake()
    {
        actions = new InputActions();
        actions.Player.Jump.performed += _ => OnJump();
    }

    void OnEnable() => actions.Enable();
    void OnDisable() => actions.Disable();

    private Vector2 MoveInput => actions.Player.Move.ReadValue<Vector2>();

    void Update()
    {
        // use MoveInput.x, MoveInput.y for movement
    }

    private void OnJump() { /* jump logic */ }
}
```
