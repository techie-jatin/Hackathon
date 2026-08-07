# Project Requirements & Prerequisites

To successfully build and demo this Offline Mesh Network application, you will need specific hardware and software. Because this app relies on low-level device radios (Bluetooth/Wi-Fi Direct), the requirements are stricter than a standard web or chat app.

---

## 1. Hardware Requirements (CRITICAL)

> [!CAUTION]
> **You CANNOT test this app on a computer emulator.** Android emulators do not have functional Bluetooth or Wi-Fi Direct hardware capable of forming a mesh network.

*   **Primary Development Machine:** A Windows PC (which you are currently using).
*   **Physical Testing Devices:** **At least 2 (preferably 3) Physical Android Smartphones.** They do not need SIM cards or active cellular plans, but their Bluetooth and Wi-Fi antennas must work.
*   **Cables:** USB data cables to connect the Android phones to your PC for live debugging and log reading.

## 2. Software Development Environment (To be installed on PC)

Since we are building this using React Native for Android, your Windows machine needs the following environment setup. *(If you don't have these, I can help you install them via terminal commands later).*

*   **Node.js:** (LTS version) The JavaScript runtime required to run the React Native packager.
*   **Java Development Kit (JDK 17):** Required to compile Android applications.
*   **Android Studio:** You don't necessarily have to write code inside Android Studio, but we absolutely need it installed because it provides the **Android SDK**, the **Build Tools**, and **ADB (Android Debug Bridge)** which lets us push the app to your physical phones.

## 3. Project Dependencies (App Libraries)

When I initialize the project for you, I will install these specific packages to power the app:

*   **Core Framework:** `react-native`
*   **Routing/Navigation:** `@react-navigation/native` and `@react-navigation/bottom-tabs` (For switching between the Chat Feed and the Map/Radar).
*   **State Management:** `zustand` (A lightweight state manager to hold the active connections and message history).
*   **The Mesh Engine:** A native module bridge for the **Google Play Services Nearby Connections API**. (This is what actually powers the offline Bluetooth/Wi-Fi mesh routing).
*   **UI/Icons:** `react-native-vector-icons` or `lucide-react-native` for professional SOS and networking icons.

## 4. Required OS Permissions (Android)

To make the mesh work legally on the phones, our app will be required to ask the user for these permissions on startup:
*   `BLUETOOTH_SCAN`, `BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT`
*   `ACCESS_FINE_LOCATION` (Android requires location access to scan for nearby Bluetooth devices)
*   `NEARBY_WIFI_DEVICES` (For Android 13+)

---
### Next Steps
If you have at least 2 Android phones available for the hackathon, you meet all the hardware requirements. 

Would you like me to check your Windows environment to see if **Node.js** and **Java** are installed, or should I go straight to scaffolding the project?
