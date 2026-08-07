# Offline Mesh Application Architecture

Here is the detailed flowchart demonstrating how the peer-to-peer mesh networking logic operates, specifically focusing on how devices connect and how messages propagate without infinite loops.

```mermaid
flowchart TD
    Start([App Launch]) --> Permissions{"Check Permissions\n(BT, WiFi, Location)"}
    Permissions -- Not Granted --> RequestPerm[Request Permissions]
    RequestPerm --> Permissions
    Permissions -- Granted --> Init[Initialize Nearby Connections API]
    
    Init --> StateSplit{Mesh State}
    StateSplit --> Advertise["Start Advertising\n(Broadcast Presence)"]
    StateSplit --> Discover["Start Discovery\n(Look for Peers)"]
    
    %% Connection Logic
    Discover --> PeerFound{Peer Discovered?}
    PeerFound -- Yes --> ReqConn[Request Connection]
    ReqConn --> ConnStatus{Connection\nAccepted?}
    ConnStatus -- Yes --> AddMesh[Add Node to Active Mesh List]
    ConnStatus -- No --> Discover
    Advertise --> IncomingConn{Incoming\nConnection?}
    IncomingConn -- Yes --> AcceptConn[Accept Connection]
    AcceptConn --> AddMesh
    
    %% Main Loop
    AddMesh --> MainLoop((Main App Loop))
    
    %% Send Flow
    MainLoop -.-> UserSend([User Creates Message\nSOS/GPS/Image])
    UserSend --> CreatePayload[Create Payload:\nID, Data, Timestamp]
    CreatePayload --> SaveLocal[Save to Local DB]
    SaveLocal --> BroadcastSend[Send to ALL Connected Peers]
    
    %% Receive Flow
    MainLoop -.-> ReceivePeer([Receive Data from Peer X])
    ReceivePeer --> CheckID{Have I seen this\nMessage ID before?}
    CheckID -- Yes --> Discard["Discard Message\n(Prevents Loops)"]
    CheckID -- No --> SaveDB[Save to Local DB]
    SaveDB --> UpdateUI[Update UI / Show Notification]
    UpdateUI --> Forward[Forward to ALL Connected Peers\nEXCEPT Peer X]

    classDef core fill:#2c3e50,stroke:#34495e,stroke-width:2px,color:#fff;
    classDef process fill:#34495e,stroke:#2980b9,stroke-width:2px,color:#fff;
    classDef decision fill:#e67e22,stroke:#d35400,stroke-width:2px,color:#fff;
    classDef action fill:#27ae60,stroke:#2ecc71,stroke-width:2px,color:#fff;
    classDef endnode fill:#c0392b,stroke:#e74c3c,stroke-width:2px,color:#fff;

    class Start,MainLoop core;
    class Permissions,PeerFound,ConnStatus,IncomingConn,CheckID decision;
    class Init,Advertise,Discover,CreatePayload,SaveLocal,SaveDB,UpdateUI process;
    class ReqConn,AcceptConn,AddMesh,BroadcastSend,Forward action;
    class RequestPerm,Discard endnode;
```

### Key Mechanisms Explained:
1. **Continuous Discovery/Advertising:** Every phone runs both simultaneously. This is what makes it a "Mesh". Everyone is looking for everyone else.
2. **Flooding Algorithm:** When a message is sent or received, it is "flooded" (sent) to every single person you are currently connected to. 
3. **Loop Prevention (Critical):** Because of flooding, a message will bounce around the network. The `Have I seen this Message ID before?` check is the most important part of the mesh. If Node A sends to Node B, and Node B forwards to Node C, Node C will try to forward it back to Node A. Node A must recognize the ID and drop it to stop an infinite loop.
