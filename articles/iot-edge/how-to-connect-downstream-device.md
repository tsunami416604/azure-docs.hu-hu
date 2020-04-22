---
title: Alsóbb rétegbeli eszközök csatlakoztatása – Azure IoT Edge | Microsoft dokumentumok
description: Hogyan konfigurálhatja az alsóbb rétegbeli vagy levélalapú eszközöket az Azure IoT Edge átjáróeszközökhöz való csatlakozáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3113f01341d2a1ec6160cfea3eb9d12d18b8495c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687171"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz

Ez a cikk utasításokat tartalmaz az alsóbb rétegbeli eszközök és az IoT Edge transzparens átjárók közötti megbízható kapcsolat létrehozásához. Egy transzparens átjáró forgatókönyv, egy vagy több eszköz átadhatja az üzeneteket egyetlen átjáróeszközön keresztül, amely fenntartja a kapcsolatot az IoT Hub. Az alsóbb rétegbeli eszköz bármely olyan alkalmazás vagy platform lehet, amely rendelkezik az [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőszolgáltatással létrehozott identitással. Ezek az alkalmazások sok esetben az [Azure IoT-eszköz SDK-t](../iot-hub/iot-hub-devguide-sdks.md)használják. Egy alsóbb rétegbeli eszköz is lehet egy alkalmazás fut az IoT Edge átjáró eszköz maga.

A sikeres transzparens átjárókapcsolat beállításának három általános lépése van. Ez a cikk a harmadik lépést ismerteti:

1. Az átjáróeszköznek biztonságosan csatlakoznia kell az alsóbb rétegbeli eszközökhöz, fogadnia kell a kommunikációt az alsóbb rétegbeli eszközökről, és üzeneteket kell irányítania a megfelelő célhelyre. További információ: [Az IoT Edge-eszköz beállítása transzparens átjáróként.](how-to-create-transparent-gateway.md)
2. Az alsóbb rétegbeli eszköznek szüksége van egy eszközidentitásra ahhoz, hogy hitelesítse magát az IoT Hubbal, és ismerje az átjáróeszközön keresztüli kommunikációt. További információ: [Hitelesítette az alsóbb rétegbeli eszközt az Azure IoT Hubhoz.](how-to-authenticate-downstream-device.md)
3. **Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáróeszközhöz.**

Ez a cikk az alsóbb rétegbeli eszközkapcsolatokkal kapcsolatos gyakori problémákat azonosítja, és az:

* A szállítási réteg biztonságának (TLS) és a tanúsítvány alapjainak magyarázata.
* A TLS-tárak működésének ismertetése a különböző operációs rendszerek között, valamint az egyes operációs rendszerek tanúsítványainak kezelése.
* Az Azure IoT-minták több nyelven történő átnézése a kezdéshez.

Ebben a cikkben a kifejezések *átjáró* és *az IoT Edge-átjáró* egy Átlátszó átjáróként konfigurált IoT Edge-eszköz hivatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Az **azure-iot-test-only.root.ca.cert.pem** tanúsítványfájl, amely az [IoT Edge-eszköz konfigurálása az IoT Edge-eszköz](how-to-create-transparent-gateway.md) az alsóbb rétegbeli eszközön elérhető transzparens átjáróként működjön. Az alsóbb rétegbeli eszköz ezt a tanúsítványt használja az átjáróeszköz identitásának ellenőrzéséhez.
* A módosított kapcsolati karakterlánc, amely az átjáró eszközre mutat, ahogy azt [az Azure IoT Hubnak egy alsóbb rétegbeli eszköz hitelesítése](how-to-authenticate-downstream-device.md)című részben ismerteti.

## <a name="prepare-a-downstream-device"></a>Alsóbb rétegbeli eszköz előkészítése

Az alsóbb rétegbeli eszköz bármely olyan alkalmazás vagy platform lehet, amely rendelkezik az [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőszolgáltatással létrehozott identitással. Ezek az alkalmazások sok esetben az [Azure IoT-eszköz SDK-t](../iot-hub/iot-hub-devguide-sdks.md)használják. Egy alsóbb rétegbeli eszköz is lehet egy alkalmazás fut az IoT Edge átjáró eszköz maga. Azonban egy másik IoT Edge-eszköz nem lehet egy IoT Edge-átjáró.

>[!NOTE]
>Az IoT Hubban regisztrált identitással rendelkező IoT-eszközök [modultwins-ek](../iot-hub/iot-hub-devguide-module-twins.md) használatával elkülöníthetik a különböző folyamatokat, hardvereket vagy függvényeket egyetlen eszközön. Az IoT Edge-átjárók szimmetrikus kulcshitelesítéssel támogatják az alsóbb rétegbeli modulkapcsolatokat, de az X.509 tanúsítványhitelesítést nem.

Ha egy alsóbb rétegbeli eszközt szeretne csatlakoztatni egy IoT Edge-átjáróhoz, két dologra van szüksége:

* Egy eszköz vagy alkalmazás, amely konfigurálva van egy IoT Hub-eszköz kapcsolati karakterlánc hozzáfűzve az adatokat, hogy csatlakoztassa az átjáróhoz.

    Ezt a lépést az [Azure IoT Hubnak egy alsóbb rétegbeli eszköz hitelesítése](how-to-authenticate-downstream-device.md)ismerteti.

* Az eszköznek vagy alkalmazásnak meg kell bíznia az átjáró **legfelső szintű hitelesítésszolgáltatói** tanúsítványát az átjáróeszközhöz való TLS-kapcsolatok érvényesítéséhez.

    Ezt a lépést részletesen ismertetjük a cikk többi részében. Ez a lépés kétféleképpen hajtható végre: a hitelesítésszolgáltatói tanúsítvány telepítésével az operációs rendszer tanúsítványtárolójában, vagy (bizonyos nyelveken) hivatkozva a tanúsítvány alkalmazásokon belül az Azure IoT SDK-k használatával.

## <a name="tls-and-certificate-fundamentals"></a>TLS és tanúsítvány alapjai

Az alsóbb rétegbeli eszközök IoT Edge-hez való biztonságos csatlakoztatásának kihívása ugyanúgy, mint bármely más biztonságos ügyfél/kiszolgáló kommunikáció, amely az interneten keresztül történik. Az ügyfél és a kiszolgáló biztonságosan kommunikálnak az interneten keresztül [a Szállítási réteg biztonsága (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)használatával. A TLS szabványos [nyilvános kulcsú infrastruktúra (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) tanúsítványoknak nevezett konstrukciók használatával épül fel. A TLS egy meglehetősen érintett specifikáció, és két végpont biztosításával kapcsolatos témakörök széles körét foglalkozik. Ez a szakasz összefoglalja az okat a fogalmakat, amelyek az eszközök biztonságos csatlakoztatásához egy IoT Edge-átjáróhoz.

Amikor egy ügyfél csatlakozik egy kiszolgálóhoz, a kiszolgáló egy tanúsítványláncot, a *kiszolgálótanúsítványláncot*jelenít meg. A tanúsítványlánc általában egy legfelső szintű hitelesítésszolgáltatói (CA) tanúsítványból, egy vagy több köztes hitelesítésszolgáltatói tanúsítványból, végül pedig magából a kiszolgáló tanúsítványból áll. Az ügyfél a kiszolgáló teljes tanúsítványláncának kriptográfiai ellenőrzésével hoz létre megbízhatóságot a kiszolgálóval. A kiszolgálói tanúsítványlánc ezen ügyfél-érvényesítése *kiszolgálólánc-érvényesítésnek*van neve. Az ügyfél kriptográfiailag megkérdőjelezi a szolgáltatást, hogy bizonyítsa a kiszolgálótanúsítványhoz társított titkos kulcs birtoklását egy olyan folyamatban, amelyet *a birtoklás igazolásának neveznek.* A kiszolgálólánc-érvényesítés és a birtoklás igazolásának kombinációját *kiszolgálóhitelesítésnek nevezzük.* A kiszolgálói tanúsítványlánc érvényesítéséhez az ügyfélnek szüksége van a kiszolgáló tanúsítványának létrehozásához (vagy kikibocsátásához) használt legfelső szintű hitelesítésszolgáltatói tanúsítvány másolatára. Általában a webhelyekhez való csatlakozáskor a böngésző előre konfigurálva van a gyakran használt hitelesítésszolgáltatói tanúsítványokkal, így az ügyfél zökkenőmentes folyamattal rendelkezik.

Amikor egy eszköz csatlakozik az Azure IoT Hubhoz, az eszköz az ügyfél, az IoT Hub felhőszolgáltatás pedig a kiszolgáló. Az IoT Hub felhőszolgáltatás a **Baltimore CyberTrust Root**nevű legfelső szintű hitelesítésszolgáltatói tanúsítvány, amely nyilvánosan elérhető és széles körben használt. Mivel az IoT Hub hitelesítésszolgáltatói tanúsítványa már telepítve van a legtöbb eszközön, számos TLS-implementáció (OpenSSL, Schannel, LibreSSL) automatikusan használja azt a kiszolgálói tanúsítványok érvényesítése során. Egy eszköz, amely sikeresen csatlakozhat az IoT Hubhoz, problémákat okozhat az IoT Edge-átjáróhoz való kapcsolódás.

Amikor egy eszköz csatlakozik egy IoT Edge-átjáróhoz, az alsóbb rétegbeli eszköz az ügyfél, az átjáró eszköz pedig a kiszolgáló. Az Azure IoT Edge lehetővé teszi az operátorok (vagy felhasználók) számára, hogy átjárótanúsítvány-láncokat építsenek, ahogy jónak látják. Az üzemeltető dönthet úgy, hogy nyilvános hitelesítésszolgáltatói tanúsítványt ( például Baltimore) használ, vagy önaláírt (vagy házon belüli) legfelső szintű hitelesítésszolgáltatói tanúsítványt használ. A nyilvános hitelesítésszolgáltatói tanúsítványok gyakran rendelkeznek költséggel, ezért általában éles környezetben használatosak. Az önaláírt hitelesítésszolgáltatói tanúsítványok at részesítik előnyben a fejlesztéshez és teszteléshez. A bevezetésben felsorolt transzparens átjáróbeállítási cikkek önaláírt legfelső szintű hitelesítésszolgáltatói tanúsítványokat használnak.

Ha önaláírt legfelső szintű hitelesítésszolgáltatói tanúsítványt használ egy IoT Edge-átjáróhoz, azt telepíteni kell, vagy az átjáróhoz csatlakozni próbáló összes alsóbb rétegbeli eszköznek biztosítani kell.

![Átjárótanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

Ha többet szeretne tudni az IoT Edge-tanúsítványokról és néhány éles hatásról, olvassa el az [IoT Edge-tanúsítvány használatának részleteit.](iot-edge-certs.md)

## <a name="provide-the-root-ca-certificate"></a>A legfelső szintű hitelesítésszolgáltatói tanúsítvány biztosítása

Az átjáróeszköz tanúsítványainak ellenőrzéséhez az alsóbb rétegbeli eszköznek szüksége van a legfelső szintű hitelesítésszolgáltatói tanúsítvány saját példányára. Ha az IoT Edge git-tárházban megadott parancsfájlokat használta teszttanúsítványok létrehozásához, akkor a legfelső szintű hitelesítésszolgáltató-tanúsítvány neve **azure-iot-test-only.root.ca.cert.pem**. Ha még nem tette meg a többi alsóbb rétegbeli eszközelőkészítési lépés részeként, helyezze át ezt a tanúsítványfájlt az alsóbb rétegbeli eszköz bármely könyvtárába. Használhatja a szolgáltatás, például [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a függvény, például a Secure copy [protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájl áthelyezéséhez.

## <a name="install-certificates-in-the-os"></a>Tanúsítványok telepítése az operációs rendszerben

A legfelső szintű hitelesítésszolgáltatói tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában általában lehetővé teszi a legtöbb alkalmazás számára a legfelső szintű hitelesítésszolgáltatói tanúsítvány használatát. Vannak kivételek, például a NodeJS-alkalmazások, amelyek nem az operációs rendszer tanúsítványtárolóját használják, hanem inkább a Node runtime belső tanúsítványtárolóját használják. Ha nem tudja telepíteni a tanúsítványt az operációs rendszer szintjén, ugorjon a [Tanúsítványok használata az Azure IoT SDK-kkal című témakörre.](#use-certificates-with-azure-iot-sdks)

### <a name="ubuntu"></a>Ubuntu

A következő parancsok egy példa, hogyan kell telepíteni a hitelesítésszolgáltatótanúsítvány egy Ubuntu host. Ez a példa feltételezi, hogy az **azure-iot-test-only.root.ca.cert.pem** tanúsítványt használja az előfeltételekről szóló cikkekből, és hogy a tanúsítványt az alsóbb rétegbeli eszközön lévő helyre másolta.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Meg kell jelennie egy üzenetet, amely azt mondja: "A tanúsítványok frissítése az /etc/ssl/certs... 1 hozzá, 0 eltávolítva; kész."

### <a name="windows"></a>Windows

Az alábbi lépések egy példa arra, hogyan telepíthet hitelesítésszolgáltatói tanúsítványt egy Windows-állomásra. Ez a példa feltételezi, hogy az **azure-iot-test-only.root.ca.cert.pem** tanúsítványt használja az előfeltételekről szóló cikkekből, és hogy a tanúsítványt az alsóbb rétegbeli eszközön lévő helyre másolta.

A tanúsítványokat a PowerShell [importálási tanúsítványával](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) telepítheti rendszergazdaként:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

A tanúsítványokat a **certlm** segédprogrammal is telepítheti:

1. A Start menüben keresse meg és válassza **a Számítógép-tanúsítványok kezelése parancsot.** Megnyílik egy **certlm** nevű segédprogram.
2. Keresse meg **a Tanúsítványok – Helyi számítógép** > **megbízható legfelső szintű legfelső szintű hitelesítésszolgáltatói nak a megugrását.**
3. Kattintson a jobb gombbal **a Tanúsítványok** elemre, és válassza az **Összes feladat** > **importálása parancsot.** A tanúsítványimportálási varázslónak el kell indulnia.
4. Kövesse az irányított és importtanúsítvány-fájl lépéseit. `<path>/azure-iot-test-only.root.ca.cert.pem` Ha elkészült, meg kell jelennie egy "Sikeresen importált" üzenetnek.

A tanúsítványokat programozott módon is telepítheti a .NET API-k használatával, amint az a cikk későbbi részében található .NET mintában látható.

Az alkalmazások általában a Windows által biztosított TLS-verem, az [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) segítségével biztonságosan csatlakoznak a TLS-en keresztül. Az Schannel *használatához* a TLS-kapcsolat létrehozása előtt telepíteni kell a tanúsítványokat a Windows tanúsítványtárolóban.

## <a name="use-certificates-with-azure-iot-sdks"></a>Tanúsítványok használata az Azure IoT SDK-kkal

Ez a szakasz azt ismerteti, hogy az Azure IoT SDK-k hogyan csatlakoznak egy IoT Edge-eszközhöz egyszerű mintaalkalmazások használatával. Az összes minta célja, hogy csatlakoztassa az eszköz-ügyfél és telemetriai üzeneteket küldeni az átjáróhoz, majd zárja be a kapcsolatot, és lépjen ki.

Az alkalmazásszintű minták használata előtt két dolgot készítsen elő:

* Az alsóbb rétegbeli eszköz IoT Hub-kapcsolati karakterlánca úgy módosult, hogy az átjáróeszközre mutasson, és az alsóbb rétegbeli eszköz IoT Hubra történő hitelesítéséhez szükséges tanúsítványokat. További információ: [Hitelesítette az alsóbb rétegbeli eszközt az Azure IoT Hubhoz.](how-to-authenticate-downstream-device.md)

* A legfelső szintű hitelesítésszolgáltatói tanúsítvány teljes elérési útja, amelyet másolt és mentett valahol az alsóbb rétegbeli eszközén.

    Például: `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Ez a szakasz egy mintaalkalmazást biztosít egy Azure IoT NodeJS eszközügyfél és egy IoT Edge-átjáró csatlakoztatásához. NodeJS-alkalmazások esetén telepítenie kell a legfelső szintű hitelesítésszolgáltatói tanúsítványt az alkalmazás szintjén, ahogy az itt látható. A NodeJS-alkalmazások nem használják a rendszer tanúsítványtárolóját.

1. A minta **edge_downstream_device.js** az [Azure IoT-eszköz SDK node.js minták társzáma.](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
2. Győződjön meg arról, hogy rendelkezik a minta futtatásához szükséges összes előfeltételsel a **readme.md** fájl áttekintésével.
3. A edge_downstream_device.js fájlban frissítse a **connectionString** és **a edge_ca_cert_path** változókat.
4. A minta eszközén való futtatásáról az SDK dokumentációjában olvashat.

A futtatott minta megértéséhez a következő kódrészlet az, ahogyan az ügyfél SDK beolvassa a tanúsítványfájlt, és biztonságos TLS-kapcsolat létrehozásához használja azt:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Ez a szakasz egy mintaalkalmazást vezet be egy Azure IoT .NET eszközügyfél és egy IoT Edge-átjáró csatlakoztatásához. A .NET-alkalmazások azonban automatikusan használhatják a rendszer tanúsítványtárolójában telepített tanúsítványokat linuxos és Windows-állomásokon is.

1. Az **EdgeDownstreamDevice** minta beszereznie az [IoT Edge .NET minták mappából.](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)
2. Győződjön meg arról, hogy rendelkezik a minta futtatásához szükséges összes előfeltételsel a **readme.md** fájl áttekintésével.
3. A **Properties / launchSettings.json** fájlban frissítse a **DEVICE_CONNECTION_STRING** és **CA_CERTIFICATE_PATH** változókat. Ha a gazdarendszer megbízható tanúsítványtárolójában telepített tanúsítványt szeretné használni, hagyja üresen ezt a változót.
4. A minta eszközén való futtatásáról az SDK dokumentációjában olvashat.

Ha programozott módon szeretne telepíteni egy megbízható tanúsítványt a tanúsítványtárolóban egy .NET alkalmazáson keresztül, olvassa el az **EdgeDownstreamDevice / Program.cs** fájl **InstallCACert()** függvényét. Ez a művelet idempotens, így több alkalommal is futtatható ugyanazzal az értékekkel, további hatás nélkül.

### <a name="c"></a>C#

Ez a szakasz egy mintaalkalmazást vezet be egy Azure IoT C-eszközügyfél és egy IoT Edge-átjáró csatlakoztatásához. A C SDK számos TLS-kódtárral működhet, például az OpenSSL, a WolfSSL és az Schannel. További információ: az [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c)

1. Az **iotedge_downstream_device_sample** alkalmazás beszereznie az [Azure IoT-eszköz SDK C-minták.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
2. Győződjön meg arról, hogy rendelkezik a minta futtatásához szükséges összes előfeltételsel a **readme.md** fájl áttekintésével.
3. A iotedge_downstream_device_sample.c fájlban frissítse a **connectionString** és **edge_ca_cert_path** változókat.
4. A minta eszközén való futtatásáról az SDK dokumentációjában olvashat.

Az Azure IoT-eszköz SDK C-hez lehetőséget biztosít a hitelesítésszolgáltatói tanúsítvány regisztrálásához az ügyfél beállításakor. Ez a művelet nem telepíti a tanúsítványt sehol, hanem a tanúsítvány karakterláncformátumát használja a memóriában. A mentett tanúsítvány a kapcsolat létrehozásakor az alapul szolgáló TLS-veremben található.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows-állomásokon, ha nem használja az OpenSSL vagy más TLS-könyvtárat, az SDK alapértelmezés szerint az Schannel-t használja. Az Schannel működéséhez az IoT Edge legfelső szintű hitelesítésszolgáltatói tanúsítványt telepíteni `IoTHubDeviceClient_SetOption` kell a Windows tanúsítványtárolóba, nem pedig a művelettel.

### <a name="java"></a>Java

Ez a szakasz egy mintaalkalmazást vezet be egy Azure IoT Java-eszközügyfél és egy IoT Edge-átjáró csatlakoztatásához.

1. A **küldési esemény** minta beszereznie az Azure [IoT-eszköz Java-mintáksk-ból.](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
2. Győződjön meg arról, hogy rendelkezik a minta futtatásához szükséges összes előfeltételsel a **readme.md** fájl áttekintésével.
3. A minta eszközén való futtatásáról az SDK dokumentációjában olvashat.

### <a name="python"></a>Python

Ez a szakasz egy mintaalkalmazást vezet be egy Azure IoT Python-eszközügyfél és egy IoT Edge-átjáró csatlakoztatásához.

1. A **minta** az send_message az [Azure IoT-eszköz SDK Python-minták.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)
2. Győződjön meg arról, hogy vagy fut egy IoT Edge-tárolóban, vagy egy hibakeresési forgatókönyvben, `EdgeHubConnectionString` a környezeti változók `EdgeModuleCACertificateFile` beállítva.
3. A minta eszközén való futtatásáról az SDK dokumentációjában olvashat.

## <a name="test-the-gateway-connection"></a>Az átjárókapcsolat tesztelése

Ezzel a mintaparanccsal tesztelheti, hogy az alsóbb rétegbeli eszköz csatlakozhat-e az átjáróeszközhöz:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Ez a parancs az MQTTS (8883-as port) kapcsolatait teszteli. Ha más protokollt használ, szükség szerint módosítsa a parancsot az AMQPS (5671) vagy a HTTPS (433) beállításhoz.

A parancs kimenete hosszú lehet, beleértve a láncösszes tanúsítványára vonatkozó információt is. Ha a kapcsolat sikeres, megjelenik egy `Verification: OK` `Verify return code: 0 (ok)`sor, mint a vagy .

![Átjárókapcsolat ellenőrzése](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Az átjárókapcsolat – problémamegoldás

Ha a levéleszköz szakaszos kapcsolattal rendelkezik az átjáróeszközhöz, próbálkozzon a következő lépésekkel a megoldásérdekében.

1. Az átjáró állomásneve a kapcsolati karakterlánc ban megegyezik az ioT Edge config.yaml fájl állomásnév értékének értékén az átjáróeszközön?
2. Az átjáró állomásneve feloldható EGY IP-címre? Az időszakos kapcsolatokat dns használatával vagy állomásfájl-bejegyzés hozzáadásával oldhatja fel a levéleszközön.
3. Meg vannak nyitva a kommunikációs portok a tűzfalon? Az alkalmazott protokollon (MQTTS:8883/AMQPS:5671/HTTPS:433) alapuló kommunikációnak lehetségesnek kell lennie az alsóbb rétegbeli eszköz és az átlátszó IoT Edge között.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan terjesztheti ki az IoT Edge [az offline képességeket](offline-capabilities.md) az alsóbb rétegbeli eszközökre.
