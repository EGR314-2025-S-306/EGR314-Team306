---
Title: Block Diagram, Process Diagram, & Message Structure
---

## Block Diagram

The station is comprised of four subsystems which are each assigned to a group member. These subsystems are connected through a UART daisy chain that functions as a continuous loop for messages. This daisy chain is also used to transfer power, ground, and other signals between neighboring subsystems.

![block diagram](./assets/images/block.png)

## Process Diagram

All critical information is sent through UART and must follow the protocol below. Messages that are sent to everyone in the chain are trashed by the sender. Messages with a designated recipient are trashed by the recipient. Messages may be single commands or continuous loops.

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

Message type for sending measured wind speed, temperature, humidity, and air pressure to all other subsystems.  
byte 1-2 | byte 3 | byte 4 | byte 5 | byte 6
---|---|---|---|---
0x01 | A(uint8_t) | B(uint8_t) | C(uint8_t) | D(uint8_t)
| | wind speed | temperature | humidity | atm pressure

**Message Type 2:** Shift Motor

Message type for sending a command to rotate base stepper "Y" degrees.  
byte 1-2 | byte 3 | byte 4
---|---|---
0x02 | X(uint8_t) | Y(uint8_t)
| | motor # | theta

**Message Type 3:** Alignment frequency

Message type for sending a command to set the panel alignment frequency "X" number of seconds.  
byte 1-2 | byte 3-4
---|---
0x03 | X(uint16_t)
| | time(sec)

**Message Type 4:** Subsystem Status Code

Message type for sending status code of subsystem "Z" to be displayed.  
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

Message type for sending string about subsystem error.  
byte 1-2 | byte 3-58
---|---
0x05 | Error Message char(uint8_t)

**Message Type 6:** Navigation Command

Byte 1-2 |Byte 3
---|---
0x06 | (uint8_t) (Navigation Option)
0x07| Show temperature
0x08 | Show humidity
0x09 | Show Wind Speed
0x10 | Show Atmospheric Pressure
0x11 | Show system status