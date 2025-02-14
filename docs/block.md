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
    w-->>k: Alignment frequency
    k->>a: Kushagra to Alex<br>Set alignment frequency 5min
    a->>i: Kushagra to Alex<br>Set alignment frequency 5min
    i->>c: Kushagra to Alex<br>Set alignment frequency 5min
    c->>c: Set alignment wait period 300s<br>trash msg
    u-->>a: Shift rotation
    a->>i: Aarshon to Alex<br>Shift 10 degrees clockwise
    i->>c: Aarshon to Alex<br>Shift 10 degrees clockwise
    c->>c: Rotate platform 10 degrees clockwise
    loop [every second]
        i->>c: Ian to Alex<br>Solar panel alignment
        c->>c: Moves solar panel<br>trash msg
        i->>c: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        c->>k: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        k-->>w: Temp is 70F & Humidity is 30%
        k->>a: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        a-->>u: Display data
        a->>i: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        i->>i: Ian to Everyone<br>trash
    end
```

## Message Structure

Message type<br>byte 1-2<br>uint16_t | Description
---|---
1 | print sensor data A B C D
2 | *more data msgs*
3 | subsystem Z status code

**Message Type 1:** Sensor Data Transmission

byte 1-2 | byte 3 | byte 4 | byte 5 | byte 6
---|---|---|---|---
0x01 | A(uint8_t) | B(uint8_t) | C(uint8_t) | D(uint8_t)
**sensor** | wind speed | temperature | humidity | atm pressure

**Message Type 2:**

byte 1-2 | byte 3
---|---
0x02 | (uint8_t)

**Message Type 3:** Subsystem Status Code

byte 1-2 | byte 3 | byte 4
---|---|---
0x03 | Z(uint_8) | (uint8_t)

code number | meaning
---|---
0 | full funtionality
1 | partial funtionality
2 | no funtionality
