
# JavaScript Audio & Video APIs: The In-Depth Guide

> [!INFO] **Core Concept** The web media stack is built on the `HTMLMediaElement` interface. Whether you use `<audio>`, `<video>`, or the `Audio()` constructor, you are interacting with this same underlying API.

## 1. The Audio Constructor & Basic Methods

### **The `Audio` Constructor**

Unlike most HTML elements that require `document.createElement`, audio has a dedicated constructor.

- **Syntax:** `const myAudio = new Audio('url_to_file.mp3');`
    
- **Return Value:** Returns an `HTMLAudioElement` instance.
    
- **Memory Management Warning:** If you create an `Audio` object in a function and don't attach it to the DOM or store it in a global variable, it may be garbage collected while playing (though modern browsers try to prevent this, it's risky).
    

### **Key Methods**

#### `play()` - _The Danger Zone_

Attempts to start playback.

- **Asynchronous:** It returns a **Promise**.
    
- **The Trap:** If you don't handle the promise rejection, your console will explode with errors when the browser blocks autoplay.
    
- **Usage:**
    

``` javascript
const playPromise = audio.play();

if (playPromise !== undefined) {
    playPromise
        .then(() => {
            // Playback started successfully
        })
        .catch(error => {
            // Auto-play was prevented. Show a "Play" button so the user can start it manually.
            console.error("Autoplay prevented:", error);
        });
}
```

#### `pause()`

Pauses playback.

- **State:** It maintains the `currentTime`. If you call `play()` again, it resumes from where it stopped.
    

#### `fastSeek(time)`

Attempts to seek to the specified time as fast as possible.

- **Trade-off:** It trades **precision for speed**. It usually jumps to the nearest "keyframe" (I-frame) rather than the exact millisecond.
    
- **Use Case:** Scrubbing through a long video where instantaneous feedback is more important than exact timing.
    
- **Browser Support:** Historically spotty (Safari/Firefox had it first), but support is widespread now. If precision matters, use `currentTime`.
    

#### `addTextTrack()`

Programmatically adds subtitles or captions.

- **Use Case:** Loading `.vtt` files dynamically based on user language selection.
    

## 2. Critical Properties

|Property|Type|Description|
|---|---|---|
|`currentTime`|`Number`|Get or Set current playback position in seconds. Setting this seeks the media (precisely).|
|`loop`|`Boolean`|If `true`, automatically restarts from 0 upon finishing.|
|`muted`|`Boolean`|If `true`, audio is silent. **Critical for Autoplay** (see below).|
|`volume`|`Number`|0.0 (silent) to 1.0 (loudest).|
|`paused`|`Boolean`|Read-only. `true` if paused.|
|`ended`|`Boolean`|Read-only. `true` if playback has finished.|

## 3. Formats & Codecs (The "Container vs Codec" Problem)

> [!WARNING] **Brutal Truth** Just because a file ends in `.mp4` doesn't mean it will play. `.mp4` is a **box** (container). The **codec** (e.g., H.264, H.265, AV1) is what's inside. Browsers support the box, but might not support the thing inside it.

### **MIME Types & The `source` Element**

- **Mechanism:** The browser iterates through `<source>` tags from top to bottom and plays the _first_ one it supports.
    
- **MIME Syntax:** `type="media/container; codecs=codec_name"`
    

``` javascript
<video controls>
  <!-- Best quality/Newest codec (AV1) -->
  <source src="video.av1.mp4" type="video/mp4; codecs=av01.0.05M.08">
  <!-- Standard fallback (H.264) -->
  <source src="video.h264.mp4" type="video/mp4; codecs=avc1.42E01E">
  <!-- Ancient fallback -->
  <source src="video.webm" type="video/webm">
</video>
```

### **Common Formats**

1. **MP3 (`audio/mpeg`):** The cockroach of audio. plays everywhere. Patented but patents expired.
    
2. **AAC (`audio/mp4`):** Better quality than MP3 at same bitrate. Standard for video audio.
    
3. **Ogg Vorbis (`audio/ogg`):** Open source, free. Great, but Apple (Safari) historically hated it.
    
4. **WebM (`video/webm`):** Google's open format. Usually contains VP8/VP9 or AV1 video.
    

## 4. Advanced APIs

### **A. HTMLMediaElement API**

The parent class for `<audio>` and `<video>`.

- **Events are King:** Don't poll `currentTime`. Use listeners:
    
    - `timeupdate`: Fires roughly 4 times a second while playing.
        
    - `ended`: Playback finished.
        
    - `waiting`: Buffering (show a spinner).
        
    - `canplay`: Enough data loaded to start.
        

### **B. Media Capture and Streams API (`getUserMedia`)**

Access the microphone and camera.

- **Privacy:** Requires explicit user permission. Fails instantly on non-HTTPS sites (except localhost).
    
- **Constraints:** You can request specific resolutions or camera modes (front/back).
    

``` javascript
navigator.mediaDevices.getUserMedia({
    audio: true,
    video: { facingMode: "user" } // Selfie camera
})
.then(stream => {
    // Attach stream to a video element to show preview
    videoElement.srcObject = stream; 
})
.catch(err => console.error("Permission denied or no cam:", err));
```

### **C. Screen Capture API (`getDisplayMedia`)**

Records the screen.

- **Difference:** Initiated via `navigator.mediaDevices.getDisplayMedia()`.
    
- **User Choice:** The browser forces a native UI popup asking the user _which_ screen/window to share. You cannot bypass this.
    

### **D. MediaStream Recording API**

Records a stream (from canvas, cam, or screen) into a file (Blob).

- **Key Object:** `MediaRecorder`.
    
- **Flow:** `start()` -> listen for `dataavailable` events -> push chunks to array -> `stop()` -> create Blob from chunks.
    

### **E. Media Source Extensions (MSE)**

> [!NOTE] **Intermediate/Advanced** This is how YouTube/Netflix work. They don't load one giant `.mp4`. They download small "chunks" of video (using `fetch`) and feed them into the video tag dynamically.

- **Why?** Adaptive Bitrate Streaming. If your internet slows down, they feed in low-quality chunks. If it speeds up, they feed in 4K chunks.
    
- **Key Object:** `MediaSource` and `SourceBuffer`.
    

### **F. Web Audio API**

> [!TIP] **Audio Tag vs Web Audio API**
> 
> - **`<audio>` tag:** Simple playback. "Play this song."
>     
> - **Web Audio API:** Audio Processing Graph. "Play this song, add reverb, route it to the left ear only, and visualize the frequencies."
>     

- **Core:** `AudioContext`.
    
- **Nodes:** You connect nodes together. Source -> Gain (Volume) -> Destination (Speakers).
    
- **Precision:** Sample-accurate timing (better for games/drum machines than `<audio>`).
    

## 5. Strategic Advisor: The "Brutally Honest" Reality Check

### **1. The Autoplay Policy Will Break Your App**

Do not design an app that relies on audio playing immediately when the page loads. **It will fail.**

- **The Rule:** Browsers block audio autoplay unless the user has interacted with the document (click, tap, keypress).
    
- **The Exception:** Muted video _is_ allowed to autoplay.
    
- **The Fix:** ALWAYS initialize audio contexts or start playback inside a click event handler (e.g., a "Start Experience" button).
    

### **2. Mobile Safari is strict**

iOS Safari is the "Internet Explorer" of media.

- It is very aggressive about suspending `AudioContexts` to save battery.
    
- It often requires a touch event to unlock audio.
    

### **3. Don't Trust `canPlayType`**

The method `audio.canPlayType('audio/mp3')` returns strings like `"probably"`, `"maybe"`, or `""`.

- It never returns `"yes"`.
    
- `"maybe"` usually means "I recognize the container but who knows about the codec."
    
- **Modern Alternative:** Use the `navigator.mediaCapabilities` API for better accuracy.
    

### **4. React/Frameworks & `muted`**

In React, setting `<video muted={true} />` works, but sometimes the browser logic overrides the DOM property.

- **Best Practice:** Use the `muted` attribute in the HTML _and_ set the `.muted = true` property directly on the ref `componentDidMount`/`useEffect` to be 100% sure.