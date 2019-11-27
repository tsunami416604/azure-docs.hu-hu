---
title: Csatlakozás az alsóbb rétegbeli eszközök – Azure IoT Edge |} A Microsoft Docs
description: Az alsóbb rétegbeli vagy a levélben lévő eszközök konfigurálása Azure IoT Edge Gateway-eszközökhöz való kapcsolódáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457120"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró

Ez a cikk útmutatást nyújt az alsóbb rétegbeli eszközök és a IoT Edge transzparens átjárók közötti megbízható kapcsolat létesítéséhez. Transzparens átjáró esetén egy vagy több eszköz átadhatja az üzeneteiket egyetlen átjárón keresztül, amely karbantartja a kapcsolatot IoT Hub. Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. 

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a harmadik lépést ismerteti:

1. Az átjáró-eszköznek biztonságosan csatlakoznia kell az alárendelt eszközökhöz, kommunikációt kell fogadnia az alsóbb rétegbeli eszközökről, és továbbítania kell az üzeneteket a megfelelő célhelyre. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz identitásával, hogy a hitelesítés a IoT Hub használatával történjen, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. **Az alsóbb rétegbeli eszköznek képesnek kell lennie az átjáró eszközéhez való biztonságos kapcsolódásra.**

Ez a cikk azonosítja az alsóbb rétegbeli eszközkapcsolatok szolgáltatással kapcsolatos gyakori problémák, és végigvezeti Önt az alsóbb rétegbeli eszközök által beállításához: 

* Elmagyarázza transport layer security (TLS) és a tanúsítvány alapjai. 
* Elmagyarázza a különböző operációs rendszerekből TLS könyvtárak működését, és hogy mindegyik operációs rendszer hogyan kezelje a tanúsítványokat.
* Ajánljuk figyelmébe az Azure IoT elindult a minták több nyelven is nyújtanak segítséget. 

Ebben a cikkben az *átjáró* és *IoT Edge átjáró* használati feltételei egy transzparens átjáróként konfigurált IoT Edge-eszközre hivatkoznak. 

## <a name="prerequisites"></a>Előfeltételek 

A **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványfájl lett létrehozva, amely [egy IoT Edge eszköz konfigurálása](how-to-create-transparent-gateway.md) , amely az alsóbb rétegbeli eszközön elérhető transzparens átjáróként működik. Az alsóbb rétegbeli eszköz ezt a tanúsítványt használja az átjáró-eszköz identitásának ellenőrzéséhez. 

## <a name="prepare-a-downstream-device"></a>Alsóbb rétegbeli eszközök előkészítése

Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. Azonban egy másik IoT Edge eszköz nem lehet egy IoT Edge átjárón. 

>[!NOTE]
>A IoT Hubban regisztrált identitásokkal rendelkező eszközök [IoT a különböző](../iot-hub/iot-hub-devguide-module-twins.md) folyamatok, hardverek vagy függvények egyetlen eszközön való elkülönítéséhez használhatók. IoT Edge átjárók támogatják az alárendelt modulok kapcsolatait a szimmetrikus kulcsos hitelesítéssel, de X. 509 tanúsítványalapú hitelesítést nem. 

Egy alárendelt eszköz csatlakoztatása az IoT Edge-átjáró, két dologra van szükség:

* Egy eszköz vagy alkalmazás, amelynek része az IoT Hub eszköz kapcsolati karakterláncának hozzáfűzi az információkat az átjáró csatlakozni. 

    Ez a lépés [egy alsóbb rétegbeli eszköz Azure-IoT hub való hitelesítését](how-to-authenticate-downstream-device.md)ismerteti.

* Az eszköznek vagy alkalmazásnak meg kell bíznia az átjáró **legfelső szintű hitelesítésszolgáltatói** tanúsítványán, hogy ellenőrizze az ÁTJÁRÓ eszköz TLS-kapcsolatait. 

    Ezt a lépést részletesen ismertetjük a cikk további részében. Ez a lépés kétféleképpen végezhető el: a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítésével az operációs rendszer tanúsítványtárolójában vagy (bizonyos nyelveken) az Azure IoT SDK-kat használó alkalmazásokon belüli tanúsítványra hivatkozva.

## <a name="tls-and-certificate-fundamentals"></a>A TLS és a tanúsítvány – alapok

Az igazi kihívást a alsóbb rétegbeli eszközök biztonságos csatlakoztatása az IoT Edge van, mint bármely más biztonságos ügyfél/kiszolgáló közötti kommunikációhoz, amely az interneten keresztül történik. Az ügyfél és a kiszolgáló biztonságos módon kommunikál az interneten keresztül a [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)protokoll használatával. A TLS-t a standard [nyilvánoskulcs-infrastruktúra (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) által készített szerkezetek nevezik ki. A TLS meglehetősen érintett specifikáció, és a két végpont biztonságossá tételével kapcsolatos témakörök széles körét kezeli. Ez a szakasz az eszközök IoT Edge átjáróhoz való biztonságos csatlakoztatásához szükséges fogalmakat összegzi.

Amikor az ügyfél egy kiszolgálóhoz csatlakozik, a kiszolgáló a tanúsítványok láncát adja meg, amelyet a *kiszolgálói tanúsítvány láncának*nevezünk. Tanúsítványlánc általában egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvány, egy vagy több köztes Hitelesítésszolgáltatói tanúsítványt, és végül magát a kiszolgáló tanúsítványa foglalja magában. Ügyfél létesít megbízhatósági kapcsolat a kiszolgálóval titkosítási szempontból a teljes kiszolgáló tanúsítványlánca ellenőrzésével. A kiszolgálói tanúsítvány láncának ezt az ügyfél-érvényesítését a *kiszolgálói lánc érvényesítésének*nevezzük. Az ügyfél kriptográfiailag megküzdi a szolgáltatást, hogy igazolja a tanúsítványhoz tartozó titkos kulcsot egy olyan folyamatban, amelynek a *birtokában bizonyítás*történt. A kiszolgálói lánc érvényesítésének és a birtokon tartás igazolásának kombinációját *kiszolgáló hitelesítésnek*nevezzük. A kiszolgáló tanúsítványlánca ellenőrzéséhez az ügyfélnek kell a legfelső szintű Hitelesítésszolgáltatói tanúsítvány használt létrehozása (vagy ki) a kiszolgálói tanúsítvány egy példányát. Általában webhelyekhez csatlakoznak, amikor egy böngészőben érhető el a gyakran használt CA-tanúsítványok az előre konfigurált, az ügyfél rendelkezik egy zökkenőmentes folyamat. 

Ha egy eszköz csatlakozik, az Azure IoT Hub, az eszköz az ügyfél és az IoT Hub-felhőszolgáltatás az a kiszolgáló. A IoT Hub Cloud Service-t a **Baltimore CyberTrust root**nevű legfelső szintű hitelesítésszolgáltatói tanúsítvány támogatja, amely nyilvánosan elérhető és széles körben használatban van. Az IoT Hub Hitelesítésszolgáltatói tanúsítványt a legtöbb eszköz már telepítve van, mivel számos TLS (Schannel, OpenSSL LibreSSL) automatikusan megvalósításokban ez kiszolgálói tanúsítvány érvényesítése során. Előfordulhat, hogy sikeresen csatlakozni az IoT Hub eszköz előfordulhat, hogy problémákba szeretne csatlakozni az IoT Edge-átjáró.

Amikor egy eszköz csatlakozik az IoT Edge-átjáró, az alsóbb rétegbeli eszköz az ügyfél és az átjáró eszköz a kiszolgálón. Az Azure IoT Edge lehetővé teszi a kezelők (vagy felhasználók) hozhat létre az átjáró tanúsítványláncok, azonban az igényei. Az operátor választhat nyilvános hitelesítésszolgáltató tanúsítvány, például a Baltimore, vagy egy önaláírt (vagy belső fejlesztésű) legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Nyilvános hitelesítésszolgáltató tanúsítványokat gyakran kell díjat fizetni, társított őket, tehát jellemzően termelési forgatókönyvekhez. Önaláírt CA-tanúsítványok használata javasolt, fejlesztési és tesztelési célra. A bevezetésben felsorolt transzparens átjáró beállítási cikkei önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat használnak. 

Amikor egy IoT Edge-átjáró egy önaláírt főtanúsítványt használ, telepíteni kell, vagy az alsóbb rétegbeli eszközökhöz szeretne csatlakozni az átjáró a megadott. 

![Átjáró tanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

Ha többet szeretne megtudni a IoT Edge tanúsítványokról és néhány üzemi hatásról, tekintse meg a [IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md)című témakört.

## <a name="provide-the-root-ca-certificate"></a>Adja meg a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt

Az átjáró-eszköz tanúsítványainak ellenőrzéséhez az alárendelt eszköznek a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány saját példányát kell használnia. Ha a IoT Edge git-tárházban megadott parancsfájlokat használta a tesztelési tanúsítványok létrehozásához, akkor a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány neve **Azure-IOT-test-only. root. ca. CERT. PEM**. Ha még nem tette meg a többi alsóbb rétegbeli eszköz előkészítésének lépéseit, helyezze át a tanúsítványfájl bármely könyvtárba az alsóbb rétegbeli eszközön. Használhat olyan szolgáltatásokat, mint a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , vagy egy olyan függvény, mint például a [biztonságos másolási protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájl áthelyezéséhez.

## <a name="install-certificates-in-the-os"></a>Tanúsítványok telepítése az operációs rendszeren

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában általában lehetővé teszi, hogy a legtöbb alkalmazás a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja. Vannak kivételek, például olyan NodeJS alkalmazások, amelyek nem használják az operációs rendszer tanúsítványtárolóját, hanem a csomópont-futtatókörnyezet belső tanúsítványtárolóját használják. Ha nem tudja telepíteni a tanúsítványt az operációs rendszer szintjén, ugorjon a [tanúsítványok használata az Azure IoT SDK](#use-certificates-with-azure-iot-sdks)-k használatával című részre. 

### <a name="ubuntu"></a>Ubuntu

Az alábbi parancsok olyan hitelesítésszolgáltató-tanúsítvány telepítése egy Ubuntu-gazdagép egy példát. Ez a példa azt feltételezi, hogy a **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványt használja az előfeltételek cikkeiből, és a tanúsítványt egy helyre másolta az alsóbb rétegbeli eszközön.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

A következő üzenetnek kell megjelennie: "tanúsítványok frissítése a/etc/SSL/certs...-ben 1 hozzáadva, 0 eltávolítva; kész. "

### <a name="windows"></a>Windows

A következő lépéseket kell egy példát egy Hitelesítésszolgáltatói tanúsítvány telepítése a Windows-gazdagépen. Ez a példa azt feltételezi, hogy a **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványt használja az előfeltételek cikkeiből, és a tanúsítványt egy helyre másolta az alsóbb rétegbeli eszközön.

A tanúsítványokat a PowerShell [importálási tanúsítványával](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) rendszergazdaként telepítheti:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

A tanúsítványokat a **certlm** segédprogrammal is telepítheti: 

1. A Start menüben keresse meg és válassza a **számítógép-tanúsítványok kezelése**elemet. Megnyílik egy **certlm** nevű segédprogram.
2. Navigáljon a **tanúsítványok – helyi számítógép** > **megbízható legfelső szintű hitelesítésszolgáltatók**elemre.
3. Kattintson a jobb gombbal a **tanúsítványok** elemre, és válassza **az összes feladat** > **Importálás**lehetőséget. A Tanúsítványimportáló varázsló kell elindítani. 
4. Kövesse az utasításokat a következő módon: utasítás és importálás tanúsítványfájl `<path>/azure-iot-test-only.root.ca.cert.pem`. Amikor elkészült, egy "Importálása sikeresen befejeződött" üzenetnek kell megjelennie. 

Tanúsítványokat programozott módon a .NET API-k, ahogyan az a .NET-minta a cikk későbbi részében is telepítheti. 

Az alkalmazások általában a [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) nevű Windows-adatkészletet használják a TLS-kapcsolat biztonságos csatlakoztatásához. A Schannel *megköveteli* , hogy a tanúsítványokat a Windows-tanúsítványtárolóba telepítse, mielőtt TLS-kapcsolatot hozna létre.

## <a name="use-certificates-with-azure-iot-sdks"></a>Tanúsítványok használata az Azure IoT SDK-k

Ez a szakasz ismerteti, hogyan csatlakozhat az Azure IoT SDK-k IoT Edge-eszköz használatával egyszerű mintaalkalmazások. Az összes minta célja, hogy csatlakozzon az eszközügyfél és telemetriai üzeneteket küldeni az átjárón, majd zárja be a kapcsolat és kilépéshez. 

Készen áll az alkalmazásszintű minták használata előtt két dolgokat rendelkezik:

* Az alsóbb rétegbeli eszköz IoT Hub kapcsolódási karakterlánca úgy lett módosítva, hogy az átjáró eszközre mutasson, és az alsóbb rétegbeli eszköz hitelesítéséhez szükséges összes tanúsítvány IoT Hub. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).

* A teljes elérési útját a legfelső szintű Hitelesítésszolgáltatói tanúsítvány másolt és mentett valahová alsóbb rétegbeli eszközén.

    Például: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Ez a témakör egy mintaalkalmazást az Azure IoT NodeJS ügyfél csatlakozik az IoT Edge-átjáró. A NodeJS alkalmazások esetében az itt látható módon telepítenie kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az alkalmazás szintjén. A NodeJS-alkalmazások nem használják a rendszertanúsítvány-tárolót. 

1. A [Node. js-hez készült Azure IoT ESZKÖZOLDALI SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)-ból **edge_downstream_device. js** -hez tartozó minta beszerzése. 
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt. 
3. A edge_downstream_device. js fájlban frissítse a **ConnectionString** és a **edge_ca_cert_path** változót. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

Szeretné megtudni, a minta, amely futtatja, a következő kódrészletet a az ügyfél-SDK beolvassa a tanúsítványfájlt, illetve hogyan használja a TLS biztonságos kapcsolatot létesíteni: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT .NET ügyfél csatlakozik az IoT Edge-átjáró. Azonban .NET-alkalmazások képesek automatikusan a rendszer tanúsítványtárolójában a Linux és a Windows gazdagépekre telepített tanúsítványokat használja.

1. Szerezze be a **EdgeDownstreamDevice** mintáját a [IoT Edge .net Samples mappából](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt. 
3. A **Properties/launchSettings. JSON** fájlban frissítse a **DEVICE_CONNECTION_STRING** és **CA_CERTIFICATE_PATH** változókat. Ha a tanúsítvány telepítve van a megbízható tanúsítványok tárába a rendszer a gazdagép használni kívánt, hagyja üresen ezt a változót. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

Ha egy .NET-alkalmazás használatával szeretne programozott módon telepíteni egy megbízható tanúsítványt a tanúsítványtárolóban, tekintse meg a **InstallCACert ()** függvényt a **EdgeDownstreamDevice/program.cs** fájlban. Tato operace je idempotensek, így futtatható többször ugyanazokkal az értékekkel további hatás nélkül. 

### <a name="c"></a>C#

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT C ügyfél csatlakozik az IoT Edge-átjáró. A C SDK számos TLS-kódtárakat, beleértve az OpenSSL WolfSSL és Schannel működhet. További információkért lásd az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-t. 

1. Szerezze be a **iotedge_downstream_device_sample** alkalmazást a [C minta Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)-ból. 
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt. 
3. A iotedge_downstream_device_sample. c fájlban frissítse a **ConnectionString** és a **edge_ca_cert_path** változót. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

A c nyelvhez készült Azure IoT eszközoldali SDK-t egy Hitelesítésszolgáltatói tanúsítvány regisztrálása, az ügyfél beállításakor lehetőséget biztosít. Ez a művelet nem települ a tanúsítvány bárhol, de inkább használja a tanúsítvány egy karakterlánc-formátum a memóriában. A mentett tanúsítvány megadva az alapul szolgáló TLS stack-kapcsolat létrehozásakor. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows gazdagépekre Ha nem használ OpenSSL vagy a TLS-könyvtár egy másik, az SDK alapértelmezés szerint a Schannel használatával. Ahhoz, hogy a Schannel működjön, a IoT Edge legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a Windows tanúsítványtárolóba kell telepíteni, nem a `IoTHubDeviceClient_SetOption` művelettel. 

### <a name="java"></a>Java

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT-Java ügyfél csatlakozik az IoT Edge-átjáró. 

1. Az [Azure IoT ESZKÖZOLDALI SDK for Java-mintákhoz](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)tartozó **Send-Event** minta beszerzése. 
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt. 
3. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját.

### <a name="python"></a>Python

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT Python ügyfél csatlakozik az IoT Edge-átjáró. 

1. Szerezze be a **send_message** mintáját a [Python-mintákhoz készült Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios)-val. 
2. Győződjön meg arról, hogy egy IoT Edge tárolóban fut, vagy hibakeresési forgatókönyvben a `EdgeHubConnectionString` és a `EdgeModuleCACertificateFile` környezeti változók vannak beállítva.
3. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 


## <a name="test-the-gateway-connection"></a>Az átjáró-kapcsolat tesztelése

Ezzel a minta paranccsal ellenőrizheti, hogy az alsóbb rétegbeli eszköz tud-e csatlakozni az átjáró eszközéhez: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Ez a parancs a MQTTS (8883-es porton) keresztül ellenőrzi a kapcsolatokat. Ha más protokollt használ, módosítsa a parancsot a AMQPS (5671) vagy a HTTPS (433) számára szükséges módon.

A parancs kimenete hosszú lehet, beleértve a láncban lévő összes tanúsítvány adatait is. Ha a sikeres a kapcsolatok, egy sor jelenik meg, például `Verification: OK` vagy `Verify return code: 0 (ok)`.

![Átjáró-kapcsolatok ellenőrzése](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Az átjáró-kapcsolatok hibáinak megoldása

Ha a levélben lévő eszköz időnként csatlakozik az átjáró eszközéhez, próbálkozzon a következő lépésekkel a megoldáshoz. 

1. Az átjáró gazdagépének állomásneve megegyezik a (z) IoT Edge config. YAML fájlban található állomásnév értékével az átjáró eszközén?
2. Feloldható az átjáró állomásneve egy IP-címhez? Az időszakos kapcsolatokat a DNS használatával vagy egy gazda-fájl bejegyzésének a levél eszközön való hozzáadásával oldhatja fel.
3. Nyitva vannak a kommunikációs portok a tűzfalon? A használt protokollon alapuló (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) kommunikációnak az alsóbb rétegbeli eszköz és az átlátszó IoT Edge között kell lennie.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan bővítheti IoT Edge az [Offline képességeket](offline-capabilities.md) az alsóbb rétegbeli eszközökre. 
