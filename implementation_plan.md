# Offline Mesh Communication Network - Hackathon Approach

This is a fantastic hackathon idea. It tackles a real-world problem (disaster communication) and uses impressive, non-standard mobile capabilities (offline peer-to-peer networking).

Here is my proposed architectural approach and implementation plan for your hackathon project.

## Core Technical Strategy: Google Nearby Connections API

Instead of manually managing the complexities of raw Bluetooth sockets and Wi-Fi Direct groups, I highly recommend using the **Google Nearby Connections API** (available on Android). 

**Why Nearby Connections?**
1. **Abstracts the hardware:** It automatically uses a combination of Bluetooth Classic, Bluetooth Low Energy (BLE), and Wi-Fi Direct under the hood. It uses BLE for low-power discovery and seamlessly upgrades to Wi-Fi Direct when high bandwidth is needed.
2. **Mesh Topology (Cluster Strategy):** It natively supports a `STRATEGY_CLUSTER` connection mode, which creates an M-to-N mesh network where every device can connect to multiple other devices, forming a loose, decentralized mesh.
3. **Fully Offline:** It requires zero internet connection.

## Proposed Tech Stack
*   **Platform:** Android (Native Kotlin) or React Native.
    *   *Recommendation:* **React Native** is great for rapid UI development in a hackathon, and we can use a wrapper library for the Nearby Connections API (like `react-native-nearby-connections`). However, **Native Android (Kotlin)** will provide the most stable and performant networking experience if you have Android experience.
*   **UI/Design:** Dark mode, emergency-themed aesthetics (high contrast, readable fonts, red/orange accents for SOS, green for rescue).
*   **Mapping:** For offline GPS, we can use simple coordinate displays (Distance/Bearing to target) or integrate a library like Mapbox with a pre-downloaded offline tile set for a specific hackathon region.

## Feature Implementation Plan

1. **Mesh Network Layer (The Core)**
   - Implement background discovery and advertising. Devices constantly look for other nearby devices.
   - When a device is found, automatically accept the connection to join the mesh.
   - Implement a **Message Flooding Protocol**: When Node A wants to send an SOS, it sends it to all its connected peers. When Node B receives it, it checks if it has seen this message ID before. If not, it displays it and forwards it to all *its* peers (except the one it received it from).

2. **SOS Messages & Rescue Announcements (Text)**
   - Small JSON payloads containing: `MessageID, SenderID, Timestamp, Role (Civilian/Rescue), MessageText`.
   - Propagated extremely quickly over the BLE/Wi-Fi mesh.

3. **GPS Sharing**
   - Use the phone's native Location Services (GPS works offline).
   - Append `Latitude, Longitude` to the messages.
   - Create a "Radar" UI that shows the relative direction and distance of other users based on their broadcasted GPS coordinates.

4. **Missing Person Broadcasts (Images)**
   - These require more bandwidth. The payload will include an image byte array.
   - The Nearby API will automatically route this over Wi-Fi Direct to peers in the mesh.

## User Review Required

> [!IMPORTANT]
> **Tech Stack Decision:** Do you prefer to build this as a **Native Android app (Kotlin)** or a cross-platform **React Native app**? Native Android will have fewer bugs with the networking APIs, but React Native might be faster to design the UI if you are familiar with web tech.

> [!NOTE]
> **Mapping Approach:** For the hackathon demo, do you want to attempt rendering a real map (which requires pre-downloading map data for offline use), or should we build a "Radar/Compass" view that points towards the GPS coordinates of the SOS signal? The Radar view is often cooler and much easier to build offline.

## Next Steps
Once you confirm the tech stack (Android vs. React Native) and mapping approach, I can initialize the codebase and we can start building the mesh networking layer!
