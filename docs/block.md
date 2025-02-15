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
    k->>a: Kushagra to Alex<br>Set alignment frequency to 5min
    a->>i: Kushagra to Alex<br>Set alignment frequency to 5min
    i->>c: Kushagra to Alex<br>Set alignment frequency to 5min
    c->>c: Set wait period<br>trash msg
    u-->>a: Shift base rotation
    a->>i: Aarshon to Alex<br>Shift 10 deg clockwise
    i->>c: Aarshon to Alex<br>Shift 10 deg clockwise
    c->>c: Rotate base 10deg clockwise<br>trash msg
    loop [every second]
        i->>c: Ian to Alex<br>Solar panel alignment
        c->>c: Moves solar panel<br>trash msg
    end
    loop [every second]
        i->>c: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        c->>k: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        k-->>w: Temp is 70F & Humidity is 30%
        k->>a: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        a-->>u: Display data
        a->>i: Ian to Everyone<br>Temp is 70F & Humidity is 30%
        i->>i: trash msg
    end
```

## Message Structure

Message type<br>byte 1-2<br>uint16_t | Description
---|---
1 | print sensor data A B C D
2 | move motor X param Y
3 | set period X
4 | subsystem Z status code
5 | subsystem Z error msg

**Message Type 1:** Sensor Data Transmission

byte 1-2 | byte 3 | byte 4 | byte 5 | byte 6
---|---|---|---|---
0x01 | A(uint8_t) | B(uint8_t) | C(uint8_t) | D(uint8_t)
| | wind speed | temperature | humidity | atm pressure

**Message Type 2:** Shift Motor

byte 1-2 | byte 3 | byte 4
---|---|---
0x02 | X(uint8_t) | Y(uint8_t)
| | motor # | theta

**Message Type 3:** Alignment frequency

byte 1-2 | byte 3-4
---|---
0x03 | X(uint16_t)
| | time(sec)

**Message Type 4:** Subsystem Status Code

byte 1-2 | byte 3 | byte 4
---|---|---
0x04 | Z(uint_8) | (uint8_t)
| | subsystem # | code

code number | meaning
---|---
0 | full funtionality
1 | partial funtionality
2 | no funtionality

**Message Type 5:** Subsystem Error Message

byte 1-2 | byte 3-58
---|---
0x05 | char(uint8_t)
