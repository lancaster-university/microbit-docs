#uBit.serial

##Overview

Serial communication provides a simple way to exchange a series of bytes between one computer and another.  The runtime's implementation of serial is general purpose and supports a number of different modes. It has a circular buffer for both the reception and transmission of data, and provides notifications to the user through the MessageBus.

By default, the baud rate for MicroBitSerial is `115200` and has very little overhead up until
it is used to `send()` or `read()`, at which point buffers are allocated in order
to accommodate incoming or outgoing data.

MicroBitSerial inherits from mbeds' [`RawSerial`](https://developer.mbed.org/users/mbed_official/code/mbed/docs/252557024ec3/classmbed_1_1RawSerial.html)
class, which exposes a lightweight version of `printf()` and incurs minimal overhead
as MicroBitSerial's buffers will not be allocated.

The MicroBitSerial class supports multithreaded operation, ensuring that only
one fiber can access the Serial port at a time.

The USB interface on the micro:bit is the [KL26Z](https://www.mbed.com/en/development/hardware/prototyping-production/daplink/daplink-on-kl26z/#Updating_your_DAPLink_firmware).

!!! note
    On macOS and Linux Serial communication works out of the box, however on Windows an additional
    **[driver](https://developer.mbed.org/handbook/Windows-serial-configuration)** is required.

!!! warning
    The baud rate is shared across all instances of MicroBitSerial (this is enforced in hardware).

### Serial modes

There are three modes of operation for all `send()` or `read()` calls:

- `ASYNC` - Returns immediately after fetching any available data for a given call
- `SYNC_SPINWAIT` - Synchronously access the serial port until the selected operation is complete.
                    This mode will lock up the processor, and isn't recommended if multiple fibers are in use.
- `SYNC_SLEEP` - Blocks the current fiber until the selected operation is complete. This mode cooperates with the
                 Fiber scheduler, and should be used in a multi-fiber program.

### Serial debug

In MicroBitConfig.h, the configuration option `MICROBIT_DEBUG` can be used to activate serial debugging
for many of the components in the runtime.


##Message Bus ID

| Constant | Value |
| ------------- |-------------|
| MICROBIT_ID_SERIAL | 32 |

##Message Bus Events

| Constant | Value |
| ------------- |-------------|
| MICROBIT_SERIAL_EVT_DELIM_MATCH | 1 |
| MICROBIT_SERIAL_EVT_HEAD_MATCH | 2 |
| MICROBIT_SERIAL_EVT_RX_FULL | 3 |

## Notify Events

These events use the notification channel `MICROBIT_ID_NOTIFY`, which provides
general purpose synchronisation.

| Constant | Value |
| ------------- |-------------|
| MICROBIT_SERIAL_EVT_TX_EMPTY | 2 |

##API
[comment]: <> ({"className":"MicroBitSerial"})
##Constructor
<br/>
####MicroBitSerial( <div style='color:#a71d5d; display:inline-block'>PinName</div> tx,  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx)
#####Description
Constructor. Create an instance of  MicroBitSerial   

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> tx - the Pin to be used for transmission

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx - the Pin to be used for receiving data
#####Example
```cpp
 MicroBitSerial serial(USBTX, USBRX); 
```

!!! note
    the default baud rate is 115200. More API details can be found: -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/SerialBase.h -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/RawSerial.h

<br/>
####MicroBitSerial( <div style='color:#a71d5d; display:inline-block'>PinName</div> tx,  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx,  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> rxBufferSize)
#####Description
Constructor. Create an instance of  MicroBitSerial   

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> tx - the Pin to be used for transmission

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx - the Pin to be used for receiving data

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> rxBufferSize - the size of the buffer to be used for receiving bytes
#####Example
```cpp
 MicroBitSerial serial(USBTX, USBRX); 
```

!!! note
    the default baud rate is 115200. More API details can be found: -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/SerialBase.h -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/RawSerial.h

<br/>
####MicroBitSerial( <div style='color:#a71d5d; display:inline-block'>PinName</div> tx,  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx,  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> rxBufferSize,  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> txBufferSize)
#####Description
Constructor. Create an instance of  MicroBitSerial   

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> tx - the Pin to be used for transmission

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx - the Pin to be used for receiving data

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> rxBufferSize - the size of the buffer to be used for receiving bytes

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> txBufferSize - the size of the buffer to be used for transmitting bytes
#####Example
```cpp
 MicroBitSerial serial(USBTX, USBRX); 
```

!!! note
    the default baud rate is 115200. More API details can be found: -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/SerialBase.h -https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/api/RawSerial.h

##sendChar
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>sendChar</div>( <div style='color:#a71d5d; display:inline-block'>char</div> c)
#####Description
Sends a single character over the serial line.  

   
 
 c 
 
 
 the character to send  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - the character is copied into the txBuff and returns immediately.

SYNC_SPINWAIT - the character is copied into the txBuff and this method
                will spin (lock up the processor) until the character has
                been sent.

SYNC_SLEEP - the character is copied into the txBuff and the fiber sleeps
             until the character has been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>char</div> c - the character to send
#####Returns
the number of bytes written, or MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>sendChar</div>( <div style='color:#a71d5d; display:inline-block'>char</div> c,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Sends a single character over the serial line.  

   
 
 c 
 
 
 the character to send  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - the character is copied into the txBuff and returns immediately.

SYNC_SPINWAIT - the character is copied into the txBuff and this method
                will spin (lock up the processor) until the character has
                been sent.

SYNC_SLEEP - the character is copied into the txBuff and the fiber sleeps
             until the character has been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>char</div> c - the character to send

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - the character is copied into the txBuff and returns immediately.

SYNC_SPINWAIT - the character is copied into the txBuff and this method
                will spin (lock up the processor) until the character has
                been sent.

SYNC_SLEEP - the character is copied into the txBuff and the fiber sleeps
             until the character has been sent. This allows other fibers
             to continue execution.

#####Returns
the number of bytes written, or MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission. 
##send
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>send</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> s)
#####Description
Sends a  ManagedString  over the serial line.  

   
 
 s 
 
 
 the string to send  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> s - the string to send
#####Returns
the number of bytes written, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission, MICROBIT_INVALID_PARAMETER if buffer is invalid, or the given bufferLen is <= 0. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>send</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> s,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Sends a  ManagedString  over the serial line.  

   
 
 s 
 
 
 the string to send  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> s - the string to send

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.

#####Returns
the number of bytes written, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission, MICROBIT_INVALID_PARAMETER if buffer is invalid, or the given bufferLen is <= 0. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>send</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer,  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen)
#####Description
Sends a buffer of known length over the serial line.  

   
 
 buffer 
 
 
 a pointer to the first character of the buffer  
 
 
 
 len 
 
 
 the number of bytes that are safely available to read.  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer - a pointer to the first character of the buffer

>  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen - a pointer to the first character of the buffer
#####Returns
the number of bytes written, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission, MICROBIT_INVALID_PARAMETER if buffer is invalid, or the given bufferLen is <= 0. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>send</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer,  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Sends a buffer of known length over the serial line.  

   
 
 buffer 
 
 
 a pointer to the first character of the buffer  
 
 
 
 len 
 
 
 the number of bytes that are safely available to read.  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer - a pointer to the first character of the buffer

>  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen - a pointer to the first character of the buffer

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - bytes are copied into the txBuff and returns immediately.

SYNC_SPINWAIT - bytes are copied into the txBuff and this method
                will spin (lock up the processor) until all bytes
                have been sent.

SYNC_SLEEP - bytes are copied into the txBuff and the fiber sleeps
             until all bytes have been sent. This allows other fibers
             to continue execution.

#####Returns
the number of bytes written, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for transmission, MICROBIT_INVALID_PARAMETER if buffer is invalid, or the given bufferLen is <= 0. 
##read
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>read</div>()
#####Description
Reads a single character from the rxBuff  

   
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - A character is read from the rxBuff if available, if there
        are no characters to be read, a value of MICROBIT_NO_DATA is returned immediately.

SYNC_SPINWAIT - A character is read from the rxBuff if available, if there
                are no characters to be read, this method will spin
                (lock up the processor) until a character is available.

SYNC_SLEEP - A character is read from the rxBuff if available, if there
             are no characters to be read, the calling fiber sleeps
             until there is a character available.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Returns
a character, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for reception, MICROBIT_NO_RESOURCES if buffer allocation did not complete successfully, or MICROBIT_NO_DATA if the rx buffer is empty and the mode given is ASYNC. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>read</div>( <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Reads a single character from the rxBuff  

   
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - A character is read from the rxBuff if available, if there
        are no characters to be read, a value of MICROBIT_NO_DATA is returned immediately.

SYNC_SPINWAIT - A character is read from the rxBuff if available, if there
                are no characters to be read, this method will spin
                (lock up the processor) until a character is available.

SYNC_SLEEP - A character is read from the rxBuff if available, if there
             are no characters to be read, the calling fiber sleeps
             until there is a character available.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - A character is read from the rxBuff if available, if there
        are no characters to be read, a value of MICROBIT_NO_DATA is returned immediately.

SYNC_SPINWAIT - A character is read from the rxBuff if available, if there
                are no characters to be read, this method will spin
                (lock up the processor) until a character is available.

SYNC_SLEEP - A character is read from the rxBuff if available, if there
             are no characters to be read, the calling fiber sleeps
             until there is a character available.

#####Returns
a character, MICROBIT_SERIAL_IN_USE if another fiber is using the serial instance for reception, MICROBIT_NO_RESOURCES if buffer allocation did not complete successfully, or MICROBIT_NO_DATA if the rx buffer is empty and the mode given is ASYNC. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>ManagedString</div> <div style='color:#795da3; display:inline-block'>read</div>( <div style='color:#a71d5d; display:inline-block'>int</div> size)
#####Description
Reads multiple characters from the rxBuff and returns them as a  ManagedString   

   
 
 size 
 
 
 the number of characters to read.  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will return
        a ManagedString with the expected size. Otherwise, it will read however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will return
                a ManagedString with the expected size. Otherwise, this method will spin
                (lock up the processor) until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will return
             a ManagedString with the expected size. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> size - the number of characters to read.
#####Returns
A  ManagedString , or an empty  ManagedString  if an error was encountered during the read. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>ManagedString</div> <div style='color:#795da3; display:inline-block'>read</div>( <div style='color:#a71d5d; display:inline-block'>int</div> size,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Reads multiple characters from the rxBuff and returns them as a  ManagedString   

   
 
 size 
 
 
 the number of characters to read.  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will return
        a ManagedString with the expected size. Otherwise, it will read however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will return
                a ManagedString with the expected size. Otherwise, this method will spin
                (lock up the processor) until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will return
             a ManagedString with the expected size. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> size - the number of characters to read.

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will return
        a ManagedString with the expected size. Otherwise, it will read however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will return
                a ManagedString with the expected size. Otherwise, this method will spin
                (lock up the processor) until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will return
             a ManagedString with the expected size. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.

#####Returns
A  ManagedString , or an empty  ManagedString  if an error was encountered during the read. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>read</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer,  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen)
#####Description
Reads multiple characters from the rxBuff and fills a user buffer.  

   
 
 buffer 
 
 
 a pointer to a user allocated buffer.  
 
 
 
 bufferLen 
 
 
 the amount of data that can be safely stored  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will fill
        the given buffer. Otherwise, it will fill the buffer with however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will fill
                the given buffer. Otherwise, this method will spin (lock up the processor)
                and fill the buffer until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will fill
             the given buffer. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer - a pointer to a user allocated buffer.

>  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen - the amount of data that can be safely stored
#####Returns
the number of characters read, or MICROBIT_SERIAL_IN_USE if another fiber is using the instance for receiving. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>read</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer,  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Reads multiple characters from the rxBuff and fills a user buffer.  

   
 
 buffer 
 
 
 a pointer to a user allocated buffer.  
 
 
 
 bufferLen 
 
 
 the amount of data that can be safely stored  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will fill
        the given buffer. Otherwise, it will fill the buffer with however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will fill
                the given buffer. Otherwise, this method will spin (lock up the processor)
                and fill the buffer until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will fill
             the given buffer. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t \*</div> buffer - a pointer to a user allocated buffer.

>  <div style='color:#a71d5d; display:inline-block'>int</div> bufferLen - the amount of data that can be safely stored

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If the desired number of characters are available, this will fill
        the given buffer. Otherwise, it will fill the buffer with however
        many characters there are available.

SYNC_SPINWAIT - If the desired number of characters are available, this will fill
                the given buffer. Otherwise, this method will spin (lock up the processor)
                and fill the buffer until the desired number of characters have been read.

SYNC_SLEEP - If the desired number of characters are available, this will fill
             the given buffer. Otherwise, the calling fiber sleeps
             until the desired number of characters have been read.

#####Returns
the number of characters read, or MICROBIT_SERIAL_IN_USE if another fiber is using the instance for receiving. 
##readUntil
<br/>
####<div style='color:#a71d5d; display:inline-block'>ManagedString</div> <div style='color:#795da3; display:inline-block'>readUntil</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters)
#####Description
Reads until one of the delimeters matches a character in the rxBuff  

   
 
 delimeters 
 
 
 a  ManagedString  containing a sequence of delimeter characters e.g.  ManagedString ("\r\n")  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If one of the delimeters matches a character already in the rxBuff
        this method will return a ManagedString up to the delimeter.
        Otherwise, it will return an Empty ManagedString.

SYNC_SPINWAIT - If one of the delimeters matches a character already in the rxBuff
                this method will return a ManagedString up to the delimeter.
                Otherwise, this method will spin (lock up the processor) until a
                received character matches one of the delimeters.

SYNC_SLEEP - If one of the delimeters matches a character already in the rxBuff
             this method will return a ManagedString up to the delimeter.
             Otherwise, the calling fiber sleeps until a character matching one
             of the delimeters is seen.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters - a  ManagedString  containing a sequence of delimeter characters e.g.  ManagedString ("\r\n")
#####Returns
A  ManagedString  containing the characters up to a delimeter, or an Empty  ManagedString , if another fiber is currently using this instance for reception.

!!! note
    delimeters are matched on a per byte basis. 

<br/>
####<div style='color:#a71d5d; display:inline-block'>ManagedString</div> <div style='color:#795da3; display:inline-block'>readUntil</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Reads until one of the delimeters matches a character in the rxBuff  

   
 
 delimeters 
 
 
 a  ManagedString  containing a sequence of delimeter characters e.g.  ManagedString ("\r\n")  
 
 
 
 mode 
 
 
 the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If one of the delimeters matches a character already in the rxBuff
        this method will return a ManagedString up to the delimeter.
        Otherwise, it will return an Empty ManagedString.

SYNC_SPINWAIT - If one of the delimeters matches a character already in the rxBuff
                this method will return a ManagedString up to the delimeter.
                Otherwise, this method will spin (lock up the processor) until a
                received character matches one of the delimeters.

SYNC_SLEEP - If one of the delimeters matches a character already in the rxBuff
             this method will return a ManagedString up to the delimeter.
             Otherwise, the calling fiber sleeps until a character matching one
             of the delimeters is seen.
   
 
 
Defaults to SYNC_SLEEP.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters - a  ManagedString  containing a sequence of delimeter characters e.g.  ManagedString ("\r\n")

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - If one of the delimeters matches a character already in the rxBuff
        this method will return a ManagedString up to the delimeter.
        Otherwise, it will return an Empty ManagedString.

SYNC_SPINWAIT - If one of the delimeters matches a character already in the rxBuff
                this method will return a ManagedString up to the delimeter.
                Otherwise, this method will spin (lock up the processor) until a
                received character matches one of the delimeters.

SYNC_SLEEP - If one of the delimeters matches a character already in the rxBuff
             this method will return a ManagedString up to the delimeter.
             Otherwise, the calling fiber sleeps until a character matching one
             of the delimeters is seen.

#####Returns
A  ManagedString  containing the characters up to a delimeter, or an Empty  ManagedString , if another fiber is currently using this instance for reception.

!!! note
    delimeters are matched on a per byte basis. 

##baud
<br/>
####<div style='color:#a71d5d; display:inline-block'>void</div> <div style='color:#795da3; display:inline-block'>baud</div>( <div style='color:#a71d5d; display:inline-block'>int</div> baudrate)
#####Description
A wrapper around the inherited method "baud" so we can trap the baud rate as it changes and restore it if  redirect()  is called.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> baudrate - the new baudrate. See: 
 https://github.com/mbedmicro/mbed/blob/master/libraries/mbed/targets/hal/TARGET_NORDIC/TARGET_MCU_NRF51822/serial_api.c  for permitted baud rates. 

#####Returns
MICROBIT_INVALID_PARAMETER if baud rate is less than 0, otherwise MICROBIT_OK.

!!! note
    the underlying implementation chooses the first allowable rate at or above that requested. 

##redirect
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>redirect</div>( <div style='color:#a71d5d; display:inline-block'>PinName</div> tx,  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx)
#####Description
A way of dynamically configuring the serial instance to use pins other than USBTX and USBRX.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> tx - the new transmission pin.

>  <div style='color:#a71d5d; display:inline-block'>PinName</div> rx - the new reception pin.
#####Returns
MICROBIT_SERIAL_IN_USE if another fiber is currently transmitting or receiving, otherwise MICROBIT_OK. 
##eventAfter
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>eventAfter</div>( <div style='color:#a71d5d; display:inline-block'>int</div> len)
#####Description
Configures an event to be fired after "len" characters.  

 Will generate an event with the ID: MICROBIT_ID_SERIAL and the value MICROBIT_SERIAL_EVT_HEAD_MATCH.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> len - the number of characters to wait before triggering the event.
#####Returns
MICROBIT_INVALID_PARAMETER if the mode given is SYNC_SPINWAIT, otherwise MICROBIT_OK. 
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>eventAfter</div>( <div style='color:#a71d5d; display:inline-block'>int</div> len,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Configures an event to be fired after "len" characters.  

 Will generate an event with the ID: MICROBIT_ID_SERIAL and the value MICROBIT_SERIAL_EVT_HEAD_MATCH.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>int</div> len - the number of characters to wait before triggering the event.

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - Will configure the event and return immediately.

SYNC_SPINWAIT - will return MICROBIT_INVALID_PARAMETER

SYNC_SLEEP - Will configure the event and block the current fiber until the
             event is received.

#####Returns
MICROBIT_INVALID_PARAMETER if the mode given is SYNC_SPINWAIT, otherwise MICROBIT_OK. 
##eventOn
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>eventOn</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters)
#####Description
Configures an event to be fired on a match with one of the delimeters.  

 Will generate an event with the ID: MICROBIT_ID_SERIAL and the value MICROBIT_SERIAL_EVT_DELIM_MATCH.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters - the characters to match received characters against e.g.  ManagedString ("\n")
#####Returns
MICROBIT_INVALID_PARAMETER if the mode given is SYNC_SPINWAIT, otherwise MICROBIT_OK.

!!! note
    delimeters are matched on a per byte basis. 

<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>eventOn</div>( <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters,  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode)
#####Description
Configures an event to be fired on a match with one of the delimeters.  

 Will generate an event with the ID: MICROBIT_ID_SERIAL and the value MICROBIT_SERIAL_EVT_DELIM_MATCH.  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>ManagedString</div> delimeters - the characters to match received characters against e.g.  ManagedString ("\n")

>  <div style='color:#a71d5d; display:inline-block'>MicroBitSerialMode</div> mode - the selected mode, one of: ASYNC, SYNC_SPINWAIT, SYNC_SLEEP. Each mode gives a different behaviour:  ASYNC - Will configure the event and return immediately.

SYNC_SPINWAIT - will return MICROBIT_INVALID_PARAMETER

SYNC_SLEEP - Will configure the event and block the current fiber until the
             event is received.

#####Returns
MICROBIT_INVALID_PARAMETER if the mode given is SYNC_SPINWAIT, otherwise MICROBIT_OK.

!!! note
    delimeters are matched on a per byte basis. 

##isReadable
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>isReadable</div>()
#####Description
Determines whether there is any data waiting in our Rx buffer.  

 


#####Returns
1 if we have space, 0 if we do not.

!!! note
    We do not wrap the super's readable() method as we don't want to interfere with communities that use manual calls to serial.readable(). 

##isWriteable
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>isWriteable</div>()
#####Description
Determines if we have space in our txBuff.  

 


#####Returns
1 if we have space, 0 if we do not.

!!! note
    We do not wrap the super's writeable() method as we don't want to interfere with communities that use manual calls to serial.writeable(). 

##setRxBufferSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>setRxBufferSize</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t</div> size)
#####Description
Reconfigures the size of our rxBuff  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> size - the new size for our rxBuff
#####Returns
MICROBIT_SERIAL_IN_USE if another fiber is currently using this instance for reception, otherwise MICROBIT_OK. 
##setTxBufferSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>setTxBufferSize</div>( <div style='color:#a71d5d; display:inline-block'>uint8_t</div> size)
#####Description
Reconfigures the size of our txBuff  

 


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>uint8_t</div> size - the new size for our txBuff
#####Returns
MICROBIT_SERIAL_IN_USE if another fiber is currently using this instance for transmission, otherwise MICROBIT_OK. 
##getRxBufferSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getRxBufferSize</div>()
#####Description
The size of our rx buffer in bytes.  

 


#####Returns
the current size of rxBuff in bytes 
##getTxBufferSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>getTxBufferSize</div>()
#####Description
The size of our tx buffer in bytes.  

 


#####Returns
the current size of txBuff in bytes 
##clearRxBuffer
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>clearRxBuffer</div>()
#####Description
Sets the tail to match the head of our circular buffer for reception, effectively clearing the reception buffer.  

 


#####Returns
MICROBIT_SERIAL_IN_USE if another fiber is currently using this instance for reception, otherwise MICROBIT_OK. 
##clearTxBuffer
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>clearTxBuffer</div>()
#####Description
Sets the tail to match the head of our circular buffer for transmission, effectively clearing the transmission buffer.  

 


#####Returns
MICROBIT_SERIAL_IN_USE if another fiber is currently using this instance for transmission, otherwise MICROBIT_OK. 
##rxBufferedSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>rxBufferedSize</div>()
#####Description
The number of bytes currently stored in our rx buffer waiting to be digested, by the user.  

 


#####Returns
The currently buffered number of bytes in our rxBuff. 
##txBufferedSize
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>txBufferedSize</div>()
#####Description
The number of bytes currently stored in our tx buffer waiting to be transmitted by the hardware.  

 


#####Returns
The currently buffered number of bytes in our txBuff. 
##rxInUse
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>rxInUse</div>()
#####Description
Determines if the serial bus is currently in use by another fiber for reception.  

 


#####Returns
The state of our mutex lock for reception.

!!! note
    Only one fiber can call read at a time 

##txInUse
<br/>
####<div style='color:#a71d5d; display:inline-block'>int</div> <div style='color:#795da3; display:inline-block'>txInUse</div>()
#####Description
Determines if the serial bus is currently in use by another fiber for transmission.  

 


#####Returns
The state of our mutex lock for transmition.

!!! note
    Only one fiber can call send at a time 

##detach
<br/>
####<div style='color:#a71d5d; display:inline-block'>void</div> <div style='color:#795da3; display:inline-block'>detach</div>( <div style='color:#a71d5d; display:inline-block'>Serial::IrqType</div> interuptType)
#####Description
Detaches a previously configured interrupt  

   
 
 interruptType 
 
 
 one of Serial::RxIrq or Serial::TxIrq   
 
 
          


#####Parameters

>  <div style='color:#a71d5d; display:inline-block'>Serial::IrqType</div> interuptType
____
[comment]: <> ({"end":"MicroBitSerial"})
