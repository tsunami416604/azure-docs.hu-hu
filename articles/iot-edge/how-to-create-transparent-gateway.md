---
title: Transzparens átjáró eszköz - létrehozása az Azure IoT Edge |} A Microsoft Docs
description: Az Azure IoT Edge-eszköz használata, amely képes feldolgozni az információkat az alsóbb rétegbeli eszközök transzparens átjáróként
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058378"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>A transzparens átjáróként működő IoT Edge-eszköz konfigurálása

Ez a cikk részletes utasításokat az IoT Edge-eszközöket az IoT hubbal való kommunikációhoz más eszközök esetében transzparens átjáróként működik. Ez a cikk az előfizetési időszak *IoT Edge-átjáró* IoT Edge-eszköz transzparens átjáróként használt hivatkozik. További információkért lásd: [hogyan az IoT Edge-eszközt átjáróként használható](./iot-edge-as-gateway.md).

>[!NOTE]
>Jelenleg:
> * Edge-kompatibilis eszközök IoT Edge-átjáró nem tud kapcsolódni. 
> * Alsóbb rétegbeli eszközök fájl feltöltése nem használható.

Transzparens átjáró sikeres kapcsolat beállítása három általános lépésből áll. Ez a cikk ismerteti az első lépés:

1. **Az átjáró eszköz képesnek kell lennie, biztonságosan csatlakozhat az alsóbb rétegbeli eszközök, fogadhatják a Microsofttól alsóbb rétegbeli eszközök és irányíthatja az üzenetek a megfelelő célhelyre.**
2. Az alsóbb rétegbeli eszköz rendelkeznie kell egy eszközidentitást az IoT Hub a hitelesítéshez, és tudja, hogy az átjáró eszköz keresztül kommunikálnak. További információkért lásd: [hitelesítése egy alsóbb rétegbeli eszközök Azure IoT hubra](how-to-authenticate-downstream-device.md).
3. Az alsóbb rétegbeli eszközök képesnek kell lennie, biztonságosan csatlakozhat a átjáróeszközt. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).


Egy eszközt átjáróként működik, az azt képesnek kell lennie az alsóbb rétegbeli eszközök biztonságosan kapcsolódhat. Az Azure IoT Edge lehetővé teszi, hogy a nyilvános kulcsokra épülő infrastruktúrájú (PKI) eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni. Ésszerű biztonságának fenntartása érdekében az alsóbb rétegbeli eszközök ellenőrizze az átjáró eszköz identitását. Ez az identitás-ellenőrzés megakadályozza, hogy az eszközök potenciálisan kártékony átjáró csatlakozik.

Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Gyakorlati okokból azonban egy alsóbb rétegbeli eszközök az IoT Edge-átjáróeszköz magát a futó alkalmazás még akkor is lehet. 

Minden olyan tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk feltételezzük, hogy az azonos tanúsítvány beállítása, hogy engedélyezni szeretné használni [x.509-es Hitelesítésszolgáltatói biztonsági](../iot-hub/iot-hub-x509ca-overview.md) az IoT Hub, amely magában foglalja egy adott IoT hub (az IoT hub legfelső szintű hitelesítésszolgáltató), egy aláírt tanúsítvány sorozatát társított X.509 Hitelesítésszolgáltatói tanúsítvány a hitelesítésszolgáltató és a egy Hitelesítésszolgáltatót az IoT Edge-eszköz.

![Átjáró tanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>A "legfelső szintű hitelesítésszolgáltató" Ez a cikk során használt kifejezés a nyilvános kulcsokra épülő infrastruktúra tanúsítványlánc legfelső hatóság nyilvános tanúsítványát, és nem feltétlenül szindikálási hitelesítésszolgáltató tanúsítvány gyökerében. Sok esetben ténylegesen egy közbenső hitelesítésszolgáltató nyilvános tanúsítványát. 

Az átjáró eszköz Hitelesítésszolgáltatói tanúsítvány az alsóbb rétegbeli eszközök megadja az IoT Edge során a kapcsolatot a kezdeményezéséről. Az alsóbb rétegbeli eszköz ellenőrzi, hogy az IoT Edge-eszköz hitelesítésszolgáltató tanúsítványát a legfelső szintű Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a folyamat lehetővé teszi az alsóbb rétegbeli eszköz győződjön meg arról, hogy az átjáró egy megbízható forrásból származik-e.

A következő lépések végigvezetik a folyamat a tanúsítványok létrehozását, és telepíti őket a megfelelő helyeken az átjárón. Minden olyan gép, használja a tanúsítványok létrehozásához, és másolja őket keresztül az IoT Edge-eszköz. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Edge-eszköz konfigurálásához átjáróként. Az IoT Edge telepítési lépéseket a következő operációs rendszerek egyikét használhatja:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Ez a cikk hivatkozik a *átjáró állomásnév* különböző időpontokban. Az átjáró állomásnév van deklarálva a **állomásnév** paraméter az IoT Edge-átjáróeszköz config.yaml fájl. Ebben a cikkben a tanúsítványok létrehozására szolgál, és az alsóbb rétegbeli eszközök a kapcsolati karakterlánc neve. Az átjáró állomásnevet kell lennie az IP-címet, vagy DNS- vagy gazdafájlbejegyzéssel használatával oldható fel.

## <a name="generate-certificates-with-windows"></a>Windows-tanúsítványok létrehozása

Ebben a szakaszban a lépések segítségével hozza létre teszttanúsítványokat a Windows. Egy Windows-számítógép használatával is létrehozhat a tanúsítványokat, és másolja őket keresztül bármely IoT Edge-eszköz bármelyik támogatott operációs rendszert futtat. 

Ebben a szakaszban létrehozott tanúsítványokat csak tesztelési célokra szolgálnak. 

### <a name="install-openssl"></a>OpenSSL telepítése

OpenSSL for Windows telepítése a gépen, amely a tanúsítványok létrehozásához használ. Ha már telepítve van az eszközén Windows OpenSSL, előfordulhat, hogy kihagyja ezt a lépést, de győződjön meg arról, hogy openssl.exe a PATH környezeti változóban érhető el. 

OpenSSL telepítése számos módja van:

* **Egyszerűbb:** Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [SourceForge az OpenSSL](https://sourceforge.net/projects/openssl/). Adja hozzá a teljes elérési útját a PATH környezeti változóba openssl.exe. 
   
* **Ajánlott:** Töltse le az OpenSSL-forráskódot, és a bináris fájlok felépítéséhez a számítógépen, saját magának vagy keresztül [vcpkg](https://github.com/Microsoft/vcpkg). Az alábbi utasításokat vcpkg használatával töltse le a forráskódot, fordítási és OpenSSL telepítése a Windows-gépen egyszerű lépésben.

   1. Navigáljon ahhoz a könyvtárhoz, ahol vcpkg telepíteni szeretné. Ez a könyvtár, mint kifejezés  *\<VCPKGDIR >* . Töltse le és telepítse az utasítások [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Egyszer vcpkg telepítve van, a következő parancsot a OpenSSL-csomag telepítéséhez a x64 Windows powershell parancssorból. A telepítés általában befejezéséhez körülbelül 5 percet vesz igénybe.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adjon hozzá `<VCPKGDIR>\installed\x64-windows\tools\openssl` útját a PATH környezeti változóba, hogy a openssl.exe fájl elindításaihoz érhető el.

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

Az Azure IoT Edge git-tárház tartalmaz parancsprogramokat, amelyek segítségével hozzon létre teszttanúsítványokat. Ez a szakasz az IoT Edge-tárház klónozása, és hajtsa végre a parancsfájlok. 

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. 

2. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. Használja a `git clone` parancs vagy [töltse le a zip-fájl](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ez a cikk során ez a könyvtár Felhívjuk  *\<WRKDIR >* . Az összes tanúsítványt és kulcsot a munkakönyvtárban hozható létre.

4. A klónozott adattárra a munkakönyvtárban konfigurációs és parancsfájl másolása. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha letöltötte a ZIP-adattárat, akkor a mappa neve `iotedge-master` és az elérési út a megegyezik. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Engedélyezze a PowerShell a parancsprogramok futtatásához.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. A PowerShell a globális névtérben, a parancsfájlok által használt funkciók használata.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A PowerShell-ablakot, hogy csak tesztelési célokat szolgálnak. Ez a szkript által létrehozott tanúsítványokat, és nem használható éles üzemi helyzetekben figyelmeztetést jelenítenek meg.

8. Ellenőrizze, hogy OpenSSL megfelelően telepítve, és győződjön meg arról, hogy nem jár együtt név ütközések a meglévő tanúsítványok. Ha probléma adódik, a parancsfájl kell azt ismertetik, hogyan javíthatók a rendszeren.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványokat hoznak létre, és csatlakoztassa őket a láncban. Helyezi el a tanúsítványokat a lánc fájl lehetővé teszi, hogy az IoT Edge-átjáróeszköz és alsóbb rétegbeli eszközök egyszerűen telepítse őket.  

1. Hozzon létre a legfelső szintű Hitelesítésszolgáltatói tanúsítványt és annak egy közbenső tanúsítvány aláírása. A tanúsítványok a munkakönyvtárban kerülnek.

   ```powershell
   New-CACertsCertChain rsa
   ```

   A parancsprogram-utasítás létrehoz néhány tanúsítvány és kulcs fájljai, de tekintse meg a megfelelőt, különösen a cikk későbbi részében lesz:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Hozzon létre az IoT Edge eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcs a következő paranccsal. Adja meg az átjáró állomásnév, az átjáró eszköz iotedge\config.yaml fájlban találja. Az átjáró állomásnevet használja a fájlokat és a tanúsítvány létrehozása során. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   A parancsprogram-utasítás létrehoz néhány tanúsítvány és kulcs fájlt, beleértve két tekintse meg a cikk későbbi részében fogunk:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Most, hogy a tanúsítványok, folytassa a [tanúsítványok az átjáró telepítése](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>A Linux és a tanúsítványok előállítása

Ebben a szakaszban a lépések segítségével hozza létre teszttanúsítványokat Linux rendszeren. Linux rendszerű gépen használatával is létrehozhat a tanúsítványokat, és másolja őket keresztül bármely IoT Edge-eszköz bármelyik támogatott operációs rendszert futtat. 

Ebben a szakaszban létrehozott tanúsítványokat csak tesztelési célokra szolgálnak. 

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

Az Azure IoT Edge git-tárház tartalmaz parancsprogramokat, amelyek segítségével hozzon létre teszttanúsítványokat. Ez a szakasz az IoT Edge-tárház klónozása, és hajtsa végre a parancsfájlok. 

1. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ez a könyvtár teljes, a cikk kifejezés  *\<WRKDIR >* . Összes tanúsítvány és kulcs fájl ebben a címtárban létrejön.
  
3. Másolja a konfigurációs és a parancsfájl fájlt a klónozott IoT Edge-adattárból a munkakönyvtár.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványokat hoznak létre, és csatlakoztassa őket a láncban. Lánc fájlba helyezi el a tanúsítványokat is könnyedén telepítheti őket az IoT Edge-átjáróeszköz és alsóbb rétegbeli eszközök lehetővé teszi.  

1. Hozzon létre a legfelső szintű Hitelesítésszolgáltatói tanúsítványt és a egy köztes tanúsítványt. Ezek a tanúsítványok vannak elhelyezve  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   A parancsfájl több tanúsítványt és kulcsot hoz létre. Jegyezze fel az egyik, amely azt a következő szakaszban fog hivatkozni:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Hozzon létre az IoT Edge eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcs a következő paranccsal. Adja meg az átjáró állomásnév, az átjáró eszköz iotedge/config.yaml fájlban találja. Az átjáró állomásnevet használja a fájlokat és a tanúsítvány létrehozása során. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   A parancsfájl több tanúsítványt és kulcsot hoz létre. Jegyezze fel a két, amely azt a következő szakaszban fog hivatkozni: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Az átjáró tanúsítványok telepítése

Most, hogy egy tanúsítványlánc végrehajtott, szüksége az IoT Edge-átjáróeszköz telepítheti és konfigurálhatja az IoT Edge-futtatókörnyezet, az új tanúsítványok hivatkozni. 

1. A következő fájlokat másolja  *\<WRKDIR >* . Mentse ezeket bárhol az IoT Edge-eszközön. A cél könyvtárát, az IoT Edge-eszköz használata a kifejezés  *\<CERTDIR >* . 

   * Eszköz CA-tanúsítvány –  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Legfelső szintű hitelesítésszolgáltató- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Használhat olyan szolgáltatásokhoz, mint [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy egy függvényt, például [biztonságos másolás protokoll](https://www.ssh.com/ssh/scp/) áthelyezni a fájlokat.  Ha létrehozta az IoT Edge-eszköz magát a tanúsítványokat, kihagyhatja ezt a lépést, és a munkakönyvtárban elérési útját használja.

2. Nyissa meg az IoT Edge biztonsági démon konfigurációs fájlt. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Állítsa be a **tanúsítvány** tulajdonságok az IoT Edge-eszközön a tanúsítvány és kulcs fájlok teljes elérési útja a config.yaml fájlban. Távolítsa el a `#` karakter előtt távolítsa el a négy sorban a tanúsítvány tulajdonságait. Ne feledje, hogy a yaml francia éppen két szóköz.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. A Linux rendszerű eszközökön, ellenőrizze, hogy a felhasználó **iotedge** rendelkezik olvasási engedéllyel a tanúsítványokat tároló könyvtár számára. 

## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub

Amikor először telepíti az IoT Edge-eszközön, a rendszer csak egy modul automatikusan elindul: az IoT Edge-ügynök. Az eszköz átjáróként működik mindkét rendszer modult kell. Ha még nem telepítette a kapcsolt modulok az átjáró eszköz előtt, hozzon létre egy kezdeti telepítés indítása a második rendszer modul, az IoT Edge hub az eszközhöz. Az üzembe helyezés fognak kinézni üres, mert nem adja hozzá a szükséges modulok a varázslóban, de lesz, győződjön meg arról, hogy fut-e a két rendszer modulok. 

Ellenőrizheti, hogy melyik modulokat futtatják egy eszközön, a parancs `iotedge list`. Ha a listában csak a modul adja vissza **edgeAgent** nélkül **edgeHub**, kövesse az alábbi lépéseket:

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

## <a name="open-ports-on-gateway-device"></a>Az átjáróeszközön portok megnyitása

IoT Edge-eszközök nem kell függvényt, hogy bemenő kapcsolatot, mert az IoT hubbal folytatott minden kommunikáció a kimenő kapcsolatok keresztül történik. Átjáróeszközök különböznek fogad üzeneteket az alsóbb rétegbeli eszközök van szükségük. Ha a tűzfal az alsóbb rétegbeli eszközök és az átjáró eszköz között, majd kommunikációs kell lennie, valamint a tűzfalon keresztül lehetséges.

Egy átjáró forgatókönyv működjön az IoT Edge hubot támogatott protokollok legalább egyikének meg kell nyitni alsóbb rétegbeli eszközök érkező bejövő forgalmat. A támogatott protokollok a következők: MQTT, AMQP és HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítását az alsóbb rétegbeli eszközök
Az IoT Edge-futtatókörnyezet továbbíthatnak hasonlóan modulok által küldött üzenetek alsóbb rétegbeli eszközök által küldött üzeneteket. Ez a funkció lehetővé teszi egy modult, mielőtt bármilyen adatot küld a felhőbe az átjáró futó analytics hajtsa végre. 

Irányíthatja a alsóbb rétegbeli eszközök által küldött üzenetek módon jelenleg megkülönböztetve azokat az üzeneteket a modulok által. Minden modulok által küldött üzeneteket tartalmaznak rendszer tulajdonsággal **connectionModuleId** , de az alsóbb rétegbeli eszközök által küldött üzenetek viszont nem. Az útvonal a WHERE záradék használatával bármilyen üzenetet a rendszer tulajdonságot tartalmazó kizárása. 

Az alábbi útvonal van lenne üzeneteket küldhet a bármely alsóbb rétegbeli eszközök nevű modulra mutat példát `ai_insights`, majd `ai_insights` az IoT hubnak.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Üzenet-útválasztással kapcsolatos további információkért lásd: [hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>A kiterjesztett offline művelet engedélyezése

Kezdve a [v1.0.4 kiadási](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) az IoT Edge-futtatókörnyezet, az átjáró eszköz és az alsóbb rétegbeli eszközök csatlakoztathatja azt is konfigurálható a kiterjesztett offline művelet. 

Ezzel a képességgel helyi modulok vagy alsóbb rétegbeli eszközök újból hitelesítheti az IoT Edge-eszközön igény szerint és kommunikálnak egymással üzenetek és a módszerekkel, még akkor is, ha az IoT hub kapcsolódik. További információkért lásd: [ismertetése az IoT Edge kiterjesztett offline képességeiről, eszközök, a modulok és a gyermek eszközök](offline-capabilities.md).

Ahhoz, hogy a kiterjesztett offline képességeiről, az IoT Edge-átjáróeszköz és csatlakozni tudjanak hozzá alsóbb rétegbeli eszközök közötti szülő-gyermek kapcsolat létrehozására. Ezeket a lépéseket mutatjuk be részletesebben [hitelesítése egy alsóbb rétegbeli eszközök Azure IoT hubra](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>További lépések

Most, hogy IoT Edge-eszköz transzparens átjáróként működik, az átjáró megbízhatósági és üzenetek küldése az alsóbb rétegbeli eszközök konfigurálásához szüksége. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md) és [hitelesítése egy alsóbb rétegbeli eszközök Azure IoT hubra](how-to-authenticate-downstream-device.md).
