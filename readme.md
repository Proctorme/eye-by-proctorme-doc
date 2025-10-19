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
| `widget.on(event, callback)` | Listens for widget events.                 |
| `widget.endProctoring()`     | Manually ends the proctoring session.      |

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
```

| Event               | Description                                       | Example Payload                         |
| ------------------- | ------------------------------------------------- | --------------------------------------- |
| `STARTED`           | Fired when proctoring begins.                     | –                                       |
| `END_PROCTORING`    | Fired when proctoring ends.                       | –                                       |
| `SET_CONFIG`        | Fired when widget config is set.                  | `{ config: {...} }`                     |
| `FULLSCREEN_CHANGE` | Fired when fullscreen mode changes.               | `{ fullscreen: true }`                  |
| `TAB_FOCUS_CHANGE`  | Fired when tab focus state changes.               | `{ focused: true/false }`               |
| `EXIT_FULLSCREEN`   | Fired when exiting fullscreen mode.               |   `  FlagData  `                        |
| `TAB_NOT_FOCUS`     | Fired when candidate switches tab or loses focus. | `  FlagData  `                          |
| `FACE_ABSENCE`      | Fired when no face is detected.                   | `  FlagDataWithFile  `                  |
| `MULTIPLE_FACE`     | Fired when multiple faces are detected.           | `  FlagDataWithFile  `                  |
| `SOUND_DETECTED`    | Fired when sound is detected.                     | `  FlagDataWithFile  `                  |
| `PERIODIC_SNAPSHOT` | Fired periodically with a snapshot of the user.   | `  FlagDataWithFile  `                  |
| `SYSTEM_CHECK_STARTED` | Fired when system checks starts.               | -                                       |
| `SYSTEM_CHECK_COMPLETED` | Fired periodically with a snapshot of the user.   | `{ status: "success" \| "failed" \| "default";  finished: boolean; name: "internetSpeed" \| "webcam" \| "microphone" \| "browser", }[]` |

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

You can manually end the session at any time:

```javascript
widget.endProctoring();
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
  "description": "Unexpected sound detected",
  "createdAt": "2025-09-24T20:04:02.080Z"
}
```

### Fields

- `event` — Type of event that was triggered
- `flagId` — ObjectId of the flag
- `candidateId` — ID of the candidate associated with the flag
- `examId` — ID of the exam associated with the flag
- `type` — string describing the flag type (enum-like)
- `mediaUrl` — optional URL to media (image/audio)
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
