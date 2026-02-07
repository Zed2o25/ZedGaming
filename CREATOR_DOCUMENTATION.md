# ZedGaming - Creator Documentation
**Real-Time Multiplayer Arabic Group Games Platform**

Version: 1.0  
Last Updated: January 15, 2026  
Author: ZedGaming Team

---

## 📑 Table of Contents
1. [Project Overview](#project-overview)
2. [Technical Architecture](#technical-architecture)
3. [Game Data Structure](#game-data-structure)
4. [Core Systems](#core-systems)
5. [Firebase Integration](#firebase-integration)
6. [Game Implementation Details](#game-implementation-details)
7. [Feature Documentation](#feature-documentation)
8. [Code Structure](#code-structure)
9. [Development Guide](#development-guide)
10. [Future Enhancements](#future-enhancements)

---

## 1. Project Overview

### Purpose
ZedGaming is a **bilingual (Arabic/English) multiplayer web-based gaming platform** designed for group entertainment, education, and social interaction. It features 8 different games with real-time synchronization, supporting both competitive and collaborative gameplay.

### Target Audience
- **Families**: Multi-generational entertainment
- **Educators**: Classroom activities and learning
- **Friends**: Social gaming sessions
- **Organizations**: Team building activities

### Key Features
✅ **8 Complete Games** with multiplayer support  
✅ **Real-time Firebase Sync** with instant updates  
✅ **Bilingual Interface** (Arabic RTL + English)  
✅ **Role-Based System** (Host, Player, Observer, Moderator)  
✅ **Privacy Controls** (Public/Private rooms)  
✅ **Screen Persistence** (survives F5 refresh)  
✅ **Eye-Relaxing Design** (purple/pink gradients)

---

## 2. Technical Architecture

### Technology Stack
```
Frontend: HTML5, CSS3, Vanilla JavaScript
Database: Firebase Realtime Database (europe-west1)
Hosting: GitHub Pages
SDK: Firebase JavaScript SDK 10.7.1
```

### File Structure
```
ZedGaming/
├── game.html                    # Main application (3,473 lines)
├── manifest.json                # PWA manifest
├── README.md                    # Project overview
├── CREATOR_DOCUMENTATION.md     # This file
├── USER_MANUAL.md              # User guide
└── game_backup_2026-01-15.html # Backup before major changes
```

### Architecture Pattern
- **Single-Page Application (SPA)**
- **Event-Driven Real-Time Updates**
- **State Management via localStorage + Firebase**
- **Room-Based Multiplayer System**

---

## 3. Game Data Structure

### All Games Use **HARDCODED DATA** (No External APIs)

#### Word Chain Topics
```javascript
const wordChainTopics = [
    { topic: 'حيوانات', words: ['أسد', 'قط', 'كلب', 'فيل', ...] },
    { topic: 'فواكه', words: ['تفاح', 'موز', 'برتقال', ...] },
    { topic: 'بلدان', words: ['مصر', 'السعودية', 'الإمارات', ...] },
    { topic: 'ألوان', words: ['أحمر', 'أزرق', 'أخضر', ...] },
    { topic: 'مهن', words: ['طبيب', 'معلم', 'مهندس', ...] }
];
```

#### Quiz Questions
```javascript
const quizQuestions = [
    {
        question: 'ما هي عاصمة السعودية؟',
        answers: ['الرياض', 'جدة', 'مكة', 'الدمام'],
        correct: 0  // Index of correct answer
    },
    // ... 20+ questions
];
```

#### True/False Questions
```javascript
const trueFalseQuestions = [
    { question: 'الشمس تدور حول الأرض', answer: false },
    { question: 'الماء يتكون من الهيدروجين والأكسجين', answer: true },
    // ... 15 statements
];
```

#### Memory Match Emojis
```javascript
const memoryEmojis = ['🌟', '🎨', '🎭', '🎪', '🎯', '🎲', '🎮', '🎸'];
// Creates 16 cards (8 pairs)
```

#### Dynamically Generated Data
- **Color Game**: Random color from 6 options
- **Number Game**: Random number 1-100
- **Reaction Speed**: Random delay (2000-5000ms)
- **Drawing Game**: User-generated content (Firebase-synced)

---

## 4. Core Systems

### 4.1 gameState Object
**Central state management stored in localStorage**

```javascript
const gameState = {
    playerName: '',           // User's display name
    roomCode: '',            // 4-letter room code
    players: [],             // Array of player objects
    score: 0,                // Current player's score
    round: 0,                // Current round number
    isHost: false,           // Is this user the room host?
    hostMode: false,         // Host-controlled or normal mode
    hostIsModerator: false,  // Host moderating (not playing)
    privateRoom: false,      // Public or private room
    role: 'player',          // 'player' or 'observer'
    currentGame: '',         // Current game type
    currentScreen: ''        // Current UI screen
};
```

### 4.2 Room System
**4-Letter Alphanumeric Codes**
- Generated on room creation
- Case-insensitive
- Unique identification
- Example: `ABCD`, `XY12`, `GAME`

### 4.3 Firebase Database Structure
```
rooms/
└── {roomCode}/
    ├── metadata/
    │   ├── hostName: "Ahmad"
    │   ├── createdAt: 1704889200000
    │   ├── isPrivate: false
    │   └── hostIsModerator: false
    ├── players/
    │   └── {playerName}/
    │       ├── name: "Ahmad"
    │       ├── score: 120
    │       ├── role: "player"
    │       ├── isModerator: false
    │       └── timestamp: 1704889200000
    ├── wordChain/
    │   ├── topic: {...}
    │   └── words: [...]
    └── drawing/
        ├── currentDrawer: "Ahmad"
        ├── currentWord: "شجرة"
        ├── strokes: [...]
        └── guesses: [...]
```

---

## 5. Firebase Integration

### 5.1 Initialization
```javascript
const firebaseConfig = {
    apiKey: "AIzaSyCdYSMZYlmQ6W7OsJw9gBn8wV2xrX_-ZZQ",
    authDomain: "zedgameing.firebaseapp.com",
    databaseURL: "https://zedgameing-default-rtdb.europe-west1.firebasedatabase.app",
    projectId: "zedgameing",
    storageBucket: "zedgameing.firebasestorage.app",
    messagingSenderId: "780256663651",
    appId: "1:780256663651:web:c7b15f3d8ac5fdd6e1b5e8"
};
```

### 5.2 Listener-First Approach (⚡ Key Innovation)
**Problem**: Player 3 delay bug - third player to join saw outdated data

**Solution**: Set up listeners BEFORE writing data
```javascript
// STEP 1: Setup listener FIRST
roomRef.on('value', (snapshot) => {
    // Handle updates
});

// STEP 2: THEN write data
roomRef.set({ ... });
```

### 5.3 Real-Time Sync Pattern
```javascript
function updateRoomScores() {
    if (!gameState.roomCode) return;
    
    if (isFirebaseReady) {
        const playerRef = database.ref(`rooms/${gameState.roomCode}/players/${gameState.playerName}`);
        playerRef.set({
            name: gameState.playerName,
            score: gameState.score,
            role: gameState.role || 'player',
            timestamp: Date.now()
        });
    }
}
```

---

## 6. Game Implementation Details

### 6.1 Color Match Game
**Type**: Host-Controlled Recognition  
**Scoring**: +10 points correct, -5 points wrong  
**Data**: Dynamically generated from 6 colors

```javascript
const colors = [
    { name: 'أحمر', color: '#ff0000' },
    { name: 'أزرق', color: '#0000ff' },
    // ... 6 colors total
];
```

**Flow**:
1. Host (or auto) shows random color
2. Players select matching color name
3. Instant feedback with color-coded status
4. Score updates in real-time

### 6.2 Quiz Game
**Type**: Multiple Choice Q&A  
**Scoring**: +20 points correct, 0 wrong  
**Data**: 20+ hardcoded questions

**Flow**:
1. Question displayed with 4 answers
2. Player selects answer
3. Visual feedback (green/red)
4. Auto or manual progression

### 6.3 Number Guess Game
**Type**: Hot-Cold Guessing  
**Scoring**: +30 exact, +20 close (±10), +10 warm (±25)  
**Timer**: 30 seconds per round

**Flow**:
1. Random number 1-100 generated
2. Players submit guesses
3. Hints: "أعلى" (higher) or "أقل" (lower)
4. Score based on proximity

### 6.4 Reaction Speed Game
**Type**: Timing Challenge  
**Scoring**: Based on reaction time (faster = more points)  
**Mechanism**: Click when color changes to green

**Flow**:
1. Red box appears with "انتظر..." (Wait...)
2. Random delay 2-5 seconds
3. Turns green → Player clicks
4. Time measured in milliseconds

### 6.5 True/False Game
**Type**: Binary Choice Questions  
**Scoring**: +15 correct, 0 wrong  
**Data**: 15 true/false statements

**Flow**:
1. Statement displayed
2. Player chooses ✅ صح or ❌ خطأ
3. Immediate feedback
4. Educational explanations possible

### 6.6 Word Chain Game
**Type**: Collaborative Word Building  
**Scoring**: +15 per valid word  
**Multiplayer**: TRUE - shared topic and word list

**Flow**:
1. Host picks random topic (animals, fruits, etc.)
2. All players see same topic via Firebase
3. Any player submits word
4. Valid words added to shared list
5. Shows player attribution
6. Prevents duplicate words

**Key Features**:
- Real-time word sync across all players
- Player names shown with their words
- Your words highlighted in green
- Refresh-persistent (topic remains)
- Race condition protection (mutex lock)

### 6.7 Drawing Game
**Type**: Turn-Based Collaborative Guessing  
**Scoring**: +25 first correct guess, +15 drawer  
**Canvas**: HTML5 with real-time stroke broadcasting

**Flow**:
1. Players take turns drawing
2. Real-time canvas sync via Firebase
3. Other players guess via chat
4. First correct guess wins
5. Chat feed shows all guesses

**Technical Details**:
- Stroke-by-stroke Firebase sync
- Turn rotation algorithm
- Clear canvas between rounds
- Drawing words pool (50+ words)

### 6.8 Memory Match Game
**Type**: Solo Card Matching  
**Scoring**: +20 per match, +100 speed bonus  
**Grid**: 4x4 (16 cards, 8 pairs)

**Flow**:
1. Cards shuffled and displayed face-down
2. Click to flip, max 2 at a time
3. Match → Cards stay revealed (+20)
4. No match → Cards flip back
5. Timer tracks completion time
6. Speed bonus awarded

**Note**: Single-player game (no real-time sync needed)

---

## 7. Feature Documentation

### 7.1 Host vs Player Roles

**Host Powers**:
- Control game progression (in Host Mode)
- Start new rounds
- Choose to play or moderate
- Access to "Next Round" buttons

**Player Limitations**:
- Cannot control game flow in Host Mode
- Auto-progression in Normal Mode
- Can view all games

### 7.2 Moderator Mode
**Purpose**: Host can observe without playing

**Implementation**:
```javascript
gameState.hostIsModerator: boolean

// Block moderator from gameplay
if (gameState.isHost && gameState.hostIsModerator) {
    return; // or show alert
}
```

**UI Indicators**:
- Badge in header: "👁️ مراقب | Moderator"
- Scoreboard shows "👁️ مراقب" instead of score
- Toggle button in game mode screen

**Use Cases**:
- Teachers monitoring students
- Parents supervising kids
- Fair competition oversight

### 7.3 Player vs Observer Mode
**New Feature** (Just Implemented!)

**Observer Role**:
- Can join rooms and watch gameplay
- Cannot participate in games
- Does NOT appear in player rankings
- Separate scoreboard section

**Implementation**:
```javascript
gameState.role: 'player' | 'observer'

// Role selection on join
function selectRole(role) {
    gameState.role = role;
    // ... setup with role
}

// Block observers from playing
if (gameState.role === 'observer') {
    alert('👁️ أنت مراقب - لا يمكنك اللعب');
    return;
}
```

**Scoreboard Display**:
```
🏆 اللاعبون | Players
─────────────────────
👑 Ahmad - 120
🎮 Sarah - 95
🎮 Ali - 80

👁️ المراقبون | Observers
─────────────────────
👁️ Teacher
👁️ Parent
```

**Benefits**:
- Educational settings (teacher observes students)
- Learning by watching
- Large group accommodation
- Spectator mode for entertainment

### 7.4 Privacy Modes

**Public Rooms**:
- Appear in room browser
- Anyone can join with code
- Default mode

**Private Rooms**:
- Hidden from browser
- Require code to join
- For exclusive groups

**Toggle**: Host can switch anytime

### 7.5 Screen Persistence
**Problem**: F5 refresh lost game state

**Solution**: localStorage + Firebase sync
```javascript
window.addEventListener('load', loadGameState);

function loadGameState() {
    const saved = localStorage.getItem('gameState');
    if (saved) {
        Object.assign(gameState, JSON.parse(saved));
        // Restore UI to saved screen
        // Re-establish Firebase listeners
    }
}
```

---

## 8. Code Structure

### 8.1 Main Sections (3,473 lines)

```
Lines 1-110:    HTML Head, Meta, Firebase SDK
Lines 111-340:  CSS Styles (gradients, animations)
Lines 341-450:  Header, Connection Status
Lines 451-900:  Game UI Screens (HTML)
Lines 901-1300: Core Functions (join, create room)
Lines 1301-1600: Color & Quiz Games
Lines 1601-1900: Number & Reaction Games
Lines 1901-2200: True/False & Word Chain Setup
Lines 2201-2700: Word Chain & Drawing Logic
Lines 2701-3100: Memory Match Game
Lines 3101-3400: Scoreboard & Firebase Sync
Lines 3401-3473: Utility Functions, Event Listeners
```

### 8.2 Critical Functions

**Game Initialization**:
- `startColorGame()`
- `startQuizGame()`
- `startNumberGame()`
- `startReactionGame()`
- `startTrueFalseGame()`
- `startWordChainGame()`
- `startDrawingGame()`
- `startMemoryGame()`

**Firebase Functions**:
- `setupRoomListener()` - Real-time updates
- `updateRoomScores()` - Sync player data
- `updateScoreboardWithPlayers()` - Display rankings

**State Management**:
- `saveGameState()` - Persist to localStorage
- `loadGameState()` - Restore on refresh

---

## 9. Development Guide

### 9.1 Adding a New Game

**Step 1: Add HTML UI**
```html
<div class="game-area hidden" id="newGame">
    <h3>Game Title</h3>
    <!-- Game interface -->
    <button onclick="startNewGame()">Start</button>
</div>
```

**Step 2: Add to Game Mode Screen**
```html
<button class="game-mode-btn" onclick="startGame('newGame')">
    🎯 Game Name
</button>
```

**Step 3: Create Game Logic**
```javascript
function startNewGame() {
    gameState.round++;
    // Initialize game state
    // Setup UI
    // Start game loop
}

function handleGameAction() {
    // Check observer/moderator
    if (gameState.role === 'observer') return;
    
    // Game logic
    // Update score
    // Sync to Firebase if needed
}
```

**Step 4: Add to loadGameState()**
```javascript
else if (gameState.currentGame === 'newGame') {
    startNewGame();
}
```

### 9.2 Adding New Question Data

**Quiz Questions**:
```javascript
// Add to quizQuestions array
{
    question: 'Your question?',
    answers: ['Option A', 'Option B', 'Option C', 'Option D'],
    correct: 0  // Index of correct answer
}
```

**True/False**:
```javascript
// Add to trueFalseQuestions array
{ question: 'Statement', answer: true }
```

**Word Chain Topics**:
```javascript
// Add to wordChainTopics array
{ 
    topic: 'Topic Name',
    words: ['word1', 'word2', 'word3', ...]
}
```

### 9.3 Modifying Game Scoring

Find the game's score update line:
```javascript
gameState.score += 20; // Change this value
updateScoreboard();    // Always call this after score change
saveGameState();       // Persist changes
```

### 9.4 Styling Changes

All styles are in `<style>` tag (lines 111-340):
```css
/* Main theme colors */
--primary: #667eea;
--secondary: #764ba2;

/* Modify gradients */
.submit-btn {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
```

---

## 10. Future Enhancements

### Priority 1: Data Management
- [ ] External JSON file for questions
- [ ] Admin panel for content management
- [ ] API integration for dynamic content
- [ ] Import/Export question banks

### Priority 2: Game Features
- [ ] Leaderboard system (all-time high scores)
- [ ] Game difficulty levels
- [ ] Custom game creation
- [ ] Tournament mode
- [ ] Achievements/Badges system

### Priority 3: Social Features
- [ ] Player profiles
- [ ] Friend system
- [ ] Chat between games
- [ ] Room history
- [ ] Replay system

### Priority 4: Technical Improvements
- [ ] Progressive Web App (PWA) full support
- [ ] Offline mode
- [ ] Better mobile responsiveness
- [ ] Performance optimization
- [ ] Error handling improvements
- [ ] Analytics integration

### Priority 5: Educational Features
- [ ] Teacher dashboard
- [ ] Student progress tracking
- [ ] Custom quizzes by educators
- [ ] Learning modules
- [ ] Reports and statistics

---

## 📞 Support & Contact

For questions, bug reports, or contributions:
- GitHub: [Repository URL]
- Email: [Contact Email]

---

## 📄 License

[Specify License]

---

**Last Updated**: January 15, 2026  
**Version**: 1.0  
**Status**: Active Development
