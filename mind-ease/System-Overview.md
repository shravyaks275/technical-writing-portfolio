# Mind Ease — User Guide

## 1. Introduction

Mind Ease is a browser-based mental wellness and stress-relief website that provides users with simple tools for relaxation, mood awareness, guided meditation, breathing exercises, games, and uplifting video content.

The application is implemented as a client-side web application using HTML, CSS, and JavaScript.

No installation or server setup is required for normal browser use.

---

## 2. Getting Started

### 2.1 Opening Mind Ease

Open the Mind Ease website in a modern web browser.

The application provides a main navigation menu with access to:

- Home
- Guided Meditations
- Stress-Relief Exercises
- Mood Tracking
- Feed

The application is designed to work in a browser and includes responsive viewport configuration on its HTML pages.

---

## 3. Home Page

The home page is provided by:

`index.html`

The page contains:

- Mind Ease branding
- Main navigation
- Side navigation menu
- Welcome section
- Mental Health Survey

### 3.1 Mental Health Survey

The survey allows the user to enter:

- Name
- Age
- Current mood
- Stress level
- Additional comments

After selecting **Submit**, the page:

1. Reads the form values.
2. Creates a `FormData` object.
3. Converts the submitted fields into a JavaScript object.
4. Logs the collected values to the browser console.
5. Displays a confirmation message.
6. Resets the form.

The current implementation does not send the survey information to a server or database.

---

## 4. Navigation

Mind Ease provides links between the main application pages.

### Main Navigation

- Home → `index.html`
- Guided Meditations → `meditations.html`
- Stress-Relief Exercises → `exercises.html`
- Mood Tracking → `mood-tracking.html`
- Feed → `resources.html`

The home page also contains a side navigation menu with links to:

- Login
- Leaderboard

---

## 5. Login

The login page is provided by:

`login.html`

The page contains:

- Username field
- Password field
- Login button
- Error message
- Sign-up link

### 5.1 Login Behavior

The current login implementation is a demonstration-only implementation.

The JavaScript compares the entered values against hard-coded credentials:

- Username: `user`
- Password: `password`

If the credentials match:

1. The user is redirected to `index.html`.

If they do not match:

1. The error message becomes visible.

### 5.2 Important Limitation

The current implementation does not provide:

- Database-backed accounts
- Password hashing
- Sessions
- JWT authentication
- Server-side authentication
- User registration

The login should therefore be considered a prototype/demo feature.

---

## 6. Guided Meditations

The Guided Meditations page is provided by:

`meditations.html`

The page currently provides two meditation sessions:

- Relaxation Meditation
- Focus Meditation

Each meditation contains:

- Title
- Description
- HTML audio player
- Meditation tip

### 6.1 Playing a Meditation

To play a meditation:

1. Open **Guided Meditations**.
2. Select the desired meditation.
3. Use the browser's audio controls.
4. Play, pause, or control the audio using the browser's audio interface.

### 6.2 Meditation Tips

The page contains tips associated with each meditation.

The tips are initially hidden.

JavaScript displays the tips sequentially:

- First tip after 15 seconds.
- Second tip after 30 seconds.

---

## 7. Stress-Relief Exercises

The Stress-Relief Exercises page is provided by:

`exercises.html`

The page provides three exercises:

1. Deep Breathing
2. Color Matching Game
3. Memory Game

---

## 8. Deep Breathing Exercise

The breathing exercise is provided by:

`breath.html`

### 8.1 Starting the Exercise

1. Open **Stress-Relief Exercises**.
2. Select **Practice Now** under Deep Breathing.
3. Select **Start**.

The application displays a circular visual indicator.

The breathing sequence is:

1. Breathe In
2. Hold
3. Breathe Out

The implementation uses timed JavaScript transitions.

### 8.2 Breathing Cycle

The current implementation uses:

- 4 seconds for breathing in
- 4 seconds for holding/breathing transition
- An 8-second repeating cycle

The animation changes the size of the breathing circle to visually represent the breathing cycle.

---

## 9. Color Matching Game

The Color Matching Game is provided by:

`colors.html`

The game displays six colored squares.

### 9.1 How to Play

1. Open the Color Matching Game.
2. Observe the colored squares.
3. A target color is randomly selected.
4. Select a square.
5. If the selected color matches the target:
   - The application displays `Correct!`
   - All squares change to the selected color.
6. If the color is incorrect:
   - The selected square becomes dark.
   - The application displays `Try Again!`

### 9.2 New Colors

Select **New Colors** to generate another set of random colors.

---

## 10. Memory Match Game

The Memory Match Game is provided by:

`memory.html`

The game contains eight cards representing four matching pairs:

- Dog
- Cat
- Bear
- Panda

### 10.1 How to Play

1. Select a card.
2. Select another card.
3. The application compares the two cards.
4. Matching cards remain revealed.
5. Non-matching cards are hidden again after a short delay.
6. Continue until all pairs are found.

### 10.2 Completing the Game

When all pairs have been matched, the application displays:

`You found all matches!`

### 10.3 Resetting

Select **Reset Game** to:

- Reset the match count.
- Clear the completion message.
- Create a new board.
- Shuffle the cards.

---

## 11. Mood Tracking

The Mood Tracking page is provided by:

`mood-tracking.html`

The page provides five mood options:

- 😊
- 😢
- 😠
- 😃
- 😰

### 11.1 Recording a Mood

1. Open **Mood Tracking**.
2. Select the emoji that represents your current mood.
3. The selected emoji is temporarily highlighted.
4. The mood is added to the Previous Entries section.

### 11.2 Important Limitation

The current implementation does not save mood entries to a database or browser local storage.

Entries are created dynamically in the current HTML document.

Refreshing or leaving the page removes the displayed entries.

---

## 12. AI Listener Bot

The Mood Tracking page contains an **AI Listener Bot**.

Select:

**Talk to the Bot**

The browser requests speech-recognition access.

The feature uses the browser's:

`SpeechRecognition`

or:

`webkitSpeechRecognition`

API.

### 12.1 Listener Flow

1. Select **Talk to the Bot**.
2. Allow microphone/speech-recognition access if requested.
3. Speak.
4. The browser converts speech into text.
5. The transcript is displayed.
6. The application displays a predefined supportive response.

### 12.2 Important Limitation

The current implementation does not connect to an AI service.

The response is a fixed message:

`I hear you. It's important to talk about how you're feeling.`

---

## 13. AI Therapist

The Mood Tracking page also contains an **AI Therapist** interface.

The user can:

1. Enter a message.
2. Select **Send**.
3. View the entered message.
4. Wait approximately one second.
5. Receive a generated response.

### 13.1 How Responses Work

The application contains a predefined list of five responses.

One response is selected randomly.

The current implementation does not use:

- OpenAI
- Gemini
- Claude
- A custom AI model
- A backend API
- A database

The feature is therefore a simulated conversational interface.

---

## 14. Video Feed

The Feed page is provided by:

`resources.html`

The page dynamically creates embedded YouTube video players.

The current implementation contains two YouTube embed URLs directly in the JavaScript.

### 14.1 Viewing Videos

1. Open **Feed**.
2. The application loads the configured video URLs.
3. Each URL is converted into an iframe.
4. The videos are displayed inside the page.

---

## 15. Leaderboard

The Leaderboard page is provided by:

`leaderboard.html`

The page displays a weekly table containing:

- User
- Monday
- Tuesday
- Wednesday
- Thursday
- Friday
- Saturday
- Sunday

The current leaderboard uses hard-coded sample data for:

- Alice
- Bob
- Charlie

The data is not retrieved from a server or database.

---

## 16. Browser Requirements

Mind Ease uses standard browser technologies including:

- HTML5
- CSS
- JavaScript
- HTML5 Audio
- Browser Speech Recognition API
- Embedded YouTube content

Speech recognition availability may vary between browsers.

---

## 17. Troubleshooting

### The breathing exercise does not start

Check that:

- JavaScript is enabled.
- The page loaded correctly.
- The Start button is available.

### Speech recognition does not work

Check that:

- Your browser supports SpeechRecognition or webkitSpeechRecognition.
- Microphone/speech permissions are allowed.
- The browser has access to the microphone.

### The video feed is empty

Check that:

- You have an internet connection.
- YouTube content is accessible.
- The configured embed URLs are valid.

### Login does not work

Use the demonstration credentials defined by the application:

- Username: `user`
- Password: `password`

This is a prototype login and does not represent production authentication.

### Mood entries disappear

This is expected with the current implementation.

Mood entries are stored only in the current page DOM and are not persisted.

---

## 18. Current Application Limitations

Mind Ease is currently a front-end prototype.

The repository does not currently implement:

- Persistent user accounts
- Database storage
- Server-side APIs
- Persistent mood history
- Real AI integration
- Production authentication
- User registration
- Server-side authorization
- Persistent leaderboard data

---

## 19. Summary

Mind Ease provides a collection of browser-based wellness and stress-relief experiences.

The main capabilities include:

- Mental health survey
- Guided meditation
- Breathing exercise
- Color matching game
- Memory game
- Mood selection
- Speech-recognition listener
- Simulated AI therapist
- Embedded video feed
- Sample leaderboard
- Demonstration login

The application is implemented primarily with HTML, CSS, and client-side JavaScript.
