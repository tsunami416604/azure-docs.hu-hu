---
title: Transzparens átjáró-eszköz létrehozása – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-eszköz használata transzparens átjáróként, amely adatokat dolgozhat fel az alsóbb rétegbeli eszközökről
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792335"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge-eszköz konfigurálása transzparens átjáróként

Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy IoT Edge eszközt úgy, hogy az más eszközök számára transzparens átjáróként működjön, hogy az IoT Hub kommunikáljon. Ebben a cikkben a *IoT Edge Gateway* kifejezés egy transzparens átjáróként használt IoT Edge eszközre hivatkozik. További tudnivalókért tekintse meg a [IoT Edge-eszköz átjáróként való használatát](./iot-edge-as-gateway.md)ismertető témakört.

>[!NOTE]
>Jelenleg
> * Az Edge-kompatibilis eszközök nem csatlakozhatnak IoT Edge átjáróhoz. 
> * Az alsóbb rétegbeli eszközök nem használhatják a fájl feltöltését.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk az első lépést ismerteti:

1. **Az átjáró-eszköznek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra, az alárendelt eszközökről érkező kommunikáció fogadására és az üzenetek megfelelő célhelyre való továbbítására.**
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz-identitással, hogy képes legyen hitelesíteni a IoT Hub, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. Az alsóbb rétegbeli eszköznek képesnek kell lennie az átjáró eszközéhez való biztonságos kapcsolódásra. További információ: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).


Ahhoz, hogy egy eszköz átjáróként működjön, az informatikai részlegnek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli eszköz olyan IoT Edge-eszközhöz kapcsolódjon, amely transzparens átjáróként működik. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáró-eszköz identitását. Ez az identitás-ellenőrzési szolgáltatás megakadályozza, hogy az eszközök esetlegesen rosszindulatú átjáróhoz csatlakozzanak.

Egy transzparens átjáró-forgatókönyvben lévő alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Az alárendelt eszköz minden gyakorlati célra akár egy, a IoT Edge átjáró eszközön futó alkalmazás is lehet. Egy IoT Edge eszköz azonban nem lehet egy IoT Edge átjárón. 

Létrehozhat olyan tanúsítvány-infrastruktúrát is, amely lehetővé teszi az eszköz-átjáró topológiához szükséges megbízhatóságot. Ez a cikk azt feltételezi, hogy ugyanazt a tanúsítványt használja, amelyet az [x. 509 hitelesítésszolgáltatói biztonság](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használ IoT Hubban, amely egy adott IoT hubhoz (az IoT hub legfelső szintű hitelesítésszolgáltatója) társított x. 509 hitelesítésszolgáltatói tanúsítvánnyal, a hitelesítésszolgáltatóval aláírt tanúsítványok sorozatával, valamint a IoT Edge eszközhöz tartozó hitelesítésszolgáltatóval is rendelkezik.

![Átjáró tanúsítványának beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a PKI-tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik, nem feltétlenül a konzorciális hitelesítésszolgáltató tanúsítványának gyökerére. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa. 

Az átjáró megjeleníti a IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az alárendelt eszközön a kapcsolat megkezdése során. Az alárendelt eszköz ellenőrzi, hogy az IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány alá van-e írva. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e.

A következő lépések végigvezetik a tanúsítványok létrehozásának és telepítésének folyamatán az átjáró megfelelő helyein. A tanúsítványok létrehozásához bármilyen gépet használhat, majd átmásolhatja őket a IoT Edge eszközre. 

## <a name="prerequisites"></a>Előfeltételek

* Egy fejlesztési gép, amely tanúsítványokat hoz létre. 
* Az átjáróként konfigurálni kívánt Azure IoT Edge-eszköz. A következő operációs rendszerek egyikének IoT Edge telepítési lépéseit használhatja:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Tanúsítványok előállítása a Windowsban

Az ebben a szakaszban ismertetett lépések segítségével tesztelheti a tanúsítványokat a Windows rendszerben. A tanúsítványokat Windows rendszerű géppel is létrehozhatja, majd átmásolhatja azokat bármely olyan IoT Edge eszközre, amely bármely támogatott operációs rendszeren fut. 

Az ebben a szakaszban létrehozott tanúsítványok kizárólag tesztelési célokra szolgálnak. 

### <a name="install-openssl"></a>OpenSSL telepítése

Telepítse a Windows rendszerhez készült OpenSSL-t arra a gépre, amelyet a tanúsítványok létrehozásához használ. Ha már telepítve van az OpenSSL a Windows-eszközön, kihagyhatja ezt a lépést, de gondoskodhat arról, hogy az OpenSSL. exe elérhető legyen a PATH környezeti változóban. 

Az OpenSSL több módon is telepíthető, beleértve a következőket:

* **Egyszerűbb:** Töltse le és telepítse a [harmadik féltől származó OpenSSL bináris fájlokat](https://wiki.openssl.org/index.php/Binaries), például az [OpenSSL-ből a SourceForge-on](https://sourceforge.net/projects/openssl/). Adja hozzá az OpenSSL. exe fájl teljes elérési útját a PATH környezeti változóhoz. 
   
* **Ajánlott:** Töltse le az OpenSSL forráskódját, és saját kezűleg vagy a [vcpkg](https://github.com/Microsoft/vcpkg)-n keresztül hozza létre a bináris fájlokat a gépen. Az alább felsorolt utasítások segítségével letöltheti a forráskódot, lefordíthatja és telepítheti az OpenSSL-t a Windows rendszerű gépén, egyszerű lépésekkel vcpkg.

   1. Navigáljon ahhoz a könyvtárhoz, amelyre telepíteni szeretné a vcpkg. Ezt a könyvtárat tekintjük *\<VCPKGDIR >* . A [vcpkg](https://github.com/Microsoft/vcpkg)letöltéséhez és telepítéséhez kövesse az utasításokat.
   
   2. Miután telepítette a vcpkg-et, futtassa a következő parancsot egy PowerShell-parancssorból a Windows x64 rendszerhez készült OpenSSL-csomag telepítéséhez. A telepítés általában körülbelül 5 percet vesz igénybe.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adja hozzá `<VCPKGDIR>\installed\x64-windows\tools\openssl` a PATH környezeti változóhoz, hogy az OpenSSL. exe fájl elérhető legyen a híváshoz.

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

A Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével létrehozhatók tesztelési tanúsítványok. Ebben a szakaszban a IoT Edge-tárház klónozásával és a parancsfájlok végrehajtásával foglalkozunk. 

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. 

2. A nem éles tanúsítványok létrehozásához szükséges parancsfájlokat tartalmazó git-tárház klónozása. Ezek a parancsfájlok segítenek létrehozni a szükséges tanúsítványokat egy transzparens átjáró beállításához. Használja a `git clone` parancsot, vagy [töltse le a zip-fájlt](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigáljon ahhoz a címtárhoz, amelyben dolgozni szeretne. Ebben a cikkben ezt a könyvtárat hívjuk *\<WRKDIR >* . Az összes tanúsítvány és kulcs ebben a munkakönyvtárban lesz létrehozva.

4. Másolja a konfigurációs és parancsfájl-fájlokat a klónozott tárházból a munkakönyvtárba. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha a tárházat ZIP-fájlként töltötte le, akkor a mappa neve `iotedge-master`, és a többi útvonal ugyanaz. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. A parancsfájlok futtatásának engedélyezése a PowerShell számára.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. A parancsfájlok által használt függvények a PowerShell globális névterében használhatók.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A PowerShell-ablak egy figyelmeztetést jelenít meg arról, hogy a parancsfájl által generált tanúsítványok csak tesztelési célokra szolgálnak, és nem használhatók éles környezetben.

8. Győződjön meg arról, hogy az OpenSSL megfelelően van telepítve, és győződjön meg róla, hogy a meglévő tanúsítványokkal nem lesznek ütközések. Ha problémák merülnek fel, a parancsfájlnak le kell írnia, hogyan kell kijavítani azokat a rendszeren.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Tanúsítványok létrehozása

Ebben a szakaszban három tanúsítványt hoz létre, majd összekapcsolja őket egy láncban. A tanúsítványok egy láncba való elhelyezése lehetővé teszi, hogy egyszerűen telepítse azokat a IoT Edge Gateway-eszközön és az alsóbb rétegbeli eszközökön.  

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, és jelentkezzen be egy köztes tanúsítvánnyal. A tanúsítványokat a rendszer a munkakönyvtárba helyezi.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, de a cikk későbbi részében a következőkre fogunk hivatkozni:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nevét, például **MyEdgeDeviceCA**. A név a fájlok elnevezésére és a tanúsítvány létrehozása során használható. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Ez a parancsfájl több tanúsítványt és kulcsot hoz létre, beleértve azt is, hogy a jelen cikk későbbi részében fogunk hivatkozni:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Ha a **MyEdgeDeviceCA**eltérő nevet ad meg, akkor a parancs által létrehozott tanúsítványok és kulcsok ezt a nevet fogják tükrözni. 

Most, hogy rendelkezik a tanúsítványokkal, ugorjon a [tanúsítványok telepítése az átjárón](#install-certificates-on-the-gateway) című lépésre.

## <a name="generate-certificates-with-linux"></a>Tanúsítványok készítése Linux rendszeren

Az ebben a szakaszban ismertetett lépések segítségével tesztelheti a Linux rendszerű tanúsítványokat. A tanúsítványok létrehozásához Linux rendszerű gépeket használhat, majd átmásolhatja azokat bármely olyan IoT Edge eszközre, amely bármely támogatott operációs rendszeren fut. 

Az ebben a szakaszban létrehozott tanúsítványok kizárólag tesztelési célokra szolgálnak. 

### <a name="prepare-creation-scripts"></a>Létrehozási parancsfájlok előkészítése

A Azure IoT Edge git-tárház olyan parancsfájlokat tartalmaz, amelyek segítségével létrehozhatók tesztelési tanúsítványok. Ebben a szakaszban a IoT Edge-tárház klónozásával és a parancsfájlok végrehajtásával foglalkozunk. 

1. A nem éles tanúsítványok létrehozásához szükséges parancsfájlokat tartalmazó git-tárház klónozása. Ezek a parancsfájlok segítenek létrehozni a szükséges tanúsítványokat egy transzparens átjáró beállításához. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigáljon ahhoz a címtárhoz, amelyben dolgozni szeretne. Erre a könyvtárra a cikk során *\<WRKDIR >* . Az összes tanúsítvány-és kulcsfájl ebben a könyvtárban lesz létrehozva.
  
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

Ebben a szakaszban három tanúsítványt hoz létre, majd összekapcsolja őket egy láncban. Ha egy láncban elhelyezi a tanúsítványokat, egyszerűen telepítheti azokat a IoT Edge Gateway-eszközön és az alsóbb rétegbeli eszközökön.  

1. Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt. Ezeket a tanúsítványokat a rendszer *\<WRKDIR >ba*helyezi.

   Ha már létrehozott egy gyökérszintű és köztes tanúsítványokat ebben a munkakönyvtárban, ne futtassa újra ezt a parancsfájlt. A parancsfájl újrafuttatása felülírja a meglévő tanúsítványokat. Ehelyett folytassa a következő lépéssel. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   A szkript több tanúsítványt és kulcsot hoz létre. Jegyezze fel az egyiket, amelyet a következő szakaszban fogunk megtekinteni:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Hozza létre a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és titkos kulcsát a következő paranccsal. Adja meg a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nevét, például **MyEdgeDeviceCA**. A név a fájlok elnevezésére és a tanúsítvány létrehozása során használható. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   A szkript több tanúsítványt és kulcsot hoz létre. Jegyezze fel a kettőt, amelyet a következő szakaszban fogunk megtekinteni: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Ha a **MyEdgeDeviceCA**eltérő nevet ad meg, akkor a parancs által létrehozott tanúsítványok és kulcsok ezt a nevet fogják tükrözni. 

## <a name="install-certificates-on-the-gateway"></a>Tanúsítványok telepítése az átjárón

Most, hogy létrehozott egy tanúsítványláncot, telepítenie kell azt a IoT Edge átjáró eszközön, és konfigurálnia kell a IoT Edge futtatókörnyezetet az új tanúsítványokra való hivatkozáshoz. 

1. Másolja a következő fájlokat *\<WRKDIR >* . Mentse őket bárhová a IoT Edge eszközön. A IoT Edge eszközön *\<CERTDIR >* néven tekintjük meg a cél könyvtárat. 

   * Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa – `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Eszköz HITELESÍTÉSSZOLGÁLTATÓjának titkos kulcsa – `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ – `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.  Ha saját maga hozta létre a tanúsítványokat a IoT Edge eszközön, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

2. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Állítsa be a **tanúsítvány** tulajdonságait a config. YAML fájlban a tanúsítvány és a kulcs fájljainak teljes elérési útjára a IoT Edge eszközön. Távolítsa el a `#` karaktert, mielőtt a tanúsítvány tulajdonságai megszüntessék a négy sort. Ne feledje, hogy a YAML behúzása két szóköz.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Linux-eszközökön ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat tároló címtárhoz. 

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub üzembe helyezése az átjárón

Amikor először telepíti a IoT Edget egy eszközön, a rendszer csak egy rendszermodult kezd automatikusan: a IoT Edge-ügynököt. Ahhoz, hogy az eszköz átjáróként működjön, a rendszermodulokra is szükség van. Ha korábban még nem telepített modulokat az átjáró eszközéhez, hozzon létre egy kezdeti központi telepítést az eszközhöz a második rendszermodul, a IoT Edge hub elindításához. A központi telepítés üresen jelenik meg, mert nem ad hozzá modulokat a varázslóban, de gondoskodni fog arról, hogy mindkét rendszer-modul fusson. 

Megtekintheti, hogy mely modulok futnak egy eszközön a `iotedge list`paranccsal. Ha a lista csak a **edgeHub**nélküli modul **edgeAgent** adja vissza, kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen **IoT Edge** , és válassza ki az átjáróként használni kívánt IoT Edge-eszközt.

3. Válassza a **Modulok beállítása** lehetőséget.

4. Kattintson a **Tovább** gombra.

5. Az **útvonalak meghatározása** lapon olyan alapértelmezett útvonalon kell lennie, amely az összes modul összes üzenetét elküldi IoT hub. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. A **sablon áttekintése** lapon válassza a **Küldés**lehetőséget.

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása átjáró eszközön

A standard IoT Edge eszközök nem igényelnek semmilyen bejövő kapcsolatot a működéshez, mert az IoT Hubkel folytatott kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáró-eszközök különböznek, mert üzeneteket kell kapniuk az alsóbb rétegbeli eszközökről. Ha a tűzfal az alárendelt eszközök és az átjáró-eszköz között van, akkor a kommunikációnak a tűzfalon keresztül is elérhetőnek kell lennie.

Az átjáró-forgatókönyvek működéséhez az IoT Edge hub által támogatott protokollok közül legalább az egyiknek nyitva kell lennie az alárendelt eszközökről érkező bejövő forgalom számára. A támogatott protokollok a MQTT, a AMQP, a HTTPS, a MQTT over WebSockets és a AMQP WebSocket-en keresztül. 

| Port | Protocol (Protokoll) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítása az alárendelt eszközökről
A IoT Edge futtatókörnyezet képes továbbítani az alárendelt eszközökről küldött üzeneteket ugyanúgy, mint a modulok által küldött üzenetek. Ez a funkció lehetővé teszi az elemzést az átjárón futó modulban, mielőtt adatokat küldene a felhőbe. 

Jelenleg az alárendelt eszközök által küldött üzenetek továbbításának módja a modulok által küldött üzenetektől való megkülönböztetés. A modulok által küldött üzenetek tartalmazzák a **connectionModuleId** nevű rendszertulajdonságot, de az alárendelt eszközök által küldött üzenetek nem. Az útvonal WHERE záradékával kizárhatja az adott System tulajdonságot tartalmazó üzeneteket. 

Az alábbi útvonal egy példa arra, hogy bármely alsóbb rétegbeli eszközről üzenetet küldjön egy `ai_insights`nevű modulra, majd `ai_insights`ról IoT Hubre.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Az üzenetek útválasztásával kapcsolatos további információkért lásd: [modulok üzembe helyezése és útvonalak létrehozása](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Kiterjesztett offline művelet engedélyezése

Az IoT Edge futtatókörnyezet [v 1.0.4 kiadásával](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) kezdődően az átjáró-eszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök konfigurálható a kiterjesztett offline művelethez. 

Ezzel a képességgel a helyi modulok és az alsóbb rétegbeli eszközök szükség szerint újra hitelesíthetők a IoT Edge eszközzel, és kommunikálhatnak egymással az üzenetek és a metódusok használatával, még akkor is, ha az IoT hub le van választva. További információ: [IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek ismertetése](offline-capabilities.md).

A kibővített offline képességek engedélyezéséhez létre kell hoznia egy szülő-gyermek kapcsolatot egy IoT Edge átjáró-eszköz és a hozzá kapcsolódó alsóbb szintű eszközök között. Ezeket a lépéseket részletesen ismertetjük az [alsóbb rétegbeli eszközök Azure-IoT hub történő hitelesítésével](how-to-authenticate-downstream-device.md)kapcsolatban.

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik egy IoT Edge eszközzel, amely transzparens átjáróként működik, konfigurálnia kell az alsóbb rétegbeli eszközöket az átjáró megbízhatóságához és az üzenetek küldéséhez. Folytassa a következő lépéssel, hogy [hitelesítse egy alsóbb rétegbeli eszközt az Azure IoT Hubban](how-to-authenticate-downstream-device.md) az átlátszó átjáró beállításának további lépéseihez. 
