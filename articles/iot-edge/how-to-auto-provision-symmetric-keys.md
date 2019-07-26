---
title: Eszközök kiépítése a DPS használatával szimmetrikus kulcs igazolásával – Azure IoT Edge | Microsoft Docs
description: A szimmetrikus kulcsos tanúsítványok használata a Azure IoT Edge automatikus eszköz-kiépítés teszteléséhez az eszköz kiépítési szolgáltatásával
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c21c0bdce6f6a5cd3c8f634bf400600b30a8ead
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414592"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával

Az Azure IoT Edge-eszközökön lehet autoprovisioned használatával a [Device Provisioning Service](../iot-dps/index.yml) ugyanúgy, mint az eszközök, amelyek az edge-kompatibilis nem. Ha még nem ismeri a autoprovisioning folyamatán, tekintse át a [autoprovisioning fogalmak](../iot-dps/concepts-auto-provisioning.md) a folytatás előtt.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni regisztrációt egy IoT Edge eszközön a szimmetrikus kulcs igazolásával egy eszköz kiépítési szolgáltatásához a következő lépések végrehajtásával:

* Hozzon létre egy példányt, IoT Hub Device Provisioning szolgáltatás (DPS).
* Hozzon létre egyéni regisztrációt az eszközön.
* Telepítse a IoT Edge futtatókörnyezetet, és kapcsolódjon a IoT Hubhoz.

A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz kiépítési szolgáltatási példánnyal való hitelesítésére. Ez az igazolási módszer a "Hello World" felhasználói élményt jelöli olyan fejlesztők számára, akik még nem ismerik az eszközök üzembe helyezését, vagy nincsenek szigorú biztonsági követelmények. Az eszköz tanúsítványa [TPM](../iot-dps/concepts-tpm-attestation.md) használatával biztonságosabb, és szigorúbb biztonsági követelményekhez kell használni.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub
* Fizikai vagy virtuális eszköz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányát az IoT Hub Device Provisioning Service az Azure-ban, és az IoT hub-ra hivatkozik. Az utasítások a [állítsa be az IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Miután a Device Provisioning Service futó, másolja az értékét **azonosító hatóköre** az Áttekintés lapon. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Válasszon egyedi regisztrációs azonosítót az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell megadni. Az eszköz MAC-címe, sorozatszáma vagy bármely egyedi adata használható.

Ebben a példában egy MAC-címe és sorozatszáma kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs AZONOSÍTÓhoz.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűs alfanumerikus és kötőjel ("-").

## <a name="create-a-dps-enrollment"></a>A DPS regisztrációjának létrehozásához

Az eszköz regisztrációs AZONOSÍTÓjának használatával egyéni regisztrációt hozhat létre a DPS-ben.

Amikor létrehoz egy regisztrációs a DPS Szolgáltatásban, lehetősége van deklarálnia egy **Ikereszköz kezdeti állapota**. Az ikereszközön, állíthat be címkéket csoportosítani az eszközöket a megoldás, mint például a régiót, környezetet, hely vagy eszköz típusa kell tetszőleges metrika szerint. Ezekkel a címkékkel hozhatók létre [automatikus központi telepítések](how-to-deploy-monitor.md).

> [!TIP]
> A csoportos regisztrációk akkor is lehetségesek, ha szimmetrikus kulcsú igazolást használ, és ugyanazokat a döntéseket vonja be, mint az egyéni regisztrációk.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **beállítások**válassza **beléptetések kezelése**.

1. Válassza ki **egyéni regisztráció hozzáadása** végezze el a regisztrációt konfigurálhatja az alábbi lépéseket:  

   1. A **mechanizmus**mezőben válassza a **szimmetrikus kulcs**lehetőséget.

   1. Jelölje be a **kulcsok automatikus generálása** jelölőnégyzetet.

   1. Adja meg az eszközhöz létrehozott **regisztrációs azonosítót** .

   1. Ha szeretné, adja meg **IoT hub eszköz azonosítóját** az eszközhöz. Az eszközazonosítókat, amelyekre egy adott eszköz számára üzembe helyezett házirendmodul használhatja. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   1. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a beléptetés egy IoT Edge eszközre vonatkozik. Csoportos regisztráció esetén minden eszköznek IoT Edge eszköznek kell lennie, vagy egyikük sem lehet.

   1. Fogadja el az alapértelmezett értéket az eszköz kiépítési szolgáltatásának kiosztási házirendjében, hogy **hogyan kívánja hozzárendelni az eszközöket** az elosztóhoz, vagy válasszon egy másik, a regisztrációra vonatkozó értéket.

   1. Válassza ki a csatolt **az IoT Hub** , hogy szeretné-e az eszközt csatlakoztatni. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   1. Válassza ki **, hogyan történjen az eszközbeállítások kezelése** az eszközök első kiépítés utáni ismételt kiépítés esetén.

   1. Címke Érték hozzáadása a **Ikereszköz kezdeti állapota** Ha szeretné. Használhatja a címkéket, hogy az eszközök célcsoportok az adatraktármodul központi telepítése. Példa:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Győződjön meg arról, hogy a **bejegyzés** engedélyezése beállítás **engedélyezve**van.

   1. Kattintson a **Mentés** gombra.

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. Ügyeljen arra, hogy az eszköz kulcsának létrehozásakor a regisztráció **elsődleges kulcsának** értékét másolja.

## <a name="derive-a-device-key"></a>Eszköz kulcsának származtatása

Az eszköz a származtatott eszköz kulcsát használja az egyedi regisztrációs AZONOSÍTÓval a szimmetrikus kulcs igazolásának végrehajtásához a regisztráció során. Az eszköz kulcsának létrehozásához használja a DPS-regisztrációból másolt kulcsot, hogy kiszámítsa az eszköz egyedi regisztrációs AZONOSÍTÓjának [HMAC-sha256](https://wikipedia.org/wiki/HMAC) , és az eredményt Base64 formátumra konvertálja.

Ne foglalja bele a regisztráció elsődleges vagy másodlagos kulcsát az eszköz kódjába.

### <a name="linux-workstations"></a>Linux-munkaállomások

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójával.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójával.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön.

Az eszköz kiépítés során a következő információkra lesz szüksége:

* A DPS- **azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**
* Az eszköz származtatott eszközének kulcsa a szimmetrikus kulcs igazolásához

### <a name="linux-device"></a>Linuxos eszköz

Kövesse az eszköz architektúrájának utasításait. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést.

[A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)

A szimmetrikus kulcs kiépítésének konfigurációs fájljának szakasza így néz ki:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Cserélje le a `{scope_id}`, `{registration_id}`és `{symmetric_key}` a helyőrző értékét a korábban összegyűjtött adatokra.

### <a name="windows-device"></a>Windows-eszköz

Az utasításokat követve telepítse a IoT Edge futtatókörnyezetet azon az eszközön, amelyhez létrehozta a származtatott eszköz kulcsát. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést.

[IoT Edge telepítése és automatikus kiépítése Windows rendszeren](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha a modul sikeresen elindult, az IoT Hub lép, és elkezdeni az eszköz IoT Edge-modulok telepítését. Az eszközön az alábbi parancsok használatával ellenőrizze, hogy a futtatókörnyezet-sikeresen elindult.

### <a name="linux-device"></a>Linuxos eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
systemctl status iotedge
```

A szolgáltatási naplók vizsgálata.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listája.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

A szolgáltatási naplók vizsgálata.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája.

```powershell
iotedge list
```

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

## <a name="next-steps"></a>További lépések

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Ismerje meg, hogyan [üzembe helyezése és figyelése az IoT Edge-modulok méretezése az Azure portal használatával](how-to-deploy-monitor.md) vagy [Azure CLI-vel](how-to-deploy-monitor-cli.md).
