---
title: Intel Edison felhőbe (C) – Intel Edison csatlakoztatása Azure IoT hubra |} A Microsoft Docs
description: Megtudhatja, hogyan és beállítása és kapcsolódás Intel Edison Intel Edison adatokat küldeni az Azure felhőalapú platformján ebben az oktatóanyagban az Azure IoT hubra.
author: rangv
manager: ''
keywords: az Azure iot intel edison, intel edison iot hub, az intel edison küldje az adatokat a felhőbe, az intel edison a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2efea8054320323df0e0eb603a20a5773d03cad8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676621"
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Intel Edison csatlakozhat az Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ez az oktatóanyag első lépésként, tanulás az Intel Edison való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe az eszközök [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Még nem rendelkezik egy csomagot? Indítsa el [Itt](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>TEENDŐ

* A telepítő Intel Edison és és a Groove-modulok.
* Hozzon létre egy IoT hubot.
* Eszköz regisztrálása az IoT hub for Edison.
* Egy mintaalkalmazás futtatása a Edison érzékelőktől kapott adatok küldése az IoT hubnak.

Intel Edison csatlakozni az IoT hub által létrehozott. Ezután egy mintaalkalmazás futtatunk Edison hőmérséklettel és páratartalommal kapcsolatos adatok gyűjtését egy Groove hőmérséklet-érzékelő. Végül az érzékelő adatokat küld az IoT hubnak.

## <a name="what-you-learn"></a>Ismertetett témák

* Útmutató az Azure IoT hub létrehozása és az új eszköz kapcsolati karakterláncának beszerzése.
* Hogyan Edison összekapcsolása egy Groove hőmérséklet-érzékelő.
* Hogyan gyűjtheti az érzékelőktől kapott adatok egy mintaalkalmazás Edison történő futtatásával.
* Hogyan küldhet az IoT hub érzékelői adatokat.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Intel Edison tábla
* Arduino bővítése tábla
* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.
* Mac vagy Windows vagy Linux rendszert futtató számítógép.
* Internetkapcsolat.
* A típus egy USB-kábelen keresztül a Micro B
* A közvetlen jelenlegi (DC) tápegység. A tápegység minősített módon kell lehet:
  - 7 – 15V DC
  - Legalább 1500mA
  - A Központ/belső PIN-kódot kell lennie az energiaellátás a pozitív pole

A következő elemek nem kötelező:

* Groove alap pajzs V2
* Groove - hőmérséklet-érzékelő
* Groove-kábellel
* Bármely Oszlopelválasztó sávok vagy csavart a csomagolás, beleértve a két csavart, a modul a helybővítés tábla és négy csavart és műanyag térköztartók vizsgálókocsihoz szerepel.

> [!NOTE] 
Ezek az elemek nem kötelező, mivel a kód minta támogatási szimulált érzékelői adatokat.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Intel Edison beállítása

### <a name="assemble-your-board"></a>Állítsa össze a tábla

Ez a szakasz tartalmazza az Intel® Edison modul csatlakoztatása a helybővítés tábla lépéseket.

1. A fehér vázlat a helybővítés táblán belül az Intel® Edison modul helye a lyuk az modulban a csavart a helybővítés táblán a sorba állítása.

2. Nyomja le az alatt a szavakat modulon `What will you make?` mindaddig, amíg úgy véli, hogy a beépülő modult.

   ![Állítsa össze a tábla 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. A modul a helybővítés táblához való védelmére használja a két hexadecimális nuts (a csomagban található).

   ![Állítsa össze a tábla 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. A bővítés táblán a négy sarokban lyuk egyik beszúrása elment. Twist nevűt, és egy alakzatot a csavart fehér műanyag térköztartók növeljük.

   ![Állítsa össze a tábla 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Ismételje meg a többi három sarokban térköztartók.

   ![Állítsa össze a tábla 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Most már a tábla összeállítva.

   ![kiépített tábla](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Csatlakozás a Groove alap pajzs ikon és a hőmérséklet-érzékelő

1. Jelölje be a tábla Groove alap pajzs ikon. Ellenőrizze, hogy minden PIN-kód szorosan csatlakoztatva vannak a tábla.
   
   ![Groove alap pajzs ikon](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Hőmérséklet-érzékelő Groove alakzatot a Groove alap pajzs Groove-kábel használatával **A0** port.

   ![Csatlakozhat a hőmérséklet-érzékelő](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Edison és érzékelő-kapcsolat](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Az érzékelő most már készen áll.

### <a name="power-up-edison"></a>Hatékonyabbá Edison

1. Beépülő modul a tápegység.

   ![Beépülő modul tápegység](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Egy zöld LED (DS1 címkével a táblán Arduino * kiterjesztése) kell a mobilszolgáltatásokkal és megvilágítottnak maradjon.

3. Várjon egy percet a rendszerindítás Befejezés a táblához tartozó.

   > [!NOTE]
   > Ha nem rendelkezik egy tartományvezérlő tápegység, melyen keresztül egy USB-porttal továbbra is működtethet. Lásd: `Connect Edison to your computer` című szakasz részletezi. Ilyen módon a tábla tárolóház azt eredményezheti, hogy előre nem látható viselkedéshez a tábláról, különösen akkor, ha a Wi-Fi használatával, vagy vezetői motorok.

### <a name="connect-edison-to-your-computer"></a>Edison kapcsolódni a számítógéphez

1. Váltsa át a két micro USB-porttal, amelyért mikrokapcsoló le, hogy Edison eszköz módban van. Az eszköz és a gazdagép üzemmód közötti különbségek, kérjük, hivatkozzon [Itt](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Váltsa át a mikrokapcsoló lefelé](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. A micro USB-kábellel csatlakoztassa a felső micro USB-porttal.

   ![Felső micro USB-port](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Másik végén USB-kábellel csatlakoztassa a számítógépet.

   ![Számítógép USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Tudni fogja, hogy a tábla teljes inicializálását, amikor a számítógép csatlakoztatja egy új meghajtót (például egy SD-kártya beszúrása a számítógép jelentősen).

## <a name="download-and-run-the-configuration-tool"></a>A konfigurációs eszköz letöltése és futtatása
A legfrissebb konfigurációs eszközt, az első [ezt a hivatkozást](https://software.intel.com/en-us/iot/hardware/edison/downloads) felsorolva a `Installers` fejléc. Hajtsa végre az eszközt, és kövesse a képernyőn megjelenő utasításokat, ahol szükséges Tovább gombra kattint

### <a name="flash-firmware"></a>Belső vezérlőprogram Flash
1. Az a `Set up options` kattintson `Flash Firmware`.
2. Válassza ki a kép Flash alakzatot a tábla a következő módszerek valamelyikével:
   - Töltse le és a tábla a legújabb Intel elérhető belsővezérlőprogram-lemezkép a flash `Download the latest image version xxxx`.
   - Válassza ki a tábla már mentette-e a számítógép lemezképét a Flash, `Select the local image`. Keresse meg és válassza ki a flash a táblához való kívánt lemezképet.
3. A telepítő eszköz megkísérli a tábla flash. A teljes villogó folyamat akár 10 percet is igénybe vehet.

### <a name="set-password"></a>Jelszó beállítása
1. Az a `Set up options` kattintson `Enable Security`.
2. Az Intel® Edison táblához tartozó egyedi nevet állíthatja be. Ez nem kötelező.
3. Adja meg a táblához tartozó jelszót, majd kattintson a `Set password`.
4. Jelölje meg a jelszót, amely később használja fel.

### <a name="connect-wi-fi"></a>Csatlakozás Wi-Fi
1. Az a `Set up options` kattintson `Connect Wi-Fi`. Várjon legfeljebb egy percet, a számítógép számára elérhető Wi-Fi-hálózatok megvizsgálja.
2. Az a `Detected Networks` legördülő listára, válassza ki a hálózaton.
3. Az a `Security` legördülő listára, válassza ki a hálózati biztonság típusa.
4. Adja meg a felhasználónevet és jelszót adatait, majd kattintson a `Configure Wi-Fi`.
5. Jelölje meg az IP-cím, amely később használható fel.

> [!NOTE]
> Győződjön meg arról, hogy Edison csatlakozik-e a számítógép ugyanazon a hálózaton. A számítógép csatlakozik a Edison az IP-cím használatával.

   ![Csatlakozhat a hőmérséklet-érzékelő](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Gratulálunk! Edison sikeresen konfigurálta.

## <a name="run-a-sample-application-on-intel-edison"></a>Intel Edison a mintaalkalmazás futtatása

### <a name="prepare-the-azure-iot-device-sdk"></a>Készítse elő az Azure IoT eszközoldali SDK-t

1. A gazdagép számítógépről a következő SSH-ügyfél használatával az Intel Edison csatlakozni. Az IP-cím van a konfigurációs eszközt, és a jelszó pedig az eszköz a beállított.
    - [A puTTY](http://www.putty.org/) Windows számára.
    - Az Ubuntu vagy macOS rendszeren beépített SSH-ügyfél (Futtatás `ssh root@"the IP address"`).

2. Klónozza a példaalkalmazást ügyfél az eszközre. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Keresse meg a tárház mappában futtassa a következő parancsot hozhat létre az Azure IoT SDK-val

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Nyissa meg a konfigurációs fájlban a következő parancsok futtatásával:

   ```bash
   nano config.h
   ```

   ![Konfigurációs fájl](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Nincsenek két makrók configurate használhatja ezt a fájlt. Az első egy `INTERVAL`, amely megadja, hogy a két a felhőbe küldött üzenetek között eltelt időt. A második érték `SIMULATED_DATA`, azaz az e használni a szimulált érzékelői adatokat, vagy nem logikai érték.

   Ha Ön **nem rendelkezik az érzékelő**állítsa be a `SIMULATED_DATA` értéket a következőre `1` , hogy a mintaalkalmazás létrehozása és használata a szimulált érzékelői adatokat.

2. Mentéséhez és bezárásához nyomja meg az ellenőrző-O > Enter > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>A minta-alkalmazás összeállítása és futtatása

1. A mintaalkalmazás létrehozása a következő parancs futtatásával:

   ```bash
   cmake . && make
   ```
   ![Kimeneti létrehozása](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. A mintaalkalmazás futtatása a következő parancs futtatásával:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Győződjön meg arról, másolás és beillesztés az eszköz kapcsolati karakterláncának be a szimpla idézőjelek között.

Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

![Kimenet – Intel Edison az IoT hubnak küldött érzékelőktől kapott adatok](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
