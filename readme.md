# 👁️‍🗨️ Eye By Proctorme

A lightweight, privacy-focused widget for online AI proctoring. Provides real-time monitoring of candidates during assessments with face detection, audio activity tracking, and automated snapshots.

---

## Features

- **Face Monitoring**: Detects absence, presence of multiple faces, and snapshots.
- **Face Recognition**: Creates periodic, randomized snapshots for face recognition during assessments.
- **Audio Monitoring**: Detects sound activity during assessments.
- **Tab Monitoring**: Tracks whether the candidate’s tab is in focus.
- **Event-Driven API**: Listen and respond to proctoring events.
- **Customizable Config**: Pass candidate and exam details for each session.

---

## Installation

Add the widget script to your project:

```html
<script src="https://widget.proctorme.com/proctormewidget-widget-init.js"></script>
```

---

## Getting an API Key

Since the admin portal is still under development, please contact us to request an API key.
When requesting, include the list of domains you plan to use with the widget (e.g. yourdomain.com, exam.yourdomain.com).

---

See the [live demo](https://eyebyproctorme-sandbox.netlify.app).

## Usage

### Basic Example

```html
<script>
  async function startProctoring() {
    try {
      // Load the widget
      const widget = await LoadProctormeWidget();

      // Add event listeners
      widget.on("STARTED", () => {
        console.log("Proctoring started ▶️");
      });

      widget.on("FACE_ABSENCE", (data) => {
        console.log("Face absence 🙈", data);
      });

      widget.on("MULTIPLE_FACE", (data) => {
        console.log("Multiple faces detected 🧑‍🧒‍🧒", data);
      });

      widget.on("SOUND_DETECTED", (data) => {
        console.log("Sound detected 🎶", data);
      });

      widget.on("FACE_MISMATCH", (data) => {
        console.log("Face mismatch detected ❌", data);
      });

      widget.on("SUSPICIOUS_ACTIVITY", (data) => {
        console.log("Suspicious activity pattern detected 🚨", data);
      });

      widget.on("TAB_NOT_FOCUS", (data) => {
        console.log("Tab not in focus 💻", data);
      });

      widget.on("PERIODIC_SNAPSHOT", (data) => {
        console.log("Periodic snapshot 📸", data);
      });

      widget.on("END_PROCTORING", () => {
        console.log("Proctoring ended ⛔️");
      });

      // Configuration
      const config = {
        apiKey: "YOUR_API_KEY",
        assessmentId: "assessment123",
        assessmentTitle: "Frontend Development Assessment",
        candidateId: "candidate456",
        candidateEmail: "test@example.com",
        candidateFirstName: "John",
        candidateLastName: "Doe",
        candidateImageUrl: "https://example.com/avatar.jpg",
        institutionName: "Tech Academy International",
        examDuration: 1800, // in seconds
        features: {
          aiProctoring: true,
          facialRecognition: true,
        },
      };

      // Initialize the widget
      widget.init(config);
    } catch (error) {
      console.error("Error initializing widget:", error);
    }
  }

  startProctoring();
</script>
```

---

## API Methods

| Method                       | Description                                |
| ---------------------------- | ------------------------------------------ |
| `widget.init(config)`        | Initializes the widget with configuration. |
| `widget.on(event, callback)`      | Listens for widget events.                                   |
| `widget.endProctoring()`          | Manually ends the proctoring session.                        |
| `widget.resetSessionListeners()`  | Clears all public event listeners registered via `widget.on()`. |

---

## Events

### Payload return types
```typescript
type FlagData = {
    examId: string;         // assessment Id
    candidateId: string;    // candidate Id
    type: Event;            // log Event type
    description: string;    // log description 
    createdAt: string;      // time of occurrence
}

type FlagDataWithFile = {
    examId: string;         // assessment Id
    candidateId: string;    // candidate Id
    mediaFile:File          // File (image or audio)
    type: Event;            // log Event type
    description: string;    // log description 
    createdAt: string;      // time of occurrence
}

type SystemCheck= {
  success: boolean;         // systems check is successful or not
  timestamp: string;        // time of occurrence
  data:{                    // data array of all system status
  status: "success" | "failed" | "default";  
  finished: boolean; 
  name: "internetSpeed" | "webcam" | "microphone" | "browser", 
  }[]
}
```

| Event               | Description                                       |  Payload Type                           |
| ------------------- | ------------------------------------------------- | --------------------------------------- |
| `STARTED`           | Fired when proctoring begins.                     | –                                       |
| `END_PROCTORING`    | Fired when proctoring ends.                       | –                                       |
| `SET_CONFIG`        | Fired when widget config is set.                  | `{ config: {...} }`                     |
| `FULLSCREEN_CHANGE` | Fired when fullscreen mode changes.               | `{ fullscreen: boolean }`               |
| `TAB_FOCUS_CHANGE`  | Fired when tab focus state changes.               | `{ focused: boolean }`                  |
| `EXIT_FULLSCREEN`   | Fired when exiting fullscreen mode.               |   `  FlagData  `                        |
| `TAB_NOT_FOCUS`     | Fired when candidate switches tab or loses focus. | `FlagData`                          |
| `FACE_ABSENCE`      | Fired when no face is detected.                   | `FlagDataWithFile`                  |
| `MULTIPLE_FACE`     | Fired when multiple faces are detected.           | `FlagDataWithFile`                  |
| `SOUND_DETECTED`    | Fired when sound is detected.                     | `FlagDataWithFile`                  |
| `PERIODIC_SNAPSHOT` | Fired periodically with a snapshot of the user.   | `FlagDataWithFile`                  |
| `FACE_MISMATCH` | Fired when face verification detects a mismatch. | `FlagDataWithFile`                  |
| `SUSPICIOUS_ACTIVITY` | Fired when 5 unique violation types are observed. | `FlagData`                       |
| `SYSTEM_CHECK_STARTED` | Fired when system checks starts.               | -                                       |
| `SYSTEM_CHECK_COMPLETED` | Fired when system checks is completed.       | `SystemCheck`                       |

---

## Configuration

### `config` Object

| Option               | Type   | Required | Description                                                               |
| -------------------- | ------ | -------- | ------------------------------------------------------------------------- |
| `apiKey`             | string | Yes      | Your Proctorme API key. Request one from us along with supported domains. |
| `assessmentId`       | string | Yes      | Unique assessment identifier.                                             |
| `assessmentTitle`    | string | Yes      | Title of the assessment.                                                  |
| `candidateId`        | string | Yes      | Unique candidate identifier.                                              |
| `candidateEmail`     | string | Yes      | Candidate’s email address.                                                |
| `candidateFirstName` | string | Yes      | Candidate’s first name.                                                   |
| `candidateLastName`  | string | Yes      | Candidate’s last name.                                                    |
| `institutionName`    | string | Yes      | Institution conducting the assessment.                                    |
| `examDuration`       | number | Yes      | Exam duration in seconds.                                                 |
| `candidateImageUrl`  | string | No       | URL of candidate’s image.                                                 |
| `features`           | object | No       | Enable/disable features (AI proctoring, facial recognition).              |

---

## Ending a Session

### `widget.endProctoring()`

Manually ends the proctoring session. This triggers the `END_PROCTORING` event, clears Redux state, purges persisted storage, and closes the modal.

**Example:**
```javascript
widget.endProctoring();
```

**When to use:**
Call this when the candidate submits their exam, when the exam time expires, or when you otherwise need to forcibly stop proctoring.

**Important:** After calling `endProctoring()`, you can call `widget.init(newConfig)` to start a new session with different candidate/exam data.

---

### `widget.resetSessionListeners()`

Clears all public event listeners registered via `widget.on()`. Useful when re-initializing the widget or switching sessions to prevent duplicate handlers from firing.

**Example:**
```javascript
widget.resetSessionListeners();
```


---

### `FACE_MISMATCH`

Fired when the face verification API detects that the candidate does not match the registered image.

**When to listen:**
Use this to notify your UI or logs when a face mismatch is detected during the verification step.

**Payload:**
`FlagDataWithFile` — includes the snapshot image that was used for verification.

**Example:**
```javascript
widget.on("FACE_MISMATCH", (data) => {
  console.log("Face mismatch detected ❌", data);
});
```

---

### `SUSPICIOUS_ACTIVITY`

Fired when 5 different unique violation types have been observed in the current session.

**Triggering violations:**
- `EXIT_FULLSCREEN`
- `TAB_NOT_FOCUS`
- `FACE_ABSENCE`
- `MULTIPLE_FACE`
- `SOUND_DETECTED`
- `FACE_MISMATCH`
- `FACE_MISALIGNMENT`

Only distinct categories count — repeats of the same type are ignored. After firing, the tracker resets and can trigger again in the same session.

**When to listen:**
Use this to escalate monitoring or notify proctors when a candidate shows a pattern of multiple different suspicious behaviors.

**Example:**
```javascript
widget.on("SUSPICIOUS_ACTIVITY", (data) => {
  console.log("Suspicious activity pattern detected 🚨", data);
});
```

---

## Browser Support

- Chrome (latest versions)
- Firefox (latest versions)
- Edge (latest versions)

---

# Flag Webhook Documentation

When a flag (suspicious event) is created for a candidate in an exam, the application may send a real-time webhook notification to the organisation’s configured webhook URL. This document describes the payload, expected receiver behavior, retry strategy, and security considerations.

## When this webhook is triggered

The webhook is fired from `createFlagHandler` after a flag is stored in the database. If an organisation has a `flagWebhookUrl` configured for the current request context (`req.decoded.flagWebhookUrl`), the server calls that URL with the flag details.

## Payload

Content-Type: `application/json`

### Example payload:

```json
{
  "event": "Flag",
  "flagId": "64fe2f1a...",
  "candidateId": "64fe2f00...",
  "examId": "64fe2eaa...",
  "type": "FACE_ABSENCE",
  "mediaUrl": "https://s3.amazonaws.com/.../screenshot.jpg",
"domain": "https://mydomain.com",
  "description": "Candidate left the desk",
  "createdAt": "2025-09-21T12:00:00.000Z"
}
```

### Example SOUND_DETECTED flag:

```json
{
  "flagId": "68d44eb26af804491d23ea90",
  "candidateId": "kdm9m00w",
  "examId": "p77qkyfp",
  "type": "SOUND_DETECTED",
  "mediaUrl": "https://proctor-module.s3.amazonaws.com/sound_detected.ogg",
"domain": "https://mydomain.com",
  "description": "Unexpected sound detected",
  "createdAt": "2025-09-24T20:04:02.080Z"
}
```

### Fields

- `event` — Type of event that was triggered
- `flagId` — ObjectId of the flag
- `candidateId` — ID of the candidate associated with the flag
- `examId` — ID of the exam / assessment associated with the flag
- `type` — string describing the flag type (enum-like)
- `mediaUrl` — optional URL to media (image/audio)
- `domain` - domain of the assessment platform 
- `description` — optional text describing the event
- `createdAt` — timestamp when the flag was created

---

## Expected receiver behavior

- The receiver must accept a `POST` with `application/json` body.\n- On successful processing, the receiver should respond with HTTP `200 OK`.\n- Non-200 responses, network failures or timeouts will cause the sender to retry (see Retry policy).

---

## Retry policy and backoff

The webhook sender uses a simple retry policy:

- The initial attempt is made immediately after the flag is created.\n- If the HTTP response status is not `200`, the sender retries up to **2 more times** (total 3 attempts).\n- Retries use **exponential backoff** (e.g., 1s, 2s, 4s).\n\nServers receiving these webhooks should be idempotent as the same payload may be delivered multiple times when retries occur.

---

### Example Receiver Implementation

```js
// receiver.js
const express = require("express");
const app = express();
app.use(express.json());

app.post("/webhook", (req, res) => {
  console.log("received webhook:", req.body);
  // validate payload, persist or alert
  res.sendStatus(200);
});

app.listen(4000);
```

### Test with cURL

```bash
curl -X POST http://localhost:4000/webhook \
  -H "Content-Type: application/json" \
  -d '{"flagId":"64fe...","candidateId":"64fe...","examId":"64fe...","type":"MULTIPLE_FACE","createdAt":"2025-09-21T12:00:00.000Z"}'
```

---

## Secure Integration

We also integrate via webhook to securely connect with your already existing system. Ensure your webhook endpoint is secured (e.g., HTTPS, token verification) before going live.

## License

MIT
