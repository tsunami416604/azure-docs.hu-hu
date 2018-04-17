---
title: A felhő (Node.js) - málna Pi csatlakozzon az Azure IoT Hub Raspberry Pi |} Microsoft Docs
description: Megtudhatja, hogyan kell beállítania, és Azure IoT-központ málna Pi adatokat küldeni az Azure felhőalapú platform ebben az oktatóanyagban málna Pi csatlakozni.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: az Azure iot raspberry pi, raspberry pi iot-központ, raspberry pi adatokat küldött a felhőben, raspberry pi felhőbe
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e7db2f78a5c1a942f64a2c0a40068fffe90749d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi csatlakozni az Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban akkor először tanulás alapjainak málna Pi Raspbian futtató használata. Majd megtudhatja, hogyan kapcsolódhat zökkenőmentesen az eszközök a felhőbe [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Windows 10 IoT minta, látogasson el a [Windows fejlesztői központ](http://www.windowsondevices.com/).

Még nem rendelkezik egy csomagot? Próbálja [málna Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md). Vagy egy új csomag vásárlása [Itt](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Mit

* Létrehoz egy IoT-központot.
* Eszköz regisztrálása az IoT hub a pi.
* Málna Pi beállítása.
* Futtassa a mintaalkalmazást érzékelő adatokat küldeni az IoT hub Pi.

## <a name="what-you-learn"></a>Ismertetett témák

* Megtudhatja, hogyan hozzon létre egy Azure IoT-központot, és az új eszköz kapcsolati karakterláncot.
* Hogyan BME280 érzékelő Pi kapcsolódni.
* Megtudhatja, hogyan futtatja a mintaalkalmazás Pi érzékelő adatok gyűjtéséért felelős ügyfélfeladatot.
* Hogyan érzékelő adatokat küldeni az IoT hub.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Olyan málna Pi 2 vagy málna Pi 3 kártya.
* Aktív Azure-előfizetés. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
* Egy figyelő, egy USB-billentyűzet és egér, amely a-pi tartományban.
* A Mac vagy Windows vagy Linux rendszerű számítógépen.
* Az internethez.
* Egy 16 GB-os vagy újabb microSD-kártyán.
* Egy USB-SD adapter vagy microSD-kártyán írása a microSD-kártyán operációsrendszer-képet.
* Egy 5-volt 2-amp tápegység a 6-mértékű kiszolgálóhasználat micro USB-kábellel.

A következő elemek nem kötelező:

* Az összeállított Adafruit BME280 hőmérséklet, a terhelés, és a páratartalom érzékelő.
* Egy breadboard.
* 6/aljzat átkötés fenyegetéseknek.
* Egy szórt 10 mm LED-jét.


> [!NOTE] 
Ha még nem rendelkezik a választható elemek, a szimulált érzékelőadatait is használhatja.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Málna Pi beállítása

### <a name="install-the-raspbian-operating-system-for-pi"></a>A Pi a Raspbian operációs rendszer telepítése

Készítse elő a microSD-kártyán Raspbian kép telepítéséhez.

1. Töltse le a Raspbian.
   1. [Töltse le a Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (a .zip-fájlt).

   > [!WARNING]
   > Használja a fenti hivatkozás letöltése `raspbian-2017-07-5` zip-lemezképet. Raspbian képek legújabb verzióját Vezetékezést-Pi csomóponttal, ami sikertelen a következő lépésekben néhány ismert problémákkal rendelkezik.
   1. Bontsa ki a Raspbian lemezképet a számítógép egyik mappájába.

1. A microSD-kártyán Raspbian telepítése.
   1. [Töltse le és telepítse a Etcher SD-kártya író segédprogram](https://etcher.io/).
   1. Futtassa a Etcher, és az 1. lépésben válassza ki a kibontott Raspbian kép.
   1. Jelölje ki a microSD-kártyát meghajtót. Etcher előfordulhat, hogy már a kijelölt a megfelelő meghajtó.
   1. Kattintson a microSD-kártyán Raspbian telepítése Flash.
   1. Eltávolítja a számítógépről a microSD-kártyán, ha a telepítés befejeződött. Biztonságos a microSD-kártyán közvetlenül eltávolítani, mert Etcher automatikusan kiadása vagy leválasztja a microSD-kártyán befejezése után is.
   1. A microSD-kártyán beszúrása Pi.

### <a name="enable-ssh-and-i2c"></a>SSH- és I2C engedélyezése

1. A Pi csatlakozni a monitor, billentyűzet és egér. 
1. Indítsa el a Pi és majd jelentkezzen be Raspbian `pi` felhasználónevet és `raspberry` a jelszót.
1. Kattintson a Raspberry ikonra > **beállítások** > **málna Pi konfigurációs**.

   ![A Raspbian beállítások menü](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. Az a **felületek** lapon **I2C** és **SSH** való **engedélyezése**, és kattintson a **OK**. Ha nem rendelkezik a fizikai érzékelők és szimulált érzékelőadatait használni szeretne, ez a lépés nem kötelező megadni.

   ![I2C és a Raspberry Pi SSH engedélyezése](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH- és I2C engedélyezéséhez található további referencia dokumentumok [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Csatlakozás az érzékelő Pi

Használják a breadboard és átkötés LED és egy BME280 Pi kell kapcsolódniuk az alábbiak szerint. Ha még nem rendelkezik az érzékelő [hagyja ki ezt a szakaszt](#connect-pi-to-the-network).

![A Pi málna és érzékelő kapcsolat](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

A BME280 érzékelő gyűjthet a hőmérséklet és a páratartalom adatokat. Az eszköz a felhőre üzenetet küld a LED villogjon. 

PIN-kód érzékelő használja a következő vezetékezést:

| Indítsa el a (érzékelő & LED)     | Záró (tábla)            | Kábel szín   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kód 5 g.)             | 3.3V PWR (1. Pin)       | A fehér kábel   |
| GND (7G PIN-kód)             | GND (PIN-kód 6)            | Barna kábel   |
| SDI (PIN-kód 10G)            | I2C1 SDA (3 PIN-kód)       | Piros kábel     |
| SCK (8G PIN-kód)             | I2C1 SCL (PIN-kód 5)       | Narancssárga kábel  |
| LED VDD (PIN-kód 18F)        | GPIO 24 (PIN-kód 18)       | A fehér kábel   |
| LED GND (PIN-kód 17F)        | GND (PIN-kód 20)           | Fekete kábel   |

Megjelenítéséhez kattintson ide [málna Pi 2. és 3 PIN-kód hozzárendelések](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) hivatkozhat.

Miután sikeresen csatlakozott a málna Pi BME280, meg kell például a kép alatt.

![Csatlakoztatott Pi és BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoznak a hálózathoz

Kapcsolja be a Pi a micro USB-kábelen és a tápegység. Az Ethernet-kábel segítségével Pi csatlakozni a vezetékes hálózatra, vagy hajtsa végre a [málna Pi alapját utasításainak](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi a vezeték nélküli hálózathoz való kapcsolódáshoz. Miután a Pi sikeresen csatlakozott a hálózathoz, azt kell jegyezze fel az a [IP-címet a pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Vezetékes hálózathoz csatlakoznak](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Győződjön meg arról, hogy a Pi és a számítógép ugyanahhoz a hálózathoz csatlakozik. Például ha a számítógép vezeték nélküli hálózathoz Pi egy vezetékes hálózathoz van csatlakoztatva, előfordulhat, hogy nem látja az IP-cím devdisco kimenet.

## <a name="run-a-sample-application-on-pi"></a>A Pi mintaalkalmazás futtatása

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klónozza a mintaalkalmazást, és az előfeltételként szükséges csomagok telepítése

1. A számítógép csatlakoztatása a málna Pi valamelyik, a következő SSH-ügyfelek:
   
   **Windows-felhasználók**
   1. Töltse le és telepítse [PuTTY](http://www.putty.org/) Windows. 
   1. Az IP-cím a tartományban a gazdagép nevét (vagy IP-cím) szakaszában másolja, és válassza ki az SSH a kapcsolattípus.
   
   ![A puTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac és Ubuntu felhasználók**
   
   A beépített SSH-ügyfél Ubuntu vagy macOS használja. Előfordulhat, hogy futtatásához szükséges `ssh pi@<ip address of pi>` Pi SSH-kapcsolaton keresztül csatlakozni.
   > [!NOTE] 
   Az alapértelmezett felhasználónév az `pi` és a jelszó `raspberry`.

1. A Pi Node.js és NPM telepíthető.
   
   Először ellenőrizze a Node.js-verzió. 
   
   ```bash
   node -v
   ```

   4.x-nél korábbi verzió esetén, vagy ha a Pi nem Node.js, telepítse a legújabb verziót.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Klónozza a mintaalkalmazást.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. A minta az összes csomagot telepítse. A rendszer az Azure IoT-eszközök SDK, BME280 érzékelő, és huzalozási Pi függvénytárak is.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   A telepítési folyamat attól függően, hogy a hálózati kapcsolat több percig is eltarthat.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Nyissa meg a konfigurációs fájlban a következő parancsok futtatásával:

   ```bash
   nano config.json
   ```

   ![Konfigurációs fájl](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Ezt a fájlt is beállíthat két elemek szerepelnek. Az első egy `interval`, amely megadja, hogy a (ezredmásodpercben) közötti időközt a felhőbe küldött üzeneteket. A második érték van `simulatedData`, vagyis az, hogy szimulált érzékelőadatait vagy nem logikai értéket.

   Ha Ön **nem rendelkezik az érzékelő**, beállíthatja a `simulatedData` egy érték `true` a minta kérelem létrehozása és használata a szimulált érzékelőadatait.

1. Mentse és zárja be, írja be a vezérlő-O > Enter > CTRL-X.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

Futtassa a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Győződjön meg arról, hogy Ön-e beillesztési az eszköz kapcsolati karakterláncát azokat a szimpla idézőjelben.


A következő kimeneti bemutatja az érzékelő adatokat és az IoT hub küldött üzenetek kell megjelennie.

![Kimeneti - érzékelő adatokat küld az IoT hub málna Pi](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelő adatokat gyűjteni, és küldje el az IoT hub futtatását. Az IoT hub a málna Pi küldött üzeneteket, és üzeneteket küldhet a parancssori felületen málna Pi olvassa el a [kezelése felhő eszközt az IOT hubbal-explorer oktatóanyag üzenetküldési](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
