---
title: Alsóbb rétegbeli eszközök hitelesítése – Azure IoT Edge | Microsoft Docs
description: Hogyan hitelesítheti az alárendelt eszközöket vagy a levélben lévő eszközöket IoT Hub, és hogyan irányíthatja át a kapcsolódást Azure IoT Edge átjáró-eszközökön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4d6c7665d281ff7c27fd8b61537804b6803b3b43
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360166"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon

Transzparens átjáró esetén az alsóbb rétegbeli eszközöknek (más néven levél-vagy gyermek-eszközöknek) identitásokra van szükségük IoT Hub mint bármely más eszköz. Ez a cikk végigvezeti az alsóbb rétegbeli eszközök IoT Hubra való hitelesítésének lehetőségeit, majd bemutatja, hogyan deklarálhatja az átjáró-kapcsolatokat.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a második lépést ismerteti:

1. Az átjáró-eszköznek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra, az alárendelt eszközökről érkező kommunikáció fogadására és az üzenetek megfelelő célhelyre való továbbítására. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. **Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz-identitással, hogy képes legyen hitelesíteni a IoT Hub, és tudnia kell kommunikálni az átjáró eszközén keresztül.**
3. Az alsóbb rétegbeli eszköznek képesnek kell lennie az átjáró eszközéhez való biztonságos kapcsolódásra. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).

Az alsóbb rétegbeli eszközök a következő három módszer egyikével hitelesíthetők a IoT Hub használatával: szimmetrikus kulcsok (más néven közös hozzáférési kulcsok), X. 509 önaláírt tanúsítvány vagy X. 509 hitelesítésszolgáltató (CA) által aláírt tanúsítvány. A hitelesítési lépések hasonlók a nem IoT-eszközök IoT Hub-vel való beállításához használt lépésekhez, és kisebb eltéréseket jelentenek az átjáró kapcsolatának bejelentéséhez.

A cikkben ismertetett lépések bemutatják a manuális eszközök kiépítés nélküli üzembe helyezését, és nem automatikus kiépítés az Azure IoT Hub Device Provisioning Service. 

## <a name="symmetric-key-authentication"></a>Szimmetrikus kulcsos hitelesítés

A szimmetrikus kulcsos hitelesítés vagy a közös hozzáférésű kulcsos hitelesítés a legegyszerűbb módszer a IoT Hub való hitelesítéshez. A szimmetrikus kulcsos hitelesítéssel Base64-kulcsot társítunk a IoT-eszköz AZONOSÍTÓJÁHOZ IoT Hub. Ezt a kulcsot a IoT-alkalmazásokban is felveszi, hogy az eszköz bemutassa a IoT Hubhoz való csatlakozáskor. 

### <a name="create-the-device-identity"></a>Az eszköz identitásának létrehozása 

Vegyen fel egy új IoT-eszközt az IoT hub-ban a Visual Studio Code-hoz készült Azure Portal, az Azure CLI vagy a IoT-bővítmény használatával. Ne feledje, hogy az alsóbb rétegbeli eszközöket IoT Hub kell azonosítani a normál IoT-eszközként, nem IoT Edge eszközöket. 

Az új eszköz identitásának létrehozásakor adja meg a következő információkat: 

* Hozzon létre egy azonosítót az eszközhöz.

* A hitelesítési típusként válassza a **szimmetrikus kulcs** lehetőséget. 

* Igény szerint beállíthatja a **szülő eszközt** , és kiválaszthatja azt a IoT Edge átjáró-eszközt, amelyre az alárendelt eszköz kapcsolódni fog. Ez a lépés nem kötelező a szimmetrikus kulcsos hitelesítéshez, de ajánlott, mert a szülő eszköz beállítása lehetővé teszi az alsóbb rétegbeli eszköz [Offline képességeit](offline-capabilities.md) . Bármikor frissítheti az eszköz adatait, hogy később hozzáadja vagy megváltoztassa a szülőt. 

   ![Eszköz AZONOSÍTÓjának létrehozása szimmetrikus kulcsú hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Ugyanezt a műveletet az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) használatával végezheti el. A következő példa egy új IoT-eszközt hoz létre szimmetrikus kulcsos hitelesítéssel, és egy fölérendelt eszközt rendel hozzá: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Az eszközök létrehozásával és a szülő-gyermek felügyelettel kapcsolatos Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok hivatkozási tartalmát.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Kapcsolódás IoT Hub átjárón keresztül

Ugyanez a folyamat használható a normál IoT-eszközöknek a szimmetrikus kulccsal való IoT Hub hitelesítésére is az alárendelt eszközökre. Az egyetlen különbség, hogy fel kell vennie egy mutatót az átjáró eszközre a kapcsolat irányításához, vagy offline helyzetekben, hogy kezelni tudja a hitelesítést a IoT Hub nevében. 

A szimmetrikus kulcsos hitelesítéshez nem kell további lépéseket végrehajtania az eszközön a IoT Hub való hitelesítéshez. Továbbra is szükség van a tanúsítványokra, hogy az alsóbb rétegbeli eszköz csatlakozhasson az átjáró eszközéhez, ahogy az [egy alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md)című témakörben leírtak szerint.

Miután létrehozta a IoT-eszköz identitását a portálon, lekérheti az elsődleges vagy másodlagos kulcsait. A kulcsok egyikének szerepelnie kell a IoT Hubkal kommunikáló alkalmazásokban szerepeltetett kapcsolódási karakterláncban. A szimmetrikus kulcsos hitelesítéshez IoT Hub biztosítja a teljes mértékben formázott kapcsolati karakterláncot az eszköz részleteiben az Ön kényelme érdekében. További információkat kell felvennie az átjáró-eszközről a kapcsolódási karakterláncra. 

Az alsóbb rétegbeli eszközök szimmetrikus kulcsú kapcsolatainak karakterláncai a következő összetevőket igényelik: 

* Az a IoT hub, amelyhez az eszköz csatlakozik:`Hostname={iothub name}.azure-devices.net`
* A központban regisztrált eszköz azonosítója:`DeviceID={device ID}`
* Vagy az elsődleges vagy a másodlagos kulcs:`SharedAccessKey={key}`
* Az az átjáró-eszköz, amelyhez az eszköz csatlakozik. Adja meg a **hostname** értéket az IoT Edge Gateway-eszköz config. YAML fájljában:`GatewayHostName={gateway hostname}`

Az összes együtt egy teljes körű kapcsolatok karakterlánca a következőképpen néz ki:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ha létrehozott egy szülő/gyermek kapcsolatot ehhez az alárendelt eszközhöz, a kapcsolati karakterlánc leegyszerűsíthető úgy, hogy az átjárót közvetlenül a kapcsolati gazdagépként hívja meg. Példa: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X. 509 hitelesítés 

Az X. 509 tanúsítványokkal kétféleképpen hitelesíthető egy IoT-eszköz. Bármelyik módszert választja, az eszköz IoT Hub való csatlakoztatásának lépései megegyeznek. Válassza ki az önaláírt vagy a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványokat a hitelesítéshez, majd folytassa a IoT Hubhoz való kapcsolódással kapcsolatos tudnivalókat. 

A IoT Hub X. 509 hitelesítés használatával kapcsolatos további információkért tekintse meg a következő cikkeket: 
* [Eszköz hitelesítése X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok használatával](../iot-hub/iot-hub-x509ca-overview.md)
* [Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok fogalmi megértése a IoT-iparágban](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Az eszköz identitásának létrehozása X. 509 önaláírt tanúsítványokkal

Az X. 509 önaláírt hitelesítés (más néven ujjlenyomatos hitelesítés) esetében új tanúsítványokat kell létrehoznia a IoT-eszközre való elhelyezéshez. Ezek a tanúsítványok olyan ujjlenyomattal rendelkeznek, amelyet IoT Hub a hitelesítéshez. 

A forgatókönyv tesztelésének legegyszerűbb módja, ha ugyanazt a számítógépet használja, amelyet a tanúsítványok létrehozásához használt a [IoT Edge eszköz konfigurálásához, hogy transzparens átjáróként működjön](how-to-create-transparent-gateway.md). Ezt a gépet már be kell állítani a megfelelő eszközzel, legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal és a köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal, hogy létrehozza a IoT-eszköz tanúsítványait. Ezt követően a végső tanúsítványokat és azok titkos kulcsait átmásolhatja az alsóbb rétegbeli eszközre. Az átjáróval kapcsolatos cikkben leírt lépéseket követve állíthatja be az OpenSSL-t a gépen, majd klónozott a IoT Edge-tárházat a tanúsítvány-létrehozási parancsfájlok eléréséhez. Ezután létrehozott egy munkakönyvtárat, amelyet a  **\<WRKDIR >** hívunk a tanúsítványok tárolására. Az alapértelmezett tanúsítványok a fejlesztéshez és teszteléshez készültek, így csak az utolsó 30 nap. Létre kell hoznia egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt. 

1. Navigáljon a munkakönyvtárhoz egy bash-vagy PowerShell-ablakban. 

2. Hozzon létre két tanúsítványt (elsődleges és másodlagos) az alsóbb rétegbeli eszközhöz. Adja meg az eszköz nevét, majd az elsődleges vagy a másodlagos címkét. Ezek az adatok a fájlok elnevezésére szolgálnak, így nyomon követheti a több eszköz tanúsítványait. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Kérje le az SHA1 ujjlenyomatot (a IoT Hub felületen található ujjlenyomatot) minden tanúsítványból, amely egy 40 hexadecimális karakterből álló karakterlánc. A következő OpenSSL-paranccsal tekintheti meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Navigáljon az IoT hubhoz a Azure Portalon, és hozzon létre egy új IoT-eszköz identitását a következő értékekkel: 

   * Válassza az **X. 509 önaláírt** hitelesítési típusként lehetőséget.
   * Illessze be azokat a hexadecimális karakterláncokat, amelyeket az eszköz elsődleges és másodlagos tanúsítványainakből másolt.
   * Válassza a **szülő eszköz beállítása** elemet, és válassza ki azt a IoT Edge átjáró-eszközt, amelyre ez az alárendelt eszköz fog csatlakoztatva. Egy alárendelt eszköz X. 509 hitelesítéséhez szülő eszköz szükséges. 

   ![Eszköz AZONOSÍTÓjának létrehozása X. 509 önaláírt hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Másolja a következő fájlokat az alsóbb rétegbeli eszköz bármelyik könyvtárába:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Ezeket a fájlokat a IoT Hubhoz csatlakozó Leaf Device-alkalmazásokban fogja hivatkozni. Olyan szolgáltatásokat használhat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protoco](https://www.ssh.com/ssh/scp/) a tanúsítványfájl áthelyezéséhez.

Az [Azure CLI-hez készült IoT](https://github.com/Azure/azure-iot-cli-extension) -bővítményt használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. A következő példa egy új IoT-eszközt hoz létre X. 509 önaláírt hitelesítéssel, és egy fölérendelt eszközt rendel hozzá: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Az eszközök létrehozásával, a tanúsítványok létrehozásával, valamint a szülő-és alárendelt felügyelettel kapcsolatos Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok hivatkozási tartalmát.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Az eszköz identitásának létrehozása X. 509 HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványokkal

Az X. 509 hitelesítésszolgáltató (CA) által aláírt hitelesítéshez szükség van egy IoT Hub regisztrált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra, amelyet a IoT-eszköz tanúsítványainak aláírásához használ. Minden olyan eszköz, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a köztes tanúsítványok valamelyikével kapcsolatos hibát okozó tanúsítványt használ, a hitelesítés engedélyezve lesz. 

Ez a szakasz az [X. 509 biztonság Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md)való beállításának IoT hub cikkében részletesen ismertetett utasításokon alapul. Az ebben a szakaszban ismertetett lépéseket követve megtudhatja, hogy mely értékeket kell használni egy átjárón keresztül csatlakozó alsóbb rétegbeli eszköz beállításához. 

A forgatókönyv tesztelésének legegyszerűbb módja, ha ugyanazt a számítógépet használja, amelyet a tanúsítványok létrehozásához használt a [IoT Edge eszköz konfigurálásához, hogy transzparens átjáróként működjön](how-to-create-transparent-gateway.md). Ezt a gépet már be kell állítani a megfelelő eszközzel, legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal és a köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal, hogy létrehozza a IoT-eszköz tanúsítványait. Ezt követően a végső tanúsítványokat és azok titkos kulcsait átmásolhatja az alsóbb rétegbeli eszközre. Az átjáróval kapcsolatos cikkben leírt lépéseket követve állíthatja be az OpenSSL-t a gépen, majd klónozott a IoT Edge-tárházat a tanúsítvány-létrehozási parancsfájlok eléréséhez. Ezután létrehozott egy munkakönyvtárat, amelyet a  **\<WRKDIR >** hívunk a tanúsítványok tárolására. Az alapértelmezett tanúsítványok a fejlesztéshez és teszteléshez készültek, így csak az utolsó 30 nap. Létre kell hoznia egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt és egy köztes tanúsítványt. 

1. Kövesse az x [. 509 hitelesítésszolgáltatói tanúsítványok regisztrálása az IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) -ban című szakasz utasításait az *x. 509 szintű biztonság beállítása az Azure IoT hub-ban*. Ebben a szakaszban a következő lépéseket hajtja végre: 

   1. Töltse fel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Ha az átlátszó átjárón létrehozott tanúsítványokat használja, töltse fel a  **\<WRKDIR >/certs/Azure-IOT-test-only.root.ca.CERT.PEM** nevű főtanúsítvány-fájlként. 
   2. Győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa. A WRKDIR > tanúsítvány- \<eszközeivel ellenőrizheti a tulajdonjogot. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Az *x. 509 Biztonság beállítása az Azure IoT hub-ban*című rész útmutatását követve [hozzon létre egy x. 509-eszközt az IoT hub létrehozásához](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) . Ebben a szakaszban a következő lépéseket hajtja végre: 

   1. Új eszköz felvétele. Adja meg az **eszköz azonosítójának**kisbetűs nevét, és válassza ki az **X. 509 hitelesítésszolgáltató által aláírt**hitelesítési típust. 
   2. Hozzon létre egy fölérendelt eszközt. Alsóbb rétegbeli eszközök esetén válassza a **fölérendelt eszköz beállítása** lehetőséget, majd válassza ki azt a IoT Edge átjáró eszközt, amely a IoT hubhoz való kapcsolódást biztosítja. 

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, amelyet a IoT Hub feltöltött a lánc elvégzéséhez. Használja ugyanazt a kisbetűs eszköz-azonosítót, amelyet az eszköz identitásához adott a portálon.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Másolja a következő fájlokat az alsóbb rétegbeli eszköz bármelyik könyvtárába: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Ezeket a fájlokat a IoT Hubhoz csatlakozó Leaf Device-alkalmazásokban fogja hivatkozni. Olyan szolgáltatásokat használhat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protoco](https://www.ssh.com/ssh/scp/) a tanúsítványfájl áthelyezéséhez.

Az [Azure CLI-hez készült IoT](https://github.com/Azure/azure-iot-cli-extension) -bővítményt használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. A következő példa egy új IoT-eszközt hoz létre X. 509 HITELESÍTÉSSZOLGÁLTATÓI aláírásos hitelesítéssel, és egy fölérendelt eszközt rendel hozzá: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Az eszközök létrehozásával és a szülő-gyermek felügyelettel kapcsolatos Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok hivatkozási tartalmát.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Kapcsolódás IoT Hub átjárón keresztül

Minden egyes Azure IoT SDK egy kicsit másképp kezeli az X. 509 hitelesítést. Ugyanakkor ugyanez a folyamat használható a normál IoT-eszközöknek az X. 509 tanúsítványokkal való IoT Hub való hitelesítésére is az alárendelt eszközökre. Az egyetlen különbség, hogy fel kell vennie egy mutatót az átjáró eszközre a kapcsolat irányításához, vagy offline helyzetekben, hogy kezelni tudja a hitelesítést a IoT Hub nevében. Általában ugyanazokat az X. 509 hitelesítési lépéseket követheti az összes IoT Hub eszközön, majd egyszerűen lecserélheti az **állomásnév** értékét a kapcsolódási karakterláncban az átjáró-eszköz állomásneveként. 

A következő részekben néhány példa látható a különböző SDK-nyelvekre. 

>[!IMPORTANT]
>Az alábbi példák azt mutatják be, hogy az IoT Hub SDK-k hogyan használnak tanúsítványokat az eszközök hitelesítéséhez. Éles környezetben az összes titkot, például a magán-vagy SAS-kulcsokat egy hardveres Secure modulban (HSM) kell tárolnia. 

#### <a name="net"></a>.NET

Ha például az X. C# 509 tanúsítványokkal IoT hub hitelesítő programot szeretne elvégezni, tekintse [meg az x. 509 Biztonság beállítása az Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)című témakört. A minta néhány fő sora itt található a hitelesítési folyamat bemutatásához.

A DeviceClient-példány állomásnévének deklarálása során használja az IoT Edge Gateway-eszköz állomásnevét. Az állomásnév az átjáró eszköz config. YAML fájljában található. 

Ha a IoT Edge git-tárház által biztosított tesztelési tanúsítványokat használja, a tanúsítványok kulcsa **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Ha például az X. 509 tanúsítvánnyal IoT Hub hitelesítő C programra van hitelesítés, tekintse meg a C IoT SDK [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) -mintáját. A minta néhány fő sora itt található a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközhöz tartozó kapcsolódási karakterlánc meghatározásakor használja az IoT Edge átjáró eszközének állomásnevét a **hostname** paraméterhez. Az állomásnév az átjáró eszköz config. YAML fájljában található. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Ha egy Node. js-programot szeretne egy X. 509 tanúsítvánnyal IoT Hub hitelesíteni, tekintse meg a Node. js IoT SDK [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) -mintáját. A minta néhány fő sora itt található a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközhöz tartozó kapcsolódási karakterlánc meghatározásakor használja az IoT Edge átjáró eszközének állomásnevét a **hostname** paraméterhez. Az állomásnév az átjáró eszköz config. YAML fájljában található. 

Ha a IoT Edge git-tárház által biztosított tesztelési tanúsítványokat használja, a tanúsítványok kulcsa **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Az X. 509 tanúsítványokkal IoT Hub hitelesítéssel rendelkező Python-programra például a Java IoT SDK [iothub_client_sample_x509.](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) a-es verziójának mintáját láthatja. A minta néhány fő sora itt található a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközhöz tartozó kapcsolódási karakterlánc meghatározásakor használja az IoT Edge átjáró eszközének állomásnevét a **hostname** paraméterhez. Az állomásnév az átjáró eszköz config. YAML fájljában található. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Az X. 509 tanúsítványokkal IoT Hubra hitelesítő Java-programokra példát a Java IoT SDK [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) -mintájában talál. A minta néhány fő sora itt található a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközhöz tartozó kapcsolódási karakterlánc meghatározásakor használja az IoT Edge átjáró eszközének állomásnevét a **hostname** paraméterhez. Az állomásnév az átjáró eszköz config. YAML fájljában található. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>További lépések

Ennek a cikknek a végrehajtásával rendelkeznie kell egy IoT Edge eszközzel, amely transzparens átjáróként és egy IoT hub-ban regisztrált alsóbb rétegbeli eszközként működik. Ezután be kell állítania az alsóbb rétegbeli eszközöket, hogy megbízzon az átjáró-eszközön, és üzeneteket küldjön neki. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).
