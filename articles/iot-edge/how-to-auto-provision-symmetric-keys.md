---
title: Eszköz kiépítése szimmetrikus kulcsú tanúsítvány használatával – Azure IoT Edge
description: A szimmetrikus kulcsos tanúsítványok használata a Azure IoT Edge automatikus eszköz-kiépítés teszteléséhez az eszköz kiépítési szolgáltatásával
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c6c2067526850ba972f002dc40bbd5d4cb24c9ba
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131011"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával

Azure IoT Edge eszközök automatikusan kiállíthatók az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml) , ugyanúgy, mint az Edge-t nem használó eszközök. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítési fogalmakat](../iot-dps/concepts-auto-provisioning.md) .

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni regisztrációt egy IoT Edge eszközön a szimmetrikus kulcs igazolásával egy eszköz kiépítési szolgáltatásához a következő lépések végrehajtásával:

* Hozzon létre egy IoT Hub Device Provisioning Service (DPS) egy példányát.
* Hozzon létre egy egyéni regisztrációt az eszközhöz.
* Telepítse a IoT Edge futtatókörnyezetet, és kapcsolódjon a IoT Hubhoz.

A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz kiépítési szolgáltatási példánnyal való hitelesítésére. Ez az igazolási módszer a "Hello World" felhasználói élményt jelöli olyan fejlesztők számára, akik még nem ismerik az eszközök üzembe helyezését, vagy nincsenek szigorú biztonsági követelmények. A [TPM](../iot-dps/concepts-tpm-attestation.md) vagy [X. 509 tanúsítványokat](../iot-dps/concepts-security.md#x509-certificates) használó eszközök igazolása biztonságosabb, és szigorúbb biztonsági követelményekhez kell használni őket.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub
* Fizikai vagy virtuális eszköz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányt a IoT Hub Device Provisioning Service az Azure-ban, és kapcsolja össze az IoT hubhoz. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Válasszon egyedi regisztrációs azonosítót az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell megadni. Az eszköz MAC-címe, sorozatszáma vagy bármely egyedi adata használható.

Ebben a példában egy MAC-címe és sorozatszáma kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs AZONOSÍTÓhoz: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`.

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűs alfanumerikus és kötőjel ("-").

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Az eszköz regisztrációs AZONOSÍTÓjának használatával egyéni regisztrációt hozhat létre a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

> [!TIP]
> A csoportos regisztrációk akkor is lehetségesek, ha szimmetrikus kulcsú igazolást használ, és ugyanazokat a döntéseket vonja be, mint az egyéni regisztrációk.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások**területen válassza a **regisztrációk kezelése**lehetőséget.

1. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   1. A **mechanizmus**mezőben válassza a **szimmetrikus kulcs**lehetőséget.

   1. Jelölje be a **kulcsok automatikus generálása** jelölőnégyzetet.

   1. Adja meg az eszközhöz létrehozott **regisztrációs azonosítót** .

   1. Ha szeretné, adja meg **IoT hub eszköz azonosítóját** az eszközhöz. Az eszközök azonosítói segítségével megcélozhat egy különálló eszközt a modulok telepítéséhez. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   1. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a beléptetés egy IoT Edge eszközre vonatkozik. Csoportos regisztráció esetén minden eszköznek IoT Edge eszköznek kell lennie, vagy egyikük sem lehet.

   > [!TIP]
   > Az Azure CLI-ben létrehozhat egy [beléptetést](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [beléptetési csoportot](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy egy eszköz vagy egy csoport IoT Edge eszköz-e.

   1. Fogadja el az alapértelmezett értéket az eszköz kiépítési szolgáltatásának kiosztási házirendjében, hogy **hogyan kívánja hozzárendelni az eszközöket az elosztóhoz** , vagy válasszon egy másik, a regisztrációra vonatkozó értéket.

   1. Válassza ki azt a csatolt **IoT hub** , amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   1. Válassza ki **, hogyan történjen az eszközbeállítások kezelése** az eszközök első kiépítés utáni ismételt kiépítés esetén.

   1. Ha szeretné, adjon hozzá egy címke értéket a **kezdeti eszköz iker állapotához** . A címkék használatával megcélozhatja az eszközök csoportjait a modulok üzembe helyezéséhez. Például:

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

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. Ügyeljen arra, hogy a beléptetés **elsődleges kulcsának** értékét a IoT Edge futtatókörnyezet telepítésekor használja, vagy ha az eszköz kulcsait a csoportos regisztrációval való használatra kívánja létrehozni.

## <a name="derive-a-device-key"></a>Eszköz kulcsának származtatása

> [!NOTE]
> Ez a szakasz csak akkor szükséges, ha csoportos regisztrációt használ.

Minden eszköz a származtatott eszköz kulcsát használja az egyedi regisztrációs AZONOSÍTÓval a szimmetrikus kulcs igazolásának végrehajtásához a regisztráció során. Az eszköz kulcsának létrehozásához használja a DPS-regisztrációból másolt kulcsot, hogy kiszámítsa az eszköz egyedi regisztrációs AZONOSÍTÓjának [HMAC-sha256](https://wikipedia.org/wiki/HMAC) , és az eredményt Base64 formátumra konvertálja.

Ne foglalja bele a regisztráció elsődleges vagy másodlagos kulcsát az eszköz kódjába.

### <a name="linux-workstations"></a>Linux-munkaállomások

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

Cserélje le **REG_ID** értékét az eszköz regisztrációs azonosítójával.

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

Cserélje le **REG_ID** értékét az eszköz regisztrációs azonosítójával.

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

## <a name="install-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható.

Az eszköz kiépítés során a következő információkra lesz szüksége:

* A DPS- **azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**
* A DPS-regisztrációból másolt **elsődleges kulcs**

> [!TIP]
> Csoportos regisztráció esetén az eszköz [származtatott kulcsára](#derive-a-device-key) van szükség a DPS regisztrációs kulcs helyett.

### <a name="linux-device"></a>Linuxos eszköz

Kövesse az eszköz architektúrájának utasításait. Győződjön meg arról, hogy a IoT Edge futtatókörnyezet automatikus, nem manuális, kiépítés esetén van konfigurálva.

[A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)

A szimmetrikus kulcs kiépítésének konfigurációs fájljának szakasza így néz ki:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Cserélje le a `<SCOPE_ID>`, `<REGISTRATION_ID>`és `<SYMMETRIC_KEY>` a helyőrző értékét a korábban összegyűjtött adatokra. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

### <a name="windows-device"></a>Windows-eszköz

Telepítse a IoT Edge futtatókörnyezetet azon az eszközön, amelyhez létrehozta a származtatott eszköz kulcsát. A IoT Edge futtatókörnyezetet automatikus, nem manuális, kiépítés esetén kell konfigurálnia.

A IoT Edge Windows rendszeren való telepítésével kapcsolatos további információkért, beleértve a tárolók kezeléséhez és a IoT Edge frissítéséhez szükséges feladatok előfeltételeit és utasításait lásd: [a Azure IoT Edge futtatókörnyezet telepítése Windows](how-to-install-iot-edge-windows.md)rendszeren.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime és a IoT Edge futtatókörnyezetet. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítását kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezés szerint a Windows-tárolók manuális üzembe helyezését választja, kivéve `-Dps` , ha a jelzőt használja az automatikus kiépítés használatára.

   Cserélje le a `{scope_id}`, `{registration_id}`és `{symmetric_key}` a helyőrző értékét a korábban összegyűjtött adatokra.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és megkezdheti a IoT Edge modulok üzembe helyezését az eszközön. Az eszközön az alábbi parancsokkal ellenőrizheti, hogy a futtatókörnyezet telepítése és elindítása sikeresen megtörtént-e.

### <a name="linux-device"></a>Linuxos eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
systemctl status iotedge
```

A szolgáltatási naplók vizsgálata.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listázása.

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

Futó modulok listázása.

```powershell
iotedge list
```

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

## <a name="next-steps"></a>További lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md).
