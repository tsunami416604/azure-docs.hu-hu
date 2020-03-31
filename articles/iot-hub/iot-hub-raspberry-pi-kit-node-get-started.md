---
title: Raspberry Pi csatlakoztatása az Azure IoT Hubhoz a felhőben (Node.js)
description: Megtudhatja, hogyan állíthatja be és csatlakoztathatja a Raspberry Pi-t az Azure IoT Hubhoz a Raspberry Pi számára, hogy adatokat küldjön az Azure felhőplatformjára ebben az oktatóanyagban.
author: wesmc7777
manager: eliotgra
keywords: az azúrkék raspberry pi, raspberry pi iot hub, raspberry pi adatokat küld a felhőbe, raspberry pi felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 7c32ae73f065aa5cd1d0dabec421d354684fbb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371501"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi csatlakoztatása az Azure IoT Hubhoz (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban először a Raspbiant futtató Raspberry Pi-vel való munka alapjait tanulhatja meg. Ezután megtudhatja, hogyan csatlakoztathatja zökkenőmentesen eszközeit a felhőhöz az [Azure IoT Hub](about-iot-hub.md)használatával. Windows 10 IoT Core minták esetén keresse fel a [Windows fejlesztői központot.](https://www.windowsondevices.com/)

Még nincs készleted? Próbálja [ki a Raspberry Pi online szimulátort.](iot-hub-raspberry-pi-web-simulator-get-started.md) Vagy vesz egy új készlet [itt](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Mit csinálsz

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a Pi számára az IoT hubban.

* Állítsa fel a Raspberry Pi-t.

* Futtasson egy mintaalkalmazást a Pi-n az IoT hubra küldött érzékelőadatok küldéséhez.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT-központ létrehozása és az új eszközkapcsolati karakterlánc beszerezni.

* A Pi csatlakoztatása BME280 érzékelővel.

* Az érzékelőadatok gyűjtése a Pi-n futó mintaalkalmazás sal.

* Az érzékelőadatok küldése az IoT hubra.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Egy Raspberry Pi 2 vagy Raspberry Pi 3 tábla.

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

* Monitor, USB-billentyűzet és a Pi-hez csatlakozó egér.

* Windows vagy Linux rendszerű Mac vagy PC.

* Internet kapcsolat.

* 16 GB-os vagy microSD-kártya.

* USB-SD adapter vagy microSD-kártya az operációs rendszer lemezképének a microSD-kártyára való írásához.

* 5 voltos, 2 amperes tápegység a 180 centis micro USB-kábellel.

A következő elemek nem kötelezőek:

* Egy összeszerelt Adafruit BME280 hőmérséklet-, nyomás- és páratartalom-érzékelő.

* Egy kenyérvágódeszka.

* 6 F/M áthidaló vezeték.

* Egy szórt 10 mm-es LED.

> [!NOTE]
> Ha nem rendelkezik a választható elemekkel, szimulált érzékelőadatokat használhat.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi beállítása

### <a name="install-the-raspbian-operating-system-for-pi"></a>Telepítse a Raspbian operációs rendszert a Pi-hez

Készítse elő a microSD-kártyát a Raspbian kép telepítéséhez.

1. Letöltés Raspbian.

   a. [Raspbian Buster asztali](https://www.raspberrypi.org/downloads/raspbian/) (a .zip fájl).

   b. Bontsa ki a Raspbian lemezképet a számítógép egy mappájába.

2. Telepítse a Raspbian-t a microSD kártyára.

   a. [Töltse le és telepítse az Etcher SD kártya író segédprogram](https://etcher.io/).

   b. Futtassa az Etcher t, és válassza ki az 1.

   c. Válassza ki a microSD-kártya meghajtóját. Lehet, hogy az Etcher már kiválasztotta a megfelelő meghajtót.

   d. Kattintson a Flash gombra a Raspbian microSD-kártyára való telepítéséhez.

   e. A telepítés befejeztével vegye ki a microSD-kártyát a számítógépről. A microSD-kártyát biztonságosan közvetlenül eltávolítani, mert az Etcher a befejezéskor automatikusan kiadja vagy leveszi a microSD-kártyát.

   f. Helyezze be a microSD-kártyát a Pi-be.

### <a name="enable-ssh-and-i2c"></a>SSH és I2C engedélyezése

1. Csatlakoztassa a Pi-t a monitorhoz, a billentyűzethez és az egérhez.

2. Indítsa el a Pi-t, majd `pi` jelentkezzen be `raspberry` a Raspbian-ba felhasználónévként és jelszóként.

3. Kattintson a Raspberry ikonra > > **Raspberry Pi konfigurációja beállításra**. **Preferences**

   ![A Raspbian Preferences menü](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. A **Kapcsolatok** lapon állítsa az **I2C** és **az SSH** **beállítását az Engedélyezés gombra,** majd kattintson **az OK**gombra. Ha nem rendelkezik fizikai érzékelőkkel, és szimulált érzékelőadatokat szeretne használni, ez a lépés nem kötelező.

   ![I2C és SSH engedélyezése Raspberry Pi-n](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Az SSH és az I2C engedélyezéséhez további referenciadokumentumokat találhat [a raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [a Adafruit.com.](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c)

### <a name="connect-the-sensor-to-pi"></a>Csatlakoztassa az érzékelőt a Pi-hez

A kenyérvágódeszkával és az áthidaló vezetékekkel csatlakoztasson egy LED-et és egy BME280-at a Pi-hez az alábbiak szerint. Ha nem rendelkezik az érzékelővel, [hagyja ki ezt a szakaszt.](#connect-pi-to-the-network)

![A Raspberry Pi és az érzékelő csatlakozás](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

A BME280 érzékelő képes a hőmérséklet- és páratartalom-adatok gyűjtésére. A LED villog, amikor a készülék üzenetet küld a felhőbe.

Érzékelőcsapok esetén a következő vezetékeket használja:

| Indítás (érzékelő & LED)     | Vége (board)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| VDD (5G-es tű)             | 3.3V PWR (1. pin)       | Fehér kábel   |
| GND (7G tű)             | GND (6. pin)            | Barna kábel   |
| SDI (10g tű)            | I2C1 SDA (3. tű)       | Piros kábel     |
| SCK (8G tű)             | I2C1 SCL (5. pin)       | Narancssárga kábel  |
| LED VDD (18F tű)        | GPIO 24 (18-as tű)       | Fehér kábel   |
| LED GND (17F tű)        | GND (20-as tű)           | Fekete kábel   |

Kattintson ide a [Raspberry Pi 2 & 3 tűs leképezések](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) megtekintéséhez.

Miután sikeresen csatlakoztatta a BME280-at a Raspberry Pi-hez, az alábbi képhez hasonlóan kell lennie.

![Csatlakoztatott Pi és BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoztatása a hálózathoz

Kapcsolja be a Pi-t a mikro USB-kábel és a tápegység segítségével. Az Ethernet-kábellel csatlakoztassa a Pi-t a vezetékes hálózathoz, vagy kövesse [a Raspberry Pi Foundation utasításait a](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi vezeték nélküli hálózathoz való csatlakoztatásához. Miután a Pi sikeresen csatlakozott a hálózathoz, meg kell jegyezned a [Pi IP-címét.](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)

![Csatlakozás vezetékes hálózathoz](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Győződjön meg arról, hogy a Pi ugyanahhoz a hálózathoz csatlakozik, mint a számítógép. Ha például a számítógép vezeték nélküli hálózathoz csatlakozik, miközben a Pi vezetékes hálózathoz csatlakozik, előfordulhat, hogy a devdisco kimenetben nem jelenik meg az IP-cím.

## <a name="run-a-sample-application-on-pi"></a>Mintaalkalmazás futtatása pi-n

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klónozza a mintaalkalmazást, és telepítse az előfeltételként szolgáló csomagokat

1. Csatlakozzon a Raspberry Pi készülékhez a gazdaszámítógépről az alábbi SSH-ügyfelek egyikével:

   **Windows-felhasználók**

   a. Töltse le és telepítse [a PuTTY](https://www.putty.org/) for Windows rendszert.

   b. Másolja a Pi IP-címét az állomásnév (vagy IP-cím) szakaszba, és válassza az SSH-t kapcsolattípusként.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac és Ubuntu felhasználók**

   Használja a beépített SSH kliensubuntu vagy macOS. Előfordulhat, hogy `ssh pi@<ip address of pi>` futnia kell a Pi SSH-n keresztüli csatlakoztatásához.

   > [!NOTE]
   > Az alapértelmezett felhasználónév, `pi` és `raspberry`a jelszó .

2. Telepítse a Node.js és az NPM programot a Pi-re.

   Először ellenőrizze a Node.js verziót.

   ```bash
   node -v
   ```

   Ha a verzió 10.x-nél alacsonyabb, vagy ha nincs Node.js a Pi készüléken, telepítse a legújabb verziót.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klónozza a mintaalkalmazást.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Telepítse a mintához szükséges összes csomagot. A telepítés magában foglalja az Azure IoT-eszköz SDK, BME280 érzékelő könyvtár, és a Wiring Pi könyvtár.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >A hálózati kapcsolattól függően a telepítési folyamat több percig is eltarthat.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Nyissa meg a konfigurációs fájlt a következő parancsok futtatásával:

   ```bash
   nano config.json
   ```

   ![Konfigurációs fájl](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   A fájlban két konfigurálható elem található. Az első `interval`a , amely meghatározza a felhőbe küldött üzenetek közötti időintervallumot (ezredmásodpercben). A második `simulatedData`a , amely egy logikai érték, hogy szimulált érzékelőadatokat használjon-e vagy sem.

   Ha **nem rendelkezik az érzékelővel,** állítsa be az `simulatedData` értéket, `true` hogy a mintaalkalmazás szimulált érzékelőadatokat hozzon létre és használjon.

   *Megjegyzés: Az oktatóanyagban használt i2c-cím alapértelmezés szerint 0x77. A konfigurációtól függően 0x76 is lehet: ha i2c hibát észlel, próbálja meg módosítani az értéket 118-ra, és nézze meg, hogy ez jobban működik-e. Ha meg szeretné tekinteni, hogy `sudo i2cdetect -y 1` az érzékelő milyen címet használ, futtasson egy héjban a raspberry pi*

2. A Control-O > Enter > Control-X beírásával mentse és lépjen ki.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

Futtassa a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Győződjön meg arról, hogy másolja be az eszköz kapcsolati karakterláncát az egyidézőjelek közé.

A következő kimenetnek kell megjelennie, amely tartalmazza az érzékelők adatait és az IoT Hubnak üzeneteket.

![Kimenet – A Raspberry Pi-ról az IoT Hubba küldött érzékelői adatok](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>A központ által fogadott üzenetek elolvasása

Az IoT hub által az eszközről fogadott üzenetek figyelésének egyik módja az Azure IoT-eszközök a Visual Studio-kódhoz használata. További információ: [Az Azure IoT-eszközök használata a Visual Studio-kódhoz az eszköz és az IoT Hub közötti üzenetek küldéséhez és fogadásához.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Az eszköz által küldött adatok feldolgozásának további módjaiért folytassa a következő szakaszsal.

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazást futtatott az érzékelőadatok gyűjtésére és az IoT-központba való elküldésre.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
