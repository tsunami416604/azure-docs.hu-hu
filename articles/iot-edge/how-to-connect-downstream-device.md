---
title: Konfigurálja az alsóbb rétegbeli eszközök Azure IoT Edge |} A Microsoft Docs
description: Aktiválásához konfigurálása vagy levéleszközök gateway-eszközök Azure IoT Edge-en keresztül csatlakozni.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b9993650492574fc45b7f15fa3424060079f5fe
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50915299"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró

Az Azure IoT Edge lehetővé teszi, hogy a transzparens átjáró-forgatókönyvekhez, ahol egy vagy több eszközt az üzenetek tevékenységeken keresztül egyetlen átjáróeszköz, amely fenntartja az IoT Hub-kapcsolat. Miután az átjáró eszköz konfigurálva, hogy való biztonságos kapcsolódás az alsóbb rétegbeli eszközök ismernie kell. 

Ez a cikk azonosítja az alsóbb rétegbeli eszközkapcsolatok szolgáltatással kapcsolatos gyakori problémák, és végigvezeti Önt az alsóbb rétegbeli eszközök által beállításához: 

* Elmagyarázza transport layer security (TLS) és a tanúsítvány alapjai. 
* Elmagyarázza a különböző operációs rendszerekből TLS könyvtárak működését, és hogy mindegyik operációs rendszer hogyan kezelje a tanúsítványokat.
* Ajánljuk figyelmébe az Azure IoT elindult a minták több nyelven is nyújtanak segítséget. 

Ez a cikk a használati *átjáró* és *IoT Edge-átjáró* transzparens átjáróként konfigurált IoT Edge-eszköz hivatkoznak. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben található lépések elvégzése előtt rendelkeznie kell két eszköz használatra kész:

1. IoT Edge-eszköz beállítása a transzparens átjáróként. 
    [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)

    Miután az átjáró eszköz konfigurált, másolja a **azure-iot-teszt – only.root.ca.cert.pem** tanúsítvány az átjáró, és bárhol az alsóbb rétegbeli eszközök már nem érhető el. 

2. Egy alárendelt eszköz, amely rendelkezik egy eszközidentitást az IoT hubról. 
    Az alsóbb rétegbeli eszközök IoT Edge-eszköz nem használható. Ehelyett használjon rendszeres IoT-eszközt az IoT Hub regisztrált eszközt. A portálon regisztrálhat egy új eszközt az **IoT-eszközök** szakaszban. Vagy használhatja az Azure CLI használatával [eszköz regisztrálása](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Másolja a kapcsolati karakterláncot, és hogy elérhető legyen a későbbi szakaszokban. 

    Jelenleg csak a szimmetrikus kulcsot az alsóbb rétegbeli eszközök csatlakozhatnak az IoT Edge-átjárókon keresztül. X.509-hitelesítésszolgáltatók és önaláírt X.509-tanúsítványok jelenleg nem támogatottak. 

## <a name="prepare-a-downstream-device"></a>Alsóbb rétegbeli eszközök előkészítése

Lehet, hogy egy alsóbb rétegbeli eszköz bármilyen alkalmazás vagy a platform, amely rendelkezik egy létrehozott identitás a [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőalapú szolgáltatás. Sok esetben ezek az alkalmazások használni a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md). Gyakorlati okokból azonban egy alsóbb rétegbeli eszközök az IoT Edge-átjáróeszköz magát a futó alkalmazás még akkor is lehet. 

Egy alárendelt eszköz csatlakoztatása az IoT Edge-átjáró, két dologra van szükség:

1. Egy eszköz vagy alkalmazás, amelynek része az IoT Hub eszköz kapcsolati karakterláncának hozzáfűzi az információkat az átjáró csatlakozni. 

    Például a kapcsolati karakterlánc formátuma: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Fűzze hozzá a **GatewayHostName** tulajdonság az átjáróeszköz, a kapcsolati karakterlánc végén az állomásnévvel. Értékét **GatewayHostName** értékét meg kell egyeznie **állomásnév** az átjáróeszköz config.yaml fájlban. 

    A végső karakterláncban néz ki: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Az eszköz vagy alkalmazás rendelkezik az átjáró megbízhatónak **legfelső szintű hitelesítésszolgáltató** vagy **tulajdonosa hitelesítésszolgáltató** főtanúsítványként az átjáróeszközhöz a TLS kapcsolatok. 

    Ez bonyolultabb lépesek részletes Ez a cikk további részében található. Ebben a lépésben lehet elvégezni egy kétféleképpen: telepítse a Hitelesítésszolgáltatói tanúsítvány az operációs rendszer tanúsítványtároló vagy (az egyes nyelvekhez) hivatkozik a tanúsítvány-alkalmazásokban az Azure IoT SDK-k használatával.

## <a name="tls-and-certificate-fundamentals"></a>A TLS és a tanúsítvány – alapok

Az igazi kihívást a alsóbb rétegbeli eszközök biztonságos csatlakoztatása az IoT Edge van, mint bármely más biztonságos ügyfél/kiszolgáló közötti kommunikációhoz, amely az interneten keresztül történik. Egy ügyfél és a egy kiszolgáló biztonságosan kommunikálhassanak az internet használatával [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). A TLS standard használatával lett összeállítva [nyilvános kulcsokra épülő infrastruktúrájú (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) szerkezeteket nevű tanúsítványokat. TLS viszonylag érintett specifikáció, és címek széles két végpontok védelme kapcsolatos témaköröket, de a következő szakasz tömören ismerteti, mire van szükség a biztonságos eszközök csatlakoztatása az IoT Edge-átjáró.

Amikor egy ügyfél csatlakozik egy kiszolgálóhoz, a kiszolgáló által bemutatott olyan tanúsítványláncból áll, úgynevezett a *kiszolgáló tanúsítványlánca*. Tanúsítványlánc általában egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvány, egy vagy több köztes Hitelesítésszolgáltatói tanúsítványt, és végül magát a kiszolgáló tanúsítványa foglalja magában. Ügyfél létesít megbízhatósági kapcsolat a kiszolgálóval titkosítási szempontból a teljes kiszolgáló tanúsítványlánca ellenőrzésével. Ez a kiszolgáló tanúsítványlánca ügyfél érvényesítése nevezzük *kiszolgálóhitelesítés*. A kiszolgáló tanúsítványlánca ellenőrzéséhez az ügyfélnek kell a legfelső szintű Hitelesítésszolgáltatói tanúsítvány használt létrehozása (vagy ki) a kiszolgálói tanúsítvány egy példányát. Általában webhelyekhez csatlakoznak, amikor egy böngészőben érhető el a gyakran használt CA-tanúsítványok az előre konfigurált, az ügyfél rendelkezik egy zökkenőmentes folyamat. 

Ha egy eszköz csatlakozik, az Azure IoT Hub, az eszköz az ügyfél és az IoT Hub-felhőszolgáltatás az a kiszolgáló. Az IoT Hub felhőalapú szolgáltatás alapját egy legfelső szintű Hitelesítésszolgáltatói tanúsítvány nevű **Baltimore CyberTrust legfelsőbb szintű**, olyan nyilvánosan elérhető és széles körben használt. Az IoT Hub Hitelesítésszolgáltatói tanúsítványt a legtöbb eszköz már telepítve van, mivel számos TLS (Schannel, OpenSSL LibreSSL) automatikusan megvalósításokban ez kiszolgálói tanúsítvány érvényesítése során. Előfordulhat, hogy sikeresen csatlakozni az IoT Hub eszköz előfordulhat, hogy problémákba szeretne csatlakozni az IoT Edge-átjáró.

Amikor egy eszköz csatlakozik az IoT Edge-átjáró, az alsóbb rétegbeli eszköz az ügyfél és az átjáró eszköz a kiszolgálón. Az Azure IoT Edge lehetővé teszi a kezelők (vagy felhasználók) hozhat létre az átjáró tanúsítványláncok, azonban az igényei. Az operátor választhat nyilvános hitelesítésszolgáltató tanúsítvány, például a Baltimore, vagy egy önaláírt (vagy belső fejlesztésű) legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Nyilvános hitelesítésszolgáltató tanúsítványokat gyakran kell díjat fizetni, társított őket, tehát jellemzően termelési forgatókönyvekhez. Önaláírt CA-tanúsítványok használata javasolt, fejlesztési és tesztelési célra. A transzparens átjáró telepítő cikkek az Előfeltételek szakaszban felsorolt önaláírt legfelső szintű Hitelesítésszolgáltatói tanúsítványokat használnak. 

Amikor egy IoT Edge-átjáró egy önaláírt főtanúsítványt használ, telepíteni kell, vagy az alsóbb rétegbeli eszközökhöz szeretne csatlakozni az átjáró a megadott. 

További információ az IoT Edge-tanúsítványok és néhány éles implications kapcsolatban lásd: [IoT Edge használatának Tanúsítványadatok](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>A tanúsítványok használatával az operációs rendszer telepítése

Ez a cikk *tulajdonosa hitelesítésszolgáltató* hivatkozik a legfelső szintű Hitelesítésszolgáltatói tanúsítvány, mivel az a kifejezés az előfeltételként szükséges átjáró a cikkben a parancsfájlok által használt. 

A tulajdonos Hitelesítésszolgáltatói tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában általában lehetővé teszi, hogy a legtöbb alkalmazás a tulajdonos használandó Hitelesítésszolgáltatói tanúsítvány. Vannak olyan kivételek, például a NodeJS-alkalmazást, amely nem az operációs rendszer tanúsítványtárolóinak használatára, de inkább használja a csomópont-futtatókörnyezet belső tanúsítványtárolójába. Ha a tanúsítvány az operációs rendszer szintjén nem tudja telepíteni, tekintse meg a nyelvspecifikus példák a tanúsítvány használata az Azure IoT SDK-alkalmazásokban Ez a cikk későbbi részében. 

### <a name="ubuntu"></a>Ubuntu

Az alábbi parancsok olyan hitelesítésszolgáltató-tanúsítvány telepítése egy Ubuntu-gazdagép egy példát. Ez a példa feltételezi, hogy használja a **azure-iot-teszt – only.root.ca.cert.pem** az Előfeltételek cikkeket a tanúsítványt, és az, hogy a tanúsítvány másolta az alsóbb rétegbeli eszközön egy helyre.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Megjelenik egy üzenet, amely szerint a "a tanúsítványok frissítése az /etc/ssl/certs... 1 hozzáadása, eltávolítása 0; kész."

### <a name="windows"></a>Windows

A következő lépéseket kell egy példát egy Hitelesítésszolgáltatói tanúsítvány telepítése a Windows-gazdagépen. Ez a példa feltételezi, hogy használja a **azure-iot-teszt – only.root.ca.cert.pem** az Előfeltételek cikkeket a tanúsítványt, és az, hogy a tanúsítvány másolta az alsóbb rétegbeli eszközön egy helyre.  

1. A Start menüben keresse meg és válassza **számítógép-tanúsítványok kezelése**. A segédprogram nevű **certlm** nyílik meg.
2. Navigáljon a **tanúsítványok – helyi számítógép** > **megbízható legfelső szintű hitelesítésszolgáltatók**.
3. Kattintson a jobb gombbal **tanúsítványok** válassza **feladatok** > **importálás**. A Tanúsítványimportáló varázsló kell elindítani. 
4. Kövesse a lépéseket megfelelően, és importálja a tanúsítványfájl `<path>/azure-iot-test-only.root.ca.cert.pem`. Amikor elkészült, egy "Importálása sikeresen befejeződött" üzenetnek kell megjelennie. 

Tanúsítványokat programozott módon a .NET API-k, ahogyan az a .NET-minta a cikk későbbi részében is telepítheti. 

Általában alkalmazásokat használnak-e a Windows a megadott TLS stack nevű [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) való biztonságos TLS protokollon keresztüli kapcsolódás. Schannel *igényel* , hogy a tanúsítványok előtt telepíteni kell a Windows-tárolóban található próbál létesíteni egy TLS-kapcsolatot.

## <a name="use-certificates-with-azure-iot-sdks"></a>Tanúsítványok használata az Azure IoT SDK-k

Ez a cikk hivatkozik a legfelső szintű Hitelesítésszolgáltatói tanúsítványt a *tulajdonosa hitelesítésszolgáltató* mivel az a parancsfájlok, amelyek létrehozzák az önaláírt tanúsítványt az Előfeltételek cikkekben használt kifejezés. 

Ez a szakasz ismerteti, hogyan csatlakozhat az Azure IoT SDK-k IoT Edge-eszköz használatával egyszerű mintaalkalmazások. Az összes minta célja, hogy csatlakozzon az eszközügyfél és telemetriai üzeneteket küldeni az átjárón, majd zárja be a kapcsolat és kilépéshez. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Az összes Azure IoT SDK-k között általánosan használt fogalmak

Készen áll az alkalmazásszintű minták használata előtt két dolgokat rendelkezik:

1. Az alsóbb rétegbeli eszközök az IoT Hub kapcsolati karakterláncot módosítva, hogy az átjáró eszköz mutasson.

    Például a kapcsolati karakterlánc formátuma: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Fűzze hozzá a **GatewayHostName** tulajdonság az átjáróeszköz, a kapcsolati karakterlánc végén az állomásnévvel. Értékét **GatewayHostName** értékét meg kell egyeznie **állomásnév** az átjáróeszköz config.yaml fájlban. 

    A végső karakterláncban néz ki: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. A teljes elérési útját a legfelső szintű Hitelesítésszolgáltatói tanúsítvány másolt és mentett valahová alsóbb rétegbeli eszközén.

    Például: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Ez a témakör egy mintaalkalmazást az Azure IoT NodeJS ügyfél csatlakozik az IoT Edge-átjáró. A Linux és Windows gazdagépekre telepítenie kell a legfelső szintű Hitelesítésszolgáltatói tanúsítvány az alkalmazás szintjén, itt látható módon, a NodeJS-alkalmazásokat ne használja a rendszer tanúsítványtárolójába. 

1. A minta első **edge_downstream_device.js** származó a [node.js-hez készült Azure IoT eszközoldali SDK-minták tárház](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Győződjön meg arról, hogy rendelkezik-e a minta futtatásához áttekintésével összes előfeltételt, a **readme.md** fájlt. 
3. A edge_downstream_device.js fájlban frissítse a **connectionString** és **edge_ca_cert_path** változókat. 
4. Tekintse meg az eszközön a minta futtatásához útmutatást az SDK dokumentációját. 

Szeretné megtudni, a minta, amely futtatja, a következő kódrészletet a az ügyfél-SDK beolvassa a tanúsítványfájlt, illetve hogyan használja a TLS biztonságos kapcsolatot létesíteni: 

```nodejs
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

Ez egy mintául szolgáló parancs melyik tesztek mindent, állítsa be megfelelően a. Megtekintheti az "OK ellenőrzése" hibaüzenet.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan ki az IoT Edge [offline képességeiről](offline-capabilities.md) alsóbb rétegbeli eszközökhöz. 