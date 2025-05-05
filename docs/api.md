---
title: Message Structure & API
---

## API: Message Structure

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

**Start Bytes:** Two specific start bytes (0x41 “A” and 0x5A “Z”) mark the beginning of a message frame.These were chosen as an easy-to-remember signature ("AZ") that also serves as a simple error check – if a node does not see "AZ" at the start where expected, it knows a message is misaligned or corrupted and will reset its parser (preventing false triggers).

**Sender and Receiver IDs:** Each subsystem has a unique ID embedded in the message so that nodes can identify messages. We used both character IDs and matching hex codes for clarity. For example, 'i' (0x69) represents the Sensor (Ian), 'c' (0x63) the Actuator (Alex), 'a' (0x61) the HMI (Aarshon), and 'k' (0x6B) the Wi-Fi/Cloud module (Kushagra). A special ID 'X' (0x58) is reserved for broadcast messages intended for all nodes.
In each message, the third byte is the sender’s ID and the fourth byte is the intended recipient’s ID (or 'X' if broadcast). This addressing scheme was a design decision to make routing simple – every board can quickly check the Receiver ID byte against itself (or 'X') to know if it should process or forward the message.

***(The message type design was informed by the need to support both binary data and human-readable info. We considered using purely binary codes for everything to save space, but given our 64-byte frame, we had room to include some ASCII for convenience. The compromise was to use numeric/char codes for known small values and plain text for longer messages only when necessary. This has worked well in practice, as it’s easy to tell message purpose at a glance in a debug log.)***

**Data Payload:** Depending on the message type, the data section can range from 0 bytes (for a simple ping or acknowledgment) up to 58 bytes. Most of our messages are short (sensor readings fit in 2 bytes, status codes in 1 byte, small commands in a few bytes).
We set an upper limit of 64 bytes total for a message frame, which is a reasonable size for our needs and prevents any runaway transmissions from blocking the bus. If a message’s end bytes are not received (due to interference or a reset), the protocol dictates that once 64 bytes total are read, the message is terminated anyway. This failsafe ensures that a missing terminator doesn’t lock the system waiting indefinitely.

**End Bytes:** We use two specific end-of-message bytes (0x59 “Y” and 0x42 “B”) to mark the conclusion of a message frame. Thus every message effectively is wrapped by “AZ” at the start and “YB” at the end. These end bytes were also chosen to be distinct from common data values to reduce the chance of accidental occurrence. When a node sees “YB”, it knows the message is complete and can be processed (if addressed to it) or passed on. If a node doesn’t see “YB” where expected by the length, as mentioned, it will drop the message after 64 bytes to avoid lock-up.
