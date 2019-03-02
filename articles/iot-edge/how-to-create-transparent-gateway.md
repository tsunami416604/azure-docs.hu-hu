---
title: Transzparens átjáró eszköz - létrehozása az Azure IoT Edge |} A Microsoft Docs
description: Az Azure IoT Edge-eszköz használata, amely képes feldolgozni az információkat az alsóbb rétegbeli eszközök transzparens átjáróként
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7e196a912db723d5d118b0aadc98f73f1c7271e0
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243754"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>A transzparens átjáróként működő IoT Edge-eszköz konfigurálása

Ez a cikk részletes utasításokat IoT Edge-eszközökön az IoT hubbal való kommunikációhoz más eszközök esetében transzparens átjáróként működik. Ez a cikk az előfizetési időszak *IoT Edge-átjáró* IoT Edge-eszköz transzparens átjáróként használt hivatkozik. Részletesebb információkért lásd: [hogyan az IoT Edge-eszközt átjáróként használható](./iot-edge-as-gateway.md), révén fogalmi áttekintése.

>[!NOTE]
>Jelenleg:
> * Ha az átjáró nem csatlakozik az IoT hubról, alsóbb rétegbeli eszközök az átjáró nem tudja hitelesíteni.
> * Edge-kompatibilis eszközök IoT Edge-átjáró nem tud kapcsolódni. 
> * Alsóbb rétegbeli eszközök fájl feltöltése nem használható.

Egy eszközhöz átjáróként működik, képesnek kell lennie, biztonságosan csatlakozhat az alsóbb rétegbeli eszközök. Az Azure IoT Edge lehetővé teszi, hogy a nyilvános kulcsokra épülő infrastruktúrájú (PKI) eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni. Ésszerű biztonságának fenntartása érdekében az alsóbb rétegbeli eszközök ellenőrizze identitását a peremhálózati eszköz, mivel csak az eszközök csatlakoztatása az átjárók és a egy potenciálisan kártékony átjáró nincs.

Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Gyakorlati okokból azonban egy alsóbb rétegbeli eszközök az IoT Edge-átjáróeszköz magát a futó alkalmazás még akkor is lehet. 

Minden olyan tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk feltételezzük, hogy az azonos tanúsítvány beállítása, hogy engedélyezni szeretné használni [x.509-es Hitelesítésszolgáltatói biztonsági](../iot-hub/iot-hub-x509ca-overview.md) az IoT Hub, amelyek egy adott IoT hub (az IoT hub tulajdonos CA), és a egy sorozat tanúsítványok társított X.509 Hitelesítésszolgáltatói tanúsítvány, a hitelesítésszolgáltató és hitelesítésszolgáltató aláírt az Edge-eszköz számára.

![Átjáró tanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

Az átjáró a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványát az alsóbb rétegbeli eszközre során a kapcsolat kezdeményezése mutat be. Az alsóbb rétegbeli eszköz ellenőrzi, hogy a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványt a tulajdonos Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszközök annak ellenőrzéséhez, hogy az átjáró egy megbízható forrásból származik.

A következő lépések végigvezetik a folyamat a tanúsítványok létrehozását, és telepíti őket a megfelelő helyeken.

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Edge-eszköz konfigurálásához átjáróként. Használhatja az IoT Edge-eszköz a lépéseket az a következő operációs rendszereken a fejlesztői gépén vagy egy virtuális gépet:
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Minden olyan gép, használja a tanúsítványok létrehozásához, és másolja őket keresztül az IoT Edge-eszköz.

>[!NOTE]
>Ezeket az utasításokat a tanúsítványok létrehozásához használja az "átjáró neve" kell lennie a neve megegyezik használt állomásnév az IoT Edge config.yaml fájlban és a kapcsolati karakterláncban az alsóbb rétegbeli eszköz GatewayHostName. A "gateway neve" kell lennie az IP-címet, vagy DNS- vagy gazdafájlbejegyzéssel használatával oldható fel. Kommunikáció a használt protokoll alapján (MQTTS:8883 / AMQPS:5671 / HTTPS:433) lehetséges alsóbb rétegbeli eszközök és az IoT Edge transparant között kell lennie. Ha tűzfal a kettő között, a megfelelő portot kell lennie nyitva.

## <a name="generate-certificates-with-windows"></a>Windows-tanúsítványok létrehozása

Ebben a szakaszban a lépések segítségével hozza létre teszttanúsítványokat egy Windows-eszközön. A tanúsítványok létrehozása az IoT Edge-eszközén, vagy használjon egy külön számítógépen, és másolja a végső tanúsítványok bármely támogatott operációs rendszert futtató bármely IoT Edge-eszköz. 

Ebben a szakaszban létrehozott tanúsítványokat csak tesztelési célokra szolgálnak. 

### <a name="install-openssl"></a>OpenSSL telepítése

OpenSSL for Windows telepítése a gépen, amely a tanúsítványok létrehozásához használ. OpenSSL telepítése számos módja van:

   >[!NOTE]
   >Ha már telepítve van az eszközén Windows OpenSSL, kihagyhatja ezt a lépést, de győződjön meg arról, hogy openssl.exe a PATH környezeti változóban érhető el.

* **Egyszerűbb:** Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [a projektet a SourceForge](https://sourceforge.net/projects/openssl/). Adja hozzá a teljes elérési útját a PATH környezeti változóba openssl.exe. 
   
* **Ajánlott:** Töltse le az OpenSSL-forráskódot, és a bináris fájlok felépítéséhez a számítógépen, saját magának vagy keresztül [vcpkg](https://github.com/Microsoft/vcpkg). Az alábbi utasításokat vcpkg használatával töltse le a forráskódot, fordítási és OpenSSL telepítése a Windows-gépen egyszerű lépésben.

   1. Navigáljon ahhoz a könyvtárhoz, ahol vcpkg telepíteni szeretné. Ez a könyvtár, mint kifejezés  *\<VCPKGDIR >*. Töltse le és telepítse az utasítások [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. A powershell parancssorba vcpkg telepítése után a következő parancsot az OpenSSL csomagot telepítse a Windows x64. A telepítés általában befejezéséhez körülbelül 5 percet vesz igénybe.

      ```PowerShell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adjon hozzá `<VCPKGDIR>\installed\x64-windows\tools\openssl` útját a PATH környezeti változóba, hogy a openssl.exe fájl elindításaihoz érhető el.

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

A c nyelvhez készült Azure IoT eszközoldali SDK-t tartalmaz parancsprogramokat, amelyek segítségével hozzon létre teszttanúsítványokat. Ebben a szakaszban klónozza az SDK-t, és a PowerShell konfigurálása.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. 

2. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. Használja a `git clone` parancs vagy [töltse le a zip-fájl](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ez a könyvtár, mint kifejezés  *\<WRKDIR >*.  Minden fájl ebben a címtárban létrejön.

4. Másolja a konfigurációs és a parancsfájl a munkakönyvtárban. 

   ```PowerShell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha az SDK zip letöltött, akkor a mappa neve `azure-iot-sdk-c-master` és az elérési út a megegyezik. 

5. Állítsa be a környezeti változó OPENSSL_CONF használhat a openssl_root_ca.cnf konfigurációs fájlt.

    ```PowerShell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Engedélyezze a PowerShell a parancsprogramok futtatásához.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. A PowerShell a globális névtérhez a parancsfájlok által használt funkciók használata.
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

8. Győződjön meg arról, hogy OpenSSL megfelelően telepítve, és ellenőrizze, hogy nem jár együtt név ütközések a meglévő tanúsítványok. Ha probléma adódik, a parancsfájl kell azt ismertetik, hogyan javíthatók a rendszeren.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványokat hoznak létre, és csatlakoztassa őket a láncban. Helyezi el a tanúsítványokat a lánc fájl lehetővé teszi, hogy az IoT Edge-átjáróeszköz és alsóbb rétegbeli eszközök egyszerűen telepítse őket.  

1. Hozza létre a tulajdonos Hitelesítésszolgáltatói tanúsítványt és annak egy közbenső tanúsítvány aláírása. A tanúsítványok összes kerülnek  *\<WRKDIR >*.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

2. A peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványok és titkos kulcs létrehozása a következő paranccsal. Adjon meg egy nevet, az átjáró eszköz, amelyet használhat a fájlokat, és a tanúsítvány létrehozása során. 

   ```PowerShell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. A Hitelesítésszolgáltatói tanúsítvány tulajdonosa, a köztes tanúsítványt és a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány a következő paranccsal hozzon létre egy tanúsítványláncra. 

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   A szkript a következő tanúsítványok és a kulcsot hoz létre:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>A Linux és a tanúsítványok előállítása

Ebben a szakaszban a lépések segítségével hozza létre teszttanúsítványokat egy Linux rendszerű eszközön. A tanúsítványok létrehozása az IoT Edge-eszközén, vagy használjon egy külön számítógépen, és másolja a végső tanúsítványok bármely támogatott operációs rendszert futtató bármely IoT Edge-eszköz. 

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

1. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ez a könyvtár, mint kifejezés  *\<WRKDIR >*.  Minden fájl ebben a címtárban létrejön.
  
3. Másolja a konfigurációs és a parancsfájl-fájlt a munkakönyvtárban.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. A megadott szkripttel tanúsítványainak létrehozásához szükséges OpenSSL konfigurálása. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványokat hoznak létre, és csatlakoztassa őket a láncban. Lánc fájlba helyezi el a tanúsítványokat is könnyedén telepítheti őket az IoT Edge-átjáróeszköz és alsóbb rétegbeli eszközök lehetővé teszi.  

1.  Hozzon létre a tulajdonos Hitelesítésszolgáltatói tanúsítvány és a egy köztes tanúsítványt. Ezek a tanúsítványok vannak elhelyezve  *\<WRKDIR >*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   A szkript a következő tanúsítványok és kulcsok hoz létre:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2.  A peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványok és titkos kulcs létrehozása a következő paranccsal. Adjon meg egy nevet, az átjáró eszköz, amelyet használhat a fájlokat, és a tanúsítvány létrehozása során. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   A szkript a következő tanúsítványok és a kulcsot hoz létre:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Nevű tanúsítványláncolat **új – edge-eszközök – teljes-chain.cert.pem** a tulajdonos Hitelesítésszolgáltatói tanúsítvány, a köztes tanúsítványt és a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Az átjáró tanúsítványok telepítése

Most, hogy egy tanúsítványlánc végrehajtott, szüksége az IoT Edge-átjáróeszköz telepítheti és konfigurálhatja az IoT Edge-futtatókörnyezet, az új tanúsítványok hivatkozni. 

1. A következő fájlokat másolja  *\<WRKDIR >*. Mentse ezeket bárhol az IoT Edge-eszközön. A cél könyvtárát, az IoT Edge-eszköz használata a kifejezés  *\<CERTDIR >*. 

   Ha maga az Edge-eszköz a tanúsítványokkal létrehozott, kihagyhatja ezt a lépést, és a munkakönyvtárban elérési utat használja.

   * Eszköz CA-tanúsítvány –  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `<WRKDIR>\private\new-edge-device.key.pem`
   * CA - tulajdonos `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Nyissa meg az IoT Edge biztonsági démon konfigurációs fájlt. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Állítsa be a **tanúsítvány** config.yaml fájlban az elérési úthoz, amelyre helyezte az IoT Edge-eszközön a tanúsítvány és kulcs fájlok tulajdonságait.

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub

Amikor először telepíti az IoT Edge-eszközön, a rendszer csak egy modul automatikusan elindul: az Edge agent. Az eszköz átjáróként működik mindkét rendszer modult kell. Ha az átjáró eszköz előtt kapcsolt modulok még nem telepítette, indítsa el a második rendszer modul, az Edge hub az eszköz telepítésének létrehozása. Az üzembe helyezés fognak kinézni üres, mert nem adja hozzá a szükséges modulok a varázslóban, de elvégzi az üzembe helyezést mindkét rendszer modulok. 

Ellenőrizheti, hogy melyik modulokat futtatják egy eszközön, a parancs `iotedge list`.

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen a **IoT Edge** , és válassza ki az IoT Edge-eszközt, hogy az átjáró használni kívánt.

3. Válassza a **Modulok beállítása** lehetőséget.

4. Kattintson a **Tovább** gombra.

5. Az a **útvonalak megadása** lap, rendelkeznie kell egy alapértelmezett útvonalat, amely az összes üzenetet küld az összes modulok az IoT hubnak. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Az a **sablon áttekintése** lapon jelölje be **küldés**.

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítását az alsóbb rétegbeli eszközök
Az IoT Edge-futtatókörnyezet továbbíthatnak hasonlóan modulok által küldött üzenetek alsóbb rétegbeli eszközök által küldött üzeneteket. Ez lehetővé teszi az átjáró futó, mielőtt bármilyen adatot küld a felhő modulban elemzés végrehajtásához. 

Irányíthatja a alsóbb rétegbeli eszközök által küldött üzenetek módon jelenleg megkülönböztetve azokat az üzeneteket a modulok által. Minden modulok által küldött üzeneteket tartalmaznak rendszer tulajdonsággal **connectionModuleId** , de az alsóbb rétegbeli eszközök által küldött üzenetek viszont nem. Az útvonal a WHERE záradék használatával bármilyen üzenetet a rendszer tulajdonságot tartalmazó kizárása. 

Az útvonal alatt használni kívánt bármely alsóbb rétegbeli eszközök üzeneteket küldeni egy modulnév `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Üzenet-útválasztással kapcsolatos további információkért lásd: [hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>További lépések

Most, hogy IoT Edge-eszköz transzparens átjáróként működik, az átjáró megbízhatósági és üzenetek küldése az alsóbb rétegbeli eszközök konfigurálásához szüksége. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).
