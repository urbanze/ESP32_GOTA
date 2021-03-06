# ESP32 Ultimate OTA (TCP)
ESP32 OTA over many forms!\
Crypted functions use AES-256 CBC.

WiFi library: [WiFi](https://github.com/urbanze/esp32-wifi)

## TCP Performance
* Without Flash write (just TCP) and crypt OFF, can go up to 11.3Mb/s.
* Without Flash write (just TCP) and crypt ON, can go up to 7.3Mb/s.
* With Flash write, can go up to 450Kb/s.

## How it works?
* Basically, OTA TCP get file (bytes) sent to ESP32 and write in OTA partition.
* .DOWNLOAD() will connect to an external TCP server and write all new incoming bytes to OTA partition.
* .UPLOAD() will host TCP server, wait new client and write all new incoming bytes to OTA partition.
* This library will write **ALL BYTES** received. After start, your external software can't send any byte that are not from the binary file.
* If you use this library in separate task, stack of 4096B should be enough.

## Simple DOWNLOAD OTA TCP (Crypto OFF)
Download file from external TCP server.
```
WF wifi;
OTA_TCP ota;
wifi.sta_connect("wifi", "1234567890"); //Connect in external WiFi.

//Download OTA hosted in '192.168.4.2:18000'
ota.download("192.168.4.2", 18000);
```

You can use a simple Python script to send binary file when any client connects to server.
![image](/docs/tcp_python.png)

## Simple DOWNLOAD OTA TCP (Crypto ON)
Insert your desired key. Download file from external TCP server.
```
WF wifi;
wifi.sta_connect("wifi", "1234567890"); //Connect in external WiFi.


OTA_TCP ota;

//Set AES-256-CBC KEY and initial IV.
ota.crypto("12345678901234567890123456789012", "0123456789012345");

//Download OTA hosted in '192.168.4.2:18000'
ota.download("192.168.4.2", 18000);
```

## Simple UPLOAD OTA TCP (Crypto OFF)
Wait upload file from remote client on port 18000.
```
WF wifi;
wifi.sta_connect("wifi", "1234567890"); //Connect in external WiFi.

OTA_TCP ota;
while (1)
{
	ota.upload(18000); //Wait client connection (up to 1sec) and read bytes sent by client in port 18000.
}
```
You can send file to ESP32 with netcat. This command send 'esp32.bin' to ESP32 in IP '192.168.9.114:18000'
![image2](/docs/tcp_netcat.png)
