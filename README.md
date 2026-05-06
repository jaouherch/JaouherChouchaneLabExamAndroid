# 🏛️ Tunisia Heritage Quest

> An Android quiz game where players identify Tunisian historical monuments and cities from images.

![Platform](https://img.shields.io/badge/Platform-Android-green)
![Language](https://img.shields.io/badge/Language-Kotlin-purple)
![UI](https://img.shields.io/badge/UI-Jetpack%20Compose-blue)
![Architecture](https://img.shields.io/badge/Architecture-MVVM-orange)

---

## 📖 Project Overview

**Tunisia Heritage Quest** is an educational image-identification quiz game built for Android. Players are shown photographs of Tunisian historical sites and must identify them from 4 multiple-choice options.

| Field | Details |
|---|---|
| **Student** | Jaouher Chouchane |
| **Institution** | Tunis Business School — Université de Tunis |
| **Course** | IT370 — Mobile Application Development |
| **Academic Year** | 2025–2026 |
| **AI Tools Used** | Claude (Anthropic) — used for code structure assistance and documentation |

### Target Audience
Students, tourists, history enthusiasts, and the Tunisian diaspora (ages 12+)

### Categories Implemented
| Category | Status | Questions |
|---|---|---|
| 🏟️ Roman Heritage | ✅ Active | 10–15 questions |
| 🕌 Islamic Heritage | ✅ Active | 5 questions |
| ⚓ Punic & Pre-Roman | ✅ Active | 7 questions |
| 🏙️ Cities | ✅ Active | 5 questions |
| 🏛️ Modern Heritage | 🔜 Coming Soon | — |
| 🌿 Natural & Mixed Sites | 🔜 Coming Soon | — |

---

## 🏗️ Architecture Explanation

The app follows the **MVVM (Model-View-ViewModel)** architecture pattern with Jetpack components.

```
app/
├── data/
│   ├── model/          # Data classes: Question, Category, Score, Difficulty
│   └── repository/     # QuizRepository — provides questions to ViewModels
├── ui/
│   ├── screens/        # Compose screens (6 screens)
│   │   ├── SplashScreen.kt
│   │   ├── HomeScreen.kt
│   │   ├── CategoryScreen.kt
│   │   ├── DifficultyScreen.kt
│   │   ├── QuizScreen.kt
│   │   ├── QuestionResultScreen.kt
│   │   └── ResultsScreen.kt
│   ├── viewmodel/      # QuizViewModel, HomeViewModel
│   ├── components/     # Reusable Compose components
│   └── theme/          # Colors, Typography, Shapes
├── navigation/
│   └── AppNavigation.kt  # NavHost + route definitions
└── MainActivity.kt
```

### Layer Responsibilities

| Layer | Files | Role |
|---|---|---|
| **Model** | `Question.kt`, `Category.kt`, `Score.kt` | Data structures & business logic |
| **Repository** | `QuizRepository.kt` | Single source of truth for question data |
| **ViewModel** | `QuizViewModel.kt`, `HomeViewModel.kt` | UI state management via `StateFlow` |
| **View** | All `*Screen.kt` files | Observes ViewModel, renders UI with Compose |

### Navigation
Navigation uses **Jetpack Navigation Compose** with a single `NavHost`:

```
SplashScreen
    └── HomeScreen
            └── CategoryScreen
                    └── DifficultyScreen/{categoryId}
                            └── QuizScreen/{categoryId}/{difficulty}
                                    ├── QuestionResultScreen
                                    └── ResultsScreen
                                            └── HomeScreen (Play Again)
```

### Activity Lifecycle
| Event | Handled In | Action |
|---|---|---|
| `onCreate` | `MainActivity` | Sets up NavHost and Compose content |
| `onResume` | `QuizViewModel` via `DisposableEffect` | Resumes countdown timer |
| `onPause` | `QuizViewModel` via `DisposableEffect` | Pauses timer, saves current state |
| `onDestroy` | `ViewModel.onCleared()` | Cancels coroutines, releases resources |
| Config change (rotation) | `ViewModel` survives automatically | State preserved via `StateFlow` |

---

## ⚙️ Setup Instructions

### Prerequisites
- Android Studio **Hedgehog (2023.1.1)** or later
- Android SDK **API 26** (Android 8.0) minimum
- Kotlin **1.9+**
- Gradle **8.0+**

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/tunisia-heritage-quest.git
   cd tunisia-heritage-quest
   ```

2. **Open in Android Studio**
   - File → Open → select the project folder
   - Wait for Gradle sync to complete

3. **Run the app**
   - Select an emulator (API 26+ recommended) or connect a physical device
   - Click the ▶ Run button or press `Shift+F10`

4. **Run tests**
   ```bash
   # Unit tests
   ./gradlew test

   # Instrumented tests (requires emulator/device)
   ./gradlew connectedAndroidTest
   ```

---

## 🧪 Testing

The project implements all **three required test types**:

### 1. Unit Tests (`app/src/test/`)
Test business logic in isolation (no device needed):

```kotlin
// Run with: ./gradlew test
class QuizViewModelTest {
    @Test fun scoreIsZeroOnInit()
    @Test fun correctAnswerIncrementsScore()
    @Test fun wrongAnswerDoesNotChangeScore()
    @Test fun nextQuestionAdvancesIndex()
    @Test fun timerCountsDown()
}

class QuizRepositoryTest {
    @Test fun romanCategoryReturnsCorrectCount()
    @Test fun questionsHaveFourOptions()
}

class DifficultyTest {
    @Test fun easyDifficultyHasHints()
    @Test fun hardDifficultyNoHints()
}
```

### 2. Navigation Tests (`app/src/androidTest/`)
Tests that screen transitions work correctly:

```kotlin
// Run with: ./gradlew connectedAndroidTest
class NavigationTest {
    @Test fun splashNavigatesToHome()
    @Test fun homeToCategories()
    @Test fun categoryToDifficulty()
    @Test fun difficultyToQuiz()
    @Test fun quizToResults()
    @Test fun resultsBackToHome()
}
```

### 3. UI / Compose Tests (`app/src/androidTest/`)
Tests that UI components render and respond correctly:

```kotlin
class HomeScreenTest {
    @Test fun startButtonIsDisplayed()
    @Test fun progressStatsRender()
}

class QuizScreenTest {
    @Test fun fourOptionsDisplayed()
    @Test fun selectOptionHighlightsIt()
    @Test fun submitButtonVisible()
}

class ResultsScreenTest {
    @Test fun scorePercentageShown()
}
```

---

## 📱 Adaptive UI

The app adapts to different screen sizes using `WindowSizeClass`:

| Screen Type | Width | Layout |
|---|---|---|
| Compact (Phone) | < 600dp | Single column, full-width cards |
| Medium (Large phone / Tablet) | 600–840dp | 2-column category grid |
| Expanded (Tablet / Foldable) | > 840dp | Side-by-side quiz layout |
| Landscape (Phone) | height < 480dp | Horizontal scroll, condensed header |

---

## ⚠️ Known Issues

- **Modern Heritage & Natural Sites** — These two categories are marked "Coming Soon" and do not have questions yet.
- **Images are bundled locally** — All monument images are stored as drawable resources. A future version could load from a remote CDN.
- **Timer on slow emulators** — On very slow emulators, the countdown timer may occasionally skip a second. Works correctly on physical devices.
- **No cloud sync** — Progress (streak, mastery %) is stored locally and resets on app uninstall.
-  **Hints are static** — Hints are not reduced when use uses them.

---

## 📊 Evaluation Rubric

| Category | Points | Implementation |
|---|---|---|
| Architecture Components | 15 | MVVM, ViewModel, StateFlow, Repository |
| Navigation Components | 10 | NavHost, NavController, typed routes |
| Activity Lifecycle | 10 | DisposableEffect, onCleared(), config changes |
| Testing | 15 | Unit + Navigation + UI Compose tests |
| Adaptive UI | 15 | WindowSizeClass: Compact / Medium / Expanded |
| UI/UX | 15 | Mediterranean blue + earth tones, animations |
| Functionality | 10 | 6 categories, timer, scoring, hints, results |
| Code Quality | 10 | Clean naming, KDoc comments, sealed classes |
| **TOTAL** | **100** | |

---

*Tunisia Heritage Quest — IT370 Practical Exam | Jaouher Chouchane | Tunis Business School 2026*
