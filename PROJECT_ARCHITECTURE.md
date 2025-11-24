# 🎮 ARKANOID GAME - KIẾN TRÚC DỰ ÁN

## 📋 MỤC LỤC
1. [Tổng Quan Dự Án](#1-tổng-quan-dự-án)
2. [Kiến Trúc Tổng Thể](#2-kiến-trúc-tổng-thể)
3. [Cấu Trúc Package](#3-cấu-trúc-package)
4. [Design Patterns](#4-design-patterns)
5. [Luồng Hoạt Động](#5-luồng-hoạt-động)
6. [Phân Tích Chi Tiết Từng Layer](#6-phân-tích-chi-tiết-từng-layer)
7. [Resource Management](#7-resource-management)
8. [Dependency Graph](#8-dependency-graph)

---

## 1. TỔNG QUAN DỰ ÁN

### 1.1 Thông Tin Cơ Bản
- **Tên dự án:** Arkanoid Game (Space Breaker)
- **Công nghệ:** JavaFX 17.0.2 + Java 21
- **Build tool:** Maven
- **Architecture:** MVC + Event-driven + OOP Design Patterns
- **Main class:** `core.MainApp`

### 1.2 Tính Năng Chính
```
├── 🎮 Game Modes
│   ├── Classic Mode (16 levels)
│   ├── Story Mode (narrative-based)
│   └── Event Mode (4 themed events)
│
├── 🛍️ Shop System
│   ├── Paddle skins
│   ├── Ball skins
│   └── Coin economy
│
├── 🏆 Progression
│   ├── Leaderboard
│   ├── Score tracking
│   └── Skin unlocking
│
└── 🎨 UI Features
    ├── Tutorial dialog
    ├── Settings
    └── Coming soon features
```

---

## 2. KIẾN TRÚC TỔNG THỂ

### 2.1 Layered Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     PRESENTATION LAYER                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │ MainMenu    │  │ SelectMode  │  │  Settings   │         │
│  │ Controller  │  │ Controller  │  │  Controller │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│         │                 │                 │               │
│         └─────────────────┴─────────────────┘               │
│                           │                                 │
└───────────────────────────┼─────────────────────────────────┘
                            │
┌───────────────────────────┼─────────────────────────────────┐
│                    CONTROLLER LAYER                          │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │ GameController   │  │ EventGame        │                │
│  │ (Classic Mode)   │  │ Controller       │                │
│  └──────────────────┘  └──────────────────┘                │
│         │                       │                            │
│         └───────────────────────┘                            │
│                     │                                        │
└─────────────────────┼────────────────────────────────────────┘
                      │
┌─────────────────────┼────────────────────────────────────────┐
│                  LOGIC LAYER                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ GameEngine   │  │ EventGame    │  │ Ball, Paddle │      │
│  │ (Classic)    │  │ Engine       │  │ Brick Logic  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│         │                  │                  │              │
└─────────┼──────────────────┼──────────────────┼──────────────┘
          │                  │                  │
┌─────────┼──────────────────┼──────────────────┼──────────────┐
│                      DATA LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ GameState    │  │ LevelLoader  │  │ SkinManager  │      │
│  │ (Singleton)  │  │              │  │ (Singleton)  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└──────────────────────────────────────────────────────────────┘
```

### 2.2 Architecture Pattern: **MVC + Event-Driven**

```
View (FXML)  ←→  Controller  →  Model (Logic + Data)
                      ↓
                  Callbacks
                      ↓
              Update View (UI)
```

---

## 3. CẤU TRÚC PACKAGE

### 3.1 Source Code Structure

```
src/main/java/
│
├── 📦 core/                          # Application Core
│   ├── MainApp.java                  # Entry point, Scene router
│   └── GameController.java           # Main game controller (Classic mode)
│
├── 📦 controller/                    # UI Controllers
│   ├── MainMenuController.java       # Main menu screen
│   ├── SelectModeController.java     # Mode selection (Classic/Story/Event)
│   ├── SelectLevelController.java    # Level selection
│   ├── SelectEventController.java    # Event selection
│   ├── SettingsController.java       # Settings screen
│   ├── ShopController.java           # Shop for skins
│   ├── SkinController.java           # Skin preview
│   ├── GameState.java                # Singleton: Game state (coins, progress)
│   ├── GameStateManager.java         # Singleton: Save/Load game
│   ├── SkinManager.java              # Singleton: Skin inventory
│   ├── SoundManager.java             # Singleton: Audio management
│   └── LeaderboardManager.java       # Leaderboard persistence
│
├── 📦 logic/                         # Game Logic (Classic Mode)
│   ├── GameEngine.java               # Core game loop, physics, collision
│   ├── Ball.java                     # Ball entity + physics
│   ├── Paddle.java                   # Paddle entity + movement
│   ├── Brick.java                    # Basic brick (hp, score, wall)
│   ├── EnhancedBrick.java            # Animated gradient brick
│   ├── PowerUp.java                  # PowerUp drop + effects
│   ├── PowerUpType.java              # Enum: PowerUp types
│   ├── ActivePowerUp.java            # PowerUp state tracking
│   └── LevelLoader.java              # Load levels from .txt files
│
├── 📦 event/                         # Event System (Special game modes)
│   ├── base/                         # Base classes (Template Method)
│   │   ├── EventGameController.java  # Abstract controller for events
│   │   ├── EventGameEngine.java      # Abstract engine for events
│   │   └── EventLevelLoader.java     # Abstract level loader
│   │
│   ├── universe/                     # Universe Event (Aliens + Lasers)
│   │   ├── controller/
│   │   │   └── UniverseController.java
│   │   └── logic/
│   │       ├── UniverseEngine.java
│   │       ├── UniverseLevelLoader.java
│   │       ├── AlienBrick.java       # Shoots lasers
│   │       ├── ShipBrick.java        # Moves horizontally
│   │       ├── StoneBrick.java       # Indestructible
│   │       └── Laser.java            # Projectile entity
│   │
│   ├── castleattack/                 # Castle Attack Event
│   │   ├── CastleAttackController.java
│   │   └── logic/
│   │       ├── CastleAttackEngine.java
│   │       ├── CastleLevelLoader.java
│   │       ├── CanonBrick.java       # Shoots cannonballs
│   │       ├── GateBrick.java        # Spawns soldiers
│   │       ├── Soldier.java          # Marching entity
│   │       ├── CanonBall.java        # Projectile
│   │       └── WallBrick.java
│   │
│   ├── penaldo/                      # Penaldo Event (Football theme)
│   │   ├── controller/
│   │   │   └── PenaldoController.java
│   │   └── logic/
│   │       ├── PenaldoEngine.java
│   │       ├── PenaldoLevelLoader.java
│   │       ├── GoalBrick.java        # Must destroy to win
│   │       ├── DefenderBrick.java    # Moves to block
│   │       └── BarrierBrick.java
│   │
│   └── treasurehunter/               # Treasure Hunter Event
│       ├── controller/
│       │   └── TreasureHunterController.java
│       └── logic/
│           ├── TreasureHunterEngine.java
│           ├── TreasureLevelLoader.java
│           ├── TreasureBrick.java    # Drops coins
│           ├── TrapBrick.java        # Penalty
│           └── ChestBrick.java
│
├── 📦 storycontroller/               # Story Mode System
│   ├── StorySceneController.java     # Story scene renderer
│   ├── StoryLoader.java              # Load story from JSON
│   ├── StoryImagePicker.java         # Character image logic
│   └── BackgroundSelector.java       # Background selection
│
└── 📦 ui/                            # UI Components (Dialogs)
    ├── TutorialDialog.java           # Tutorial popup
    ├── TutorialDialogController.java
    ├── GameOverDialog.java           # Game over popup
    ├── LeaderboardDialog.java        # Leaderboard popup
    ├── ComingSoonDialog.java         # "Coming soon" popup
    ├── CongratsAnimation.java        # Victory animation
    ├── TaiXiuDialog.java             # Casino minigame
    ├── DialogResult.java             # Enum: Dialog return values
    └── StyleConstants.java           # CSS constants
```

### 3.2 Resources Structure

```
src/main/resources/
│
├── 📁 fxml/                          # JavaFX Layout Files
│   ├── MainMenu.fxml                 # Main menu UI
│   ├── Game.fxml                     # Classic game UI
│   ├── SelectMode.fxml               # Mode selection UI
│   ├── SelectLevel.fxml              # Level selection UI
│   ├── SelectEvent.fxml              # Event selection UI
│   ├── Settings.fxml                 # Settings UI
│   ├── Shop.fxml                     # Shop UI
│   ├── Skin.fxml                     # Skin preview UI
│   ├── TutorialDialog.fxml           # Tutorial dialog UI
│   ├── StoryScene.fxml               # Story mode UI
│   ├── Universe.fxml                 # Universe event UI
│   ├── Castle.fxml                   # Castle event UI
│   ├── Penaldo.fxml                  # Penaldo event UI
│   └── TreasureHunter.fxml           # Treasure event UI
│
├── 📁 levels/                        # Level Data Files (Classic Mode)
│   ├── level1.txt                    # 16 levels total
│   ├── level2.txt
│   └── ...
│
├── 📁 event/                         # Event-specific Resources
│   ├── backgrounds/                  # Event backgrounds
│   │   ├── universe_background.png
│   │   ├── castle_background.png
│   │   ├── penaldo_background.png
│   │   └── treasure_hunter_background.png
│   │
│   ├── skins/                        # Event-specific skins
│   │   ├── universe/
│   │   │   ├── universe_paddle.png
│   │   │   ├── universe_ball.png
│   │   │   ├── universe_alien_brick.png
│   │   │   ├── universe_ship_brick.png
│   │   │   ├── universe_stone_brick.png
│   │   │   └── universe_laser.png
│   │   │
│   │   ├── castle/
│   │   ├── penaldo/
│   │   └── treasure/
│   │
│   └── levels/                       # Event level files
│       ├── universe/
│       │   └── universe1.txt
│       ├── castle_attack/
│       ├── penaldo/
│       └── treasure_hunter/
│
├── 📁 backgrounds/                   # Global backgrounds
│   ├── event_background.png
│   └── main_menu_background.png
│
├── 📁 images/                        # Game Assets
│   ├── paddle/                       # Paddle skins
│   │   ├── paddle_default.png
│   │   ├── paddle_iron.png
│   │   └── ...
│   │
│   ├── ball/                         # Ball skins
│   │   ├── ball_default.png
│   │   ├── ball_fire.png
│   │   └── ...
│   │
│   ├── bricks/                       # Brick textures
│   │   ├── brick1.png
│   │   └── ...
│   │
│   └── powerup/                      # PowerUp icons
│       ├── expand.png
│       ├── shrink.png
│       ├── multiball.png
│       └── ...
│
├── 📁 sound/                         # Audio Files
│   ├── menu_music.mp3
│   ├── game_music.mp3
│   ├── brick_break.mp3
│   ├── paddle_hit.mp3
│   └── powerup.mp3
│
└── 📁 storymode/                     # Story Mode Resources
    ├── story/                        # Story JSON files
    │   ├── chapter1.json
    │   └── ...
    │
    ├── chap/                         # Chapter images
    │   └── chapter_thumbnails/
    │
    └── bg/                           # Story backgrounds
        └── scene_backgrounds/
```

---

## 4. DESIGN PATTERNS

### 4.1 Creational Patterns

#### **Singleton Pattern** (4 instances)
```java
// 1. GameState.java - Global game state
public class GameState {
    public static final GameState INSTANCE = new GameState();
    private int coins = 0;
    private int highScore = 0;
    
    private GameState() {}  // Private constructor
}

// 2. GameStateManager.java - Save/Load state
public class GameStateManager {
    public static final GameState INSTANCE = new GameStateManager();
    private boolean hasGame = false;
    private int level = 1;
    // ...
}

// 3. SkinManager.java - Skin inventory
public class SkinManager {
    public static final SkinManager INSTANCE = new SkinManager();
    private Set<PaddleSkin> unlockedPaddles = new HashSet<>();
    private Set<BallSkin> unlockedBalls = new HashSet<>();
    // ...
}

// 4. SoundManager.java - Audio management
public class SoundManager {
    public static final SoundManager INSTANCE = new SoundManager();
    private MediaPlayer menuMusicPlayer;
    private MediaPlayer gameMusicPlayer;
    // ...
}
```

**Lý do sử dụng:**
- ✅ Global access point
- ✅ Ensure only one instance
- ✅ Shared state across application

#### **Factory Method Pattern**
```java
// EventLevelLoader.java
protected abstract Brick createBrickFromChar(char ch, int col, int row);

// UniverseLevelLoader.java
@Override
protected Brick createBrickFromChar(char ch, int col, int row) {
    return switch (ch) {
        case 'A' -> new AlienBrick(rect);
        case 'S' -> new ShipBrick(rect);
        case 'W' -> new StoneBrick(rect);
        default -> null;
    };
}
```

### 4.2 Structural Patterns

#### **Facade Pattern**
```java
// MainApp.java acts as Facade
public class MainApp extends Application {
    public static void showMainMenu() throws Exception { ... }
    public static void showLevelSelect() throws Exception { ... }
    public static void showGame(int level) throws Exception { ... }
    public static void showShop() throws Exception { ... }
    public static void showUniverse(int level) throws Exception { ... }
    // ... simplified interface for scene navigation
}
```

### 4.3 Behavioral Patterns

#### **Template Method Pattern** ⭐⭐⭐
```java
// EventGameController.java (Abstract base)
public abstract class EventGameController {
    @FXML
    public void initialize() {
        applyTheme();           // Step 1: Abstract
        engine = createEngine(...);  // Step 2: Abstract
        resetPaddlePosition();  // Step 3: Concrete
        setupButtonHandlers();  // Step 4: Concrete
        setupKeyboardHandlers(); // Step 5: Concrete
        setupGameLoop();        // Step 6: Concrete
    }
    
    protected abstract void applyTheme();
    protected abstract Object createEngine(...);
    protected abstract Ball getEngineBall();
}

// UniverseController.java (Concrete implementation)
public class UniverseController extends EventGameController {
    @Override
    protected void applyTheme() {
        // Universe-specific theme
    }
    
    @Override
    protected Object createEngine(...) {
        return new UniverseEngine(...);
    }
}
```

#### **Strategy Pattern**
```java
// EventLevelLoader.java defines strategy interface
public abstract class EventLevelLoader {
    protected abstract String getEventFolder();
    protected abstract Brick createBrickFromChar(char ch, int col, int row);
    protected abstract List<Brick> createFallbackLevel(int levelIndex);
}

// Different strategies for each event
class UniverseLevelLoader extends EventLevelLoader { ... }
class CastleLevelLoader extends EventLevelLoader { ... }
class PenaldoLevelLoader extends EventLevelLoader { ... }
```

#### **Observer Pattern** (Callbacks)
```java
// EventGameEngine.java (Subject)
protected IntConsumer scoreCallback;
protected IntConsumer livesCallback;
protected Runnable winCallback;

// Notify observers
score += 100;
scoreCallback.accept(score);

// EventGameController.java (Observer)
engine = new UniverseEngine(
    pane, paddle, ball,
    this::updateScoreUI,    // Observer 1
    this::updateLivesUI,    // Observer 2
    this::updateLevelUI     // Observer 3
);
```

#### **State Pattern** (Implicit)
```java
// GameEngine.java
private boolean levelDone = false;
private boolean gameOver = false;

public void update() {
    if (gameOver) return;           // State: GAME_OVER
    if (levelDone) { ... return; }  // State: LEVEL_COMPLETE
    if (ball.isAttached()) { ... }  // State: READY_TO_LAUNCH
    // State: PLAYING
}
```

---

## 5. LUỒNG HOẠT ĐỘNG

### 5.1 Application Startup Flow

```
┌──────────────┐
│ MainApp.java │ ← Entry point
└──────┬───────┘
       │ start(Stage stage)
       ↓
┌──────────────────────┐
│ showMainMenu()       │
│ Load MainMenu.fxml   │
└──────┬───────────────┘
       │
       ↓
┌────────────────────────┐
│ MainMenuController     │
│ initialize()           │
│  ├─ Apply background   │
│  ├─ Play menu music    │
│  └─ Setup button events│
└────────────────────────┘
```

### 5.2 Classic Mode Game Flow

```
User clicks "Play Classic"
       ↓
showLevelSelect()
       ↓
User selects Level 5
       ↓
MainApp.showGame(5)
       ↓
Load Game.fxml → GameController
       ↓
GameController.startLevel(5, false)
       │
       ├─ Create GameEngine(pane, paddle, ball)
       │   ├─ Initialize Ball, Paddle
       │   └─ Load bricks from level5.txt
       │
       ├─ Setup keyboard handlers (LEFT, RIGHT, SPACE)
       │
       └─ Start AnimationTimer (60 FPS)
           │
           └─ Game Loop (every frame):
               ├─ Handle input (move paddle)
               ├─ Update physics (ball.move())
               ├─ Check collisions:
               │   ├─ Ball ↔ Wall
               │   ├─ Ball ↔ Paddle (angle calculation)
               │   └─ Ball ↔ Brick (corner detection!)
               ├─ Update PowerUps
               └─ Check win/lose conditions
```

### 5.3 Event Mode Flow (Universe Example)

```
User clicks "Universe Event"
       ↓
MainApp.showUniverse(1)
       ↓
Load Universe.fxml → UniverseController
       ↓
UniverseController.initialize()  [Template Method]
       │
       ├─ applyTheme()  [Hook] → Universe space theme
       │
       ├─ createEngine()  [Hook] → UniverseEngine
       │   └─ UniverseEngine.loadLevel(1)
       │       └─ UniverseLevelLoader.loadLevel(1)
       │           ├─ Read universe1.txt
       │           └─ createBrickFromChar('A') → AlienBrick
       │
       ├─ setupKeyboardHandlers()  [Concrete]
       │
       └─ setupGameLoop()  [Concrete]
           └─ AnimationTimer:
               ├─ Handle input
               ├─ engine.update()
               │   ├─ updateAliens()  → Shoot lasers
               │   ├─ updateShips()   → Move horizontally
               │   └─ updateLasers()  → Check paddle hit
               └─ Update UI
```

### 5.4 Shop System Flow

```
User clicks "Shop"
       ↓
MainApp.showShop()
       ↓
Load Shop.fxml → ShopController
       ↓
ShopController.initialize()
       │
       ├─ Load GameState.INSTANCE.getCoins()  → Display balance
       │
       ├─ Load SkinManager.INSTANCE  → Check unlocked skins
       │
       └─ Display shop items:
           ├─ Paddle Skins (50-100 coins)
           └─ Ball Skins (50-100 coins)
           
User clicks "Buy Paddle Skin"
       ↓
Check if sufficient coins
       ↓ YES
Deduct coins:
    GameState.INSTANCE.spendCoins(50)
       ↓
Unlock skin:
    SkinManager.INSTANCE.unlockPaddleSkin(PaddleSkin.IRON)
       ↓
Update UI: Show "UNLOCKED!" message
```

---

## 6. PHÂN TÍCH CHI TIẾT TỪNG LAYER

### 6.1 PRESENTATION LAYER (Controllers + FXML)

#### **Responsibility:**
- Handle user input (button clicks, keyboard)
- Display UI (labels, dialogs)
- Navigate between scenes

#### **Key Components:**

| Controller | Purpose | FXML File |
|-----------|---------|-----------|
| `MainMenuController` | Main menu screen, play/shop/settings buttons | `MainMenu.fxml` |
| `SelectModeController` | Choose Classic/Story/Event mode | `SelectMode.fxml` |
| `SelectLevelController` | Choose level 1-16 (Classic) | `SelectLevel.fxml` |
| `SelectEventController` | Choose event (Universe/Castle/etc) | `SelectEvent.fxml` |
| `SettingsController` | Music/SFX volume, controls | `Settings.fxml` |
| `ShopController` | Buy paddle/ball skins | `Shop.fxml` |
| `GameController` | Main game UI (Classic mode) | `Game.fxml` |
| `UniverseController` | Universe event UI | `Universe.fxml` |

#### **Communication Pattern:**
```java
// Controller → MainApp (Facade)
@FXML
private void handlePlayClassic() {
    MainApp.showLevelSelect();
}

// Controller → Singleton
@FXML
private void handleBuyItem() {
    if (GameState.INSTANCE.getCoins() >= price) {
        GameState.INSTANCE.spendCoins(price);
        SkinManager.INSTANCE.unlockPaddleSkin(skin);
    }
}
```

---

### 6.2 LOGIC LAYER (Game Engines)

#### **Classic Mode: GameEngine.java**

**Responsibilities:**
- Physics simulation (ball movement, paddle collision)
- Brick collision detection (corner-aware algorithm)
- PowerUp system (drop, collect, activate, deactivate)
- Score/lives tracking
- Win/lose conditions

**Key Methods:**
```java
public void update() {
    if (gameOver) return;
    
    // 1. Move ball
    ball.move();
    
    // 2. Check collisions
    checkWallCollision();
    checkPaddleCollision();  // Angle calculation based on hit position
    checkBrickCollision();   // Corner-aware algorithm
    
    // 3. Update PowerUps
    updatePowerUps();
    
    // 4. Check win/lose
    if (allBricksDestroyed()) {
        levelComplete();
    }
    if (ballFell()) {
        loseLife();
    }
}
```

**Collision Detection Algorithm:**
```java
// 1. Find closest point on brick to ball center
double nearX = clamp(ballX, brickLeft, brickRight);
double nearY = clamp(ballY, brickTop, brickBottom);

// 2. Check distance
double dist = distance(ballX, ballY, nearX, nearY);
if (dist > ballRadius) return;  // No collision

// 3. Determine hit side (handle corners!)
boolean atCorner = (nearX == left || nearX == right) && 
                   (nearY == top || nearY == bottom);

if (atCorner) {
    // Use velocity direction to decide bounce
    if (abs(vx) > abs(vy)) bounceX(); else bounceY();
} else {
    // Use overlap to decide bounce
    if (minOverlap == overlapTop || minOverlap == overlapBottom)
        bounceY();
    else
        bounceX();
}
```

#### **Event Mode: EventGameEngine.java (Abstract)**

**Template Method Pattern:**
```java
public abstract class EventGameEngine {
    // Template method
    public void update() {
        if (gameOver) return;
        
        // Common logic
        ball.move();
        checkWallCollision();
        checkPaddleCollision();
        checkBrickCollision();
        
        // Hook for event-specific logic
        onUpdate();  // ← Abstract method
        
        if (checkWinCondition()) {  // ← Abstract method
            levelComplete();
        }
    }
    
    protected abstract void onUpdate();
    protected abstract boolean checkWinCondition();
    protected abstract List<Brick> loadLevelBricks(int level);
}
```

**Event-Specific Implementations:**

| Event | Engine Class | Special Logic |
|-------|-------------|---------------|
| Universe | `UniverseEngine` | `updateAliens()` → Shoot lasers<br>`updateShips()` → Move horizontally<br>`updateLasers()` → Check paddle hit |
| Castle Attack | `CastleAttackEngine` | `updateCanons()` → Shoot cannonballs<br>`updateSoldiers()` → March down<br>`updateGates()` → Spawn soldiers |
| Penaldo | `PenaldoEngine` | `updateDefenders()` → Block ball<br>`updateGoals()` → Must destroy all |
| Treasure Hunter | `TreasureHunterEngine` | `updateTreasures()` → Drop coins<br>`updateTraps()` → Penalty |

---

### 6.3 DATA LAYER (Singletons + Loaders)

#### **GameState.java** (Global State)
```java
public class GameState {
    public static final GameState INSTANCE = new GameState();
    
    private int coins = 0;
    private int highScore = 0;
    
    public void addCoins(int amount) { coins += amount; }
    public void spendCoins(int amount) { coins -= amount; }
    public int getCoins() { return coins; }
}
```

#### **SkinManager.java** (Inventory)
```java
public class SkinManager {
    public static final SkinManager INSTANCE = new SkinManager();
    
    private Set<PaddleSkin> unlockedPaddles = new HashSet<>();
    private Set<BallSkin> unlockedBalls = new HashSet<>();
    
    public void unlockPaddleSkin(PaddleSkin skin) {
        unlockedPaddles.add(skin);
    }
    
    public boolean isUnlocked(PaddleSkin skin) {
        return unlockedPaddles.contains(skin);
    }
}
```

#### **LevelLoader.java** (File I/O)
```java
public class LevelLoader {
    public static List<Brick> loadLevel(int levelNum) {
        String path = "/levels/level" + levelNum + ".txt";
        InputStream stream = LevelLoader.class.getResourceAsStream(path);
        
        // Read file line by line
        // Parse characters: '1'→1hp brick, '2'→2hp brick, 'W'→wall
        
        return bricks;
    }
}
```

**Level File Format:**
```
# level1.txt
# Format: '1'=1hp, '2'=2hp, '3'=3hp, 'W'=wall, ' '=empty

2222222222222
111111111111
  222222  
    1111    
```

---

## 7. RESOURCE MANAGEMENT

### 7.1 Asset Loading Strategy

```java
// Image loading pattern (used in skins)
try {
    Image img = new Image(getClass().getResourceAsStream(
        "/images/paddle/paddle_iron.png"
    ));
    paddle.setFill(new ImagePattern(img));
} catch (Exception e) {
    // Fallback to solid color
    paddle.setFill(Color.GRAY);
}
```

### 7.2 Sound Management

```java
public class SoundManager {
    private MediaPlayer menuMusicPlayer;
    private MediaPlayer gameMusicPlayer;
    private Map<String, AudioClip> sfxClips = new HashMap<>();
    
    public void playMenuMusic() {
        if (menuMusicPlayer != null) menuMusicPlayer.stop();
        
        Media media = new Media(getClass().getResource(
            "/sound/menu_music.mp3").toExternalForm());
        menuMusicPlayer = new MediaPlayer(media);
        menuMusicPlayer.setCycleCount(MediaPlayer.INDEFINITE);
        menuMusicPlayer.play();
    }
    
    public void playSFX(String name) {
        AudioClip clip = sfxClips.get(name);
        if (clip != null) clip.play();
    }
}
```

### 7.3 FXML Loading Pattern

```java
// In MainApp.java (Facade)
public static void showGame(int level) throws Exception {
    FXMLLoader loader = new FXMLLoader(
        MainApp.class.getResource("/fxml/Game.fxml")
    );
    Scene scene = new Scene(loader.load(), 1148, 708);
    
    // Get controller and call its methods
    GameController controller = loader.getController();
    controller.startLevel(level, false);
    
    stage.setScene(scene);
    stage.show();
}
```

---

## 8. DEPENDENCY GRAPH

### 8.1 Package Dependencies

```
core.MainApp (Facade)
    ↓
    ├─→ controller.*Controller
    │       ↓
    │       ├─→ logic.GameEngine
    │       ├─→ event.*.Engine
    │       ├─→ controller.GameState (Singleton)
    │       ├─→ controller.SkinManager (Singleton)
    │       └─→ controller.SoundManager (Singleton)
    │
    ├─→ ui.*Dialog
    │       ↓
    │       └─→ controller.GameState
    │
    └─→ storycontroller.*
            ↓
            └─→ storycontroller.StoryLoader
```

### 8.2 Key Dependency Rules

**✅ GOOD:**
- Controllers depend on Engines (abstraction)
- Engines don't know about Controllers (callbacks)
- Singletons are accessed globally (GameState, SkinManager)
- MainApp acts as Facade for scene navigation

**❌ AVOID:**
- Engine depending on JavaFX UI components
- Circular dependencies
- Direct Scene manipulation in non-controller classes

---

## 9. CLASS DIAGRAM (Key Classes)

```
┌─────────────────────┐
│   MainApp           │
│  (Facade)           │
└──────────┬──────────┘
           │
           ├──────────────────────────────────┐
           │                                  │
┌──────────▼──────────┐         ┌────────────▼─────────┐
│ MainMenuController  │         │ GameController       │
└──────────┬──────────┘         └────────────┬─────────┘
           │                                  │
           │ uses                             │ uses
           ▼                                  ▼
┌────────────────────┐          ┌─────────────────────┐
│  GameState         │◄─────────│  GameEngine         │
│  (Singleton)       │          │  (Logic)            │
└────────────────────┘          └──────────┬──────────┘
                                           │
                           ┌───────────────┼───────────────┐
                           │               │               │
                     ┌─────▼────┐   ┌─────▼────┐   ┌─────▼────┐
                     │   Ball   │   │  Paddle  │   │  Brick   │
                     │ (Entity) │   │ (Entity) │   │ (Entity) │
                     └──────────┘   └──────────┘   └──────────┘

┌─────────────────────────────────────────────────────────────┐
│              EVENT SYSTEM (Inheritance Hierarchy)            │
├─────────────────────────────────────────────────────────────┤
│  EventGameController (Abstract)                             │
│  EventGameEngine (Abstract)                                 │
│  EventLevelLoader (Abstract)                                │
└──────┬──────────────────────────────────────────────────────┘
       │
       ├──────────────────────────────────┐
       │                                  │
┌──────▼──────────┐          ┌───────────▼─────────┐
│ UniverseController│        │ CastleAttackController│
│ UniverseEngine    │        │ CastleAttackEngine    │
│ UniverseLoader    │        │ CastleLoader          │
└───────────────────┘        └───────────────────────┘
```

---

## 10. KẾT LUẬN

### 10.1 Điểm Mạnh Của Kiến Trúc

✅ **Separation of Concerns:** UI, Logic, Data tách biệt rõ ràng  
✅ **Extensibility:** Dễ dàng thêm events mới (Template Method)  
✅ **Code Reuse:** Base classes chứa common logic  
✅ **Maintainability:** Sửa 1 module không ảnh hưởng modules khác  
✅ **Testability:** Logic layer có thể test độc lập  
✅ **Scalability:** Singleton pattern cho global state  

### 10.2 Hạn Chế Và Cải Tiến

❌ **God Class:** MainApp có quá nhiều static methods  
→ **Solution:** Tạo `SceneNavigator` service

❌ **Tight Coupling:** Controllers biết về MainApp  
→ **Solution:** Dependency Injection

❌ **No Interfaces:** EventGameEngine nên implement `IGameEngine`  
→ **Solution:** Extract interfaces

❌ **Hardcoded Values:** Width/Height hardcoded  
→ **Solution:** Config file (properties)

❌ **No Persistence:** Coins/skins không save vào file  
→ **Solution:** Add JSON serialization

### 10.3 Recommendations

1. **Add Interfaces:**
```java
public interface IGameEngine {
    void update();
    void loadLevel(int level);
    Ball getBall();
}
```

2. **Dependency Injection:**
```java
public class GameController {
    private final IGameEngine engine;
    private final ISoundManager soundManager;
    
    public GameController(IGameEngine engine, ISoundManager soundManager) {
        this.engine = engine;
        this.soundManager = soundManager;
    }
}
```

3. **Configuration File:**
```properties
# game.properties
game.width=1148
game.height=708
game.fps=60
```

4. **Persistence Layer:**
```java
public class SaveManager {
    public void saveProgress(GameState state) {
        // Serialize to JSON
    }
    
    public GameState loadProgress() {
        // Deserialize from JSON
    }
}
```

---

## 11. LEARNING PATH (Để hiểu dự án)

### Bước 1: Entry Point
1. Đọc `MainApp.java` → Hiểu scene routing
2. Đọc `MainMenuController.java` → Hiểu UI handling

### Bước 2: Classic Mode
1. Đọc `GameController.java` → Hiểu game initialization
2. Đọc `GameEngine.java` → Hiểu game loop + physics
3. Đọc `Ball.java`, `Paddle.java`, `Brick.java` → Hiểu entities

### Bước 3: Event System
1. Đọc `EventGameController.java` → Hiểu Template Method
2. Đọc `EventGameEngine.java` → Hiểu abstraction
3. Đọc `UniverseController.java` + `UniverseEngine.java` → Hiểu concrete implementation

### Bước 4: Advanced Features
1. Đọc `PowerUp.java` → Hiểu PowerUp system
2. Đọc `SkinManager.java` → Hiểu shop system
3. Đọc `StorySceneController.java` → Hiểu story mode

---

**Tác giả:** AI Analysis  
**Ngày tạo:** November 22, 2025  
**Version:** 1.0
