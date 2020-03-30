---
title: Eszköz kiépítése szimmetrikus kulcsigazolással - Azure IoT Edge
description: Szimmetrikus kulcsigazolás használata az Azure IoT Edge automatikus eszközkiépítéséhez az eszközkiépítési szolgáltatással
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535918"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>IoT Edge-eszköz létrehozása és kiépítése szimmetrikus kulcsigazolás sal

Az Azure IoT Edge-eszközök is automatikusan kiépíthető az [eszközkiépítési szolgáltatás,](../iot-dps/index.yml) csakúgy, mint az eszközök, amelyek nem él-kompatibilis. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítés iményét.](../iot-dps/concepts-auto-provisioning.md)

Ez a cikk bemutatja, hogyan hozhat létre egy eszközkiépítési szolgáltatás egyéni regisztráció szimmetrikus kulcs igazolása egy IoT Edge-eszközön a következő lépésekkel:

* Hozzon létre egy példányt az IoT Hub-eszközlétesítési szolgáltatás (DPS) példánya.
* Hozzon létre egyéni regisztrációt az eszközhöz.
* Telepítse az IoT Edge futásidejű, és csatlakozzon az IoT Hub.

A szimmetrikus kulcsigazolás egy egyszerű módszer az eszköz kiépítési szolgáltatáspéldánysal való hitelesítéséhez. Ez az igazolási módszer egy "Hello world" élményt jelent azoknak a fejlesztőknek, akik újak az eszközkiépítésben, vagy nem rendelkeznek szigorú biztonsági követelményekkel. A [TPM](../iot-dps/concepts-tpm-attestation.md) vagy [X.509 tanúsítvánnyal](../iot-dps/concepts-security.md#x509-certificates) használt eszközigazolás biztonságosabb, és szigorúbb biztonsági követelmények hez kell használni.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív IoT Hub
* Fizikai vagy virtuális eszköz

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub-eszközkiépítési szolgáltatás beállítása

Hozzon létre egy új példányt az IoT Hub-eszközkiépítési szolgáltatás az Azure-ban, és kapcsolja össze az IoT hub. Az [IoT Hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című útmutató utasításait követheti.

Miután futtatta az eszközkiépítési szolgáltatást, másolja az **ID Scope** értékét az áttekintő lapról. Ezt az értéket használja az IoT Edge futásidejű konfigurálásakor.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Egyedi regisztrációs azonosító kiválasztása az eszközhöz

Az egyes eszközök azonosításához egyedi regisztrációs azonosítót kell definiálni. Használhatja a MAC-címet, a sorozatszámot vagy az eszközbármely egyedi információját.

Ebben a példában egy MAC-cím és egy sorszám kombinációját használjuk, amely a következő karakterláncot képezi a regisztrációs azonosítóhoz: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`.

Hozzon létre egy egyedi regisztrációs azonosítót az eszközhöz. Az érvényes karakterek a kisbetűk alfanumerikus és a kötőjel ('-').

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Az eszköz regisztrációs azonosítójával egyéni regisztrációt hozhat létre a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van egy **kezdeti ikereszköz-állapot**deklarálni. Az ikereszközben beállíthatja az eszközök csoportosítását a megoldásban szükséges bármely mutató, például a régió, a környezet, a hely vagy az eszköz típusa szerint. Ezek a címkék az automatikus központi telepítések létrehozásához [használatosak.](how-to-deploy-monitor.md)

> [!TIP]
> A csoportos beléptetések akkor is lehetségesek, ha szimmetrikus kulcsigazolást használ, és ugyanazokat a döntéseket vonja magában, mint az egyes regisztrációk.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát.

1. A **Beállítások csoportban**válassza **a Regisztrációk kezelése**lehetőséget.

1. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd hajtsa végre a következő lépéseket a regisztráció konfigurálásához:  

   1. A **Mechanizmus**csoportban válassza **a Szimmetrikus billentyű**lehetőséget.

   1. Jelölje be a **billentyűk automatikus generálása** jelölőnégyzetet.

   1. Adja meg az eszközhöz létrehozott **regisztrációs azonosítót.**

   1. Adja meg az **IoT Hub-eszközazonosítóját** az eszközhöz, ha szeretné. Az eszközazonosítók segítségével megcélozhat egy adott eszközt a modul telepítéséhez. Ha nem adja meg az eszközazonosítót, a regisztrációs azonosítót használja a készülék.

   1. **Válassza a True lehetőséget,** ha deklarálni szeretné, hogy a regisztráció egy IoT Edge-eszközhöz kapcsolódik. Egy csoport regisztráció, minden eszköznek IoT Edge-eszközöknek kell lennie, vagy egyikük sem lehet.

   1. Fogadja el az alapértelmezett értéket az Eszközkiépítési szolgáltatás foglalási szabályzatából **arra vonatkozóan, hogy miként szeretné eszközöket hozzárendelni a hubokhoz,** vagy hogy milyen eltérő értéket szeretne választani a regisztrációhoz.

   1. Válassza ki azt a csatolt **IoT-központot,** amelyhez csatlakoztatni szeretné az eszközt. Több elosztót is kiválaszthat, és az eszköz a kiválasztott foglalási házirendnek megfelelően lesz hozzárendelve az egyikhez.

   1. Válassza **ki, hogyan szeretné kezelni az eszközadatokat az újraépítés során,** amikor az eszközök első alkalommal kérik a kiépítést.

   1. Ha szeretné, adjon hozzá egy címkeértéket a **kezdeti ikereszköz-állapothoz.** A címkék segítségével a modul üzembe helyezéséhez eszközcsoportokat célozhat meg. Példa:

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

   1. Győződjön meg arról, hogy a **Belépés engedélyezése** beállítás **engedélyezése.**

   1. Kattintson a **Mentés** gombra.

Most, hogy egy regisztráció létezik ehhez az eszközhöz, az IoT Edge futásidejű automatikusan kiépítheti az eszközt a telepítés során. Győződjön meg arról, hogy másolja a regisztráció **elsődleges kulcs** értéke az IoT Edge futásidejű telepítésekor, vagy ha lesz eszközkulcsok létrehozása a csoport regisztrációhoz.

## <a name="derive-a-device-key"></a>Eszközkulcs leszármaztatása

> [!NOTE]
> Ez a szakasz csak akkor kötelező, ha csoportos regisztrációt használ.

Minden eszköz a származtatott eszközkulcsot használja az egyedi regisztrációs azonosítóval, hogy szimmetrikus kulcsigazolást hajtson végre a regisztráció során a kiépítés során. Az eszközkulcs létrehozásához használja a DPS-regisztrációból másolt kulcsot az eszköz egyedi regisztrációs azonosítójának [HMAC-SHA256-os](https://wikipedia.org/wiki/HMAC) számának kiszámításához és az eredmény Base64 formátumba történő konvertálásához.

Ne adja meg a regisztráció elsődleges vagy másodlagos kulcsát az eszközkódban.

### <a name="linux-workstations"></a>Linux munkaállomások

Ha Linux munkaállomást használ, az openssl segítségével létrehozhatja a származtatott eszközkulcsot, ahogy az a következő példában látható.

Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójára.

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

Ha Windows-alapú munkaállomást használ, a PowerShell segítségével létrehozhatja a származtatott eszközkulcsot, ahogy az a következő példában látható.

Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

Cserélje le a **REG_ID** értékét az eszköz regisztrációs azonosítójára.

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

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge futásidejű telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközre, így a kód a peremhálózaton futtatható.

Az eszköz kiépítésekor a következő adatokra lesz szüksége:

* A **DPS-azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**
* A DPS-regisztrációból másolt **elsődleges kulcs**

> [!TIP]
> A csoportos regisztrációkhoz az egyes eszközök [származtatott kulcsára](#derive-a-device-key) van szükség a DPS regisztrációs kulcs helyett.

### <a name="linux-device"></a>Linux-eszköz

Kövesse az eszköz architektúrájára vonatkozó utasításokat. Győződjön meg arról, hogy konfigurálja az IoT Edge futásidejű automatikus, nem manuális, kiépítés.

[Az Azure IoT Edge futásidejű telepítésélinuxra](how-to-install-iot-edge-linux.md)

A szimmetrikus kulcs kiépítéskonfigurációjának konfigurációs fájljában lévő szakasz a következőképpen néz ki:

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

Cserélje le a `<SCOPE_ID>`helyőrző értékeket a, `<REGISTRATION_ID>`és `<SYMMETRIC_KEY>` a korábban gyűjtött adatokra. Győződjön meg arról, hogy a **kiépítés:** sor nem rendelkezik előző szóközzel, és hogy a beágyazott elemeket két szóköz bevan húzva.

### <a name="windows-device"></a>Windows-eszköz

Telepítse az IoT Edge futásidejű az on device, amelyhez létrehozott egy származtatott eszköz kulcsot. Az IoT Edge-futásidejű automatikus, nem manuális, kiépítés konfigurálása.

Az IoT Edge Windows rendszeren történő telepítéséről, beleértve az olyan feladatok előfeltételeit és utasításait, mint a tárolók kezelése és az IoT Edge frissítése, [az Azure IoT Edge futásidejének telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakörben talál további információt.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Győződjön meg arról, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, ne a PowerShellt (x86).

1. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, majd letölti a moby futásidejű és az IoT Edge futásidejű. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Más Windows 10- vagy Windows Server-eszközök újraindítást kérhetnek. Ha igen, indítsa újra az eszközt most. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

1. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuális annektálás `-Dps` a Windows-tárolók, kivéve, ha a jelző t használja az automatikus kiépítés.

   Cserélje le a `{scope_id}`helyőrző értékeket a, `{registration_id}`és `{symmetric_key}` a korábban gyűjtött adatokra.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futásidő sikeresen elindult, az IoT Hubba léphet, és elkezdheti az IoT Edge-modulok üzembe helyezését az eszközre. Az alábbi parancsokkal ellenőrizheti, hogy a futásidő sikeresen telepítve van-e, és sikeresen elindult-e.

### <a name="linux-device"></a>Linux-eszköz

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a szolgáltatásnaplókat.

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

Vizsgálja meg a szolgáltatásnaplókat.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája.

```powershell
iotedge list
```

Ellenőrizheti, hogy az eszközkiépítési szolgáltatásban létrehozott egyéni regisztrációt használta-e. Keresse meg az Eszközkiépítési szolgáltatás példányát az Azure Portalon. Nyissa meg a létrehozott egyéni beléptetési adatokat. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító szerepel a listában.

## <a name="next-steps"></a>További lépések

Az eszközkiépítési szolgáltatás regisztrációs folyamat lehetővé teszi, hogy az eszközazonosítóés az ikercímkék beállítása az új eszköz kiépítésével egy időben. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával történő célzásához. Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat az Azure Portalon](how-to-deploy-monitor.md) vagy az [Azure CLI használatával.](how-to-deploy-monitor-cli.md)
