---
title: Eszközök automatikus kiépítése a DPS használatával X. 509 tanúsítványokkal – Azure IoT Edge | Microsoft Docs
description: Az X. 509 tanúsítványokkal tesztelheti a Azure IoT Edge automatikus eszköz-üzembe helyezését az eszközök kiépítési szolgáltatásával
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ccd8d383db265826d8644ee89d7300128fc3a350
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131308"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>IoT Edge-eszköz létrehozása és kiépítése X. 509 tanúsítványok használatával

Az [Azure IoT hub Device Provisioning Service (DPS)](../iot-dps/index.yml)segítségével az X. 509 tanúsítvánnyal automatikusan kiépítheti IoT Edge eszközeit. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítési fogalmakat](../iot-dps/concepts-auto-provisioning.md) .

Ebből a cikkből megtudhatja, hogyan hozhat létre egy eszköz-kiépítési szolgáltatást az X. 509 tanúsítványokkal egy IoT Edge eszközön a következő lépésekkel:

* Tanúsítványok és kulcsok generálása.
* Hozzon létre egy eszköz egyéni regisztrációját, vagy egy csoportos regisztrációt az eszközök készletén.
* Telepítse a IoT Edge futtatókörnyezetet, és regisztrálja az eszközt a IoT Hub.

Az X. 509 tanúsítványok az igazolási mechanizmusként való használata kiváló módszer a termelés méretezésére és az eszközök üzembe helyezésének egyszerűsítésére. Az X. 509 tanúsítványok általában megbízhatósági tanúsítványlánc szerint vannak rendezve. Egy önaláírt vagy megbízható főtanúsítványtól kezdve a lánc minden tanúsítványa aláírja a következő alacsonyabb tanúsítványt. Ez a minta létrehoz egy delegált megbízhatósági láncot a főtanúsítványból az egyes közbenső tanúsítványokon keresztül az eszközre telepített utolsó "levél" tanúsítványon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív IoT Hub.
* A IoT Edge eszköz fizikai vagy virtuális eszköze.
* A [git](https://git-scm.com/download/) legújabb verziója telepítve van.
* Az Azure-beli IoT Hub Device Provisioning Service egy példánya, amely az IoT hubhoz van csatolva.
  * Ha nincs eszköz kiépítési szolgáltatási példánya, kövesse az [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör utasításait.
  * Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="generate-device-identity-certificates"></a>Eszköz-azonosító tanúsítványok előállítása

Az eszköz-identitás tanúsítványa egy levélbeli tanúsítvány, amely egy megbízható tanúsítványlánc használatával csatlakozik az első X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz. Az eszköz identitási tanúsítványának köznapi névvel (CN) kell rendelkeznie ahhoz az eszköz-AZONOSÍTÓhoz, amelyhez az eszköznek az IoT hub-ban szüksége van.

Az eszköz-azonosító tanúsítványokat csak az IoT Edge eszköz kiépítési és az Azure-IoT Hub hitelesítő eszköz használatával lehet használni. A tanúsítványokat nem aláírják, ellentétben a IoT Edge eszköz által a moduloknak vagy a Leaf eszközöknek az ellenőrzés céljából megjelenített HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal. További információ: [Azure IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md).

Az eszköz azonosító tanúsítványának létrehozása után két fájlt kell tartalmaznia: egy. cer vagy. PEM fájlt, amely tartalmazza a tanúsítvány nyilvános részét, valamint egy. cer vagy. PEM fájlt a tanúsítvány titkos kulcsával. Ha azt tervezi, hogy csoportos regisztrációt használ a DPS-ben, akkor a közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nyilvános részét is meg kell adni ugyanabban a tanúsítványlánc-megbízhatósági láncban.

A következő fájlokra van szükség az automatikus kiépítés beállításához az X. 509 használatával:

* Az eszköz identitásának tanúsítványa és a titkos kulcs tanúsítványa. Ha egyéni regisztrációt hoz létre, a rendszer feltölti az eszköz Identity tanúsítványát a DPS-be. A titkos kulcsot a IoT Edge futtatókörnyezet adja át.
* Egy teljes láncú tanúsítvány, amelynek tartalmaznia kell legalább az eszköz identitását és a köztes tanúsítványokat. A rendszer átadja a teljes lánc tanúsítványát a IoT Edge futtatókörnyezetnek.
* Egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány a megbízhatósági tanúsítványlánc alapján. A tanúsítványt a rendszer feltölti a DPS-be, ha létrehoz egy csoportos regisztrációt.

### <a name="use-test-certificates"></a>Tesztelési tanúsítványok használata

Ha nincs elérhető hitelesítésszolgáltató az új identitási tanúsítványok létrehozásához, és szeretné kipróbálni ezt a forgatókönyvet, a Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével tesztelési tanúsítványokat hozhat létre. Ezek a tanúsítványok csak fejlesztési tesztelésre vannak kialakítva, és nem használhatók éles környezetben.

A tesztelési tanúsítványok létrehozásához kövesse a [bemutató tanúsítványok létrehozása a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md)című témakör lépéseit. A tanúsítvány-létrehozási parancsfájlok beállításához és a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához hajtsa végre a két szükséges szakaszt. Ezután kövesse az eszköz-identitás tanúsítványának létrehozásához szükséges lépéseket. Ha elkészült, a következő tanúsítványláncot és kulcspárt kell használnia:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Mindkét tanúsítványra szükség van a IoT Edge eszközön. Ha egyéni regisztrációt fog használni a DPS-ben, akkor fel kell töltenie a. CERT. PEM fájlt. Ha a DPS szolgáltatásban csoportos regisztrációt fog használni, akkor a feltöltéshez ugyanahhoz a tanúsítványlánchez is szüksége lesz egy közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra. Ha bemutató tanúsítványokat használ, használja a `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` tanúsítványt a csoportos regisztrációhoz.

## <a name="create-a-dps-individual-enrollment"></a>Egyéni DPS-regisztráció létrehozása

A generált tanúsítványok és kulcsok használatával egyéni regisztrációt hozhat létre a DPS-ben egyetlen IoT Edge eszközhöz. Az egyéni regisztrációk az eszköz személyazonossági tanúsítványának nyilvános részét képezik, és megfelelnek az eszköz tanúsítványának.

Ha több IoT Edge eszközt szeretne kiépíteni, kövesse a következő szakaszban leírt lépéseket, és [hozzon létre egy DPS csoportos regisztrációt](#create-a-dps-group-enrollment).

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

További információ az eszközök kiépítési szolgáltatásának regisztrálásáról: az [eszközök regisztrálásának kezelése](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > Az Azure CLI-ben létrehozhat egy [beléptetést](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [beléptetési csoportot](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , és az **Edge-kompatibilis** jelző használatával meghatározhatja, hogy egy eszköz vagy egy csoport IoT Edge eszköz-e.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások**területen válassza a **regisztrációk kezelése**lehetőséget.

1. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   * **Mechanizmus**: válassza az **X. 509**elemet.

   * **Elsődleges tanúsítvány. PEM vagy. cer fájl**: töltse fel a nyilvános fájlt az eszköz identitása tanúsítványból. Ha a parancsfájlokat a tesztelési tanúsítvány létrehozásához használta, válassza a következő fájlt:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT hub eszköz azonosítója**: adja meg az eszköz azonosítóját, ha szeretné. Az eszközök azonosítói segítségével megcélozhat egy különálló eszközt a modulok telepítéséhez. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer az X. 509 tanúsítvány köznapi nevét (CN) használja.

   * **IoT Edge eszköz**: a **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a beléptetés egy IoT Edge eszközre vonatkozik.

   * **Válassza ki az eszközhöz hozzárendelni kívánt IoT hubokat**: válassza ki azt a társított IoT hubot, amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz kettős állapota**: adjon hozzá egy címke értéket, amelyet hozzá szeretne adni az eszközhöz, ha szeretné. Az automatikus központi telepítéshez címkéket használhat az eszközök célcsoportjának célzásához. Például:

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

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. A IoT Edge eszköz beállításához folytassa a [IoT Edge futtatókörnyezet telepítése](#install-the-iot-edge-runtime) című szakaszát.

## <a name="create-a-dps-group-enrollment"></a>DPS csoportos regisztráció létrehozása

A generált tanúsítványok és kulcsok használatával csoportos regisztrációt hozhat létre a DPS-ben több IoT Edge eszközhöz. A csoportos regisztrációk egy köztes vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használnak az egyes eszköz-azonosító tanúsítványok létrehozásához használt megbízhatósági tanúsítványlánc alapján.

Ha ehelyett egyetlen IoT Edge eszközt szeretne kiépíteni, kövesse az előző szakaszban ismertetett lépéseket, [hozzon létre egy DPS egyéni regisztrációt](#create-a-dps-individual-enrollment).

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-at-scale.md)létrehozásához használhatók.

### <a name="verify-your-root-certificate"></a>A főtanúsítvány ellenőrzése

Beléptetési csoport létrehozásakor lehetősége van ellenőrzött tanúsítvány használatára. A DPS-tanúsítvánnyal ellenőrizheti, hogy rendelkezik-e a főtanúsítvány tulajdonjogával. További információ: [How to do proof of-of-of-of-the-of-for X. 509 hitelesítésszolgáltatói tanúsítványok](../iot-dps/how-to-verify-certificates.md).

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. Válassza ki a **tanúsítványok** elemet a bal oldali menüben.

1. Új tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. Adjon egy rövid nevet a tanúsítványnak, majd keresse meg az X. 509 tanúsítvány nyilvános részét jelképező. cer vagy. PEM fájlt.

   Ha a bemutató tanúsítványait használja, töltse fel a `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` tanúsítványt.

1. Kattintson a **Mentés** gombra.

1. A tanúsítványnak mostantól szerepelnie kell a **tanúsítványok** lapon. Válassza ki a tanúsítvány adatainak megnyitásához.

1. Válassza az **ellenőrző kód előállítása** lehetőséget, majd másolja a generált kódot.

1. Akár a saját HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát, akár a bemutató tanúsítványait használja, a IoT Edge adattárában található ellenőrzési eszközzel ellenőrizheti a birtoklás igazolását. Az ellenőrző eszköz a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja egy olyan új tanúsítvány aláírására, amely a megadott ellenőrző kóddal rendelkezik tulajdonos neveként.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. A Azure Portal azonos tanúsítvány részletei lapján töltse fel az újonnan létrehozott ellenőrző tanúsítványt.

1. Válassza az **Ellenőrzés** lehetőséget.

### <a name="create-enrollment-group"></a>Beléptetési csoport létrehozása

További információ az eszközök kiépítési szolgáltatásának regisztrálásáról: az [eszközök regisztrálásának kezelése](../iot-dps/how-to-manage-enrollments.md).

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

1. A **Beállítások**területen válassza a **regisztrációk kezelése**lehetőséget.

1. Válassza a **regisztrációs csoport hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:

   * **Csoport neve**: adjon meg egy emlékezetes nevet a csoport beléptetéséhez.

   * **Igazolás típusa**: válassza a **tanúsítvány**elemet.

   * **IoT Edge eszköz**: válassza az **igaz**lehetőséget. Csoportos regisztráció esetén minden eszköznek IoT Edge eszköznek kell lennie, vagy egyikük sem lehet.

   * **Tanúsítvány típusa**: válassza ki a **hitelesítésszolgáltatói tanúsítvány** elemet, ha olyan hitelesített hitelesítésszolgáltatói tanúsítvánnyal rendelkezik, amely DPS vagy **köztes tanúsítvánnyal** rendelkezik, ha új fájlt szeretne feltölteni ehhez a regisztrációhoz.

   * **Elsődleges tanúsítvány**: Ha az utolsó szakaszban a hitelesítésszolgáltatói tanúsítvány lehetőséget választotta, válassza ki a tanúsítványt a legördülő listából. Ha a köztes tanúsítványt választotta, töltse fel a nyilvános fájlt egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból az eszköz identitási tanúsítványainak létrehozásához használt megbízhatósági láncban.

   * **Válassza ki az eszközhöz hozzárendelni kívánt IoT hubokat**: válassza ki azt a társított IoT hubot, amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   * **Kezdeti eszköz kettős állapota**: adjon hozzá egy címke értéket, amelyet hozzá szeretne adni az eszközhöz, ha szeretné. Az automatikus központi telepítéshez címkéket használhat az eszközök célcsoportjának célzásához. Például:

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

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során. Folytassa a következő szakasszal a IoT Edge eszköz beállításához.

## <a name="install-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható.

Az X. 509 kiépítés a DPS-vel csak IoT Edge 1.0.9 vagy újabb verzióban támogatott.

Az eszköz kiépítés során a következő információkra lesz szüksége:

* A DPS- **azonosító hatókörének** értéke. Ezt az értéket az Azure Portal DPS-példányának áttekintés lapjáról kérheti le.
* Az eszköz Identity Certificate Chain fájlja az eszközön.
* Az eszköz Identity Key fájlja az eszközön.
* Egy opcionális regisztrációs azonosító (ha nincs megadva a köznapi név az eszköz identitásának tanúsítványa).

### <a name="linux-device"></a>Linuxos eszköz

A következő hivatkozásra kattintva telepítheti az eszközön a Azure IoT Edge futtatókörnyezetet az eszköz architektúrájának megfelelő parancsok használatával. Amikor megkapja a biztonsági démon konfigurálásának szakaszát, állítsa be a IoT Edge Runtime X. 509 automatikus, nem manuális, kiépítés beállítását. A jelen cikk előző részeinek befejezése után minden szükséges információhoz és tanúsítványfájl-fájlhoz szükség van.

[A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)

Ha hozzáadja az X. 509 tanúsítványt és a legfontosabb adatokat a config. YAML fájlhoz, az elérési utakat fájl URI-ként kell megadni. Például:

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

Az X. 509 formátumú, automatikus kiépítés konfigurációs fájljának szakasza így néz ki:

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

Cserélje le a (z `scope_id`) `identity_cert`, `identity_pk` , és a hatókör-azonosító értékét a (z), és az URI-k között a tanúsítvány-láncra és a kulcsfájl helyére az eszközön. Ha szeretné `registration_id` , adjon meg egy eszközt az eszközhöz, vagy hagyja meg ezt a sort, hogy regisztrálja az eszközt az identitási tanúsítvány CN-nevével.

A config. YAML fájl frissítése után mindig indítsa újra a biztonsági démont.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows-eszköz

Telepítse a IoT Edge futtatókörnyezetet azon az eszközön, amelyhez létrehozta az Identity Certificate láncot és a személyazonossági kulcsot. A IoT Edge futtatókörnyezetet automatikus, nem manuális, kiépítés esetén kell konfigurálnia.

A IoT Edge Windows rendszeren való telepítésével kapcsolatos további információkért, beleértve a tárolók kezeléséhez és a IoT Edge frissítéséhez szükséges feladatok előfeltételeit és utasításait lásd: [a Azure IoT Edge futtatókörnyezet telepítése Windows](how-to-install-iot-edge-windows.md)rendszeren.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime és a IoT Edge futtatókörnyezetet. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítását kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a manuális kiépítés, kivéve, ha `-Dps` a jelzőt használja az automatikus kiépítés használatára.

   Cserélje le a `{scope_id}`, `{identity cert chain path}`a és `{identity key path}` a érték helyőrzőjét a DPS-példány megfelelő értékeire, valamint az eszköz fájlelérési útjaira. Ha a regisztrációs azonosítót is meg szeretné adni, `-RegistrationId {registration_id}` akkor a helyőrzőt a megfelelő módon cserélje le.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >A config. YAML fájl a tanúsítvány és a kulcs adatait fájl URI-ként tárolja. Az inicializálási IoTEdge parancs azonban kezeli ezt a formázási lépést, így megadhatja az eszközön a tanúsítvány és a kulcs fájljainak abszolút elérési útját.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és megkezdheti a IoT Edge modulok üzembe helyezését az eszközön.

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

Az eszközön az alábbi parancsokkal ellenőrizheti, hogy a futtatókörnyezet telepítése és elindítása sikeresen megtörtént-e.

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

## <a name="next-steps"></a>További lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-at-scale.md) [Azure CLI használatával](how-to-deploy-cli-at-scale.md).
