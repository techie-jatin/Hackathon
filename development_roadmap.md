# Hackathon Development Roadmap

This flowchart outlines the step-by-step process of developing the Offline Mesh Communication app from scratch. Since this is for a hackathon, the phases are structured to ensure you get a working prototype quickly, saving the "nice-to-have" features for the end.

```mermaid
flowchart TD
    Start([Hackathon Start]) --> Phase1
    
    subgraph Phase1 [Phase 1: Setup & Architecture (Hours 1-2)]
        TechStack[Finalize Tech Stack\nReact Native OR Android Native] --> Scaffold[Scaffold Base Project]
        Scaffold --> Repo[Initialize Git Repository]
    end
    
    Phase1 --> Phase2
    
    subgraph Phase2 [Phase 2: Permissions & Skeleton (Hours 2-4)]
        Perms[Implement OS Permissions\nBT, WiFi, Location, Nearby] --> Nav[Build Base UI Navigation\nTabs: Feed, Map, Status]
        Nav --> StateMgmt[Setup Global State/Store]
    end
    
    Phase2 --> Phase3
    
    subgraph Phase3 [Phase 3: The Core Network Layer (Hours 4-10)]
        direction TB
        API[Integrate Nearby Connections API] --> Discovery[Build Advertising & Discovery Service]
        Discovery --> ConnLogic[Implement Connection Acceptance Logic]
        ConnLogic --> ByteTransfer[Implement Byte Array Send/Receive]
    end
    
    Phase3 --> Checkpoint1{Core Networking\nWorking?}
    Checkpoint1 -- No --> Phase3
    Checkpoint1 -- Yes --> Phase4
    
    subgraph Phase4 [Phase 4: Application Logic (Hours 10-16)]
        LocalDB[Setup Local DB/Storage] --> MeshProtocol[Implement Flooding & Loop Prevention]
        MeshProtocol --> BindUI[Bind Send/Receive to UI]
        BindUI --> TextSOS[Test Simple Text SOS Broadcasts]
    end

    Phase4 --> Checkpoint2{Text Chat\nWorking Offline?}
    Checkpoint2 -- No --> Phase4
    Checkpoint2 -- Yes --> Phase5
    
    subgraph Phase5 [Phase 5: Advanced Features (Hours 16-20)]
        GPS[Integrate Device GPS] --> Radar[Build Radar/Compass UI]
        Radar --> ImageUpload[Implement Image Chunking for Missing Persons]
    end
    
    Phase5 --> Phase6
    
    subgraph Phase6 [Phase 6: Testing & Demo Prep (Final Hours)]
        Deploy[Deploy to 3+ Physical Phones] --> TestRun[Simulate Disaster:\nAirplane Mode ON, BT/WiFi ON]
        TestRun --> Polish[UI Polish, Dark Mode, Animations]
        Polish --> Pitch[Prepare Pitch & Demo Script]
    end
    
    Phase6 --> Finish([Hackathon Submission])

    classDef phase fill:#2c3e50,stroke:#34495e,stroke-width:2px,color:#fff;
    classDef task fill:#34495e,stroke:#2980b9,stroke-width:1px,color:#fff;
    classDef check fill:#e67e22,stroke:#d35400,stroke-width:2px,color:#fff;
    classDef endpoint fill:#27ae60,stroke:#2ecc71,stroke-width:2px,color:#fff;

    class Phase1,Phase2,Phase3,Phase4,Phase5,Phase6 phase;
    class TechStack,Scaffold,Repo,Perms,Nav,StateMgmt,API,Discovery,ConnLogic,ByteTransfer,LocalDB,MeshProtocol,BindUI,TextSOS,GPS,Radar,ImageUpload,Deploy,TestRun,Polish,Pitch task;
    class Checkpoint1,Checkpoint2 check;
    class Start,Finish endpoint;
```

### Strategic Advice for the Hackathon:
* **The "Kill Switch" Strategy**: If Phase 3 (Networking) proves too difficult with the Google Nearby API, have a fallback plan to just use raw Bluetooth Low Energy (BLE) advertising for tiny text strings. It won't support images, but you'll have a working text SOS demo.
* **Physical Devices are Mandatory**: You **cannot** test mesh networking on an emulator. You absolutely need at least 2 (preferably 3+) physical Android devices to test the `STRATEGY_CLUSTER` mesh topology.
* **Fake the UI First**: While one teammate wrestles with the complex networking API (Phase 3), the other teammate should be building the entire UI with "dummy" data (Phase 2 & 4). Connect the two halves when both are ready.
