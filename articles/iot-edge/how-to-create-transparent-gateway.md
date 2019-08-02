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
ms.openlocfilehash: a91860e9ec8d503a01d079925466093d19bbbccf
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698606"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>A transzparens átjáróként működő IoT Edge-eszköz konfigurálása

Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy IoT Edge eszközt úgy, hogy az más eszközök számára transzparens átjáróként működjön, hogy az IoT Hub kommunikáljon. Ez a cikk az előfizetési időszak *IoT Edge-átjáró* IoT Edge-eszköz transzparens átjáróként használt hivatkozik. További tudnivalókért tekintse meg a [IoT Edge-eszköz átjáróként való használatát](./iot-edge-as-gateway.md)ismertető témakört.

>[!NOTE]
>Jelenleg:
> * Edge-kompatibilis eszközök IoT Edge-átjáró nem tud kapcsolódni. 
> * Alsóbb rétegbeli eszközök fájl feltöltése nem használható.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk az első lépést ismerteti:

1. **Az átjáró-eszköznek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra, az alárendelt eszközökről érkező kommunikáció fogadására és az üzenetek megfelelő célhelyre való továbbítására.**
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz-identitással, hogy képes legyen hitelesíteni a IoT Hub, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. Az alsóbb rétegbeli eszköznek képesnek kell lennie az átjáró eszközéhez való biztonságos kapcsolódásra. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).


Ahhoz, hogy egy eszköz átjáróként működjön, az informatikai részlegnek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra. Az Azure IoT Edge lehetővé teszi, hogy a nyilvános kulcsokra épülő infrastruktúrájú (PKI) eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáró-eszköz identitását. Ez az identitás-ellenőrzési szolgáltatás megakadályozza, hogy az eszközök esetlegesen rosszindulatú átjáróhoz csatlakozzanak.

Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Gyakorlati okokból azonban egy alsóbb rétegbeli eszközök az IoT Edge-átjáróeszköz magát a futó alkalmazás még akkor is lehet. 

Minden olyan tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk azt feltételezi, hogy ugyanazt a tanúsítványt használja, amelyet az [x. 509 hitelesítésszolgáltatói biztonság](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használ IoT Hubban, amely egy adott IoT hub (az IoT hub legfelső szintű hitelesítésszolgáltatója) egy x. 509 hitelesítésszolgáltatói tanúsítványát is magában foglalja, amely egy adott hitelesítésszolgáltatóval aláírt tanúsítványok sorozata. és a IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓja.

![Átjáró tanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a PKI-tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik, nem feltétlenül a konzorciális hitelesítésszolgáltató tanúsítványának gyökerére. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa. 

Az átjáró megjeleníti a IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az alárendelt eszközön a kapcsolat megkezdése során. Az alárendelt eszköz ellenőrzi, hogy az IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány alá van-e írva. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e.

A következő lépések végigvezetik a tanúsítványok létrehozásának és telepítésének folyamatán az átjáró megfelelő helyein. Minden olyan gép, használja a tanúsítványok létrehozásához, és másolja őket keresztül az IoT Edge-eszköz. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Edge-eszköz konfigurálásához átjáróként. A következő operációs rendszerek egyikének IoT Edge telepítési lépéseit használhatja:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

Ez a cikk az *átjáró állomásneve* több ponton is hivatkozik. Az átjáró állomásneve deklarálva van a config. YAML fájl **hostname** paraméterében a IoT Edge Gateway eszközön. Ez a cikk a tanúsítványok létrehozásához használatos, és az alsóbb rétegbeli eszközök kapcsolódási karakterláncában hivatkozik rá. Az átjáró állomásneve feloldhatónak kell lennie egy IP-címhez, vagy a DNS-t vagy egy gazdagépet tartalmazó bejegyzést kell használnia.

## <a name="generate-certificates-with-windows"></a>Windows-tanúsítványok létrehozása

Az ebben a szakaszban ismertetett lépések segítségével tesztelheti a tanúsítványokat a Windows rendszerben. A tanúsítványokat Windows rendszerű géppel is létrehozhatja, majd átmásolhatja azokat bármely olyan IoT Edge eszközre, amely bármely támogatott operációs rendszeren fut. 

Ebben a szakaszban létrehozott tanúsítványokat csak tesztelési célokra szolgálnak. 

### <a name="install-openssl"></a>OpenSSL telepítése

OpenSSL for Windows telepítése a gépen, amely a tanúsítványok létrehozásához használ. Ha már telepítve van az OpenSSL a Windows-eszközön, kihagyhatja ezt a lépést, de gondoskodhat arról, hogy az OpenSSL. exe elérhető legyen a PATH környezeti változóban. 

OpenSSL telepítése számos módja van:

* **Könnyebben** Töltse le és telepítse a [harmadik féltől származó OpenSSL bináris fájlokat](https://wiki.openssl.org/index.php/Binaries), például az [OpenSSL-ből a SourceForge-on](https://sourceforge.net/projects/openssl/). Adja hozzá a teljes elérési útját a PATH környezeti változóba openssl.exe. 
   
* **Ajánlott** Töltse le az OpenSSL forráskódját, és saját kezűleg vagy a [vcpkg](https://github.com/Microsoft/vcpkg)-n keresztül hozza létre a bináris fájlokat a gépen. Az alábbi utasításokat vcpkg használatával töltse le a forráskódot, fordítási és OpenSSL telepítése a Windows-gépen egyszerű lépésben.

   1. Navigáljon ahhoz a könyvtárhoz, ahol vcpkg telepíteni szeretné. Ez a könyvtár, mint kifejezés  *\<VCPKGDIR >* . Töltse le és telepítse az utasítások [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Miután telepítette a vcpkg-et, futtassa a következő parancsot egy PowerShell-parancssorból a Windows x64 rendszerhez készült OpenSSL-csomag telepítéséhez. A telepítés általában befejezéséhez körülbelül 5 percet vesz igénybe.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adjon hozzá `<VCPKGDIR>\installed\x64-windows\tools\openssl` útját a PATH környezeti változóba, hogy a openssl.exe fájl elindításaihoz érhető el.

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

A Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével létrehozhatók tesztelési tanúsítványok. Ebben a szakaszban a IoT Edge-tárház klónozásával és a parancsfájlok végrehajtásával foglalkozunk. 

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. 

2. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. Használja a `git clone` parancs vagy [töltse le a zip-fájl](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ebben a cikkben ezt a könyvtárat  *\<WRKDIR >* fogjuk hívni. Az összes tanúsítvány és kulcs ebben a munkakönyvtárban lesz létrehozva.

4. Másolja a konfigurációs és parancsfájl-fájlokat a klónozott tárházból a munkakönyvtárba. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha a tárházat zip-fájlként töltötte le, akkor a mappa neve `iotedge-master` , az elérési út többi része pedig ugyanaz. 
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

7. A parancsfájlok által használt függvények a PowerShell globális névterében használhatók.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A PowerShell-ablak egy figyelmeztetést jelenít meg arról, hogy a parancsfájl által generált tanúsítványok csak tesztelési célokra szolgálnak, és nem használhatók éles környezetben.

8. Győződjön meg arról, hogy az OpenSSL megfelelően van telepítve, és győződjön meg róla, hogy a meglévő tanúsítványokkal nem lesznek ütközések. Ha probléma adódik, a parancsfájl kell azt ismertetik, hogyan javíthatók a rendszeren.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványokat hoznak létre, és csatlakoztassa őket a láncban. Helyezi el a tanúsítványokat a lánc fájl lehetővé teszi, hogy az IoT Edge-átjáróeszköz és alsóbb rétegbeli eszközök egyszerűen telepítse őket.  

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, és jelentkezzen be egy köztes tanúsítvánnyal. A tanúsítványokat a rendszer a munkakönyvtárba helyezi.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de a cikk későbbi részében a következőkre fogunk hivatkozni:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg az átjáró gazdagépét, amely az átjáró eszköz iotedge\config.YAML fájljában található. Az átjáró állomásneve a fájlok elnevezésére és a tanúsítvány létrehozása során használatos. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, beleértve azt is, hogy a jelen cikk későbbi részében fogunk hivatkozni:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Most, hogy rendelkezik a tanúsítványokkal, ugorjon a [tanúsítványok telepítése az átjárón](#install-certificates-on-the-gateway) című lépésre.

## <a name="generate-certificates-with-linux"></a>A Linux és a tanúsítványok előállítása

Az ebben a szakaszban ismertetett lépések segítségével tesztelheti a Linux rendszerű tanúsítványokat. A tanúsítványok létrehozásához Linux rendszerű gépeket használhat, majd átmásolhatja azokat bármely olyan IoT Edge eszközre, amely bármely támogatott operációs rendszeren fut. 

Ebben a szakaszban létrehozott tanúsítványokat csak tesztelési célokra szolgálnak. 

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

A Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével létrehozhatók tesztelési tanúsítványok. Ebben a szakaszban a IoT Edge-tárház klónozásával és a parancsfájlok végrehajtásával foglalkozunk. 

1. Klónozza a git-adattár, amely nem éles tanúsítványainak létrehozásához szükséges parancsfájlokat tartalmazza. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Lépjen abba a könyvtárba, amelyben a használni kívánt. Ezt a könyvtárat a cikk  *\<a WRKDIR >* . Az összes tanúsítvány-és kulcsfájl ebben a könyvtárban lesz létrehozva.
  
3. Másolja a config és a script fájlokat a klónozott IoT Edge-tárházból a munkakönyvtárba.

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

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt. Ezek a tanúsítványok vannak elhelyezve  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   A szkript több tanúsítványt és kulcsot hoz létre. Jegyezze fel az egyiket, amelyet a következő szakaszban fogunk megtekinteni:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg az átjáró állomásnevét, amely az átjáró eszköz iotedge/config. YAML fájljában található. Az átjáró állomásneve a fájlok elnevezésére és a tanúsítvány létrehozása során használatos. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   A szkript több tanúsítványt és kulcsot hoz létre. Jegyezze fel a kettőt, amelyet a következő szakaszban fogunk megtekinteni: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Az átjáró tanúsítványok telepítése

Most, hogy egy tanúsítványlánc végrehajtott, szüksége az IoT Edge-átjáróeszköz telepítheti és konfigurálhatja az IoT Edge-futtatókörnyezet, az új tanúsítványok hivatkozni. 

1. A következő fájlokat másolja  *\<WRKDIR >* . Mentse ezeket bárhol az IoT Edge-eszközön. A cél könyvtárát, az IoT Edge-eszköz használata a kifejezés  *\<CERTDIR >* . 

   * Eszköz CA-tanúsítvány –  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ –`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.  Ha saját maga hozta létre a tanúsítványokat a IoT Edge eszközön, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

2. Nyissa meg az IoT Edge biztonsági démon konfigurációs fájlt. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Állítsa be a **tanúsítvány** tulajdonságait a config. YAML fájlban a tanúsítvány és a kulcs fájljainak teljes elérési útjára a IoT Edge eszközön. Távolítsa `#` el a karaktert, mielőtt a tanúsítvány tulajdonságai megszüntessék a négy sort. Ne feledje, hogy a YAML behúzása két szóköz.

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

4. Linux-eszközökön ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat tároló címtárhoz. 

## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub

Amikor először telepíti a IoT Edget egy eszközön, a rendszer csak egy rendszermodult kezd automatikusan: a IoT Edge-ügynököt. Az eszköz átjáróként működik mindkét rendszer modult kell. Ha korábban még nem telepített modulokat az átjáró eszközéhez, hozzon létre egy kezdeti központi telepítést az eszközhöz a második rendszermodul, a IoT Edge hub elindításához. A központi telepítés üresen jelenik meg, mert nem ad hozzá modulokat a varázslóban, de gondoskodni fog arról, hogy mindkét rendszer-modul fusson. 

Ellenőrizheti, hogy melyik modulokat futtatják egy eszközön, a parancs `iotedge list`. Ha a lista csak a **edgeHub**nélküli modul **edgeAgent** adja vissza, kövesse az alábbi lépéseket:

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

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása átjáró eszközön

A standard IoT Edge eszközök nem igényelnek semmilyen bejövő kapcsolatot a működéshez, mert az IoT Hubkel folytatott kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáró-eszközök különböznek, mert üzeneteket kell kapniuk az alsóbb rétegbeli eszközökről. Ha a tűzfal az alárendelt eszközök és az átjáró-eszköz között van, akkor a kommunikációnak a tűzfalon keresztül is elérhetőnek kell lennie.

Az átjáró-forgatókönyvek működéséhez az IoT Edge hub által támogatott protokollok közül legalább az egyiknek nyitva kell lennie az alárendelt eszközökről érkező bejövő forgalom számára. A támogatott protokollok a következők: MQTT, AMQP és HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítását az alsóbb rétegbeli eszközök
Az IoT Edge-futtatókörnyezet továbbíthatnak hasonlóan modulok által küldött üzenetek alsóbb rétegbeli eszközök által küldött üzeneteket. Ez a funkció lehetővé teszi az elemzést az átjárón futó modulban, mielőtt adatokat küldene a felhőbe. 

Irányíthatja a alsóbb rétegbeli eszközök által küldött üzenetek módon jelenleg megkülönböztetve azokat az üzeneteket a modulok által. Minden modulok által küldött üzeneteket tartalmaznak rendszer tulajdonsággal **connectionModuleId** , de az alsóbb rétegbeli eszközök által küldött üzenetek viszont nem. Az útvonal a WHERE záradék használatával bármilyen üzenetet a rendszer tulajdonságot tartalmazó kizárása. 

Az alábbi útvonal egy példa arra, hogy bármely alsóbb rétegbeli eszközről üzeneteket küldjön egy nevű `ai_insights`modulba, majd `ai_insights` a IoT hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Üzenet-útválasztással kapcsolatos további információkért lásd: [hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Kiterjesztett offline művelet engedélyezése

Az IoT Edge futtatókörnyezet [v 1.0.4 kiadásával](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) kezdődően az átjáró-eszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök konfigurálható a kiterjesztett offline művelethez. 

Ezzel a képességgel a helyi modulok és az alsóbb rétegbeli eszközök szükség szerint újra hitelesíthetők a IoT Edge eszközzel, és kommunikálhatnak egymással az üzenetek és a metódusok használatával, még akkor is, ha az IoT hub le van választva. További információkért lásd: [ismertetése az IoT Edge kiterjesztett offline képességeiről, eszközök, a modulok és a gyermek eszközök](offline-capabilities.md).

A kibővített offline képességek engedélyezéséhez létre kell hoznia egy szülő-gyermek kapcsolatot egy IoT Edge átjáró-eszköz és a hozzá kapcsolódó alsóbb szintű eszközök között. Ezeket a lépéseket részletesen ismertetjük az [alsóbb rétegbeli eszközök Azure-IoT hub történő hitelesítésével](how-to-authenticate-downstream-device.md)kapcsolatban.

## <a name="next-steps"></a>További lépések

Most, hogy IoT Edge-eszköz transzparens átjáróként működik, az átjáró megbízhatósági és üzenetek küldése az alsóbb rétegbeli eszközök konfigurálásához szüksége. További információkért lásd: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge](how-to-connect-downstream-device.md) -átjáróhoz és [egy alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
