# clever_card_kit
This is where you will find the source code for the Python programs used in the MonkMakes Smartcard Kit for Raspberry Pi (https://monkmakes.com/mmcck)

Feel free to make use of this code with any project that uses an RC-522 tag reader/writer. The class SimpleMFRC522 reduces the process of reading from and writing to a card to just two functions called read and write! Actually its a little more complicated than that, because read and write both block and wait for a card to be presented, and sometimes you need to be doing other stuff in your program while you wait for a tag to be scanned. So, there are also two extra functions read_no_block and write_no_block.

# Prerequisites

This library requires the SPI-Py library to be installed. I have made a clone of the original project here: https://github.com/lthiery/SPI-Py

This fork of SPI-Py made to change the line cs_change to 0 not 1 in spi.c to allow the RC522 RFID tag readers to work correctly. This issue is described here: https://github.com/raspberrypi/linux/issues/1547

So, you will either need to get the original and modify spi.c yourself, or use my fork here: https://github.com/simonmonk/SPI-Py


# Connecting up your Reader

This library assumes that the reader is connected to your Raspberry Pi in what seems to be the standard configuration of:

Lead color|Smartcard Reader|Raspberry Pi pin
Purple|SDA|8
Orange|SCK|11
Yellow|MOSI|10
White|MISO|9
Green|IRQ|Not connected
Black|GND|GND
Gray|RST|25
Red|3.3V|3.3V


# API

## Import and Create an Instance

```
import SimpleMFRC522

reader = SimpleMFRC522.SimpleMFRC522()
```

## read()

This function takes no parameters and just waits for a card to be presented to the reader. It then returns a dict with two keys: id (the card id) and text (the 16 characters stored in sector 8 of the card)

```
tag = reader.read()
print(tag['id'])
print(tag['text'])
```



## write(text)

This function takes a text string and just waits for a card to be presented to the reader. It then writes the first 16 characters of text (padding if needed) to sector 8 of the card. It then returns a dict with two keys: id (the card id) and text (the 16 characters stored in sector 8 of the card)


```
text = raw_input('New Text: ')
print("Now scan a tag to write")
tag = reader.write(8, text) 
print("written")
print(tag['id'])
print(tag['text'])
```


## read_no_block()

This function takes no parameters and if no card is being presented to the reader immediately returns None. If a card is on the reader, then it returns a dict with two keys: id (the card id) and text (the 16 characters stored in sector 8 of the card)

The code example below would normally be in a loop.

```
tag = reader.read_no_block()
if tag:
    id = tag['id']
    print(id)
```


## write_no_block(text)

This function takes a text string and if no card is being presented to the reader immediately returns None. If a card is on the reader, then it writes the text string to sector 8 of the card and returns a dict with two keys: id (the card id) and text (the 16 characters stored in sector 8 of the card)

The code example below would normally be in a loop.

```
tag = reader.write_no_block('hello')
if tag:
    id = tag['id']
    print(id)
```

