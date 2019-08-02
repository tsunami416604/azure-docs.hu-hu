---
title: Csatlakozás az alsóbb rétegbeli eszközök – Azure IoT Edge |} A Microsoft Docs
description: Az alsóbb rétegbeli vagy a levélben lévő eszközök konfigurálása Azure IoT Edge Gateway-eszközökhöz való kapcsolódáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f739bdbd295662006a964f890147ad67c373d7b5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698631"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró

Ez a cikk útmutatást nyújt az alsóbb rétegbeli eszközök és a IoT Edge transzparens átjárók közötti megbízható kapcsolat létesítéséhez. Transzparens átjáró esetén egy vagy több eszköz átadhatja az üzeneteiket egyetlen átjárón keresztül, amely karbantartja a kapcsolatot IoT Hub. Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. 

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a harmadik lépést ismerteti:

1. Az átjáró-eszköznek biztonságosan csatlakoznia kell az alárendelt eszközökhöz, kommunikációt kell fogadnia az alsóbb rétegbeli eszközökről, és továbbítania kell az üzeneteket a megfelelő célhelyre. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz identitásával, hogy a hitelesítés a IoT Hub használatával történjen, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. **Az alsóbb rétegbeli eszköznek képesnek kell lennie az átjáró eszközéhez való biztonságos kapcsolódásra.**

Ez a cikk azonosítja az alsóbb rétegbeli eszközkapcsolatok szolgáltatással kapcsolatos gyakori problémák, és végigvezeti Önt az alsóbb rétegbeli eszközök által beállításához: 

* Elmagyarázza transport layer security (TLS) és a tanúsítvány alapjai. 
* Elmagyarázza a különböző operációs rendszerekből TLS könyvtárak működését, és hogy mindegyik operációs rendszer hogyan kezelje a tanúsítványokat.
* Ajánljuk figyelmébe az Azure IoT elindult a minták több nyelven is nyújtanak segítséget. 

Ez a cikk a használati *átjáró* és *IoT Edge-átjáró* transzparens átjáróként konfigurált IoT Edge-eszköz hivatkoznak. 

## <a name="prepare-a-downstream-device"></a>Alsóbb rétegbeli eszközök előkészítése

Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. Azonban egy másik IoT Edge eszköz nem lehet egy IoT Edge átjárón. 

>[!NOTE]
>A IoT Hubban regisztrált identitásokkal rendelkező eszközök IoT a különböző [](../iot-hub/iot-hub-devguide-module-twins.md) folyamatok, hardverek vagy függvények egyetlen eszközön való elkülönítéséhez használhatók. IoT Edge átjárók támogatják az alárendelt modulok kapcsolatait a szimmetrikus kulcsos hitelesítéssel, de X. 509 tanúsítványalapú hitelesítést nem. 

Egy alárendelt eszköz csatlakoztatása az IoT Edge-átjáró, két dologra van szükség:

* Egy eszköz vagy alkalmazás, amelynek része az IoT Hub eszköz kapcsolati karakterláncának hozzáfűzi az információkat az átjáró csatlakozni. 

    Ez a lépés [egy alsóbb rétegbeli eszköz Azure-IoT hub való hitelesítését](how-to-authenticate-downstream-device.md)ismerteti.

* Az eszköznek vagy alkalmazásnak meg kell bíznia az átjáró **legfelső szintű hitelesítésszolgáltatói** tanúsítványán, hogy ellenőrizze az ÁTJÁRÓ eszköz TLS-kapcsolatait. 

    Ezt a lépést részletesen ismertetjük a cikk további részében. Ez a lépés kétféleképpen végezhető el: a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítésével az operációs rendszer tanúsítványtárolójában vagy (bizonyos nyelveken) az Azure IoT SDK-kat használó alkalmazásokon belüli tanúsítványra hivatkozva.

## <a name="tls-and-certificate-fundamentals"></a>A TLS és a tanúsítvány – alapok

Az igazi kihívást a alsóbb rétegbeli eszközök biztonságos csatlakoztatása az IoT Edge van, mint bármely más biztonságos ügyfél/kiszolgáló közötti kommunikációhoz, amely az interneten keresztül történik. Egy ügyfél és a egy kiszolgáló biztonságosan kommunikálhassanak az internet használatával [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). A TLS standard használatával lett összeállítva [nyilvános kulcsokra épülő infrastruktúrájú (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) szerkezeteket nevű tanúsítványokat. A TLS meglehetősen érintett specifikáció, és a két végpont biztonságossá tételével kapcsolatos témakörök széles körét kezeli. Ez a szakasz az eszközök IoT Edge átjáróhoz való biztonságos csatlakoztatásához szükséges fogalmakat összegzi.

Amikor egy ügyfél csatlakozik egy kiszolgálóhoz, a kiszolgáló által bemutatott olyan tanúsítványláncból áll, úgynevezett a *kiszolgáló tanúsítványlánca*. Tanúsítványlánc általában egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvány, egy vagy több köztes Hitelesítésszolgáltatói tanúsítványt, és végül magát a kiszolgáló tanúsítványa foglalja magában. Ügyfél létesít megbízhatósági kapcsolat a kiszolgálóval titkosítási szempontból a teljes kiszolgáló tanúsítványlánca ellenőrzésével. A kiszolgálói tanúsítvány láncának ezt az ügyfél-érvényesítését a *kiszolgálói lánc érvényesítésének*nevezzük. Az ügyfél kriptográfiailag megküzdi a szolgáltatást, hogy igazolja a tanúsítványhoz tartozó titkos kulcsot egy olyan folyamatban, amelynek a *birtokában bizonyítás*történt. A kiszolgálói lánc érvényesítésének és a birtokon tartás igazolásának kombinációját *kiszolgáló hitelesítésnek*nevezzük. A kiszolgáló tanúsítványlánca ellenőrzéséhez az ügyfélnek kell a legfelső szintű Hitelesítésszolgáltatói tanúsítvány használt létrehozása (vagy ki) a kiszolgálói tanúsítvány egy példányát. Általában webhelyekhez csatlakoznak, amikor egy böngészőben érhető el a gyakran használt CA-tanúsítványok az előre konfigurált, az ügyfél rendelkezik egy zökkenőmentes folyamat. 

Ha egy eszköz csatlakozik, az Azure IoT Hub, az eszköz az ügyfél és az IoT Hub-felhőszolgáltatás az a kiszolgáló. Az IoT Hub felhőalapú szolgáltatás alapját egy legfelső szintű Hitelesítésszolgáltatói tanúsítvány nevű **Baltimore CyberTrust legfelsőbb szintű**, olyan nyilvánosan elérhető és széles körben használt. Az IoT Hub Hitelesítésszolgáltatói tanúsítványt a legtöbb eszköz már telepítve van, mivel számos TLS (Schannel, OpenSSL LibreSSL) automatikusan megvalósításokban ez kiszolgálói tanúsítvány érvényesítése során. Előfordulhat, hogy sikeresen csatlakozni az IoT Hub eszköz előfordulhat, hogy problémákba szeretne csatlakozni az IoT Edge-átjáró.

Amikor egy eszköz csatlakozik az IoT Edge-átjáró, az alsóbb rétegbeli eszköz az ügyfél és az átjáró eszköz a kiszolgálón. Az Azure IoT Edge lehetővé teszi a kezelők (vagy felhasználók) hozhat létre az átjáró tanúsítványláncok, azonban az igényei. Az operátor választhat nyilvános hitelesítésszolgáltató tanúsítvány, például a Baltimore, vagy egy önaláírt (vagy belső fejlesztésű) legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Nyilvános hitelesítésszolgáltató tanúsítványokat gyakran kell díjat fizetni, társított őket, tehát jellemzően termelési forgatókönyvekhez. Önaláírt CA-tanúsítványok használata javasolt, fejlesztési és tesztelési célra. A bevezetésben felsorolt transzparens átjáró beállítási cikkei önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat használnak. 

Amikor egy IoT Edge-átjáró egy önaláírt főtanúsítványt használ, telepíteni kell, vagy az alsóbb rétegbeli eszközökhöz szeretne csatlakozni az átjáró a megadott. 

![Átjáró tanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

További információ az IoT Edge-tanúsítványok és néhány éles implications kapcsolatban lásd: [IoT Edge használatának Tanúsítványadatok](iot-edge-certs.md).

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

Megjelenik egy üzenet, amely szerint a "a tanúsítványok frissítése az /etc/ssl/certs... 1 hozzáadása, eltávolítása 0; kész."

### <a name="windows"></a>Windows

A következő lépéseket kell egy példát egy Hitelesítésszolgáltatói tanúsítvány telepítése a Windows-gazdagépen. Ez a példa azt feltételezi, hogy a **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványt használja az előfeltételek cikkeiből, és a tanúsítványt egy helyre másolta az alsóbb rétegbeli eszközön.

1. A Start menüben keresse meg és válassza **számítógép-tanúsítványok kezelése**. A segédprogram nevű **certlm** nyílik meg.
2. Navigáljon a **tanúsítványok – helyi számítógép** > **megbízható legfelső szintű hitelesítésszolgáltatók**.
3. Kattintson a jobb gombbal **tanúsítványok** válassza **feladatok** > **importálás**. A Tanúsítványimportáló varázsló kell elindítani. 
4. Kövesse a lépéseket megfelelően, és importálja a tanúsítványfájl `<path>/azure-iot-test-only.root.ca.cert.pem`. Amikor elkészült, egy "Importálása sikeresen befejeződött" üzenetnek kell megjelennie. 

Tanúsítványokat programozott módon a .NET API-k, ahogyan az a .NET-minta a cikk későbbi részében is telepítheti. 

Általában alkalmazásokat használnak-e a Windows a megadott TLS stack nevű [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) való biztonságos TLS protokollon keresztüli kapcsolódás. Schannel *igényel* , hogy a tanúsítványok előtt telepíteni kell a Windows-tárolóban található próbál létesíteni egy TLS-kapcsolatot.

## <a name="use-certificates-with-azure-iot-sdks"></a>Tanúsítványok használata az Azure IoT SDK-k

Ez a szakasz ismerteti, hogyan csatlakozhat az Azure IoT SDK-k IoT Edge-eszköz használatával egyszerű mintaalkalmazások. Az összes minta célja, hogy csatlakozzon az eszközügyfél és telemetriai üzeneteket küldeni az átjárón, majd zárja be a kapcsolat és kilépéshez. 

Készen áll az alkalmazásszintű minták használata előtt két dolgokat rendelkezik:

* Az alsóbb rétegbeli eszköz IoT Hub kapcsolódási karakterlánca úgy lett módosítva, hogy az átjáró eszközre mutasson, és az alsóbb rétegbeli eszköz hitelesítéséhez szükséges összes tanúsítvány IoT Hub. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).

* A teljes elérési útját a legfelső szintű Hitelesítésszolgáltatói tanúsítvány másolt és mentett valahová alsóbb rétegbeli eszközén.

    Például: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Ez a témakör egy mintaalkalmazást az Azure IoT NodeJS ügyfél csatlakozik az IoT Edge-átjáró. A NodeJS alkalmazások esetében az itt látható módon telepítenie kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az alkalmazás szintjén. A NodeJS-alkalmazások nem használják a rendszertanúsítvány-tárolót. 

1. A minta első **edge_downstream_device.js** származó a [node.js-hez készült Azure IoT eszközoldali SDK-minták tárház](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. A edge_downstream_device.js fájlban frissítse a **connectionString** és **edge_ca_cert_path** változókat. 
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

1. A minta első **EdgeDownstreamDevice** származó a [IoT Edge .NET mintákat tartalmazó mappára](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. Az a **tulajdonságok / launchSettings.json** fájlt, frissítse a **DEVICE_CONNECTION_STRING** és **CA_CERTIFICATE_PATH** változókat. Ha a tanúsítvány telepítve van a megbízható tanúsítványok tárába a rendszer a gazdagép használni kívánt, hagyja üresen ezt a változót. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

Programozott módon telepíti a megbízható tanúsítványt a tanúsítványtárolóban .NET-alkalmazás használatával, tekintse meg a **InstallCACert()** működni a **EdgeDownstreamDevice / Program.cs** fájlt. Tato operace je idempotensek, így futtatható többször ugyanazokkal az értékekkel további hatás nélkül. 

### <a name="c"></a>C

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT C ügyfél csatlakozik az IoT Edge-átjáró. A C SDK számos TLS-kódtárakat, beleértve az OpenSSL WolfSSL és Schannel működhet. További információkért lásd: a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

1. Első a **iotedge_downstream_device_sample** az alkalmazás a [Azure IoT eszközoldali SDK-C-minták az](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. A iotedge_downstream_device_sample.c fájlban frissítse a **connectionString** és **edge_ca_cert_path** változókat. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

A c nyelvhez készült Azure IoT eszközoldali SDK-t egy Hitelesítésszolgáltatói tanúsítvány regisztrálása, az ügyfél beállításakor lehetőséget biztosít. Ez a művelet nem települ a tanúsítvány bárhol, de inkább használja a tanúsítvány egy karakterlánc-formátum a memóriában. A mentett tanúsítvány megadva az alapul szolgáló TLS stack-kapcsolat létrehozásakor. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows gazdagépekre Ha nem használ OpenSSL vagy a TLS-könyvtár egy másik, az SDK alapértelmezés szerint a Schannel használatával. A Schannel működjön, az IoT Edge legfelső szintű Hitelesítésszolgáltatói tanúsítványt kell telepíteni, nincs beállítva a használatával a Windows tanúsítványtároló a `IoTHubDeviceClient_SetOption` műveletet. 

### <a name="java"></a>Java

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT-Java ügyfél csatlakozik az IoT Edge-átjáró. 

1. A minta első **küldési-esemény** származó a [Azure IoT eszközoldali SDK-Java-példák az](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját.

### <a name="python"></a>Python

Ez a szakasz bemutatja egy mintaalkalmazást az Azure IoT Python ügyfél csatlakozik az IoT Edge-átjáró. 

1. A minta első **edge_downstream_client** származó a [Python-minták az Azure IoT eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. A edge_downstream_client.py fájlban frissítse a **connections_string** és **TRUSTED_ROOT_CA_CERTIFICATE_PATH** változókat. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 


## <a name="test-the-gateway-connection"></a>Az átjáró-kapcsolat tesztelése

Ez egy minta-parancs, amely azt ellenőrzi, hogy minden megfelelően be van-e állítva. Megtekintheti az "OK ellenőrzése" hibaüzenet.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Az átjáró-kapcsolatok hibáinak megoldása

Ha a levélben lévő eszköz időnként csatlakozik az átjáró eszközéhez, próbálkozzon a következő lépésekkel a megoldáshoz. 

1. Az átjáró gazdagépének állomásneve megegyezik a (z) IoT Edge config. YAML fájlban található állomásnév értékével az átjáró eszközén?
2. Feloldható az átjáró állomásneve egy IP-címhez? Az időszakos kapcsolatokat a DNS használatával vagy egy gazda-fájl bejegyzésének a levél eszközön való hozzáadásával oldhatja fel.
3. Nyitva vannak a kommunikációs portok a tűzfalon? A használt protokollon alapuló (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) kommunikációnak az alsóbb rétegbeli eszköz és az átlátszó IoT Edge között kell lennie.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan ki az IoT Edge [offline képességeiről](offline-capabilities.md) alsóbb rétegbeli eszközökhöz. 
