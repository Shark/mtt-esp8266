# Monster Tech Tales
# ----Juni 2016----

ESP8266 & NodeMCU im Hands on                                   Felix Seidel @ Zweitag GmbH
https://github.com/Shark/mtt-esp8266                                             2016-06-29

▄▄▄▄▄▄▄  ▄  ▄▄▄ ▄  ▄  ▄▄▄▄▄▄▄  
█ ▄▄▄ █ ▄▄█▀█▄██  ▄▀█ █ ▄▄▄ █  
█ ███ █  ▀█▀ ▀███▄██  █ ███ █  
█▄▄▄▄▄█ ▄▀▄ ▄▀▄▀▄ ▄▀▄ █▄▄▄▄▄█  
▄▄▄ ▄▄▄▄█▀█▄▀▀█ ▀███▄▄▄   ▄    
█ ▀▀ █▄▄▄▄▀▀ ▄▀ ▄▄  ▀▄█ ▄▀▄▄█  
▀▄    ▄ █  ▀▄    █▀  ▄▄▀▀▄ █▄  
██▀▀ █▄█▄▄▄██▀▄ ▄  ▀▀▀█ ▄█ ▄█  
█▀▄ ▀ ▄█▀▄▄▄▀▀██ █▀▄█▀█▄ █ █▄  
▄▀█▄▄▀▄ ▄█ ▀ ▄▀ ▄▀▀  █▀▄▄▀▀▄█  
▄▀▀▄▀█▄▄▀▀ ▀▄  ▀ █ ██████▀ ▀   
▄▄▄▄▄▄▄ █▄▄██▀▄ ▀▀▀██ ▄ █▄▀██  
█ ▄▄▄ █ █▄█▄▀▀█  █▄ █▄▄▄█▀ █▄  
█ ███ █ ▄▄█▀ ▄▀ ▀  ▄▀  ██▄▀▀█  
█▄▄▄▄▄█ █▄█▀▄  ▀██▀███ ▄█  █▄  


02
--> Hardware

- erschienen Ende 2014
- 32-bit RISC CPU @ 80 Mhz
- WiFi 802.11 b/g/n
- ursprünglich gedacht als Zusatzbaustein ('Modem')
- relativ leistungsfähiger Microcontroller
- großes Interesse in der Bastlercommunity
- ohne weitere Hardware programmierbar


03
--> Firmware

- Standard: AT-Firmware - das Modul wird über UART seriell über AT-Befehle angesprochen
- Micropython
- smartJS
- ESP8266 Basic
- Arduino Core
- NodeMCU


04
--> NodeMCU

- basiert auf eLua
- "Glue Code" zwischen Lua Runtime und dem ESP8266 SDK
- Programming Model ähnlich dem von Node.js (asynchronous, event-driven)


05
--> ESP8266 Unboxing und Flashen

# Demo


06
-> Projekt - (1) Grundlagen

- Ziel: IOT-Thing
- Lufttemperatur und -feuchtigkeit messen
- zur Speicherung und Visualisierung der Daten nutzen wir ThingSpeak.com


07
-> Projekt - (2) ThingSpeak-Channel einrichten

# Demo


08
-> Projekt - (3) NodeMCU-Skript schreiben

```lua
wifi_ssid   = "Zweitag-Gast"
wifi_passwd = "sings-shred-spine"
thingspeak_key = "1IFINDH9Q3EFBDGQ"

tmr.alarm(0, 2000, tmr.ALARM_SINGLE, function()
  wifi.setmode(wifi.STATION)
  wifi.sta.config(wifi_ssid, wifi_passwd, 1)

  tmr.alarm(1, 1000, tmr.ALARM_AUTO, function()
    if wifi.sta.getip() == nil then
      print("IP unavaiable, Waiting...")
    else
      tmr.stop(1)
      print("Done, IP is "..wifi.sta.getip())
      send()
    end
  end)
end)

function send()
  tmr.alarm(1, 20000, tmr.ALARM_SEMI, function()
    status, temp, humi, temp_dec, humi_dec = dht.read(5)

    if status == dht.OK then
      url = "http://api.thingspeak.com/update?key="..thingspeak_key.."&field1="..temp.."&field2="..humi
      http.get(url, nil, function(code, body)
        print("Sent data, response code: "..code)
        tmr.start(1)
      end)
    else
      print("Error reading from DHT")
      tmr.start(1)
    end
  end)
end
```


09
-> Projekt - (4) NodeMCU-Skript hochladen

```lua
nodemcu-uploader --start_baud 115200 upload --dofile init.lua
nodemcu-uploader --start_baud 115200 terminal
```


10
-> Frage 1

Wie konkateniert man in Lua Strings?

a) "a" .. "b" .. "c"
b) "a" + "b" + "c"
c) strings.concat("a", "b", "c")


11
-> Frage 2

Für welchen primären Anwendungsfall entwickelte Espressif den ESP8266?

a) als Sensor für Industrie 4.0-Anwendungen
b) als smartes IoT-Device
c) als Kommunikationsgateway für andere Microcontroller


12
-> Frage 3

Wie heißt das Skript, welches der Lua-Interpreter der NodeMCU-Firmware nach
der Initialisierung ausführt?

a) start.lua
b) boot.lua
c) init.lua


13
-> Frage 4

Welches ist ein Akronym für ein ereignisorientiertes Kommunikationsprotokoll
für das Internet der Dinge?

a) Advanced Message Queuing Protocol
b) Message Queue Telemetry Transport
c) Constrained Application Protocol


14
-> Frage 5

Wie groß ist ein MQTT Header?
Tipp:  HTTP Header i.d.R. ~700 Bytes
(Quelle: http://dev.chromium.org/spdy/spdy-whitepaper)

a) 2 Byte
b) 150 Byte
c) variabel


15
-> Frage 6

Welche Messages werden an einen MQTT Client mit der Subscription
"devices/+/temperature/#" geroutet?

a) publish message, "devices/1/sensors/temperature/average"
b) publish message, "devices/nest/temperature/kitchen/average/06-28-16"
c) publish message, "devices/temperature/nest"


16
-> Frage 7

Wer bestimmt in MQTT den "last will"

a) der Message Broker
b) der Client
c) beide


17
-> Frage 8

Bei der Nutzung von MQTT ist die Verschlüsselung von Daten ...

a) ... nicht möglich
b) ... möglich über TLS
c) ... möglich über MQTTS
