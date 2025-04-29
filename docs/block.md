---
Title: Block Diagram, Process Diagram, & Message Structure
---

## Block Diagram

The station is comprised of four subsystems which are each assigned to a group member. These subsystems are connected through a UART daisy chain that functions as a continuous loop for messages. This daisy chain is also used to transfer power, ground, and other signals between neighboring subsystems.

![block diagram](./assets/images/block.png)

Daisy Chain Header Pin Assignment  
![UART header](./assets/images/uart.png)

1. External Power (+9-12V)
2. UART Transmit Line (TX/RX)
3. UART Ready to Send (inactive)
4. UART Clear to Send (inactive)
5. Subsystem Specific/No Connection
6. Subsystem Specific/No Connection
7. Subsystem Specific/No Connection
8. External Ground

## Process Diagram

All critical information is sent through UART and must follow the protocol below. Messages that are sent to everyone in the chain are trashed by the sender. Messages with a designated recipient are trashed by the recipient. Messages may be single commands or continuous loops.  
Relevant message data is differentiated by utilizing the recipient ID and message type bytes. Certain message types are relevant to all subsystems. Messages that don't pertain to the subsystem are passed along down the chain.

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
    w-->>k: Local temperature
    k->>a: Kushagra to Aarshon<br>Local Temperature: 73F
    a-->>u: Display temperature
    a->>a: trash msg
    u-->>a: Shift base rotation
    a->>i: Aarshon to Alex<br>Shift 10 deg clockwise
    i->>c: Aarshon to Alex<br>Shift 10 deg clockwise
    c->>c: Rotate base 10deg clockwise<br>trash msg
    loop [every second]
        i->>c: Ian to Alex<br>Solar panel alignment
        c->>c: Moves solar panel<br>trash msg
    end
    c->>k: Alex to Aarshon<br>Subsystem malfunction
    k->>a: Alex to Aarshon<br>Subsystem malfunction
    a-->>u: Display error
    a->>a: trash msg
    loop [every second]
        i->>c: Ian to Everyone<br>Wind Speed is 10 mph
        c->>k: Ian to Everyone<br>Wind Speed is 10 mph
        k-->>w: Wind Speed is 10 mph
        k->>a: Ian to Everyone<br>Wind Speed is 10 mph
        a-->>u: Display data
        a->>i: Ian to Everyone<br>Wind Speed is 10 mph
        i->>i: trash msg
    end
```

## Message Structure

All important communication between subsystems is done over the UART daisy chain. UART messages all follow the same message structure which uses up to 64 bytes. This structure is started by two start bytes, followed by the sender ID byte and the recipient ID byte. The message information is held in the following bytes and can be up to 58 bytes in length. The message is terminated with two stop bytes.  
If any of the 4 prefix bytes are corrupted, the message is rejected. Messages are terminated after 64 bytes to stop an open loop upon failure to receive either of the two stop bytes.

0    | 1    | 2       | 3          | 4 - 61  | 62   | 63
-----|------|---------|------------|---------|------|---
`0x41` | `0x5A` | Send ID | Receive ID | Message | `0x59` | `0x42`

Team Member      | Subsystem ID
-----------------|--------------
Broadcast        | `0x58 'X'`
Aarshon George   | `0x61 'a'`
Alex Comeaux     | `0x63 'c'`
Ian Anderson     | `0x69 'i'`
Kushagra Dashora | `0x6B 'k'`

The following defines the various messages and their structures to be sent within the UART message protocol. The first message byte is used to identify the type of message, and the following 57 bytes contain the data.

Message type<br>byte[5]<br>(`char`) | Description | Ian<br>Role: Sensor<br>`'i'` | Alex<br>Role: Actuator<br>`'c'` | KD<br>Role: MQTT Server<br>`'k'` | Aarshon<br>Role: HMI<br>`'a'`
---------|---------------------------|---|---|---|---
1 | print sensor X data Y            | Send | ~       | Receive | Receive
2 | move motor X param Y             | ~    | Receive | Send    | Send
3 | subsystem status code X          | Send | Send    | Receive | Send
4 | subsystem Z error msg            | Send | Send    | Receive | Send

**Message Type 1:** Sensor Data Transmission  
Message type for sending measured wind speed, temperature, humidity, and air pressure to all other subsystems.

byte 1 | byte 2        | byte 3-4<br>big endian
-------|---------------|------------
`0x31` | X(`uint8_t`)  | Y(`uint16_t`)
~      | sensor number | data value

Number Code | Sensor
------------|------
'1' `0x01`  | wind speed
'2' `0x02`  | temperature
'3' `0x03`  | humidity
'4' `0x04`  | atm pressure

Sender | Destination
---|---
Ian `'i'` | broadcast `'X'`

**Message Type 2:** Shift Motor  
Message type for sending a command to rotate base stepper "Y" degrees.

byte 1 | byte 2       | byte 3
-------|--------------|---
`0x32` | X(`uint8_t`) | Y(`uint8_t`)
~      | direction<br>0x01 = clockwise<br>0x02 = counterclockwise | degree shift

Senders | Destination
---|---
Aarshon `'a'`<br>Kushagra `'k'` | Alex `'c'`

**Message Type 3:** Subsystem Status Code  
Message type for sending status code of a subsystem to be displayed. Sender ID is used to determine affected subsystem.

byte 1 | byte 2
-------|-----------
`0x33` | X(`uint8_t`)
~      | error code

code number | meaning
------------|----------------
'1' `0x01`  | full funtionality
'2' `0x02`  | partial funtionality
'3' `0x03`  | no funtionality

Senders | Destination
---|---
Alex `'c'`<br>Ian `'i'`<br>Kushagra `'k'` | Aarshon `'a'`

**Message Type 4:** Subsystem Error Message  
Message type for sending string about subsystem error. Sender ID is used to determine affected subsystem.

byte 1 | byte 2-58
-------|----------------------------
`0x34` | Error Message char(`uint8_t`)

Senders | Destination
---|---
Alex `'c'`<br>Ian `'i'`<br>Kushagra `'k'` | Aarshon `'a'`
