# Execution Playbook: Phases 2 & 3

This document outlines the professional developer workflow, architecture, and task breakdown for executing the most critical parts of your hackathon project: **Phase 2 (Permissions & Skeleton)** and **Phase 3 (Core Network Layer)**. 

We will assume a **React Native (TypeScript)** technology stack for this playbook, as it allows rapid UI development while still supporting native Android network APIs via bridging or libraries.

---

## 1. Professional Team Workflow

In a time-constrained hackathon, a professional team stays organized to prevent merge conflicts and duplicated work.

### Status Tracking & Task Management
*   **Kanban Board:** Create a simple Kanban board (GitHub Projects or Trello) with columns: `Backlog`, `In Progress`, `Blocked`, `Done`.
*   **Division of Labor:** 
    *   **Developer A (The Architect):** Focuses entirely on Phase 3 (Networking API, State Machine, Data Serialization).
    *   **Developer B (The Builder):** Focuses on Phase 2 (Permissions, Navigation, UI Shell, Local State).
*   **Branching Strategy:** Use **Trunk-Based Development**. Everyone pushes to `main` frequently. Avoid long-lived feature branches. If something is broken, push a fix immediately.

### Essential Tooling
*   **ADB (Android Debug Bridge):** Crucial for reading logs from multiple physical devices simultaneously. Command: `adb -s <device_id> logcat | grep -i "MeshNetwork"`
*   **Physical Devices:** Have at least 3 Android devices and 3 data cables ready.

---

## 2. Recommended File Structure

A clean, feature-based file structure is vital for speed.

```text
/
├── android/                   # Native Android code (Permissions, Network config)
├── src/
│   ├── app/                   # Entry points and Navigation setup
│   │   ├── _layout.tsx        # Main navigation shell (Tabs)
│   │   └── App.tsx 
│   ├── components/            # Reusable UI elements
│   │   ├── SOSButton.tsx
│   │   ├── RadarView.tsx
│   │   └── MessageBubble.tsx
│   ├── core/                  # The Brains (Phase 3 lives here)
│   │   ├── permissions.ts     # OS permission request logic
│   │   ├── MeshNetwork.ts     # Wrapper class for Nearby Connections API
│   │   └── MeshProtocol.ts    # Flooding logic, deduplication, message hashing
│   ├── store/                 # Global State Management (Zustand or Context)
│   │   ├── useMeshStore.ts    # Holds active connections and messages
│   └── types/                 # TypeScript interfaces
│       └── index.ts           # e.g., MessagePayload, Peer types
├── package.json
└── README.md
```

---

## 3. Phase 2: Permissions & UI Skeleton (Hours 2-4)

**Objective:** Prepare the app to legally use the hardware and create the visual shell for the user to interact with.

### Technical Workflow
1.  **Android Manifest Updates:** Modify `android/app/src/main/AndroidManifest.xml`.
    *   `ACCESS_FINE_LOCATION` (Required for Bluetooth discovery in Android)
    *   `BLUETOOTH`, `BLUETOOTH_ADMIN`, `BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT`, `BLUETOOTH_SCAN`
    *   `ACCESS_WIFI_STATE`, `CHANGE_WIFI_STATE`, `NEARBY_WIFI_DEVICES`
2.  **Runtime Permissions Prompt:** Write a utility function in `src/core/permissions.ts` that triggers the OS prompts when the app opens. The app must handle scenarios where the user denies permission gracefully.
3.  **UI Navigation Skeleton:** Build bottom tabs (e.g., [ SOS Feed ] | [ Radar ] | [ Network Status ]).
4.  **Global State Setup:** Create `useMeshStore.ts` to hold dummy data so Developer B can build UI components while Developer A builds the real network.

### Task Checklist (Kanban)
- [ ] Add permissions to `AndroidManifest.xml`.
- [ ] Create runtime permission request utility.
- [ ] Block app UI with a "Permissions Required" screen if denied.
- [ ] Setup Bottom Tab Navigation.
- [ ] Create `useMeshStore` with mock messages and mock connected peers.

---

## 4. Phase 3: The Core Network Layer (Hours 4-10)

**Objective:** Implement the Google Nearby Connections API, manage connection states, and ensure data can flow between devices. This is the hardest part.

### The State Machine Workflow
The network layer (`MeshNetwork.ts`) should operate as a state machine:
1.  **`IDLE`**: Network is off.
2.  **`DISCOVERING_AND_ADVERTISING`**: 
    *   Start Advertising: "I am Node A, here is my endpoint ID."
    *   Start Discovering: "Looking for other endpoints."
3.  **`CONNECTING`**: A peer is found. Initiate a connection request automatically.
4.  **`CONNECTED`**: Both peers accept. Add the peer to the `connectedPeers` array in `useMeshStore`.

### The Networking Protocol (Crucial Logic)
When building `MeshProtocol.ts`, you must establish a standard payload format. All data sent over the network should be serialized (e.g., to JSON strings or MessagePack byte arrays).

**Standard Payload Structure:**
```typescript
interface MeshMessage {
  msgId: string;       // UUID to prevent infinite loops!
  senderId: string;    // Who originally sent this?
  timestamp: number;
  type: 'SOS' | 'GPS' | 'ANNOUNCEMENT';
  payload: string;     // The actual text, or base64 image data
}
```

### Development Process for the Network
1.  **Initialize API:** Import the React Native library (e.g., `react-native-google-nearby-messages` or similar wrapper, or write a native module bridge to the official Android SDK).
2.  **Implement Listeners:** Set up event listeners for `onEndpointFound`, `onEndpointLost`, `onConnectionInitiated`, `onConnectionResult`, `onDisconnected`.
3.  **Auto-Accept Logic:** In a disaster, you don't want users manually pairing. When `onConnectionInitiated` fires, automatically accept it.
4.  **Test the Ping:** Send a simple `"PING"` byte array from Device 1. Ensure Device 2 receives it, parses it, and logs it.

### Task Checklist (Kanban)
- [ ] Setup Nearby Connections API library / Native Module.
- [ ] Implement `startAdvertising()` and `startDiscovery()`.
- [ ] Implement connection event listeners (Found, Lost, Initiated, Accepted).
- [ ] Write logic to auto-accept incoming connections.
- [ ] Update `useMeshStore` when peers connect/disconnect.
- [ ] Implement `sendPayload(peerId, byteData)` and `onPayloadReceived(data)`.
- [ ] Test bidirectional string transmission between 2 physical devices.

---

> [!CAUTION]
> **The Most Common Hackathon Pitfall:**
> Trying to test Bluetooth/Wi-Fi Direct on an emulator will fail silently and waste hours of your time. **Only test Phase 3 code on physical devices.** Use Android Studio's Logcat filtering by your app's package name to watch the network events fire in real-time.
