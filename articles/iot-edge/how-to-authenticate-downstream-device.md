---
title: Alsóbb rétegbeli eszközök hitelesítése - Azure IoT Edge | Microsoft dokumentumok
description: Az alsóbb rétegbeli eszközök vagy levéleszközök hitelesítése az IoT Hubra, és a kapcsolat uk az Azure IoT Edge átjáróeszközökön keresztül történő irányítása.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8db3fedc5886e86d5f49739b87b26535665bdbc
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389324"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon

Egy transzparens átjáró forgatókönyv, alsóbb rétegbeli eszközök (más néven levél eszközök vagy gyermekeszközök) identitások az IoT Hub, mint bármely más eszköz. Ez a cikk bemutatja az alsóbb rétegbeli eszközök IoT Hubra való hitelesítésének lehetőségeit, majd bemutatja, hogyan deklarálható az átjárókapcsolat.

A sikeres transzparens átjárókapcsolat beállításának három általános lépése van. Ez a cikk a második lépést ismerteti:

1. Az átjáróeszköznek képesnek kell lennie biztonságosan csatlakozni az alsóbb rétegbeli eszközökhöz, fogadnia kell a kommunikációt az alsóbb rétegbeli eszközökről, és üzeneteket kell irányítania a megfelelő célhelyre. További információ: [Az IoT Edge-eszköz beállítása transzparens átjáróként.](how-to-create-transparent-gateway.md)
2. **Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszközidentitással ahhoz, hogy hitelesítse magát az IoT Hubbal, és ismerje az átjáróeszközön keresztüli kommunikációt.**
3. Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáróeszközhöz. További információ: [Alsóbb rétegbeli eszköz csatlakoztatása egy Azure IoT Edge-átjáróhoz.](how-to-connect-downstream-device.md)

Az alsóbb rétegbeli eszközök a következő három módszer egyikével hitelesíthetik magukat az IoT Hubbal: szimmetrikus kulcsok (más néven megosztott hozzáférési kulcsok), X.509 önaláírt tanúsítványok vagy X.509 hitelesítésszolgáltató (CA) aláírással rendelkező tanúsítványok. A hitelesítési lépések hasonlóak az IoT Hubral való nem IoT-Edge-eszközök beállításához használt lépésekhez, kis eltérésekkel az átjárókapcsolat deklarálásához.

Ebben a cikkben a lépéseket az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) manuális üzembe helyezés, nem automatikus kiépítése ismerteti. Az alsóbb rétegbeli eszközök dps-sel való kiépítése nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [IoT Edge-eszköz konfigurálása transzparens átjáróként való fellépéslépéseit.](how-to-create-transparent-gateway.md) Ha X.509-es hitelesítést használ az alsóbb rétegbeli eszközhöz, ugyanazt a tanúsítványgeneráló parancsfájlt kell használnia, amelyet az átlátszó átjáró cikkben állított be.

Ez a cikk az *átjáró állomásnevére* hivatkozik több ponton. Az átjáró állomásnév deklarálva van a config.yaml fájl **állomásnév** paraméterében az IoT Edge átjáróeszközön. Az alsóbb rétegbeli eszköz kapcsolati karakterláncában hivatkozik rá. Az átjáró állomásnevének feloldhatónak kell lennie egy IP-címre, akár DNS, akár állomásfájl-bejegyzés használatával.

## <a name="register-device-symmetric-key"></a>Eszköz regisztrálása (szimmetrikus kulcs)

A szimmetrikus kulcshitelesítés vagy a megosztott hozzáférésű kulcshitelesítés a legegyszerűbb módja az IoT Hubbal való hitelesítésnek. A szimmetrikus kulcsos hitelesítéssel egy base64-kulcs van társítva az IoT-eszközazonosítóhoz az IoT Hubban. Ezt a kulcsot az IoT-alkalmazásokban is felveheti, hogy az eszköz bemutathassa, amikor csatlakozik az IoT Hubhoz.

### <a name="create-the-device-identity"></a>Az eszközidentitás létrehozása

Adjon hozzá egy új IoT-eszközt az IoT-központban az Azure Portal, az Azure CLI vagy a Visual Studio-kód IoT-bővítményhasználatával. Ne feledje, hogy az alsóbb rétegbeli eszközöket az IoT Hubban rendszeres IoT-eszközökként kell azonosítani, nem IoT Edge-eszközökként.

Az új eszközidentitás létrehozásakor adja meg a következő adatokat:

* Hozzon létre egy azonosítót az eszközhöz.

* Válassza a **Szimmetrikus kulcsot** hitelesítési típusként.

* Szükség esetén válassza a **szülőeszköz beállítása,** és válassza ki az IoT Edge átjáróeszköz, amely az alsóbb rétegbeli eszköz csatlakozik. Ez a lépés nem kötelező a szimmetrikus kulcshitelesítéshez, de ajánlott, mert a szülőeszköz beállítása offline [funkciókat](offline-capabilities.md) tesz lehetővé az alsóbb rétegbeli eszköz számára. Az eszköz adatait bármikor frissítheti a szülő későbbi hozzáadásához vagy módosításához.

   ![Eszközazonosító létrehozása szimmetrikus kulcshitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Használhatja az [IoT-bővítmény az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension) ugyanannak a műveletnek a végrehajtásához. A következő példa egy új IoT-eszközt hoz létre szimmetrikus kulcshitelesítéssel, és hozzárendel egy szülőeszközt:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Az Azure CLI-parancsok eszközlétrehozásához és szülő-gyermek kezeléshez való további információkért tekintse meg az [aziot hub eszközidentitás-parancsok](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) referenciatartalmát.


Ezután [olvassa be és módosítsa a kapcsolati karakterláncot,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogy az átjárón keresztül csatlakozzon.

## <a name="register-device-x509-self-signed"></a>Eszköz regisztrálása (X.509 önaláírt)

Az X.509 önaláírt hitelesítés, más néven ujjlenyomat-hitelesítés, létre kell hoznia az új tanúsítványokat elhelyezni az IoT-eszközön. Ezek a tanúsítványok egy ujjlenyomatot rendelkeznek bennük, amelyet az IoT Hubhitelesítéshez megoszt.

Ha nem rendelkezik hitelesítésszolgáltatóval az X.509-es tanúsítványok létrehozásához, [demótanúsítványokat hozhat létre az IoT Edge-eszköz szolgáltatásainak teszteléséhez.](how-to-create-test-certificates.md) Az alsóbb rétegbeli eszköz teszttanúsítványainak létrehozásakor használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, amely az átjáróeszköz tanúsítványait hozta létre.

1. A hitelesítésszolgáltatói tanúsítvány használatával hozzon létre két (elsődleges és másodlagos) eszköztanúsítványt az alsóbb rétegbeli eszközhöz.

   Az eszköztanúsítványnak rendelkeznie kell a tulajdonos nevével az eszközazonosítóra, amelyet az IoT-eszköz Azure IoT Hubban való regisztrálásakor használni fog. Ez a beállítás szükséges a hitelesítéshez.

2. Az SHA1-ujjlenyomat (az IoT Hub-felületen található ujjlenyomat) lekérése minden tanúsítványból, amely egy 40 hexadecimális karakterlánc. A tanúsítvány megtekintéséhez és az ujjlenyomat megkereséséhez használja a következő openssl parancsot:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Futtassa ezt a parancsot kétszer, egyszer az elsődleges tanúsítványhoz, és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítványhoz ujjlenyomatot ad, ha önaláírt X.509-es tanúsítványokkal regisztrál egy új IoT-eszközt.

3. Keresse meg az IoT-központot az Azure Portalon, és hozzon létre egy új IoT-eszközidentitást a következő értékekkel:

   * Adja meg az eszköztanúsítványok tulajdonosnevének megfelelő **eszközazonosítót.**
   * Hitelesítési típusként válassza az **X.509 Önaláírt** lehetőséget.
   * Illessze be az eszköz elsődleges és másodlagos tanúsítványaiból másolt hexadecimális karakterláncokat.
   * Válassza **a Szülőeszköz beállítása** lehetőséget, és válassza ki azt az IoT Edge átjáróeszközt, amelyen keresztül ez az alsóbb rétegbeli eszköz csatlakozik. Az alsóbb rétegbeli eszközök X.509-es hitelesítéséhez szülőeszköz szükséges.

   ![Eszközazonosító létrehozása X.509 önaláírt hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Másolja az eszköztanúsítványt és a kulcsokat az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű hitelesítésszolgáltatói tanúsítvány egy példányát is, amely az átjáróeszköz-tanúsítványt és az alsóbb rétegbeli eszköztanúsítványokat is létrehozta.

   Ezeket a fájlokat az IoT Hubhoz csatlakozó levéleszköz-alkalmazásokban fogja hivatkozni. Használhatja a szolgáltatást, például [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a függvény, például a Secure copy [protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájlok áthelyezéséhez.

5. A kívánt nyelvtől függően tekintse át az X.509-tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [Az X.509-es biztonság beállítása az Azure IoT hubon](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Használhatja az [IoT-bővítmény az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension) ugyanazt az eszközlétrehozási műveletet. A következő példa létrehoz egy új IoT-eszközt X.509 önaláírt hitelesítéssel, és hozzárendel egy szülőeszközt:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Az Azure CLI-parancsok eszközlétrehozásához, a tanúsítványok létrehozásához, valamint a szülő- és gyermekkezeléshez való további információkért tekintse meg az [aziot hub eszközidentitás-parancsok](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) referenciatartalmát.

Ezután [olvassa be és módosítsa a kapcsolati karakterláncot,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogy az átjárón keresztül csatlakozzon.

## <a name="register-device-x509-ca-signed"></a>Eszköz regisztrálása (X.509 aláírt hitelesítésnél)

Az X.509-es hitelesítésszolgáltató (CA) aláírt hitelesítési hitelesítésesetén az IoT Hubban regisztrált legfelső szintű hitelesítésszolgáltatói tanúsítványra van szükség, amelyet az IoT-eszköz tanúsítványainak aláírásához használ. Minden olyan eszköz, amely olyan tanúsítványt használ, amelyet a legfelső szintű hitelesítésszolgáltatói tanúsítvány vagy annak bármely köztes tanúsítványa adott ki, hitelesítheti magát.

Ez a szakasz az IoT [Hub-cikkx.509-es biztonság beállítása az Azure IoT hubon](../iot-hub/iot-hub-security-x509-get-started.md)című cikkében részletezett utasításokon alapul. Ebben a szakaszban ismertetett lépéseket, hogy tudja, mely értékeket kell használni, hogy hozzanak létre egy átjárón keresztül csatlakozó alsóbb rétegbeli eszköz.

Ha nem rendelkezik hitelesítésszolgáltatóval az X.509-es tanúsítványok létrehozásához, [demótanúsítványokat hozhat létre az IoT Edge-eszköz szolgáltatásainak teszteléséhez.](how-to-create-test-certificates.md) Az alsóbb rétegbeli eszköz teszttanúsítványainak létrehozásakor használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, amely az átjáróeszköz tanúsítványait hozta létre.

1. Kövesse az [X.509-es hitelesítésszolgáltatói tanúsítványok regisztrálása](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) című szakaszutasításait az *X.509-es biztonság beállítása az Azure IoT-központban*című IoT hub szakaszához. Ebben a szakaszban hajtsa végre a következő lépéseket:

   1. Töltse fel a legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ha a demó tanúsítványokat használja, a legfelső szintű hitelesítésszolgáltató a ** \<>/certs/azure-iot-test-only.root.ca.cert.pem elérési út.**

   2. Ellenőrizze, hogy ön-e a legfelső szintű hitelesítésszolgáltatói tanúsítvány a tulajdonában.

2. Kövesse az [X.509-es eszköz létrehozása az](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *Azure.509-es biztonság beállítása az Azure IoT hubban*című IoT hubhoz című szakaszutasításait. Ebben a szakaszban hajtsa végre a következő lépéseket:

   1. Új eszköz hozzáadása. Adja meg az **eszközazonosító**kisbetűs nevét, és válassza ki az **X.509 hitelesítésszolgáltató aláírt**hitelesítéstípusát.
   2. Állítsa be a szülőeszközt. Alsóbb rétegbeli eszközök esetén válassza **a Szülőeszköz beállítása** lehetőséget, és válassza ki azt az IoT Edge átjáróeszközt, amely biztosítja a kapcsolatot az IoT Hubhoz.

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt, amelyet az IoT Hubba töltött fel a lánc hozhoz. Használja ugyanazt a kis-eszköz azonosítót, amelyet az eszközidentitásának adott a portálon.

4. Másolja az eszköztanúsítványt és a kulcsokat az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű hitelesítésszolgáltatói tanúsítvány egy példányát is, amely az átjáróeszköz-tanúsítványt és az alsóbb rétegbeli eszköztanúsítványokat is létrehozta.

   Ezeket a fájlokat az IoT Hubhoz csatlakozó levéleszköz-alkalmazásokban fogja hivatkozni. Használhatja a szolgáltatást, például [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a függvény, például a Secure copy [protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájlok áthelyezéséhez.

5. A kívánt nyelvtől függően tekintse át az X.509-tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [Az X.509-es biztonság beállítása az Azure IoT hubon](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Használhatja az [IoT-bővítmény az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension) ugyanazt az eszközlétrehozási műveletet. A következő példa létrehoz egy új IoT-eszközt X.509 hitelesítésszolgáltató aláírásával, és hozzárendel egy szülőeszközt:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

További információkért tekintse meg az Azure CLI referenciatartalom [az az iot hub eszköz-identitás](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsokat.

Ezután [olvassa be és módosítsa a kapcsolati karakterláncot,](#retrieve-and-modify-connection-string) hogy az eszköz tudja, hogy az átjárón keresztül csatlakozzon.

## <a name="retrieve-and-modify-connection-string"></a>Kapcsolati karakterlánc beolvasása és módosítása

Miután létrehozott egy IoT-eszköz identitást a portálon, lekérheti az elsődleges vagy másodlagos kulcsokat. Ezek a kulcsok egyikét tartalmaznia kell a kapcsolati karakterlánc, amely az alkalmazások segítségével kommunikálni az IoT Hub. A szimmetrikus kulcshitelesítéshez az IoT Hub biztosítja a teljesen kialakított kapcsolati karakterláncot az eszköz részleteiben az Ön kényelme érdekében. További információkat kell hozzáadnia az átjáróeszközről a kapcsolati karakterlánchoz.

Az alsóbb rétegbeli eszközök csatlakozási karakterláncaihoz a következő összetevőkre van szükség:

* Az IoT hub, amelyhez az eszköz csatlakozik:`Hostname={iothub name}.azure-devices.net`
* A hubon regisztrált eszközazonosító:`DeviceID={device ID}`
* Az elsődleges vagy másodlagos kulcs:`SharedAccessKey={key}`
* Az átjáróeszköz, amelyen keresztül az eszköz csatlakozik. Adja meg a **hostname** értéket az IoT Edge átjáróeszköz config.yaml fájljából:`GatewayHostName={gateway hostname}`

A teljes kapcsolati karakterlánc együttesen a következőképpen néz ki:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ha szülő-gyermek kapcsolatot létesített ehhez az alsóbb rétegbeli eszközhöz, egyszerűsítheti a kapcsolati karakterláncot, ha közvetlenül hívja meg az átjárót a kapcsolatgazdaként. Szülő-gyermek kapcsolatok szükségesek az X.509 hitelesítéshez, de nem kötelező a szimmetrikus kulcshitelesítéshez. Példa:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Ezen a ponton rendelkeznie kell egy IoT Edge-eszköz regisztrálva és átjáróként konfigurálva. Egy alsóbb rétegbeli IoT-eszköz regisztrálva is van, és az átjáróeszközre mutat. Az utolsó lépés az, hogy tanúsítványokat helyezzen el az alsóbb rétegbeli eszközön, hogy biztonságosan kapcsolódjon az átjáróhoz.

Folytassa az átjárósorozat következő cikkében, [amely egy alsóbb rétegbeli eszközt csatlakoztat egy Azure IoT Edge-átjáróhoz.](how-to-connect-downstream-device.md)

## <a name="next-steps"></a>További lépések

Ezzel a cikkel egy IoT Edge-eszköz működik, mint egy átlátszó átjáró és egy alsóbb rétegbeli eszköz regisztrált egy IoT hub. Ezután konfigurálnia kell az alsóbb rétegbeli eszközöket, hogy megbízzon az átjáróeszközben, és biztonságosan csatlakozzon hozzá. További információ: [Alsóbb rétegbeli eszköz csatlakoztatása egy Azure IoT Edge-átjáróhoz.](how-to-connect-downstream-device.md)
