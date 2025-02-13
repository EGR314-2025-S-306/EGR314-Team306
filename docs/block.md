---
Title: Block Diagram, Process Diagram, & Message Structure
---

## Block Diagram

1[block diagram](./assets/images/block.png)

## Process Diagram

``` mermaid
sequenceDiagram
    autonumber
    Web-->>Kushagra: data
    loop Main
        Ian->>Alex: data
        Note over Ian: Transmit Sensor Data
        Alex->>Aarshon: data
        Aarshon-->>User: data
        Aarshon->>Kushagra: data
        Kushagra->>Ian: data
        Ian->>Ian: Terminate MSG
    end
```
