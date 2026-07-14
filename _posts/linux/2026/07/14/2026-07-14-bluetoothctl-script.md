---
layout: post
date: 2026-07-14
categories: [Linux, dayliusage]
tags: [Linux]
title: Skrypt bluetoothctl

---
Komputera używam tylko prawą ręką. Lewa jest nie do końca pod moją kontrolą (drobna niepełnosprawność). 
Z tego powodu przełączanie się pomiędzy myszką, a klawiaturą kilkadziesiąt razy dziennie potrafi trochę zmęczyć. Aby ograniczyć to "machanie", na mój window manager wybrałem i3wm. Tailing Window ma jeszcze jedną zaletę, jaką jest możliwość konfiguracji minimalistycznego wyglądu, który po prostu lubię. 

I3wm ma też jedną drobną wadę. Otóż brak obramowania okien (a tym samym brak możliwości kliknięcia 'x', aby zamknąć) wymusza na mnie używanie skrótów klawiszowych w najmniej oczekiwanych momentach. 
Jednym z takich momentów jest sytuacja, kiedy moje urządzenie Bluetooth łączy się z komputerem. Powstaje wtedy natywne okno  bluemana. Aby je zamknąć muszę najechać na nie myszą i wcisnąć Ctrl+Shift+Q. Czyli jest to dokładnie ta sytuacja, która powoduje mój dyskomfort.  
![connected device](./images/2026-07-14_10-24.png)

Za chwilę do tego wrócę...

Kolejna rzecz to kwestia samego podłączania urządzenia. W większości orzypadków, po sparowaniu, urządzenie podłącza się samo do komputera i pozostaje mi zamknięcie nieszczęsnego okienka. Ale czasami (np. po aktualizacjach kernela) coś pójdzie niezgodnie z oczekiwaniami.
Albo najzwyczajniej na świecie nie chce mi się klikać ;)

Stąd pomysł na prosty skrypt, który restartuje urządzenie bluethoot na moim komputerze; wyświetla adresy MAC sparowanych urządzeń i czeka na input, z którym urządzeniem się połączyć.

Na samym końcu ubija nieszczęsne okienko informacyjne.

```bash
#!/bin/bash

# --- Config ---

DEVICE_MAC=<Adres MAC mojego Controllera>

echo "rfkill unblock & restart bluetooth"
sudo rfkill unblock bluetooth
sudo systemctl restart bluetooth
sleep 3

echo "controller turning on..."
bluetoothctl power on
sleep 2

echo "show already paired devices..."
bluetoothctl <<EOF
agent on
default-agent
devices
EOF
sleep 5 

echo -n "Connect to..."
read REMOTE_MAC
bluetoothctl connect $REMOTE_MAC

# --- Wait 2 second & Exit info window ---
sleep 2
xdotool windowkill $(xdotool search --any "blueman" | tail -n 1) 2>/dev/null
```
Ta ostatnia linijka znajduje ostatnie okno na stosie X11 i je zamyka.

Po głowie chodzi mi rozbudowa skryptu, na przykład o wybór opcji, czy chcę tylko połączyć się ze sparowanym urządzeniem, czy może dodać nowe urządzenie (bluetoothctl pair && bluetoothctl trust). 
