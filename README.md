# Plaiable Unity Gameplay Recorder

A Unity editor tool for capturing gameplay sessions as structured data. Records object transformations, gameplay events, scene assets, and optional video for analysis, machine learning, or automated testing.

![Unity Version](https://img.shields.io/badge/Unity-2019.4%2B-blue)
![.NET](https://img.shields.io/badge/.NET-Framework%204.7.1-purple)
![License](https://img.shields.io/badge/license-Proprietary-red)
![Company](https://img.shields.io/badge/owner-Orvind%20BV-orange)


## 🚀 Installation

### Method 1: Unity Package Manager (Recommended)

1. Open Unity Package Manager: __Window > Package Manager__
2. Click the `+` button → __Add package from git URL__
3. Enter: `https://github.com/orvind/plaible-recorder/{choose you OS and version subfolder}`
4. Click __Add__

### Method 2: Manual Installation

1. Download or clone this repository
2. Choose your OS and vresion subfolder
3. Copy the `com.gameplay.recorder` folder to your project's `Packages` directory
4. Unity will automatically detect and import the package

## 📖 How to Use

### 1. Open the Recorder Window

After installation:
- Go to __Window > Gameplay Recorder__ in Unity's menu bar
- The Recorder window will appear (you can dock it anywhere)

### 2. Configure Your Recording

In the Recorder window, configure these settings:

| Setting | Description | Default Value |
|---------|-------------|---------------|
| **Output Directory** | Where recordings will be saved | `Assets/../ExportedData/GameplayRecording` |
| **Duration (seconds)** | How long to record | `30` |
| **Capture Video** | Enable gameplay video recording | `false` |
| **Video FPS** | Video frame rate (if video enabled) | `30` |

### 3. Start Recording

You have two options:

**Option A: Start Recording** (Recommended)
- Click the __Start Recording__ button
- The tool will:
  1. Export your scene as a GLB file
  2. Enter Play mode
  3. Start recording automatically
  4. Stop and save after the configured duration

**Option B: Start Game Only**
- Click the __Start Game__ button to test without recording
- Useful for verifying your scene before recording

### 4. During Recording

The Status section shows:
- **Game Status**: Whether Play mode is active
- **Recording Status**: Whether actively recording
- **Time Elapsed**: How long you've been recording
- **Time Remaining**: Time left until auto-save

You can:
- Click __Save Recording__ to stop and save early
- Click __Cancel__ to stop without saving

### 5. After Recording

When recording completes:
- The session folder opens automatically
- A ZIP archive is created
- All data is ready for analysis

## 📂 Understanding Your Recording Data

### Session Folder Structure

Each recording creates a timestamped folder like this:

```
session_20260117_143022/
├── MainScene.glb   # Your scene as a 3D model
├── session_metadata.json       # Recording settings and info
├── mechanics_events/   # Gameplay events (one file per second)
│   ├── mechanics_events_0001.json
│   ├── mechanics_events_0002.json
│   └── ...
├── semantic_events/      # Enhanced event analysis
├── asset_timeline.json       # When objects were spawned/destroyed
├── script_snapshots/   # Component state snapshots
├── gameplay_video.mp4               # Video (if enabled)
└── session_20260117_143022.zip      # Compressed archive
```

### Key Files Explained

#### `session_metadata.json`
Basic information about your recording:
```json
{
  "sessionId": "session_20260117_143022",
  "startTime": "2026-01-17T14:30:22",
  "duration": 30.0,
  "gameType": "3D",
  "captureVideo": true,
  "videoFrameRate": 30
}
```

#### `MainScene.glb`
- Your entire scene exported as a 3D model
- Industry-standard GLB/glTF format
- View in [glTF Viewer](https://gltf-viewer.donmccurdy.com/), Blender, or other 3D tools
- Includes geometry, materials, textures, and object hierarchy

#### `mechanics_events/mechanics_events_XXXX.json`
Events that happened during gameplay (one file per second):
```json
{
  "timestamp": 1.5,
  "eventType": "Collision",
  "gameObjectName": "Player",
  "position": [0.0, 1.0, 0.0],
  "additionalData": {
    "colliderName": "Enemy",
    "impactVelocity": 5.2
  }
}
```

Captured events include:
- Collisions
- Animation transitions
- Object spawning/destruction
- UI interactions
- Custom gameplay events

#### `asset_timeline.json`
Timeline of when objects were created and destroyed:
```json
{
  "assetPath": "Prefabs/Enemy.prefab",
  "instances": [
 {
      "instanceId": "Enemy_0001",
      "spawnTime": 2.3,
 "destroyTime": 15.7,
      "transformData": [
        {
          "time": 2.3,
          "position": [10.0, 0.0, 5.0],
     "rotation": [0.0, 0.0, 0.0, 1.0],
  "scale": [1.0, 1.0, 1.0]
        }
      ]
    }
  ]
}
```

#### `gameplay_video.mp4`
- H.264 encoded video of your gameplay
- Only created if "Capture Video" is enabled
- Requires FFmpeg (see Video Recording Setup below)

## ⚙️ Configuration Guide

### Output Directory

- Can be absolute path: `C:/MyRecordings/`
- Or relative path: `Assets/../ExportedData/` (default)
- Each recording creates a subfolder with timestamp: `session_20260117_143022/`

**Tips:**
- Use a location outside `Assets/` to avoid Unity importing the data
- Ensure the path is writable
- Keep on a drive with sufficient space

### Recording Duration

- Set how many seconds to record
- Recording stops automatically when duration is reached
- Can manually save earlier with __Save Recording__ button

**Recommended durations:**
- Quick tests: 10-15 seconds
- Gameplay analysis: 30-60 seconds
- ML training data: 60-300 seconds


### Asset Export Configuration

Control what gets exported during recording using the `AssetExportConfig.json` file.

**Location:** `Packages/com.gameplay.recorder/AssetExportConfig.json`

#### Configuration Options

Edit the JSON file to customize which assets are exported:

```json
{
    "exportPhysicsLayerCollisionMatrix": true,
    "exportReferencedNonModelAssets": true,
    "exportReferencedSprites": true,
    "exportOriginalReferencedAssets": true,
    "exportReferenced3DModels": true,
    "exportAllScripts": true,
    "exportCompletePrefabs": true
}
```

| Setting | Description | When to Disable |
|---------|-------------|-----------------|
| **exportPhysicsLayerCollisionMatrix** | Exports physics layer collision settings | Disable if you don't need physics configuration data |
| **exportReferencedNonModelAssets** | Exports non-3D assets like audio files, particle effects | Disable to reduce export size when only geometry matters |
| **exportReferencedSprites** | Exports 2D sprites and UI images | Disable for 3D-only projects to save space |
| **exportOriginalReferencedAssets** | Exports original asset files (textures, audio, etc.) | Disable if you only need GLB geometry |
| **exportReferenced3DModels** | Exports 3D models and meshes | Keep enabled unless recording abstract/non-visual data |
| **exportAllScripts** | Exports C# scripts as compiled DLLs | Disable if you don't need script analysis |
| **exportCompletePrefabs** | Exports full prefab hierarchies | Disable to only export instantiated objects |

#### How to Customize

1. **Locate the Config File:**
   - Navigate to `Packages/com.gameplay.recorder/AssetExportConfig.json`
   - Or use your IDE's file explorer

2. **Edit Settings:**
   - Open the file in any text editor
   - Change `true` to `false` for settings you want to disable
   - Save the file

3. **Example: Minimal Export (Geometry Only)**
   ```json
   {
   "exportPhysicsLayerCollisionMatrix": false,
       "exportReferencedNonModelAssets": false,
       "exportReferencedSprites": false,
    "exportOriginalReferencedAssets": false,
       "exportReferenced3DModels": true,
       "exportAllScripts": false,
       "exportCompletePrefabs": false
   }
   ```

4. **Example: Full Export (Everything)**
   ```json
   {
       "exportPhysicsLayerCollisionMatrix": true,
       "exportReferencedNonModelAssets": true,
       "exportReferencedSprites": true,
       "exportOriginalReferencedAssets": true,
     "exportReferenced3DModels": true,
       "exportAllScripts": true,
       "exportCompletePrefabs": true
   }
   ```

#### Common Use Cases

**Machine Learning (Visual Only):**
```json
{
    "exportPhysicsLayerCollisionMatrix": false,
    "exportReferencedNonModelAssets": false,
    "exportReferencedSprites": false,
    "exportOriginalReferencedAssets": false,
    "exportReferenced3DModels": true,
    "exportAllScripts": false,
    "exportCompletePrefabs": false
}
```

**Complete Scene Analysis:**
```json
{
    "exportPhysicsLayerCollisionMatrix": true,
    "exportReferencedNonModelAssets": true,
    "exportReferencedSprites": true,
    "exportOriginalReferencedAssets": true,
    "exportReferenced3DModels": true,
    "exportAllScripts": true,
    "exportCompletePrefabs": true
}
```

**Performance Testing (Minimal):**
```json
{
    "exportPhysicsLayerCollisionMatrix": false,
    "exportReferencedNonModelAssets": false,
    "exportReferencedSprites": false,
    "exportOriginalReferencedAssets": false,
    "exportReferenced3DModels": true,
    "exportAllScripts": false,
 "exportCompletePrefabs": false
}
```



## 🎯 Use Cases

### For Machine Learning
1. Set duration to match training episode length
2. Enable video for visual training data
3. Parse `mechanics_events/` for state-action pairs
4. Use `MainScene.glb` for environment representation
5. Extract player trajectories from `asset_timeline.json`

### For Gameplay Analysis
1. Record shorter sessions (15-30 seconds)
2. Review event timeline alongside video
3. Identify player behavior patterns
4. Analyze collision frequency and locations
5. Track object lifecycle and spawning patterns

### For Automated Testing
1. Record expected gameplay as baseline
2. Compare event timelines between runs
3. Verify object spawn/destroy timing
4. Check for missing events or anomalies
5. Use video for visual regression testing

## 🔧 Troubleshooting

### Recording Won't Start

**Symptoms:** Nothing happens when clicking "Start Recording"

**Solutions:**
1. Check Unity Console for errors (filter by `GameplayRecorderWindow`)
2. Verify output directory path is valid and writable
3. Fix any compilation errors in your project
4. Ensure no other systems are blocking Play mode

### Video Not Recording

**Symptoms:** No `gameplay_video.mp4` file in output

**Solutions:**

1. **Check Console:** Look for video-related errors
2. **Fallback Mode:** Without FFmpeg, find PNG frames in `RawFrames/` folder
3. **Game View:** Ensure Game view is visible during recording

### Objects Missing from Export

**Symptoms:** Some objects don't appear in `MainScene.glb`

**Solutions:**
1. **Dynamic Objects:** Check `asset_timeline.json` for spawn times
2. **Disabled Objects:** Currently exported with invisible flag
3. **Very Brief Objects:** Objects destroyed before first capture may be missed
4. **Custom Renderers:** Some custom render systems may not export

### Large File Sizes

**Symptoms:** Session folders are very large

**Solutions:**
1. **Reduce Duration:** Shorter recordings = smaller files
2. **Lower Video FPS:** 24-30 fps sufficient for most uses
3. **Disable Video:** Saves hundreds of MB per session
4. **Archive Old Sessions:** Move completed sessions to external storage
5. **Check Asset Complexity:** High-poly models increase GLB size

### Recording Stops Immediately

**Symptoms:** Recording starts then stops right away

**Solutions:**
1. **Check Duration:** Ensure duration > 0 in settings
2. **Asset Export Errors:** Look in Console for GLB export failures
3. **Disk Space:** Verify sufficient storage available
4. **Scene Complexity:** Very complex scenes may fail to export

### Buttons Are Grayed Out

**Symptoms:** Can't click "Start Recording" or "Start Game"

**Explanation:** This is normal in these situations:
- Already recording (use "Save Recording" or "Cancel")
- Currently exporting assets (wait for completion)
- In Play mode without recording (use "Start Recording" to begin)

## 💡 Tips and Best Practices

### Before Recording
1. ✅ Test your scene with __Start Game__ first
2. ✅ Start with 10-second test recordings
3. ✅ Check output directory has enough space
4. ✅ Close unnecessary Editor windows for better performance

### During Setup
1. ✅ Use descriptive GameObject names (they appear in event data)
2. ✅ Remove unnecessary objects to reduce export size
3. ✅ Keep recorder window visible to monitor status
4. ✅ Save your scene before recording

### After Recording
1. ✅ Review session folder before closing Unity
2. ✅ Archive or delete old sessions regularly
3. ✅ Check Console for any warnings or errors
4. ✅ Verify critical events are captured in JSON files

### Performance
- Typical overhead: 2-5% CPU during recording
- Video capture adds 5-10% additional overhead
- Shorter recording intervals = more data = larger files
- Close Preview windows and Profiler during recording

## 📋 Requirements

| Requirement | Version/Details |
|------------|-----------------|
| **Unity** | 2019.4 or later |
| **Platform** | Windows, macOS, Linux |
| **.NET** | Framework 4.7.1 |
| **FFmpeg** | Optional (for video encoding) |
| **Disk Space** | ~100 MB per 30-second session with video |

## ❓ FAQ

**Q: Can I record longer than the configured duration?**  
A: No, recording stops automatically. However, you can click "Save Recording" to stop early.

**Q: Can I change settings during recording?**  
A: No, settings are locked once recording starts. Stop and start a new recording to change settings.

**Q: What happens if I exit Play mode during recording?**  
A: Recording is cancelled and data is not saved.

**Q: Can I record multiple scenes?**  
A: Yes, record each scene separately. Load the scene you want, then start recording.

**Q: Does this work with VR/AR projects?**  
A: Yes, but video capture may not work as expected. Event and asset data will still be captured.

**Q: Can I record in builds/standalone players?**  
A: No, this is an Editor-only tool. Recordings only work in Play mode within the Unity Editor.

**Q: How do I view the GLB file?**  
A: Use online viewers like [glTF Viewer](https://gltf-viewer.donmccurdy.com/), or 3D software like Blender.


---

**Ready to start recording?**  
1. Install the package
2. Open __Window > Gameplay Recorder__
3. Configure your settings
4. Click __Start Recording__!
