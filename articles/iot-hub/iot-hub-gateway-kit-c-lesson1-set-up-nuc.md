---
title: "SensorTag eszköz & Azure IoT átjáró - lecke 1: Intel NUC beállítása |} Microsoft Docs"
description: "Intel NUC beállítása érzékelő és az Azure IoT Hub érzékelő adatokat gyűjteni, és elküldi a IoT-központ közötti IoT átjáróként működik."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az IOT-átjárón intel nuc nuc számítógép, DE3815TYKE"
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Az IoT átjáróként Intel NUC beállítása
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>Mit fog

- Az IoT-átjáró Intel NUC állíthatja be.
- Az Intel NUC Azure IoT peremhálózati telepítéséhez.
- Futtassa a "hello_world" mintaalkalmazás az Intel NUC átjáró működésének ellenőrzéséhez.

  > Ha bármilyen problémába ütközik, tekintse meg a megoldások a [oldal hibaelhárítási](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Amiről tanulni fog

Ez a lecke témák köre:

- Hogyan perifériák Intel NUC kapcsolódni.
- Hogyan kell telepíteni, és frissítse a szükséges csomagokat a Intel NUC az intelligens Package Manager segítségével.
- Hogyan futtassa a "hello_world" mintaalkalmazást átjáró működésének ellenőrzéséhez.

## <a name="what-you-need"></a>Mi szükséges

- Az Intel NUC Kit DE3815TYKE a Intel IoT átjáró termékcsomag (szél folyó Linux * 7.0.0.13) előtelepítése mellett. [Kattintson ide a beszerzési Groove IoT kereskedelmi átjáró Kit](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Kábel.
- A billentyűzet.
- Egy HDMI vagy VGA kábel.
- Egy figyelő HDMI vagy VGA-port.
- Választható lehetőség: [Texas eszközök érzékelő címke (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Átjáró csomag](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Csatlakozás a perifériák Intel NUC

Az alábbi képen látható egy példa Intel NUC különböző perifériák kapcsolódnak:

1. A billentyűzet csatlakozik.
2. A figyelő egy VGA vagy HDMI kábellel csatlakozik.
3. Ethernet kábel egy vezetékes hálózathoz csatlakoznak.
4. Egy tápegység power kábellel csatlakozik.

![Intel NUC perifériák csatlakozik.](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>A gazdaszámítógép keresztül Secure Shell (SSH) az Intel NUC rendszerhez való csatlakozás

Szüksége lesz a billentyűzet és egy figyelőt a beolvasása az Intel NUC eszköz IP-címét. Ha már ismeri az IP-cím, kihagyhatja azokat, amelyek a jelen szakaszban 3. lépésre.

1. Az Intel NUC bekapcsolása a power gombra kattintva, majd jelentkezzen be.

   Az alapértelmezett felhasználónév és jelszó mindkét `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Az Intel NUC IP-címének lekéréséhez futtassa a `ifconfig` parancsot az Intel NUC eszközön.

   Íme egy példa a parancs kimenetét.

   ![Intel NUC IP megjelenítő ifconfig kimeneti](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Ebben a példában az érték, amely a következő `inet addr:` van szüksége, amikor csatlakoznak a Intel NUC egy számítógép IP-cím.

3. A számítógép a következő SSH-ügyfél segítségével Intel NUC csatlakozni.

    - [A puTTY](http://www.putty.org/) Windows.
    - A beépített SSH-ügyfél Ubuntu vagy macOS.

   Hatékony és hatékony működéséhez egy állomásról egy Intel NUC. Az Intel NUC IP-cím, a felhasználónév és jelszó egy SSH-ügyfél keresztül kapcsolódni hozzá lesz szüksége. Íme egy példa, egy SSH-ügyfél által az macOS.
   ![MacOS futó SSH-ügyfél](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Telepítse az Azure IoT Edge-csomagot

Az Azure IoT biztonsági csomag IoT széle és annak függőségeit a előre lefordított bináris fájljait tartalmazza. A bináris fájljait a Azure IoT él, az Azure IoT SDK és a megfelelő eszközök. A csomag is tartalmaz egy "hello_world" mintaalkalmazást az átjárófunkció ellenőrzésére szolgál. Az IoT-Edge az átjáró alapvető részét képezi. 

Kövesse az alábbi lépéseket a csomag telepítéséhez.

1. Adja hozzá a felhőben az IoT-tárház egy terminál ablakban a következő parancsok futtatásával:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Adja meg az "y", amikor a rendszer kérni fogja, hogy az "Include ezt a csatornát?"
   
   Ha megjelenik egy `import read failed(-1)` hiba, a probléma megoldásához használja a következő parancsokat:
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   A `rpm` parancs importálja a rpm kulcsot. A `smart channel` parancs hozzáadja a rpm-csatorna a intelligens Csomagkezelő. Futtatása előtt a `smart update` parancsban, látni fogja, például egy kimeneti alatt.

   ![rpm és intelligens csatorna parancs kimenete](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Az intelligens frissítés parancs hajtható végre:

   ```bash
   smart update
   ```

3. Az Azure IoT-átjáró telepítéséhez a következő parancs futtatásával:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure`a csomag neve van. A `smart install` a parancsnak a csomag telepítéséhez.

    > Futtassa a következő parancsot, ha ezt a hibaüzenetet látja: "nyilvános kulcs nem érhető el"

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Indítsa újra az Intel NUC, ha ezt a hibaüzenetet látja: "nincs csomag biztosítja a fejlesztői linux util"

   A csomag telepítése után Intel NUC készen áll az átjáró működhet.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Az Azure IoT peremhálózati "hello_world" mintaalkalmazás futtatása

Az alábbi minta-alkalmazást hoz létre az átjáró egy `hello_world.json` fájlt, és az Azure IoT peremhálózati architektúra alapvető összetevői hello world üzenet jelentkezzenek be egy fájl (log.txt) 5 másodpercentként használja.

A Hello World PéldaAlkalmazás futtathatja a következő parancsok végrehajtásával:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Lehetővé teszik a Hello World alkalmazásról futtatása néhány percet, és majd kattintson a állítsa le az Enter billentyűt.
![alkalmazás kimenete](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> A "argumentum érvénytelen handle(NULL)" hibák után le az ENTER billentyűt figyelmen kívül hagyhatja.

Ellenőrizheti, hogy az átjáró, amelyik most már a hello_world mappában log.txt fájlt sikeresen lefutott ![log.txt directory megtekintése](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Nyissa meg a következő paranccsal log.txt:

```bash
vim log.txt
```

Ekkor megjelenik a log.txt, ami egy formázott JSON-kimenetét a naplózási üzeneteket, hogy az átjáró Hello World modul 5 másodpercenként írt tartalmát.
![log.txt directory megtekintése](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Ha bármilyen problémába ütközik, tekintse meg a megoldások a [oldal hibaelhárítási](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Összefoglalás

Gratulálunk! Intel NUC átjáróként beállításának befejezése után. Most már készen áll áthelyezése a következő lecke állítsa be a gazdaszámítógép, Azure IoT Hub létrehozása és regisztrálása az Azure IoT Hub logikai eszköz.

## <a name="next-steps"></a>Következő lépések
[Egy eszköz csatlakoztatása az Azure IoT Hub egy IoT-átjáró használatával](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

