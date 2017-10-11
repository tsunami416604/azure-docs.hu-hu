---
title: "Intel Edison felhőbe (Node.js) - Intel Edison csatlakozzon az Azure IoT Hub |} Microsoft Docs"
description: "Megtudhatja, hogyan kell beállítania, és Azure IoT-központ Intel Edison adatokat küldeni az Azure felhőalapú platform ebben az oktatóanyagban Intel Edison csatlakozni."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az Azure iot intel edison, intel edison iot-központot, intel edison adatküldés intel felhőbe edison felhőbe"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Intel Edison csatlakozni az Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban akkor először tanulás alapjainak Intel Edison használata. Majd megtudhatja, hogyan kapcsolódhat zökkenőmentesen az eszközök a felhőbe [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Még nem rendelkezik egy csomagot? Start [Itt](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Mit

* A telepítő Intel Edison és és Groove-modulok.
* Létrehoz egy IoT-központot.
* Eszköz regisztrálása az Edison az IoT hub a.
* Futtassa a mintaalkalmazást érzékelő adatokat küldeni az IoT hub Edison.

Az IoT-központ az Ön által létrehozott Intel Edison csatlakozni. Majd futtassa a mintaalkalmazást a Edison hőmérséklet és a páratartalom adatokat gyűjteni a Groove hőmérséklet-érzékelő. Végezetül az érzékelő adatokat küldött az IoT hub.

## <a name="what-you-learn"></a>Ismertetett témák

* Megtudhatja, hogyan hozzon létre egy Azure IoT-központot, és az új eszköz kapcsolati karakterláncot.
* Hogyan Edison kapcsolódni egy Groove hőmérséklet-érzékelő.
* Megtudhatja, hogyan futtatja a mintaalkalmazás Edison érzékelő adatok gyűjtéséért felelős ügyfélfeladatot.
* Hogyan érzékelő adatokat küldeni az IoT hub.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Az Intel Edison Tanács
* Arduino bővítése tábla
* Aktív Azure-előfizetés. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
* A Mac vagy a Windows vagy Linux rendszerű számítógép.
* Az internethez.
* A típus egy USB-kábel Micro B
* A közvetlen aktuális (DC) tápegység. A tápegység kell tekinthető meg az alábbiak szerint:
  - 7-15V TARTOMÁNYVEZÉRLŐ
  - Legalább 1500mA
  - A Központ/belső PIN-kódot kell lennie a pozitív sarkpontot az energiaellátás

A következő elemek nem kötelező:

* Groove alap pajzs V2
* Groove - hőmérséklet-érzékelő
* Groove kábel
* Bármely Oszlopelválasztó sávok vagy csavart a csomagban, beleértve a két csavart vizsgálókocsihoz a bővítés board és négy csavart és műanyag térköztartók modul a része.

> [!NOTE] 
Ezek az elemek nem kötelező, mert a kód a minta támogatási szimulált érzékelőadatait.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>A telepítő Intel Edison

### <a name="assemble-your-board"></a>A tábla összeállítása

Ez a szakasz az Intel® Edison modul csatlakoztatni a bővítés board lépéseket tartalmazza.

1. A bővítés üzenőfalon, a bővítés táblán csavart, a modul a lyuk sorba állítása az Intel® Edison modul, a fehér vázlatban helyezze el.

2. Nyomja le a modul a szavakat alatt `What will you make?` csak úgy érzi, hogy a beépülő modult.

   ![indítópanel 2 összeállítása](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. A bővítés board modul védelmére használja a két hexadecimális nuts (a csomagban található).

   ![indítópanel 3 összeállítása](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. Helyezze be elment egyet a négy sarok lyuk a bővítés táblán. Csavarás és szigoríthatja a csavart alakzatot fehér műanyag térköztartók egyikét.

   ![indítópanel 4 összeállítása](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. Ismételje meg a másik három sarok térköztartók.

   ![indítópanel 5 összeállítása](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

Most már a üzenőfalon kész.

   ![üzenőfalon kész](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Csatlakoztassa a Groove talál pajzs és a hőmérséklet-érzékelő

1. Jelölje be a tábla a Groove talál pajzs. Győződjön meg arról, hogy a tábla szorosan csatlakoztatott összes PIN-kód.
   
   ![Groove alap pajzs ikon](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Groove kábellel csatlakoztassa a Groove talál pajzs alakzatot Groove hőmérséklet-érzékelő **A0** port.

   ![Csatlakozás hőmérséklet-érzékelő](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Edison és érzékelő kapcsolat](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

Most már készen áll az érzékelő.

### <a name="power-up-edison"></a>Energiagazdálkodási Edison mentése

1. Csatlakoztassa a tápegység.

   ![Beépülő modul tápegység](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. A zöld LED-jét (DS1 feliratú a táblán Arduino * bővítése) kell bonyolít le, és megvilágítottnak maradnak.

3. Várjon egy percet a kártya a rendszerindítás befejezéséhez.

   > [!NOTE]
   > Ha nem rendelkezik a tartományvezérlő tápegység, továbbra is a USB-porton keresztül board power. Lásd: `Connect Edison to your computer` című szakaszban talál információt. A tábla, ilyen módon működtetéséhez azt eredményezheti, hogy a tábláról előre nem látható viselkedéshez különösen akkor, ha a Wi-Fi használatával, vagy befolyásoló tényezők motorok.

### <a name="connect-edison-to-your-computer"></a>Edison kapcsolódni a számítógéphez

1. Váltás a két micro USB-porttal felé mikrokapcsoló le, hogy Edison eszköz módban van. Az eszköz és a gazdagép üzemmód közötti különbségeket, tekintse át [Itt](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Váltás a mikrokapcsoló le](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. A micro USB-kábellel csatlakoztassa a felső micro USB-porttal.

   ![Felső micro USB-port](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. Másik végén USB-kábellel csatlakoztassa a számítógépet.

   ![Számítógép USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. Tudni fogják, hogy a tábla teljes inicializálását, amikor a számítógép csatlakoztat egy új meghajtót (hasonlóan egy SD-kártya beszúrása a számítógép).

## <a name="download-and-run-the-configuration-tool"></a>A konfigurációs eszköz letöltése és futtatása
A legfrissebb konfigurációs eszköz az beszerzése [erre a hivatkozásra](https://software.intel.com/en-us/iot/hardware/edison/downloads) tartozó a `Installers` fejléc. Az eszköz hajtható végre, és kövesse a képernyőn megjelenő utasításokat, amennyiben szükséges Tovább gombra kattint

### <a name="flash-firmware"></a>Belső vezérlőprogram Flash
1. Az a `Set up options` kattintson `Flash Firmware`.
2. Válassza ki a lemezképet flash alakzatot a tábla a következő módszerek valamelyikével:
   - Töltse le, és a tábla a legújabb belső vezérlőprogram lemezképpel érhetők el az Intel flash, jelölje be `Download the latest image version xxxx`.
   - A tábla már mentette a számítógépen lemezképhez flash, jelölje be `Select the local image`. Keresse meg és jelölje ki a flash a kártyához kívánt lemezképet.
3. A telepítő eszköz megkísérli a tábla flash. A teljes villogó folyamat akár 10 percet is igénybe vehet.

### <a name="set-password"></a>Jelszó beállítása
1. Az a `Set up options` kattintson `Enable Security`.
2. Az Intel® Edison board egyéni nevet állíthatja be. Ez nem kötelező.
3. Adja meg a tábla jelszavát, majd kattintson az `Set password`.
4. A jelszót, amely később üzemszünetének.

### <a name="connect-wi-fi"></a>Wi-Fi csatlakozás
1. Az a `Set up options` kattintson `Connect Wi-Fi`. Várjon, amíg legfeljebb egy percig a számítógép elérhető Wi-Fi hálózatok keres.
2. Az a `Detected Networks` legördülő listára, válassza ki a hálózaton.
3. Az a `Security` legördülő listára, válassza ki a hálózati biztonság típusa.
4. Adja meg a felhasználónevet és jelszót információkat, majd kattintson az `Configure Wi-Fi`.
5. Az IP-cím, amely később üzemszünetének.

> [!NOTE]
> Győződjön meg arról, hogy Edison és a számítógép ugyanahhoz a hálózathoz csatlakozik. A számítógép csatlakozik a Edison az IP-cím használatával.

   ![Csatlakozás hőmérséklet-érzékelő](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

Gratulálunk! Sikeresen konfigurálta az Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Futtassa a mintaalkalmazást az Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Az Azure IoT eszköz SDK előkészítése

1. A számítógép a következő SSH-ügyfél használatával az Intel Edison csatlakozni. Az IP-cím származik-e a konfigurációs eszközt, és a jelszót, az egy adott eszköz a beállított.
    - [A puTTY](http://www.putty.org/) Windows.
    - A beépített SSH-ügyfél Ubuntu vagy macOS.

2. Klónozza a mintaalkalmazást ügyfél az eszközre. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. Keresse meg a tárház mappa összes csomag telepítése a következő parancsot, közegészségre veszélyesnek percig is eltarthat.
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>Konfigurálja és futtassa a mintaalkalmazást

1. Nyissa meg a konfigurációs fájlban a következő parancsok futtatásával:

   ```bash
   nano config.json
   ```

   ![A konfigurációs fájl](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   Két makrók van ebben a fájlban configurate is. Az első egy `INTERVAL`, amely megadja, hogy két felhőbe küldött üzeneteket közötti időközt. A második érték `simulatedData`, vagyis az, hogy szimulált érzékelőadatait vagy nem logikai értéket.

   Ha Ön **nem rendelkezik az érzékelő**, beállíthatja a `simulatedData` egy érték `true` a minta kérelem létrehozása és használata a szimulált érzékelőadatait.

1. Mentse és zárja be a vezérlő-O billentyűkombináció lenyomásával > Enter > CTRL-X.


1. Futtassa a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Győződjön meg arról, hogy Ön-e beillesztési az eszköz kapcsolati karakterláncát azokat a szimpla idézőjelben.

A következő kimeneti bemutatja az érzékelő adatokat és az IoT hub küldött üzenetek kell megjelennie.

![Kimeneti - érzékelő adatokat küldött az Intel Edison az IoT hubhoz](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>Következő lépések

Egy mintaalkalmazás érzékelő adatokat gyűjteni, és küldje el az IoT hub futtatását.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
