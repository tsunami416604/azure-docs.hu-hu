---
title: Alsóbb rétegbeli eszközök hitelesítése – Azure IoT Edge | Microsoft Docs
description: Hogyan hitelesítheti az alárendelt eszközöket vagy a levélben lévő eszközöket IoT Hub, és hogyan irányíthatja át a kapcsolódást Azure IoT Edge átjáró-eszközökön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411498"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon

Transzparens átjáró esetén az alsóbb rétegbeli eszközöknek (más néven levél-vagy gyermek-eszközöknek) identitásokra van szükségük IoT Hub mint bármely más eszköz. Ez a cikk végigvezeti az alsóbb rétegbeli eszközök IoT Hubra való hitelesítésének lehetőségeit, majd bemutatja, hogyan deklarálhatja az átjáró-kapcsolatokat.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a második lépést ismerteti:

1. Az átjáró-eszköznek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra, az alárendelt eszközökről érkező kommunikáció fogadására és az üzenetek megfelelő célhelyre való továbbítására. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. **Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz-identitással, hogy képes legyen hitelesíteni a IoT Hub, és tudnia kell kommunikálni az átjáró eszközén keresztül.**
3. Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáró eszközéhez. További információ: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).

Az alsóbb rétegbeli eszközök a következő három módszer egyikével hitelesíthetők a IoT Hub használatával: szimmetrikus kulcsok (más néven közös hozzáférési kulcsok), X. 509 önaláírt tanúsítvány vagy X. 509 hitelesítésszolgáltató (CA) által aláírt tanúsítvány. A hitelesítési lépések hasonlók a nem IoT-eszközök IoT Hub-vel való beállításához használt lépésekhez, és kisebb eltéréseket jelentenek az átjáró kapcsolatának bejelentéséhez.

A cikkben ismertetett lépések a manuális eszközök kiépítés nélküli üzembe helyezését mutatják be az Azure IoT Hub Device Provisioning Service (DPS) szolgáltatással való automatikus kiépítés nélkül. Az alárendelt eszközök DPS-vel való kiépítés nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [IoT Edge-eszköz konfigurálásának lépéseit transzparens átjáróként való](how-to-create-transparent-gateway.md)művelethez. Ha X. 509 hitelesítést használ az alsóbb rétegbeli eszközhöz, ugyanazt a tanúsítvány-létrehozási parancsfájlt kell használnia, amelyet az átlátszó átjáró című cikkben állított be.

Ez a cikk az *átjáró állomásneve* több ponton is hivatkozik. Az átjáró állomásneve deklarálva van a config. YAML fájl **hostname** paraméterében a IoT Edge Gateway eszközön. Az alárendelt eszköz kapcsolódási sztringje hivatkozik rá. Az átjáró állomásneve feloldhatónak kell lennie egy IP-címhez, vagy a DNS-t vagy egy gazdagépet tartalmazó bejegyzést kell használnia.

## <a name="register-device-symmetric-key"></a>Eszköz regisztrálása (szimmetrikus kulcs)

A szimmetrikus kulcsos hitelesítés vagy a közös hozzáférésű kulcsos hitelesítés a legegyszerűbb módszer a IoT Hub való hitelesítéshez. A szimmetrikus kulcsos hitelesítéssel Base64-kulcsot társítunk a IoT-eszköz AZONOSÍTÓJÁHOZ IoT Hub. Ezt a kulcsot a IoT-alkalmazásokban is felveszi, hogy az eszköz bemutassa a IoT Hubhoz való csatlakozáskor.

### <a name="create-the-device-identity"></a>Az eszköz identitásának létrehozása

Vegyen fel egy új IoT-eszközt az IoT hub-ban a Visual Studio Code-hoz készült Azure Portal, az Azure CLI vagy a IoT-bővítmény használatával. Ne feledje, hogy az alsóbb rétegbeli eszközöket IoT Hub kell azonosítani a normál IoT-eszközként, nem IoT Edge eszközöket.

Az új eszköz identitásának létrehozásakor adja meg a következő információkat:

* Hozzon létre egy azonosítót az eszközhöz.

* A hitelesítési típusként válassza a **szimmetrikus kulcs** lehetőséget.

* Igény szerint **beállíthatja a szülő eszközt** , és kiválaszthatja azt a IoT Edge átjáró-eszközt, amelyre az alárendelt eszköz kapcsolódni fog. Ez a lépés nem kötelező a szimmetrikus kulcsos hitelesítéshez, de ajánlott, mert a szülő eszköz beállítása lehetővé teszi az alsóbb rétegbeli eszköz [Offline képességeit](offline-capabilities.md) . Bármikor frissítheti az eszköz adatait, hogy később hozzáadja vagy megváltoztassa a szülőt.

   ![Eszköz AZONOSÍTÓjának létrehozása szimmetrikus kulcsú hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Ugyanezt a műveletet az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) használatával végezheti el. A következő példa egy új IoT-eszközt hoz létre szimmetrikus kulcsos hitelesítéssel, és egy fölérendelt eszközt rendel hozzá:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Az eszközök létrehozásával és a szülő-gyermek felügyelettel kapcsolatos Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok hivatkozási tartalmát.


Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

## <a name="register-device-x509-self-signed"></a>Eszköz regisztrálása (X. 509 önaláírt)

Az X. 509 önaláírt hitelesítés (más néven ujjlenyomatos hitelesítés) esetében új tanúsítványokat kell létrehoznia a IoT-eszközre való elhelyezéshez. Ezek a tanúsítványok olyan ujjlenyomattal rendelkeznek, amelyet IoT Hub a hitelesítéshez.

Ha nem rendelkezik hitelesítésszolgáltatóval X. 509 tanúsítványok létrehozásához, [létrehozhat bemutató-tanúsítványokat IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md). Az alsóbb rétegbeli eszközhöz tartozó tesztelési tanúsítványok létrehozásakor ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja, amely az átjáró-eszköz tanúsítványait generálta.

1. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával hozzon létre két (elsődleges és másodlagos) tanúsítványokat az alsóbb rétegbeli eszközhöz.

   Az eszköz tanúsítványához meg kell adni a tulajdonos nevét a IoT eszköz Azure IoT Hubban való regisztrálásakor használni kívánt eszköz AZONOSÍTÓjának. Ez a beállítás a hitelesítéshez szükséges.

2. Kérje le az SHA1 ujjlenyomatot (a IoT Hub felületen található ujjlenyomatot) minden tanúsítványból, amely egy 40 hexadecimális karakterből álló karakterlánc. A következő OpenSSL-paranccsal tekintheti meg a tanúsítványt, és keresse meg az ujjlenyomatot:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Futtassa kétszer ezt a parancsot az elsődleges tanúsítványhoz és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítvány ujjlenyomatát adja meg, ha új IoT-eszközt regisztrál önaláírt X. 509 tanúsítvánnyal.

3. Navigáljon az IoT hubhoz a Azure Portalon, és hozzon létre egy új IoT-eszköz identitását a következő értékekkel:

   * Adja meg az eszköz tanúsítványának tulajdonos nevével megegyező **azonosítóját** .
   * Válassza az **X. 509 önaláírt** hitelesítési típusként lehetőséget.
   * Illessze be azokat a hexadecimális karakterláncokat, amelyeket az eszköz elsődleges és másodlagos tanúsítványainakből másolt.
   * Válassza a **szülő eszköz beállítása** elemet, és válassza ki azt a IoT Edge átjáró-eszközt, amelyre ez az alárendelt eszköz fog csatlakoztatva. Egy alárendelt eszköz X. 509 hitelesítéséhez szülő eszköz szükséges.

   ![Eszköz AZONOSÍTÓjának létrehozása X. 509 önaláírt hitelesítéssel a portálon](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Másolja az eszköz tanúsítványát és kulcsait az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatát is, amely az átjáró-eszköz tanúsítványát és az alsóbb rétegbeli eszköz tanúsítványait is létrehozta.

   Ezeket a fájlokat a IoT Hubhoz csatlakozó Leaf Device-alkalmazásokban fogja hivatkozni. Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.

5. Az előnyben részesített nyelvtől függően tekintse át az X. 509 tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [az X. 509 Biztonság beállítása az Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension) használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. A következő példa egy új IoT-eszközt hoz létre X. 509 önaláírt hitelesítéssel, és egy fölérendelt eszközt rendel hozzá:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Az eszközök létrehozásával, a tanúsítványok létrehozásával, valamint a szülő-és alárendelt felügyelettel kapcsolatos Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok hivatkozási tartalmát.

Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

## <a name="register-device-x509-ca-signed"></a>Eszköz regisztrálása (X. 509 HITELESÍTÉSSZOLGÁLTATÓ aláírva)

Az X. 509 hitelesítésszolgáltató (CA) által aláírt hitelesítéshez szükség van egy IoT Hub regisztrált legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra, amelyet a IoT-eszköz tanúsítványainak aláírásához használ. Minden olyan eszköz, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a köztes tanúsítványok valamelyikével kapcsolatos hibát okozó tanúsítványt használ, a hitelesítés engedélyezve lesz.

Ez a szakasz az [X. 509 biztonság Azure IoT hub-ban való beállításának](../iot-hub/iot-hub-security-x509-get-started.md)IoT hub cikkében részletesen ismertetett utasításokon alapul. Az ebben a szakaszban ismertetett lépéseket követve megtudhatja, hogy mely értékeket kell használni egy átjárón keresztül csatlakozó alsóbb rétegbeli eszköz beállításához.

Ha nem rendelkezik hitelesítésszolgáltatóval X. 509 tanúsítványok létrehozásához, [létrehozhat bemutató-tanúsítványokat IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md). Az alsóbb rétegbeli eszközhöz tartozó tesztelési tanúsítványok létrehozásakor ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja, amely az átjáró-eszköz tanúsítványait generálta.

1. Kövesse az x [. 509 hitelesítésszolgáltatói tanúsítványok regisztrálása az IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) -ban című szakasz utasításait az *x. 509 szintű biztonság beállítása az Azure IoT hub-ban*. Ebben a szakaszban a következő lépéseket hajtja végre:

   1. Töltse fel a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Ha a bemutató tanúsítványait használja, a legfelső szintű hitelesítésszolgáltató ** \<elérési útja>/certs/Azure-IOT-test-only.root.ca.CERT.PEM**.

   2. Győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány tulajdonosa.

2. Az *x. 509 Biztonság beállítása az Azure IoT hub-ban*című rész útmutatását követve [hozzon létre egy x. 509-eszközt az IoT hub létrehozásához](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) . Ebben a szakaszban a következő lépéseket hajtja végre:

   1. Adjon hozzá egy új eszközt. Adja meg az **eszköz azonosítójának**kisbetűs nevét, és válassza ki az **X. 509 hitelesítésszolgáltató által aláírt**hitelesítési típust.
   2. Hozzon létre egy fölérendelt eszközt. Alsóbb rétegbeli eszközök esetén válassza a **fölérendelt eszköz beállítása** lehetőséget, majd válassza ki azt a IoT Edge átjáró eszközt, amely a IoT hubhoz való kapcsolódást biztosítja.

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, amelyet a IoT Hub feltöltött a lánc elvégzéséhez. Használja ugyanazt a kisbetűs eszköz-azonosítót, amelyet az eszköz identitásához adott a portálon.

4. Másolja az eszköz tanúsítványát és kulcsait az alsóbb rétegbeli eszköz bármely helyére. Helyezze át a megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatát is, amely az átjáró-eszköz tanúsítványát és az alsóbb rétegbeli eszköz tanúsítványait is létrehozta.

   Ezeket a fájlokat a IoT Hubhoz csatlakozó Leaf Device-alkalmazásokban fogja hivatkozni. Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.

5. Az előnyben részesített nyelvtől függően tekintse át az X. 509 tanúsítványok IoT-alkalmazásokban való hivatkozásának mintáit:

   * C#: [az X. 509 Biztonság beállítása az Azure IoT hub-ban](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension) használhatja ugyanazon eszköz-létrehozási művelet végrehajtásához. A következő példa egy új IoT-eszközt hoz létre X. 509 HITELESÍTÉSSZOLGÁLTATÓI aláírásos hitelesítéssel, és egy fölérendelt eszközt rendel hozzá:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

További információ: Azure CLI-hivatkozási tartalom az az [IOT hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) parancsok.

Ezután [kérje le és módosítsa a kapcsolati karakterláncot](#retrieve-and-modify-connection-string) , hogy az eszköz képes legyen csatlakozni az átjárón keresztül.

## <a name="retrieve-and-modify-connection-string"></a>A kapcsolatok karakterláncának lekérése és módosítása

Miután létrehozta a IoT-eszköz identitását a portálon, lekérheti az elsődleges vagy másodlagos kulcsait. Az egyik kulcsnak szerepelnie kell a kapcsolódási karakterláncban, amelyet az alkalmazások a IoT Hubsal való kommunikációra használnak. A szimmetrikus kulcsos hitelesítéshez IoT Hub biztosítja a teljes mértékben formázott kapcsolati karakterláncot az eszköz részleteiben az Ön kényelme érdekében. További információkat kell felvennie az átjáró-eszközről a kapcsolódási karakterláncra.

Az alsóbb rétegbeli eszközökhöz tartozó kapcsolatok karakterláncának a következő összetevőket kell megadnia:

* Az a IoT hub, amelyhez az eszköz csatlakozik:`Hostname={iothub name}.azure-devices.net`
* A központban regisztrált eszköz azonosítója:`DeviceID={device ID}`
* Vagy az elsődleges vagy a másodlagos kulcs:`SharedAccessKey={key}`
* Az az átjáró-eszköz, amelyhez az eszköz csatlakozik. Adja meg a **hostname** értéket az IoT Edge Gateway-eszköz config. YAML fájljában:`GatewayHostName={gateway hostname}`

Az összes együtt egy teljes körű kapcsolatok karakterlánca a következőképpen néz ki:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ha létrehozott egy szülő/gyermek kapcsolatot ehhez az alárendelt eszközhöz, a kapcsolati karakterlánc leegyszerűsíthető úgy, hogy az átjárót közvetlenül a kapcsolati gazdagépként hívja meg. Az X. 509 hitelesítéshez szülő/gyermek kapcsolat szükséges, a szimmetrikus kulcsos hitelesítés azonban nem kötelező. Például:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Ekkor egy IoT Edge eszköz regisztrálva van, és átjáróként kell konfigurálni. Egy alsóbb rétegbeli IoT-eszköz is regisztrálva van, és az átjáró eszközére mutat. Az utolsó lépés az alsóbb rétegbeli eszközön lévő tanúsítványok elhelyezése, hogy biztonságosan lehessen csatlakozni az átjáróhoz.

Folytassa az átjáró sorozat következő cikkével, [csatlakoztasson egy alsóbb rétegbeli eszközt egy Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>További lépések

Ennek a cikknek a végrehajtásával rendelkeznie kell egy IoT Edge eszközzel, amely transzparens átjáróként és egy IoT hub-ban regisztrált alsóbb rétegbeli eszközként működik. Ezután be kell állítania az alsóbb rétegbeli eszközöket, hogy megbízzon az átjáró-eszközön, és biztonságosan kapcsolódjon hozzá. További információ: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).
