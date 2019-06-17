---
title: Hitelesítés az alsóbb rétegbeli eszközök – Azure IoT Edge |} A Microsoft Docs
description: Hogyan alsóbb rétegbeli eszközök és az IoT hub levéleszközök hitelesítését és kapcsolatukat átirányítása az Azure IoT Edge-átjáró eszközökön keresztül.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082390"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Az Azure IoT hubra egy alsóbb rétegbeli eszköz hitelesítéséhez

Alsóbb rétegbeli eszközök (más néven levéleszközök vagy gyermek-eszközök) egy transzparens átjáró használata esetén, mint bármilyen más eszközről az IoT Hub identitások van szükség. Ez a cikk végigvezeti a beállításokat az IoT hub egy alsóbb rétegbeli eszköz hitelesítéséhez, és ezután bemutatja, hogyan deklarálnia az átjárókapcsolat.

Transzparens átjáró sikeres kapcsolat beállítása három általános lépésből áll. Ez a cikk ismerteti a második lépés:

1. Az átjáró eszköz képesnek kell lennie, biztonságosan csatlakozhat az alsóbb rétegbeli eszközök, fogadhatják a Microsofttól alsóbb rétegbeli eszközök és irányíthatja az üzenetek a megfelelő célhelyre. További információkért lásd: [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md).
2. **Az alsóbb rétegbeli eszköz rendelkeznie kell egy eszközidentitást az IoT Hub a hitelesítéshez, és tudja, hogy az átjáró eszköz keresztül kommunikálnak.**
3. Az alsóbb rétegbeli eszközök képesnek kell lennie, biztonságosan csatlakozhat a átjáróeszközt. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).

Alsóbb rétegbeli eszközök hitelesítheti az IoT Hub három módszer egyikével: szimmetrikus kulcsokat (más néven megosztott hozzáférési kulcsokat), önaláírt X.509-tanúsítványokat vagy X.509-tanúsítvány hitelesítésszolgáltató (CA) önaláírt tanúsítványok. A hitelesítési lépések hasonlóak a bármely nem IoT Edge-eszköz az IoT Hub, az átjáró kapcsolatot deklarálni kisebb eltéréssel beállítására szolgáló lépéseket.

A jelen cikkben ismertetett lépések bemutatják manuális eszközök üzembe helyezését, nem az Automatikus kiépítés az Azure IoT Hub Device Provisioning Service szolgáltatással. 

## <a name="symmetric-key-authentication"></a>A szimmetrikus kulcsos hitelesítés

Szimmetrikus kulcsot vagy a közös hozzáférési kulcs alapú hitelesítés esetén nem a legegyszerűbb módja az IoT Hub-hitelesítést. A szimmetrikus kulcsot base64 kulcs az IoT-eszköz azonosítója, az IoT Hub társítva. A kulcs az, hogy az eszköz is megjeleníti azt az IoT Hub szolgáltatáshoz való csatlakozáskor tartalmazzák az IoT-alkalmazásokat. 

### <a name="create-the-device-identity"></a>Hozza létre az eszközidentitást 

Adjon hozzá egy új IoT-eszközt az IoT hub, az Azure portal, Azure CLI-vel vagy az IoT-bővítmény használata a Visual Studio Code. Ne feledje, hogy az alsóbb rétegbeli eszközök való rendszeres IoT-eszköz, nem az IoT Edge-eszközök azonosítását az IoT Hub szükség. 

Amikor az új eszközidentitást hoz létre, adja meg a következő információkat: 

* Hozzon létre egy Azonosítót az eszközt.

* Válassza ki **szimmetrikus kulcs** hitelesítési típusként. 

* Opcionálisan, **egy szülő eszköz beállítása** , és válassza ki az IoT Edge-átjáróeszköz, amely az alsóbb rétegbeli eszköz kapcsolaton keresztül fog csatlakozni. Ez a lépés nem kötelező a szimmetrikus kulcsos hitelesítés, de ajánlott, mert a szülő eszköz beállítás lehetővé teszi, hogy [offline képességeiről](offline-capabilities.md) az alsóbb rétegbeli eszközhöz. Az eszköz részleteinek hozzáadása, vagy megváltoztatni a szülőt később bármikor frissítheti. 

   ![Hozzon létre Eszközazonosítót portálon szimmetrikus kulcs hitelesítéssel](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Használhatja a [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension) ugyanaz a művelet végrehajtásához. Az alábbi példa szimmetrikus kulcsot hoz létre egy új IoT-eszközt, és hozzárendeli egy szülő eszköz: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Azure CLI-parancsokat eszköz létrehozása és a szülő-gyermek felügyeleti kapcsolatos további információkért lásd: a segédanyagok [az iot hub-eszközidentitást](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsokat.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Egy átjárón keresztül kapcsolódik az IoT hubhoz

Ugyanez a folyamat rendszeres IoT hitelesítésére használt eszközöket az IoT hub szimmetrikus kulcsokat alsóbb rétegbeli eszközök is vonatkozik. Az egyetlen különbség, hogy hozzá kell mutató továbbítani a kapcsolat az átjáróeszközhöz vagy kapcsolat nélküli esetekben az IoT Hub nevében a hitelesítésnek a kezelésére. 

A szimmetrikus kulcsos hitelesítés nincs további lépéseket kell tennie, hogy a hitelesítést az eszközön az IoT Hub szolgáltatással nem. Önnek kell elvégeznie a tanúsítványok helyen úgy, hogy az alsóbb rétegbeli eszközök csatlakozhatnak az átjáró eszköz leírtak szerint [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).

Miután létrehozott egy IoT-eszközt identitás a portálon, kérheti le az elsődleges vagy másodlagos kulcsok. Ezek a kulcsok egyikét kell szerepelnie a kapcsolati karakterláncot tartalmazó bármely alkalmazásban, amely kommunikál az IoT hubbal. A szimmetrikus kulcsos hitelesítés az IoT Hub célú a teljes mértékben megfelelő formátumú kapcsolati karakterlánc az eszköz részleteinek biztosít. További információ az átjáró eszköz hozzá a kapcsolati karakterláncot kell. 

Szimmetrikus kulcs kapcsolati karakterláncok alsóbb rétegbeli eszközök esetében a következő összetevőket kell: 

* Az IoT hub, amely az eszköz csatlakozik: `Hostname={iothub name}.azure-devices.net`
* Az eszköz azonosítója, a központon regisztrált: `DeviceID={device ID}`
* Vagy az elsődleges vagy másodlagos kulcsot: `SharedAccessKey={key}`
* Az átjáróeszköz, amely az eszköz keresztül kapcsolódik. Adja meg a **állomásnév** értéket az IoT Edge átjáróeszköz config.yaml fájlból: `GatewayHostName={gateway hostname}`

A kapcsolati karakterlánc minden együtt, hasonlóan néz ki:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ha az alsóbb rétegbeli eszköz szülő-gyermek kapcsolat létrejöttét hívása az átjáró közvetlenül a kapcsolat gazdagépként egyszerűbbé a kapcsolati karakterláncot. Példa: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509-hitelesítéssel 

Kétféleképpen egy X.509 tanúsítványok használatával IoT-eszköz hitelesítéséhez. Bármelyik módszert úgy dönt, hogy hitelesítést végezni, a lépéseket az eszköz csatlakoztatása az IoT Hub azonos. Ismerje meg, hogyan lehet csatlakozni az IoT Hub továbbra is, majd válassza a hitelesítéshez önaláírt vagy hitelesítésszolgáltató által aláírt tanúsítványokat. 

Hogyan használja az IoT Hub a X.509-hitelesítéssel kapcsolatos további információkért lásd a következő cikkeket: 
* [Eszközhitelesítés x.509-es Hitelesítésszolgáltatói tanúsítványok használatával](../iot-hub/iot-hub-x509ca-overview.md)
* [Az IoT-iparág x.509-es Hitelesítésszolgáltatói tanúsítványok fogalmi ismertetése](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Önaláírt X.509-tanúsítványokat az eszközidentitás létrehozása

X.509 önaláírt hitelesítéshez, más néven ujjlenyomat hitelesítést kell új tanúsítványokat helyezi az IoT-eszköz létrehozása. Ezek a tanúsítványok neki, hogy az IoT Hub-hitelesítéshez megoszt egy ujjlenyomatot rendelkezik. 

A legegyszerűbben úgy, hogy a forgatókönyv teszteléséhez ugyanarra a gépre, amely a tanúsítványok létrehozásához használt használandó [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md). Azon a gépen már létre kell hozni a megfelelő eszköz, a legfelső szintű Hitelesítésszolgáltatói tanúsítványt és a köztes Hitelesítésszolgáltatói tanúsítványt az IoT-eszköz tanúsítványok létrehozásához. Másolhatja a végső tanúsítványokat és azok titkos kulcsait keresztül az alsóbb rétegbeli eszköz ezt követően. Az átjárókkal foglalkozó cikk lépéseit, a következő openssl beállítása a gépén, majd az adattár klónja létrejött az IoT Edge hozzáférési tanúsítvány létrehozása és parancsfájlok. Ezt követően végzett egy működő könyvtárba, amely nevezzük  **\<WRKDIR >** ahhoz, hogy a tanúsítványokat. Az alapértelmezett tanúsítványokat úgy van kialakítva, a fejlesztés és tesztelés, így csak az utolsó 30 nap. A legfelső szintű Hitelesítésszolgáltatói tanúsítványt és a egy közbenső tanúsítvány kell hozott létre. 

1. Keresse meg a bash vagy a PowerShell-ablakot a munkakönyvtár. 

2. Hozzon létre két tanúsítványt (elsődleges és másodlagos) az alsóbb rétegbeli eszköz. Adja meg az eszköz nevét és az elsődleges vagy másodlagos címkét. Ez az információ segítségével nevezze el a fájlokat, hogy Ön is nyomon követheti, több eszközre tanúsítványai. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Minden tanúsítványt, amely egy 40 hexadecimális karakterlánc lekérdezni (egy ujjlenyomatot néven az IoT Hub-felületen) SHA1-ujjlenyomatot. Tekintse meg a tanúsítványt, és keresse meg az ujjlenyomatot a következő openssl-parancs segítségével:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Keresse meg az IoT hub, az Azure Portalon, és hozzon létre egy új IoT-eszközidentitást az alábbi értékeket: 

   * Válassza ki **önaláírt X.509** hitelesítési típusként.
   * Illessze be a hexadecimális karakterlánc, amely az eszköz elsődleges és másodlagos tanúsítványát, amelyet másolt.
   * Válassza ki **egy szülő eszköz beállítása** , és válassza ki az IoT Edge átjáróeszköz, amely az alsóbb rétegbeli eszköz kapcsolaton keresztül fog csatlakozni. Egy szülő eszköz szükség az alsóbb rétegbeli eszközök X.509-hitelesítéssel. 

   ![Hozzon létre Eszközazonosítót portálon X.509 önaláírt hitelesítéssel](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. A következő fájlokat másolja bármelyik könyvtárába, az alsóbb rétegbeli eszközök:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Ezeket a fájlokat a levél eszköz csatlakoztatása az IoT hubhoz alkalmazások fog tudni hivatkozni. Használhat olyan szolgáltatásokhoz, mint [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy egy függvényt, például [biztonságos másolás protoco](https://www.ssh.com/ssh/scp/) áthelyezni a fájlokat.

Használhatja a [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension) az ugyanazon eszközön-létrehozási művelet végrehajtásához. Az alábbi példa létrehoz egy új IoT-eszköz önaláírt X.509-hitelesítéssel, és hozzárendeli egy szülő eszköz: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Eszköz létrehozásához, a tanúsítvány létrehozása és a szülő és gyermek kezelése az Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg a segédanyagok [az iot hub-eszközidentitást](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsokat.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Hozzon létre az eszköz identitását az x.509-es Hitelesítésszolgáltatói önaláírt tanúsítványok

X.509 tanúsítvány hitelesítésszolgáltató (CA) aláírt hitelesítéshez regisztrálni az IoT Hub, IoT-eszközök tanúsítványok aláírásához használt legfelső szintű Hitelesítésszolgáltatói tanúsítvány szükséges. Bármilyen eszközön, a legfelső szintű Hitelesítésszolgáltatói tanúsítvány, vagy a köztes tanúsítványok bármilyen problémák volt tanúsítványt használ hitelesítésre lesz engedélyezett. 

Ez a szakasz az IoT Hub cikkben részletes utasítások alapján [állítsa be az Azure IoT hub X.509 biztonsági](../iot-hub/iot-hub-security-x509-get-started.md). Ebben a szakaszban tudni, hogy mely értékeket állíthat be egy alsóbb rétegbeli eszközök egy átjárón keresztül csatlakozó lépésekkel. 

A legegyszerűbben úgy, hogy a forgatókönyv teszteléséhez ugyanarra a gépre, amely a tanúsítványok létrehozásához használt használandó [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md). Azon a gépen már létre kell hozni a megfelelő eszköz, a legfelső szintű Hitelesítésszolgáltatói tanúsítványt és a köztes Hitelesítésszolgáltatói tanúsítványt az IoT-eszköz tanúsítványok létrehozásához. Másolhatja a végső tanúsítványokat és azok titkos kulcsait keresztül az alsóbb rétegbeli eszköz ezt követően. Az átjárókkal foglalkozó cikk lépéseit, a következő openssl beállítása a gépén, majd az adattár klónja létrejött az IoT Edge hozzáférési tanúsítvány létrehozása és parancsfájlok. Ezt követően végzett egy működő könyvtárba, amely nevezzük  **\<WRKDIR >** ahhoz, hogy a tanúsítványokat. Az alapértelmezett tanúsítványokat úgy van kialakítva, a fejlesztés és tesztelés, így csak az utolsó 30 nap. A legfelső szintű Hitelesítésszolgáltatói tanúsítványt és a egy közbenső tanúsítvány kell hozott létre. 

1. Kövesse az utasításokat a [regisztrálása X.509 Hitelesítésszolgáltatói tanúsítványok az IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) szakaszában *állítsa be az Azure IoT hub X.509 biztonsági*. A szakaszban a következő lépéseket fogja végrehajtani: 

   1. Töltse fel egy legfelső szintű Hitelesítésszolgáltatói tanúsítványt. Ha a transzparens átjáró cikkben létrehozott tanúsítványt használja, töltse fel  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** a legfelső szintű tanúsítványt fájlként. 
   2. Győződjön meg arról, hogy Ön a tulajdonosa a legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Ellenőrizheti, hogy a tanúsítvány eszközökkel birtokában \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Kövesse az utasításokat a [hozzon létre egy X.509-eszközt az IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) szakaszában *állítsa be az Azure IoT hub X.509 biztonsági*. A szakaszban a következő lépéseket fogja végrehajtani: 

   1. Új eszköz hozzáadásához. Kis nevezze el **Eszközazonosító**, és válassza ki a hitelesítéstípust **X.509 Hitelesítésszolgáltatói aláírással**. 
   2. Egy szülő eszköz beállítása. Válassza az alsóbb rétegbeli eszközök **egy szülő eszköz beállítása** és az IoT Edge-átjáróeszköz, amely a kapcsolatot biztosít az IoT hub válassza. 

3. Tanúsítványláncolat kialakításához az alsóbb rétegbeli eszközhöz. IoT Hub segítségével a lánc feltöltött azonos legfelső szintű Hitelesítésszolgáltatói tanúsítványt használjon. Az azonos kisbetűs eszköz azonosítója, az eszközidentitás a portálon megadott használja.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. A következő fájlokat másolja bármelyik könyvtárába, az alsóbb rétegbeli eszközök: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Ezeket a fájlokat a levél eszköz csatlakoztatása az IoT hubhoz alkalmazások fog tudni hivatkozni. Használhat olyan szolgáltatásokhoz, mint [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy egy függvényt, például [biztonságos másolás protoco](https://www.ssh.com/ssh/scp/) áthelyezni a fájlokat.

Használhatja a [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension) az ugyanazon eszközön-létrehozási művelet végrehajtásához. Az alábbi példa létrehoz egy új IoT-eszköz x.509-es Hitelesítésszolgáltatói aláírt hitelesítéssel, és hozzárendeli egy szülő eszköz: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Azure CLI-parancsokat eszköz létrehozása és a szülő-gyermek felügyeleti kapcsolatos további információkért lásd: a segédanyagok [az iot hub-eszközidentitást](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsokat.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Egy átjárón keresztül kapcsolódik az IoT hubhoz

Minden egyes Azure IoT SDK-t egy kicsit másképp kezeli X.509-hitelesítéssel. Viszont ugyanez a folyamat rendszeres IoT hitelesítésére használt eszközöket az IoT hub X.509-tanúsítványokat az alsóbb rétegbeli eszközök is vonatkozik. Az egyetlen különbség, hogy hozzá kell mutató továbbítani a kapcsolat az átjáróeszközhöz vagy kapcsolat nélküli esetekben az IoT Hub nevében a hitelesítésnek a kezelésére. Általánosságban véve ugyanezekkel a lépésekkel X.509 hitelesítési minden IoT Hub-eszközök számára, majd egyszerűen értékét cserélje **állomásnév** kell az átjáró eszköz állomásnevét a kapcsolati karakterláncban. 

A következő szakaszok bemutatják a különböző SDK-t nyelvekhez néhány példa. 

>[!IMPORTANT]
>A következő példák bemutatják, hogyan az IoT Hub SDK-k tanúsítványok segítségével hitelesíti az eszközöket. Az éles környezet esetén például a privát vagy SAS-kulcsokat hardveres biztonsági modul (HSM) az összes titkos tárolja. 

#### <a name="net"></a>.NET

Példa egy C# program hitelesítése az IoT hubhoz az X.509-tanúsítványokat, lásd: [állítsa be az Azure IoT hub X.509 biztonsági](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). A kulcsfontosságú sorokat, a minta néhány megtalálhatók itt a hitelesítési folyamat bemutatásához.

A DeviceClient példány gazdagépnevét deklarálásakor használja az IoT Edge átjáróeszköz állomásnevet. Az állomásnév az átjáróeszköz config.yaml fájlban található. 

A git-tárház IoT Edge által biztosított a teszt-tanúsítványokat használ, ha van-e a kulcs a tanúsítványok **1234**.

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

Az X.509-tanúsítványokat, az IoT hub hitelesítése C program példa a C IoT SDK-k: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) minta. A kulcsfontosságú sorokat, a minta néhány megtalálhatók itt a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközök kapcsolati karakterláncára definiálásakor használja az IoT Edge átjáróeszköz állomásnevet a **állomásnév** paraméter. Az állomásnév az átjáróeszköz config.yaml fájlban található. 

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

Példa Node.js-program hitelesítése az IoT hubhoz az X.509-tanúsítványokat, a Node.js IoT SDK: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) minta. A kulcsfontosságú sorokat, a minta néhány megtalálhatók itt a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközök kapcsolati karakterláncára definiálásakor használja az IoT Edge átjáróeszköz állomásnevet a **állomásnév** paraméter. Az állomásnév az átjáróeszköz config.yaml fájlban található. 

A git-tárház IoT Edge által biztosított a teszt-tanúsítványokat használ, ha van-e a kulcs a tanúsítványok **1234**.

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

Példa Python program hitelesítése az IoT hubhoz az X.509-tanúsítványokat, a Java IoT SDK: [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) minta. A kulcsfontosságú sorokat, a minta néhány megtalálhatók itt a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközök kapcsolati karakterláncára definiálásakor használja az IoT Edge átjáróeszköz állomásnevet a **állomásnév** paraméter. Az állomásnév az átjáróeszköz config.yaml fájlban található. 

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

A Java-programok X.509-tanúsítványokat, az IoT hubhoz történő találhat példát a Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) minta. A kulcsfontosságú sorokat, a minta néhány megtalálhatók itt a hitelesítési folyamat bemutatásához.

Az alsóbb rétegbeli eszközök kapcsolati karakterláncára definiálásakor használja az IoT Edge átjáróeszköz állomásnevet a **állomásnév** paraméter. Az állomásnév az átjáróeszköz config.yaml fájlban található. 

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

Ez a cikk kitöltésével rendelkeznie kell egy IoT Edge-eszköz transzparens átjáróként működik, és egy alsóbb rétegbeli eszközök regisztrálva az IoT hub. Következő lépésként, konfigurálnia kell az átjáró eszköz megbízható, és üzeneteket küldenek az alsóbb rétegbeli eszközök. További információkért lásd: [egy alsóbb rétegbeli eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md).
