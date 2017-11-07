## <a name="prepare-your-raspberry-pi"></a>Készítse elő a Raspberry Pi

### <a name="install-raspbian"></a>Raspbian telepítése

Ha ez az első alkalommal használja a málna Pi, telepítendő a Raspbian operációs rendszer NOOBS használja a csomag tartalmazza az SD-kártyára. A [málna Pi szoftver útmutató] [ lnk-install-raspbian] ismerteti, hogyan lehet a málna Pi operációs rendszer telepítéséhez. Ez az oktatóanyag feltételezi, hogy telepítette a Raspbian operációs rendszer a málna Pi.

> [!NOTE]
> Az SD-kártya szerepel a [a Microsoft Azure IoT Starter Kit málna Pi 3] [ lnk-starter-kits] már rendelkezik telepített NOOBS. Indítsa el a málna Pi a kártyáról, és a Raspbian operációs rendszer telepítése.

### <a name="set-up-the-hardware"></a>A hardver beállítása

Ez az oktatóanyag használja a BME280 érzékelő szerepel a [a Microsoft Azure IoT Starter Kit málna Pi 3] [ lnk-starter-kits] telemetriai adatok létrehozásához. Azt jelzi, ha a málna Pi dolgozza fel a megoldás irányítópultról metódushívási LED használ.

A be táblán összetevők a következők:

- Piros LED
- 220 ohmos ellenállás (piros, a piros, a barna.)
- BME280 érzékelő

Az alábbi ábrán látható, a hardver összekapcsolása:

![Málna Pi hardver beállítása][img-connection-diagram]

A következő táblázat összefoglalja a breadboard az összetevőinek a málna Pi közötti kapcsolatok:

| Raspberry Pi            | Breadboard             |Szín         |
| ----------------------- | ---------------------- | ------------- |
| GND (14 PIN-kód)            | LED - nálói PIN-kódot (18A)      | Lila          |
| GPCLK0 (PIN-kód 7)          | Ellenállás [25]         | Narancssárga          |
| SPI_CE0 (PIN-kód 24)        | CS (39A)               | Kék          |
| SPI_SCLK (PIN-kód 23)       | SCK (36A)              | Sárga        |
| SPI_MISO (21 PIN-kód)       | SDO (37A)              | Fehér         |
| SPI_MOSI (PIN-kód 19)       | SDI (38A)              | Zöld         |
| GND (PIN-kód 6)             | GND (35A)              | Fekete         |
| 3.3 V (1. Pin)           | 3Vo (34A)              | Piros           |

A hardver-telepítés befejezéséhez kell:

- Csatlakoztassa a málna Pi Kit része tápegység.
- A málna Pi csatlakoznak a hálózathoz, a csomag tartalmazza az Ethernet-kábelen keresztül. Másik lehetőségként beállíthatja [vezeték nélküli kapcsolat] [ lnk-pi-wireless] a málna pi.

Ezzel befejezte a málna pi a hardverbeállításokat.

### <a name="sign-in-and-access-the-terminal"></a>Bejelentkezhet és elérheti a Terminálszolgáltatások

A Terminálszolgáltatások tesztkörnyezetben, a málna Pi eléréséhez két lehetőség közül választhat:

- Ha a figyelő a málna Pi csatlakoztatott és a billentyűzeten, használhatja a Raspbian grafikus felhasználói felület egy terminálablakot eléréséhez.

- A parancssorban meg az SSH használata a asztali gépen málna Pi eléréséhez.

#### <a name="use-a-terminal-window-in-the-gui"></a>A grafikus felhasználói felületen terminálablakot használni

Az alapértelmezett Raspbian a hitelesítő adatok felhasználónév **pi** és a jelszó **málna**. A tálcán a grafikus felhasználói felületén, elindíthatja a **Terminálszolgáltatások** segédprogram használatával egy figyelőt a ikon.

#### <a name="sign-in-with-ssh"></a>SSH bejelentkezés

SSH használható parancssori hozzáférést a málna Pi. A cikk [SSH (Secure Shell)] [ lnk-pi-ssh] útmutatás a málna Pi SSH konfigurálása, valamint csatlakozhat a [Windows] [ lnk-ssh-windows] vagy [ Linux és Mac OS][lnk-ssh-linux].

Jelentkezzen be a felhasználónevet **pi** és a jelszó **málna**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Választható lehetőség: A málna Pi a mappa megosztása

Ha szükséges érdemes lehet a málna Pi a mappa megosztása az asztali környezetet. A mappa megosztása lehetővé teszi az előnyben részesített asztali szövegszerkesztővel (például [Visual Studio Code](https://code.visualstudio.com/) vagy [Sublime Text](http://www.sublimetext.com/)) a málna Pi használata helyett a fájlok szerkesztésére `nano` vagy `vi`.

A Windows mappa megosztásához a málna Pi Samba kiszolgáló konfigurálása. Másik megoldásként használhatja a beépített [SFTP](https://www.raspberrypi.org/documentation/remote-access/) kiszolgáló az asztalon egy SFTP-ügyféllel.

### <a name="enable-spi"></a>SPI engedélyezése

A mintaalkalmazás futtatása előtt engedélyeznie kell a soros perifériák Interface (SPI) buszhoz a málna Pi. A málna Pi a SPI bus keresztül kommunikál a BME280 érzékelő eszközzel. Az alábbi parancs segítségével módosítsa a konfigurációs fájlt:

```sh
sudo nano /boot/config.txt
```

Keresse meg a sor:

`#dtparam=spi=on`

- Állítsa vissza a sort, törölje a `#` elején.
- Menti a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).
- Ahhoz, hogy SPI, indítsa újra a málna Pi. Újraindítás bontja a kapcsolatot a Terminálszolgáltatások, be kell jelentkeznie a újra Ha a málna Pi újraindul:

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-v1-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/