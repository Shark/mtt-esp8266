```lua
docker run -it --rm --privileged -v /dev/ttyUSB0:/dev/ttyUSB0 sh4rk/esp8266-tools

esptool.py write_flash 0x00000 boot_v1.2.bin 0x01000 at/512+512/user1.1024.new.2.bin
nodemcu-uploader --start_baud 115200 terminal
AT+GMR
AT+CWMODE=1
AT+CWLAP

esptool.py write_flash 0x00000 nodemcu_float_sh4rk_2016-06-25.bin
nodemcu-uploader --start_baud 115200 terminal

function listap(t)
  for k,v in pairs(t) do
    print(k.." : "..v)
  end
end
wifi.sta.getap(listap)

wifi.setmode(wifi.STATION)
wifi.sta.config("Zweitag-Gast", "sings-shred-spine", 1)
= wifi.sta.getip()

http.get("http://icanhazip.com", nil, function(code, data)
  print(code)
  print(data)
end)
```
