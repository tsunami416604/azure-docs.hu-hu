---
title: Eszközök automatikus kiépítése A DPS segítségével X.509 tanúsítványok - Azure IoT Edge | Microsoft dokumentumok
description: Az X.509-es tanúsítványok használata az Azure IoT Edge automatikus eszközkiépítéséhez az eszközkiépítési szolgáltatással
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537361"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>IoT Edge-eszköz létrehozása és kiépítése X.509-es tanúsítványokkal

Az [Azure IoT Hub-eszközlétesítési szolgáltatás (DPS)](../iot-dps/index.yml)segítségével automatikusan kiépítheti az IoT Edge-eszközöket X.509-es tanúsítványokkal. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítés iményét.](../iot-dps/concepts-auto-provisioning.md)

Ez a cikk bemutatja, hogyan hozhat létre eszközkiépítési szolgáltatás rakoncátort X.509-es tanúsítványokkal egy IoT Edge-eszközön a következő lépésekkel:

* Tanúsítványok és kulcsok létrehozása.
* Hozzon létre egyéni regisztrációt egy eszközhöz, vagy hozzon létre egy csoportregisztrációt egy eszközcsoporthoz.
* Telepítse az IoT Edge futásidejű és regisztrálja az eszközt az IoT Hub.

Az X.509 tanúsítványok igazolási mechanizmusként való használata kiváló módja az éles környezet méretezésének és az eszközkiépítés egyszerűsítésének. Az X.509 tanúsítványok általában egy megbízhatósági láncba vannak rendezve. Az önaláírt vagy megbízható legfelső szintű tanúsítványsal kezdve a láncban lévő minden tanúsítvány aláírja a következő alacsonyabb tanúsítványt. Ez a minta létrehoz egy delegált bizalmi láncot a főtanúsítványtól az egyes köztes tanúsítványokon keresztül az eszközre telepített végső "levél" tanúsítványig.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív IoT Hub.
* Fizikai vagy virtuális eszköz az IoT Edge-eszköz.
* A [Git](https://git-scm.com/download/) legújabb verziója telepítve.
* Az IoT Hub-eszközlétesítési szolgáltatás az Azure-ban, az IoT hub hoz kapcsolódó példánya.
  * Ha nem rendelkezik eszközkiépítési szolgáltatáspéldával, kövesse [az IoT Hub DPS beállítása című](../iot-dps/quick-setup-auto-provision.md)útmutató utasításait.
  * Miután futtatta az eszközkiépítési szolgáltatást, másolja az **ID Scope** értékét az áttekintő lapról. Ezt az értéket használja az IoT Edge futásidejű konfigurálásakor.

## <a name="generate-device-identity-certificates"></a>Eszközidentitás-tanúsítványok létrehozása

Az eszközazonosító tanúsítvány egy levéltanúsítvány, amely egy megbízhatósági tanúsítványláncon keresztül kapcsolódik a legfelső X.509 hitelesítésszolgáltatói (CA) tanúsítványhoz. Az eszköz identitástanúsítványának rendelkeznie kell a közös név (CN) beállításával arra az eszközazonosítóra, amelyet az eszköznek az IoT-hubban kell tartania.

Az eszközidentitás-tanúsítványok csak az IoT Edge-eszköz kiépítéséhez és az eszköz Azure IoT Hubhasználatával való hitelesítéséhez használatosak. Nem írnak alá tanúsítványokat, ellentétben az IoT Edge-eszköz által a moduloknak vagy a levéleszközöknek ellenőrzésre szolgáló hitelesítésszolgáltatói tanúsítványokkal. További információ: [Azure IoT Edge tanúsítvány használati részletek.](iot-edge-certs.md)

Az eszközazonosító tanúsítvány létrehozása után két fájlnak kell rendelkeznie: egy .cer vagy .pem fájl, amely a tanúsítvány nyilvános részét tartalmazza, és egy .cer vagy .pem fájl a tanúsítvány titkos kulcsával. Ha a csoportigénylést a DPS-ben kívánja használni, akkor egy köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítvány nyilvános részére is szüksége van ugyanabban a megbízhatósági láncban.

### <a name="use-test-certificates"></a>Teszttanúsítványok használata

Ha nem rendelkezik egy hitelesítésszolgáltató áll rendelkezésre az új identitás tanúsítványok létrehozásához, és szeretné kipróbálni ezt a forgatókönyvet, az Azure IoT Edge git tárház parancsfájlokat tartalmaz, amelyek segítségével teszttanúsítványok létrehozásához. Ezek a tanúsítványok csak fejlesztési tesztelésre szolgálnak, és nem használhatók éles környezetben.

Teszttanúsítványok létrehozásához kövesse a Demótanúsítványok létrehozása című részben leírt lépéseket az [IoT Edge-eszközszolgáltatások teszteléséhez.](how-to-create-test-certificates.md) Végezze el a két szükséges szakaszt a tanúsítvány-létrehozási parancsfájlok beállításához és a legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozásához. Ezután kövesse az eszközidentitás-tanúsítvány létrehozásához szükséges lépéseket. Ha végzett, a következő tanúsítványlánccal és kulcspárral kell rendelkeznie:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Mindkét tanúsítványra szüksége van az IoT Edge-eszközön. Ha egyéni regisztrációt szeretne használni a DPS-ben, akkor feltölti a .cert.pem fájlt. Ha a DPS-ben csoportigénylést szeretne használni, akkor a feltöltéshez ugyanabban a megbízhatósági tanúsítványláncban lévő köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítványra is szüksége van. Ha bemutatótanúsítványokat használ, használja a `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` tanúsítványt a csoportos regisztrációhoz.

## <a name="create-a-dps-individual-enrollment"></a>Egyéni DPS-regisztráció létrehozása

A létrehozott tanúsítványok és kulcsok segítségével hozzon létre egy egyéni regisztráció a DPS egyetlen IoT Edge-eszköz. Az egyéni regisztrációk az eszköz identitástanúsítványának nyilvános részét egyeznek meg az eszközön lévő tanúsítvánnyal.

Ha több IoT Edge-eszközt szeretne kiépíteni, kövesse a következő, [DPS-csoportregisztráció létrehozása című szakasz lépéseit.](#create-a-dps-group-enrollment)

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van egy **kezdeti ikereszköz-állapot**deklarálni. Az ikereszközben beállíthatja az eszközök csoportosítását a megoldásban szükséges bármely mutató, például a régió, a környezet, a hely vagy az eszköz típusa szerint. Ezek a címkék az automatikus központi telepítések létrehozásához [használatosak.](how-to-deploy-monitor.md)

Az Eszközkiépítési szolgáltatásban történő regisztrációkról az [Eszközregisztrációk kezelése című témakörben](../iot-dps/how-to-manage-enrollments.md)talál további információt.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát.

1. A **Beállítások csoportban**válassza **a Regisztrációk kezelése**lehetőséget.

1. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd hajtsa végre a következő lépéseket a regisztráció konfigurálásához:  

   * **Mechanizmus**: Válassza az **X.509**lehetőséget.

   * **Elsődleges tanúsítvány .pem vagy .cer fájl**: Töltse fel a nyilvános fájlt az eszköz identitástanúsítványából. Ha a parancsfájlokat teszttanúsítvány létrehozásához használta, válassza ki a következő fájlt:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT Hub-eszközazonosító:** adja meg az eszköz azonosítóját, ha szeretné. Az eszközazonosítók segítségével megcélozhat egy adott eszközt a modul telepítéséhez. Ha nem ad meg eszközazonosítót, a készülék az X.509 tanúsítványban található köznapi nevet (CN) használja.

   * **IoT Edge-eszköz:** **Válassza az Igaz lehetőséget,** ha deklarálni szeretné, hogy a regisztráció egy IoT Edge-eszközhöz kapcsolódik.

   * **Válassza ki azokat az IoT-központokat, amelyekhez az eszköz hozzárendelhető:** Válassza ki azt a csatolt IoT-központot, amelyhez csatlakoztatni szeretné az eszközt. Több elosztót is kiválaszthat, és az eszköz a kiválasztott foglalási házirendnek megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz ikerállapot:** Adja hozzá a címke értékét hozzá kell adni az eszköz iker, ha szeretné. A címkék segítségével az eszközök csoportjait célozhatja meg az automatikus telepítéshez. Példa:

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

1. Kattintson a **Mentés** gombra.

Most, hogy egy regisztráció létezik ehhez az eszközhöz, az IoT Edge futásidejű automatikusan kiépítheti az eszközt a telepítés során. Folytassa az [IoT Edge futásidejű](#install-the-iot-edge-runtime) szakasz telepítése az IoT Edge-eszköz beállításához.

## <a name="create-a-dps-group-enrollment"></a>DPS-csoport igénylésének létrehozása

A létrehozott tanúsítványok és kulcsok segítségével hozzon létre egy csoport regisztráció dps több IoT Edge-eszközök. A csoportbeléptetések az egyes eszközidentitás-tanúsítványok létrehozásához használt megbízhatósági tanúsítványláncból származó köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítványt használnak.

Ha ehelyett egyetlen IoT Edge-eszközt szeretne létrehozni, kövesse az előző, [DPS egyéni regisztráció létrehozása](#create-a-dps-individual-enrollment)című szakaszlépéseit.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van egy **kezdeti ikereszköz-állapot**deklarálni. Az ikereszközben beállíthatja az eszközök csoportosítását a megoldásban szükséges bármely mutató, például a régió, a környezet, a hely vagy az eszköz típusa szerint. Ezek a címkék az automatikus központi telepítések létrehozásához [használatosak.](how-to-deploy-monitor.md)

### <a name="verify-your-root-certificate"></a>A főtanúsítvány ellenőrzése

Amikor létrehoz egy igénylési csoportot, lehetősége van ellenőrzött tanúsítvány használatára. A DPS-szel ellenőrizheti a tanúsítványt, ha igazolja, hogy rendelkezik a főtanúsítvány tulajdonjogával. További információ: [A birtoklás igazolása az X.509 hitelesítésszolgáltatói tanúsítványokhoz.](../iot-dps/how-to-verify-certificates.md)

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát.

1. Válassza a bal oldali menü **Tanúsítványok** parancsát.

1. Új tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. Adja meg a tanúsítvány rövid nevét, majd keresse meg az X.509 tanúsítvány nyilvános részét képviselő .cer vagy .pem fájlt.

   Ha a demó tanúsítványokat `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` használja, töltse fel a tanúsítványt.

1. Kattintson a **Mentés** gombra.

1. A tanúsítványnak most már szerepelnie kell a **Tanúsítványok** lapon. Jelölje ki a tanúsítvány részleteinek megnyitásához.

1. Válassza **az Ellenőrző kód létrehozása lehetőséget,** majd másolja a létrehozott kódot.

1. Akár saját hitelesítésszolgáltatói tanúsítványt hozott, akár a demótanúsítványokat használja, az IoT Edge-tárházban található ellenőrző eszközzel ellenőrizheti a birtoklás igazolását. Az ellenőrző eszköz a hitelesítésszolgáltatói tanúsítvány segítségével ír alá egy új tanúsítványt, amelynek tulajdonosneve a megadott ellenőrző kód.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Az Azure Portalon a tanúsítvány részletei lapon töltse fel az újonnan létrehozott ellenőrzési tanúsítványt.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="create-enrollment-group"></a>Igénylési csoport létrehozása

Az Eszközkiépítési szolgáltatásban történő regisztrációkról az [Eszközregisztrációk kezelése című témakörben](../iot-dps/how-to-manage-enrollments.md)talál további információt.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát.

1. A **Beállítások csoportban**válassza **a Regisztrációk kezelése**lehetőséget.

1. Válassza **a Beiktatási csoport hozzáadása** lehetőséget, majd hajtsa végre a következő lépéseket a regisztráció konfigurálásához:

   * **Csoportnév**: Adjon meg egy emlékezetes nevet a csoportos regisztrációhoz.

   * **Tanúsítvány típusa**: Válassza a **Tanúsítvány**lehetőséget.

   * **IoT Edge-eszköz:** Válassza az **Igaz**lehetőséget. Egy csoport regisztráció, minden eszköznek IoT Edge-eszközöknek kell lennie, vagy egyikük sem lehet.

   * **Tanúsítványtípus**: Válassza a **hitelesítésszolgáltatói tanúsítványt,** ha a Hitelesítésszolgáltatói tanúsítvány dps-szel van tárolva, vagy **köztes tanúsítványt,** ha új fájlt szeretne feltölteni ehhez a regisztrációhoz.

   * **Elsődleges tanúsítvány:** Ha az utolsó szakaszban a hitelesítésszolgáltatói tanúsítványt választotta, válassza ki a tanúsítványt a legördülő listából. Ha köztes tanúsítványt választott, töltse fel a nyilvános fájlt egy hitelesítésszolgáltatói tanúsítványból az eszközidentitás-tanúsítványok létrehozásához használt megbízhatósági láncba.

   * **Válassza ki azokat az IoT-központokat, amelyekhez az eszköz hozzárendelhető:** Válassza ki azt a csatolt IoT-központot, amelyhez csatlakoztatni szeretné az eszközt. Több elosztót is kiválaszthat, és az eszköz a kiválasztott foglalási házirendnek megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz ikerállapot:** Adja hozzá a címke értékét hozzá kell adni az eszköz iker, ha szeretné. A címkék segítségével az eszközök csoportjait célozhatja meg az automatikus telepítéshez. Példa:

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

1. Kattintson a **Mentés** gombra.

Most, hogy egy regisztráció létezik ehhez az eszközhöz, az IoT Edge futásidejű automatikusan kiépítheti az eszközt a telepítés során. Folytassa a következő szakaszban az IoT Edge-eszköz beállításához.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge futásidejű telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközre, így a kód a peremhálózaton futtatható.

Az X.509-es üzembe építés a DPS-szel csak az IoT Edge 1.0.9-es vagy újabb verziójában támogatott.

Az eszköz kiépítésekor a következő adatokra lesz szüksége:

* A **DPS-azonosító hatókörének** értéke. Ezt az értéket a DPS-példány áttekintő lapjáról az Azure Portalon lekérheti.
* Az eszköz identitástanúsítvány-fájlja az eszközön.
* Az eszköz identitáskulcs-fájlja az eszközön.
* Választható regisztrációs azonosító (az eszközidentitás-tanúsítvány köznapi nevéből lehívva, ha nincs megadva).

### <a name="linux-device"></a>Linux-eszköz

Az alábbi hivatkozássegítségével telepítse az Azure IoT Edge futásidejű az eszközön, az eszköz architektúrájának megfelelő parancsok használatával. Amikor a biztonsági démon konfigurálásáról szóló szakaszra kerül, konfigurálja az IoT Edge futásidejét az X.509 automatikus, nem manuális kiépítéséhez. A cikk előző szakaszainak kitöltése után rendelkeznie kell az összes szükséges információval és tanúsítványfájlval.

[Az Azure IoT Edge futásidejű telepítésélinuxra](how-to-install-iot-edge-linux.md)

Amikor hozzáadja az X.509 tanúsítványt és a legfontosabb információkat a config.yaml fájlhoz, az elérési utakat fájl URI-ként kell megadni. Példa:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

Az X.509 automatikus kiépítési konfigurációs fájljának szakasza a következőképpen néz ki:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Cserélje le a `scope_id`helyőrző értékeit a `identity_cert`, `identity_pk` a dps-példány hatókör-azonosítójára, valamint az URI-kra az eszközön lévő tanúsítvány- és kulcsfájl-helyekre. Adja `registration_id` meg az eszköz a, ha akarod, vagy hagyja ezt a sort kommentálta, hogy regisztrálja az eszközt a KN neve az identitástanúsítvány.

A config.yaml fájl frissítése után mindig indítsa újra a biztonsági démont.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows-eszköz

Telepítse az IoT Edge futásidejű az on device, amelyhez létrehozta az identitástanúsítvány és az identitáskulcs. Az IoT Edge-futásidejű automatikus, nem manuális, kiépítés konfigurálása.

Az IoT Edge Windows rendszeren történő telepítéséről, beleértve az olyan feladatok előfeltételeit és utasításait, mint a tárolók kezelése és az IoT Edge frissítése, [az Azure IoT Edge futásidejének telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakörben talál további információt.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Győződjön meg arról, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, ne a PowerShellt (x86).

1. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, majd letölti a moby futásidejű és az IoT Edge futásidejű. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Más Windows 10- vagy Windows Server-eszközök újraindítást kérhetnek. Ha igen, indítsa újra az eszközt most. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

1. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuális kiépítés, kivéve, ha a jelző t használja az `-Dps` automatikus kiépítés.

   Cserélje le a `{scope_id}`helyőrző értékeket a alkalmazáshoz, `{identity cert path}`valamint `{identity key path}` a DPS-példány megfelelő értékeire és az eszközön lévő fájlelérési utakra. Ha meg szeretné adni a regisztrációs `-RegistrationId {registration_id}` azonosítót, adja meg a helyőrzőt is.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >A config.yaml fájl fájlként tárolja a tanúsítványt és a kulcsadatokat. Az Initialize-IoTEdge parancs azonban kezeli ezt a formázási lépést, így megadhatja az eszközön lévő tanúsítvány és kulcsfájlok abszolút elérési útját.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futásidő sikeresen elindult, az IoT Hubba léphet, és elkezdheti az IoT Edge-modulok üzembe helyezését az eszközre.

Ellenőrizheti, hogy az eszközkiépítési szolgáltatásban létrehozott egyéni regisztrációt használta-e. Keresse meg az Eszközkiépítési szolgáltatás példányát az Azure Portalon. Nyissa meg a létrehozott egyéni beléptetési adatokat. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító szerepel a listában.

Az alábbi parancsokkal ellenőrizheti, hogy a futásidő sikeresen telepítve van-e, és sikeresen elindult-e.

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

## <a name="next-steps"></a>További lépések

Az eszközkiépítési szolgáltatás regisztrációs folyamat lehetővé teszi, hogy az eszközazonosítóés az ikercímkék beállítása az új eszköz kiépítésével egy időben. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával történő célzásához. Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat az Azure Portalon](how-to-deploy-monitor.md) vagy az [Azure CLI használatával.](how-to-deploy-monitor-cli.md)
