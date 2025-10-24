# PNGTuber Remix WebSocket API Documentation

This document provides comprehensive documentation for all available WebSocket commands in PNGTuber Remix.

## Connection Information
- **Default Port**: 9321
- **Protocol**: WebSocket
- **Message Format**: JSON

## Available Commands

### 1. Ping/Pong
Test connection to the server.

**Request:**
```json
{"event": "ping"}
```

**Response:**
```json
{"event": "pong"}
```

---

### 2. State Management
Switch between different character states (expressions, poses, etc.) by name or ID.

**Request by Name:**
```json
{"event": "state", "state_name": "Pickles Idle"}
```

**Request by ID:**
```json
{"event": "state", "state_id": 1}
```

**Parameters:**
- `state_name` (string): The name of the state to switch to
- `state_id` (integer): The state number to switch to (1-based indexing)

**Success Response:**
```json
{"event": "state", "result": "success", "state_id": 1, "state_name": "Pickles Idle"}
```

**Error Response:**
```json
{"event": "state", "result": "failed", "error": "state not found", "identifier": "Invalid State", "available_states": 3}
```

**Example Usage:**
```javascript
// Switch by name (recommended - more user-friendly)
websocket.send('{"event": "state", "state_name": "Pickles Idle"}');
websocket.send('{"event": "state", "state_name": "Pickles Smug"}');
websocket.send('{"event": "state", "state_name": "Pickles Distracted"}');

// Switch by ID (still supported)
websocket.send('{"event": "state", "state_id": 1}');
websocket.send('{"event": "state", "state_id": 2}');
websocket.send('{"event": "state", "state_id": 3}');
```

---

### 3. General Key Press
Trigger hotkey events in the application.

**Request:**
```json
{"event": "general", "key": "space"}
```

**Parameters:**
- `key` (string): The key name to trigger

**Response:**
```json
{"event": "state", "result": "success"}
```

---

### 4. Hide Sprite
Hide a specific sprite by name or ID.

**Request:**
```json
{"event": "hide_sprite", "sprite_name": "Hat"}
```
OR
```json
{"event": "hide_sprite", "sprite_id": "12345"}
```
OR
```json
{"event": "hide_sprite", "id": "12345"}
```

**Parameters:**
- `sprite_name` (string): Name of the sprite to hide
- `sprite_id` (string): ID of the sprite to hide
- `id` (string/number): Generic ID of the sprite to hide

**Success Response:**
```json
{"event": "hide_sprite", "result": "success", "identifier": "Hat"}
```

**Error Response:**
```json
{"event": "hide_sprite", "result": "failed", "identifier": "Hat", "error": "sprite not found"}
```

---

### 5. Show Sprite
Show a specific sprite by name or ID.

**Request:**
```json
{"event": "show_sprite", "sprite_name": "Glasses"}
```
OR
```json
{"event": "show_sprite", "sprite_id": "67890"}
```
OR
```json
{"event": "show_sprite", "id": 67890.0}
```

**Parameters:**
- `sprite_name` (string): Name of the sprite to show
- `sprite_id` (string): ID of the sprite to show
- `id` (string/number): Generic ID of the sprite to show

**Success Response:**
```json
{"event": "show_sprite", "result": "success", "identifier": "Glasses"}
```

**Error Response:**
```json
{"event": "show_sprite", "result": "failed", "identifier": "Glasses", "error": "sprite not found"}
```

---

### 6. Toggle Sprite Visibility
Toggle the visibility of a specific sprite by name or ID.

**Request:**
```json
{"event": "toggle_sprite", "sprite_name": "Background"}
```
OR
```json
{"event": "toggle_sprite", "id": 537693010870.0}
```

**Parameters:**
- `sprite_name` (string): Name of the sprite to toggle
- `sprite_id` (string): ID of the sprite to toggle
- `id` (string/number): Generic ID of the sprite to toggle

**Success Response:**
```json
{"event": "toggle_sprite", "result": "success", "identifier": "Background", "visibility": "visible"}
```
OR
```json
{"event": "toggle_sprite", "result": "success", "identifier": "Background", "visibility": "hidden"}
```

**Error Response:**
```json
{"event": "toggle_sprite", "result": "failed", "identifier": "Background", "error": "sprite not found"}
```

---

### 7. List All Sprites
Get a list of all sprites with their current visibility status and IDs.

**Request:**
```json
{"event": "list_sprites"}
```

**Response:**
```json
{
  "event": "list_sprites",
  "result": "success",
  "sprites": [
    {"name": "Hat", "visible": true, "id": 12345},
    {"name": "Glasses", "visible": false, "id": 67890},
    {"name": "Background", "visible": true, "id": 537693010870}
  ]
}
```

---

### 8. List All States
Get a list of all available states with their names, IDs, and current status.

**Request:**
```json
{"event": "list_states"}
```

**Response:**
```json
{
  "event": "list_states",
  "result": "success",
  "states": [
    {"name": "Pickles Idle", "id": 1, "is_current": true},
    {"name": "Pickles Smug", "id": 2, "is_current": false},
    {"name": "Pickles Distracted", "id": 3, "is_current": false}
  ]
}
```

---

### 9. Load Model
Load a different PNGTuber model file for performance optimization or model switching.

**Request:**
```json
{"event": "load_model", "file_path": "C:/Models/SimpleModel.pngRemix"}
```

**Parameters:**
- `file_path` (string): Full path to the model file (.pngRemix or .save)

**Success Response:**
```json
{"event": "load_model", "result": "success", "file_path": "C:/Models/SimpleModel.pngRemix"}
```

**Error Responses:**
```json
{"event": "load_model", "result": "failed", "file_path": "", "error": "file_path is required"}
```
```json
{"event": "load_model", "result": "failed", "file_path": "missing.pngRemix", "error": "file not found"}
```
```json
{"event": "load_model", "result": "failed", "file_path": "model.txt", "error": "invalid file extension, must be .pngRemix or .save"}
```

**Supported File Types:**
- `.pngRemix` - PNGTuber Remix model files
- `.save` - Legacy format files

---

## Usage Examples

### JavaScript WebSocket Client
```javascript
// Connect to the WebSocket server
const websocket = new WebSocket('ws://localhost:9321');

websocket.onopen = function() {
    console.log('Connected to PNGTuber Remix');
    
    // Test connection
    websocket.send('{"event": "ping"}');
};

websocket.onmessage = function(event) {
    const response = JSON.parse(event.data);
    console.log('Received:', response);
};

// Switch to happy state by name (recommended)
websocket.send('{"event": "state", "state_name": "Pickles Smug"}');

// Switch to state by ID (still works)
websocket.send('{"event": "state", "state_id": 2}');

// Hide hat
websocket.send('{"event": "hide_sprite", "sprite_name": "Hat"}');

// Show glasses
websocket.send('{"event": "show_sprite", "sprite_name": "Glasses"}');

// Toggle background visibility
websocket.send('{"event": "toggle_sprite", "sprite_name": "Background"}');

// Get list of all sprites
websocket.send('{"event": "list_sprites"}');

// Get list of all states
websocket.send('{"event": "list_states"}');

// Load a different model
websocket.send('{"event": "load_model", "file_path": "C:/Models/PerformanceModel.pngRemix"}');
```

### Python WebSocket Client
```python
import websocket
import json

def on_message(ws, message):
    response = json.loads(message)
    print(f"Received: {response}")

def on_open(ws):
    print("Connected to PNGTuber Remix")
    # Test connection
    ws.send('{"event": "ping"}')

# Connect to WebSocket
ws = websocket.WebSocketApp("ws://localhost:9321",
                           on_open=on_open,
                           on_message=on_message)

# Switch states by name (recommended)
ws.send('{"event": "state", "state_name": "Pickles Idle"}')

# Switch states by ID (still works)
ws.send('{"event": "state", "state_id": 1}')

# Get available states
ws.send('{"event": "list_states"}')

# Control sprite visibility
ws.send('{"event": "hide_sprite", "sprite_name": "Hat"}')
ws.send('{"event": "toggle_sprite", "id": 12345}')
```

## Error Handling

All commands follow a consistent error response format:
- `result`: Always "failed" for errors
- `error`: Description of what went wrong
- Additional context-specific fields when applicable

Common error types:
- **"sprite not found"**: The specified sprite name or ID doesn't exist
- **"invalid state_id"**: The state number is out of range
- **"file_path is required"**: Missing required parameter
- **"file not found"**: The specified file doesn't exist
- **"invalid file extension"**: Unsupported file format

## Tips for Performance

1. **Model Switching**: Use `load_model` to switch between complex and simple models based on performance needs
2. **Sprite Management**: Use `list_sprites` to discover available sprites before controlling them
3. **State Management**: 
   - Use `list_states` to discover available states and their names
   - Prefer state names over IDs for better readability: `"Pickles Smug"` vs `2`
   - States are 1-indexed when using IDs (state 1, state 2, etc.)
4. **ID vs Name**: Both sprite names and IDs work - use whichever is more convenient
5. **File Paths**: Use absolute paths for `load_model` to avoid path resolution issues

## Troubleshooting

1. **No Response**: Check that the WebSocket server is running on port 9321
2. **State Not Changing**: 
   - Use `list_states` to see all available states and their exact names
   - Verify state names match exactly (case-sensitive)
   - If using state_id, verify it's within the available range
3. **Sprite Not Found**: Use `list_sprites` to see all available sprites and their current names/IDs
4. **Model Won't Load**: Check file path exists and has correct extension (.pngRemix or .save)