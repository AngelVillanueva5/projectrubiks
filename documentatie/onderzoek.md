# Onderzoek project rubiks cube

## Hoe kunnen we data uit de IMU lezen om te gebruiken in de motionUI

### Onderzoeksmethodes

### Onderzoek

### IMU

Zoals beschreven in (vorig onderzoek), een IMU is een combinatie van beiden accelerometer en gyroscoop. De "SparkFun 6 degrees of Freedom IMU Digital Combo Board" is een IMU bestaande uit de ITG3200 gyroscope en ADXL 345 Accelerometer. Beiden chips bevinden zich op hetzelfde bord maar hebben hun eigen logica als het aankomt op het lezen van data

### Accelerometer (ADXL 345)

De ADXL 345 is beschreven als een 3 axis, low power accelerometer met ((meetranges)) van 2, 4, 8 of 16 g (verwijzing naar vorig onderzoek over wat voor meetwaarde gebruikt). De chip bevat meerdere extra functionaliteiten zoals het meten van activiteit/inactiviteit aan de hand van specificeerde grenzen, tap en double tap sensing, etc. 

#### Hoe lees je data uit de ADXL345

Dit ((kopje)) zal kijken naar welke stappen er nodig zijn om bruikbare ((motion data)) te lezen uit de ADXL345. De tekst zal hierbij ingaan op de aanwezige registers op de chip en welke functionaliteiten deze bieden om het doel te bereiken.

##### Meten aanzetten (0x2D POWER_CTL)

Het POWER_CTL register bevat verschillende functionaliteiten wat betreft meetfunctionaliteit van de chip. Het enige bit wat nodig is in dit register is ide D3. Als dit bit op 1 is gezet begint de chip met het meten van data.

##### Meet bereik (0x31 DATA_FORMAT)

Register DATA_FORMAT bepaald hoe data in register 0x32 tot 0x37 gepresenteert word. Bit d0 en d1 bepalen de gevoelligheid van de chip zoals zichtbaar in ((foto)).

##### Frequentie van data (0x2C BW_RATE)

Het BW_RATE register gaat over de frequentie van gelezen data. Zoals zichtbaar in ((foto)) gaan bit d0 tot d3 over de RATE die verder uitgebeeld staat in ((Foto)). Naast de frequnetie van output data bepaald het ook de gebruikte bandwidth.

#### Extra functionaliteiten

De ADXL345 bevat een ingebouwde functie die kijkt naar "taps" of sterke toename in bewegingen van de chip. De SINGLE_TAP en DOUBLE_TAP functies zijn op verschillende manieren instelbaar waar dit ((kopje)) op in zal gaan

##### Parameters

###### Drempelwaarde 
Dit kopje kijkt naar de verschillende instelbare parameters, tabel ((hieronder)) kijkt naar de parameters die gaan over de drempelwaarden voor het registreren van een tap event

| Naam | Address | Grootte register | Functionaliteit | Schaal factor |
| -- | -- | -- | -- | -- |
| THRESH_TAP | 0x1D | 8 bits | Bepaald de drempelwaarde voor de TAP functies | 62.6mg/LSB |
| X Y Z offset | 0x1E 0x1F 0x20 | 8 bits | Bied offset aanpassingen in twos complement voor de x y en z as | 16.5 mg/lsb |
| DUR | 0x21 | 8 bits | Bepaald de tijd dat een waarde boven de THRESH_TAP moet zitten om als een tap event te tellen | 625 us/LSB|
| Latent | 0x22 | 8 bits | Bevat een unsigned time value de tijd bepaald tot een window event begint | 1.25ms/LSB |
| Window | 0x23 | 8 bits | Bevat een unsiged time value die de tijd bepaald waarin een DOUBLE_TAP geregistreerd kan worden | 1.25 ms/LSB |

| Register naam | Register address|  Bit naam | Bit | Functionaliteit |
| -- | -- | -- | -- | -- |
| TAP_AXES | 0x2A | TAP_Z TAP_Y TAP_X enable | D0 D1 D2 | Bepaalt of de TAP functionaliteit actief is op verschillende assen |
| ACT_TAP_STATUS | 0x2B | TAP_Z TAP_Y TAP_X source | D0 D1 D2 | Bepaald welke as prioriteit krijgt op het meten van events | 
| INT_SOURCE | 0x30 | DOUBLE_TAP SINGLE_TAP | D5 D6 | Het lees register van de DOUBLE en SINGLE_TAP functies |:



#### LOW POWER
Low power mode kan aangezet worden door bit 4 te zetten in de BW_RATE register (Address 0x2C). Low power mode werkt door werkt door de frequentie van output data and bandwidth te verminderen. Hierbij komt er dus minder vaak data door maar veroorzaakt het ook meer noise bij het uitlezen van de data.

De chip bevat ook een auto sleep mode waarbij het automatisch naar een slaapstand wisselt tijdens langdurige inactiviteit. (pagina 7).

(foto van table 6 pagina 6)

#### Communicatie

Al bevat de chip beiden I2C en SPI zal dit onderzoek alleen kijken naar I2C communicatie. 

Er zijn verschillende manieren om data te lezen van de ADXL345. Meeste manieren maken gebruik van het FIFO register dat als volgt werkt. 

##### Data uitlezen

Een fifo (first in first out) register is het register waar data in opgeslagen word. de ADXL345 heeft verschillende manieren om het registeren in te stellen

- FIFO Mode
    - Hierbij word data van de x, y en z axis opgeslagen in het fifo register. 
- Stream Mode
    - sdasd 
- Trigger mode

fifo data is read through datax datay and dataz registers, address 0x32 to 0x37

### Gyroscoop (ITG 3200)

