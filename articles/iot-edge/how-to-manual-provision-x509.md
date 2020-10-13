---
title: Kiépítés X. 509 tanúsítvánnyal – Azure IoT Edge | Microsoft Docs
description: A telepítés után helyezzen üzembe egy IoT Edge eszközt az eszköz azonosító tanúsítványával, és hitelesítse magát a IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979541"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Azure IoT Edge eszköz beállítása X. 509 tanúsítványalapú hitelesítéssel

Ez a cikk bemutatja, hogyan regisztrálhat új IoT Edge eszközt a IoT Hubban, és hogyan konfigurálhatja az eszközt X. 509 tanúsítvánnyal történő hitelesítésre.

A cikkben ismertetett lépések végigvezetik a manuális kiépítés folyamatán, ahol az egyes eszközöket manuálisan csatlakoztathatja az IoT hub-hoz. A másik lehetőség az automatikus kiépítés a IoT Hub Device Provisioning Service használatával, ami hasznos lehet, ha sok eszközt kell kiépíteni.

<!--TODO: Add auto-provision info/links-->

A manuális kiépítés esetén két lehetősége van IoT Edge eszközök hitelesítésére:

* **Szimmetrikus kulcs**: amikor új eszköz-identitást hoz létre IoT Hubban, a szolgáltatás két kulcsot hoz létre. Elhelyezi a kulcsok egyikét az eszközön, és megjeleníti a hitelesítő IoT Hub kulcsát.

  Ez a hitelesítési módszer gyorsabb a kezdéshez, de nem biztonságos.

* **X. 509 önaláírt**: két x. 509 identitás-tanúsítványt hoz létre, és elhelyezi őket az eszközön. Amikor új eszköz-identitást hoz létre IoT Hubban, mindkét tanúsítványból ujjlenyomatai megfelelnek biztosít. Amikor az eszköz hitelesíti IoT Hub, megjeleníti a tanúsítványait, és IoT Hub ellenőrizheti, hogy egyeznek-e a ujjlenyomatai megfelelnek.

  Ez a hitelesítési módszer biztonságosabb, és éles környezetekben ajánlott.

Ez a cikk végigvezeti a regisztrációs és kiépítési folyamaton az X. 509 Tanúsítványos hitelesítéssel. Ha meg szeretné tudni, hogyan állíthat be egy szimmetrikus kulccsal rendelkező eszközt, tekintse meg a [Azure IoT Edge eszköz szimmetrikus kulcsú hitelesítéssel történő beállítását](how-to-manual-provision-symmetric-key.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzése előtt rendelkeznie kell egy olyan eszközzel, amelyen telepítve van a IoT Edge futtatókörnyezet. Ha nem, kövesse az [Azure IoT Edge futtatókörnyezet telepítése vagy eltávolítása](how-to-install-iot-edge.md)című témakör lépéseit.

Az X. 509 tanúsítvánnyal történő manuális üzembe helyezéshez IoT Edge 1.0.10 vagy újabb verzió szükséges.

## <a name="create-certificates-and-thumbprints"></a>Tanúsítványok és ujjlenyomatai megfelelnek létrehozása



<!-- TODO -->

## <a name="register-a-new-device"></a>Új eszköz regisztrálása

A IoT Hubhoz csatlakozó összes eszközhöz tartozik egy eszköz-azonosító, amely a felhőből az eszközre vagy az eszközről a felhőbe irányuló kommunikáció nyomon követésére szolgál. Olyan eszközt konfigurál, amely tartalmazza a kapcsolati adatait, beleértve az IoT hub állomásnévét, az eszköz AZONOSÍTÓját és az eszköz által a IoT Hub való hitelesítéshez használt információkat.

X. 509 tanúsítványalapú hitelesítés esetén ezt az információt az *ujjlenyomatai megfelelnek* származó adatok formájában kell megadnia. Ezek a ujjlenyomatai megfelelnek az eszköz regisztrációjának időpontjában IoT Hub kapnak, hogy a szolgáltatás felismerje az eszközt a csatlakozáskor.

Számos eszköz használatával regisztrálhat egy új IoT Edge eszközt a IoT Hub, és feltöltheti a tanúsítvány-ujjlenyomatai megfelelnek. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>A Azure Portal előfeltételei

Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) .

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>IoT Edge-eszköz létrehozása a Azure Portal

A Azure Portal IoT Hub a IoT Edge eszközöket külön hozza létre és kezeli a nem engedélyezett IOT-eszközökről.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktáblán válassza a menü **IoT Edge** elemét, majd válassza a **IoT Edge eszköz hozzáadása**lehetőséget.

   ![IoT Edge-eszköz hozzáadása a Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Az **eszköz létrehozása** lapon adja meg a következő információkat:

   * Hozzon létre egy leíró eszköz AZONOSÍTÓját. Jegyezze fel az eszköz AZONOSÍTÓját, ahogy azt a következő szakaszban fogja használni.
   * Válassza az **X. 509 önaláírt** hitelesítési típusként lehetőséget.
   * Adja meg az elsődleges és a másodlagos identitás tanúsítványának ujjlenyomatai megfelelnek. Az ujjlenyomatok értéke 40 – hexadecimális karakter az SHA-1 kivonatokhoz, vagy 64 – hexadecimális karakter az SHA-256 kivonatokhoz.

1. Kattintson a **Mentés** gombra.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge eszközök megtekintése a Azure Portal

Az IoT hub-hoz csatlakozó összes Edge-kompatibilis eszköz megjelenik a **IoT Edge** oldalon.

![Az IoT hub összes IoT Edge eszközének megtekintése](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Az Azure CLI előfeltételei

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>IoT Edge-eszköz létrehozása az Azure CLI-vel

Az az [IOT hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) paranccsal hozzon létre egy új eszköz-identitást az IOT hub-ban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Ez a parancs több paramétert is tartalmaz:

* `--device-id` vagy `-d` : adjon meg egy leíró nevet, amely egyedi az IoT hub számára. Jegyezze fel az eszköz AZONOSÍTÓját, ahogy azt a következő szakaszban fogja használni.
* `hub-name` vagy `-n` : adja meg az IoT hub nevét.
* `--edge-enabled` vagy `--ee` : állapítsa meg, hogy az eszköz egy IoT Edge eszköz.
* `--auth-method` vagy `--am` : deklarálja azt az engedélyezési típust, amelyet az eszköz használni fog. Ebben az esetben a ujjlenyomatai megfelelnek X. 509 tanúsítványt használunk.
* `--primary-thumbprint` vagy `--ptp` : adjon meg egy X. 509 tanúsítvány ujjlenyomatát, amelyet elsődleges kulcsként kíván használni.
* `--secondary-thumbprint` vagy `--stp` : adjon meg egy X. 509 tanúsítvány-ujjlenyomatot, amelyet másodlagos kulcsként kíván használni.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge eszközök megtekintése az Azure CLI-vel

Az az [IOT hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) paranccsal megtekintheti az IOT hub összes eszközét. Például:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Adja hozzá a jelzőt, `--edge-enabled` vagy `--ee` csak az IoT hub IoT Edge eszközeit listázhatja.

A IoT Edge eszközként regisztrált eszközökön a tulajdonság képességei lesznek. a **iotEdge** értéke **true (igaz**).

--- 

## <a name="configure-an-iot-edge-device"></a>IoT Edge-eszköz konfigurálása

Miután a IoT Edge eszköz identitása szerepel a IoT Hubban, konfigurálnia kell az eszközt a Felhőbeli identitásával, valamint az azonosító tanúsítványával.

Linux-eszközön ezeket az információkat a config. YAML fájl szerkesztésével adhatja meg. Egy Windows-eszközön egy PowerShell-szkript futtatásával adja meg ezeket az adatokat.

# <a name="linux"></a>[Linux](#tab/linux)

1. A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. A fájl kiépítési konfigurációk szakaszának megkeresése. 

1. Megjegyzés a **manuális üzembe helyezési konfigurációhoz a kapcsolatok karakterlánca szakasz használatával** .

1. A manuális üzembe helyezési konfiguráció megjegyzésének **visszautasítása X. 509 azonosító tanúsítvány használatával** szakasz. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Frissítse a következő mezőket:

   * **iothub_hostname**: a IoT hub állomásneve, amelyhez az eszköz csatlakozni fog. Például: `{IoT hub name}.azure-devices.net`.
   * **device_id**: az eszköz regisztrálása során megadott azonosító.
   * **identity_cert**: URI az eszközön lévő identitási tanúsítványhoz. Például: `file:///path/identity_certificate.pem`.
   * **identity_pk**: a megadott identitás tanúsítványához tartozó titkos kulcs fájljának URI-ja. Például: `file:///path/identity_key.pem`.

1. Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

1. A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. A IoT Edge eszközön futtassa a PowerShellt rendszergazdaként.

2. Az [inicializálás-IoTEdge](reference-windows-scripts.md#initialize-iotedge) parancs használatával konfigurálja a IoT Edge futtatókörnyezetet a gépen.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Ha Linux-tárolókat használ, adja hozzá a `-ContainerOs` paramétert a jelzőhöz. Konzisztensnek kell lennie a `Deploy-IoTEdge` korábban futtatott paranccsal kiválasztott tároló beállítással.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Ha a IoTEdgeSecurityDaemon.ps1 szkriptet offline vagy adott verzióra történő telepítésre töltötte le az eszközön, akkor ügyeljen arra, hogy a parancsfájl helyi példányára hivatkozzon.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Ha a rendszer kéri, adja meg a következő információkat:

   * **IotHubHostName**: az az IoT hub állomásneve, amelyhez az eszköz csatlakozni fog. Például: `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: az eszköz regisztrálása során megadott azonosító.
   * **X509IdentityCertificate**: az eszközön lévő identitási tanúsítvány abszolút elérési útja. Például: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: a megadott identitási tanúsítványhoz tartozó titkos kulcs fájljának abszolút elérési útja. Például: `C:\path\identity_key.pem`.

Amikor manuálisan épít ki egy eszközt, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* Egy adott edgeAgent-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a [IoT Edge-modulok üzembe](how-to-deploy-modules-portal.md) helyezésével, hogy megtudja, hogyan helyezhet üzembe modulokat az eszközön.
