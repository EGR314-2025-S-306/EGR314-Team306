---
Title: Block Diagram, Process Diagram, & Message Structure
---

## Block Diagram

![block diagram](./assets/images/block.png)

## Process Diagram

``` mermaid
sequenceDiagram
    actor u as User
    participant a as Aarshon
    participant i as Ian
    participant c as Alex
    participant k as Kushagra
    actor w as Web
    loop Main
        i->>c: alignment sensor data
        c->>c: trash
        i->>c: weather sensor data
        c->>k: weather sensor data
        k->>w: weather sensor data
        k->>a: weather sensor data
        a->>u: weather sensor data
        a->>i: weather sensor data
        i->>i: trash
    end
```

## Message Structure

Message type<br>bytes 1-2<br>uint16_t | Description
---|---
1 | smth
2 | *cont.*

Message Type: 1

bytes 1-2 | byte 3
---|---
0x01 | more
