## <a name="prepare-your-raspberry-pi"></a>Készítse elő a Raspberry Pi

### <a name="install-raspbian"></a>Raspbian telepítése

Ha ez az első alkalommal használja a málna Pi, telepítendő a Raspbian operációs rendszer NOOBS használja a csomag tartalmazza az SD-kártyára. A [málna Pi szoftver útmutató] [ lnk-install-raspbian] ismerteti, hogyan lehet a málna Pi operációs rendszer telepítéséhez. Ez az oktatóanyag feltételezi, hogy telepítette a Raspbian operációs rendszer a málna Pi.

> [!NOTE]
> Az SD-kártya szerepel a [a Microsoft Azure IoT Starter Kit málna Pi 3] [ lnk-starter-kits] már rendelkezik telepített NOOBS. Indítsa el a málna Pi a kártyáról, és a Raspbian operációs rendszer telepítése.

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

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/