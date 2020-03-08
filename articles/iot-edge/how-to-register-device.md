---
title: Új Azure IoT Edge-eszköz regisztrálása | Microsoft Docs
description: Le a kapcsolati karakterláncot, és a egy új IoT Edge-eszköz regisztrálása az IoT-bővítmény, az Azure CLI használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7facb539a84d39e6659627e80ecc560fc87591c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673508"
---
# <a name="register-an-azure-iot-edge-device"></a>Azure IoT Edge-eszköz regisztrálása

Ahhoz, hogy a IoT-eszközöket Azure IoT Edge használatával használhassa, regisztrálnia kell őket az IoT hub-ban. Ha egy eszköz regisztrálva van, lekérhet egy kapcsolódási karakterláncot az eszköz IoT Edge számítási feladatokhoz való beállításához.

A regisztrációt a következő eszközök egyikével választhatja ki:

* Ha a grafikus felhasználói felületen szeretne Azure-erőforrásokat létrehozni, megtekinteni és felügyelni, [regisztrálja az eszközt a Azure Portalban](#register-in-the-azure-portal) .
* Ha az Azure IoT-erőforrásokat ugyanazon a helyen szeretné kezelni, ahol a IoT-megoldásokat fejleszti, [regisztrálja az eszközt a Visual Studio Code](#register-with-visual-studio-code) -ban.
* [Egy eszköz regisztrálása az Azure CLI-vel](#register-with-the-azure-cli) , ha inkább parancssori eszközöket szeretne használni az Azure-erőforrások kezeléséhez, vagy automatizálni szeretné a feladatokat.

## <a name="register-in-the-azure-portal"></a>Regisztráljon a Azure Portal

A Azure Portal összes regisztrációs feladatát elvégezheti.

### <a name="prerequisites-for-the-azure-portal"></a>A Azure Portal előfeltételei

Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) .

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>IoT Edge-eszköz létrehozása a Azure Portal

A Azure Portal IoT Hub a IoT Edge eszközöket külön hozza létre és kezeli a nem engedélyezett IOT-eszközökről.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
2. A bal oldali ablaktáblán válassza a menü **IoT Edge** elemét.
3. Válassza **a IoT Edge eszköz hozzáadása**lehetőséget.
4. Adjon meg egy leíró eszközazonosító. Használja az alapértelmezett beállításokat a hitelesítési kulcsok automatikus létrehozásához és az új eszköz csatlakoztatásához a központhoz.
5. Kattintson a **Mentés** gombra.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge eszközök megtekintése a Azure Portal

Az IoT hub-hoz csatlakozó összes Edge-kompatibilis eszköz megjelenik a **IoT Edge** oldalon.

![Az IoT hub IoT Edge-eszközök megtekintése](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>A Azure Portal a kapcsolatok karakterláncának beolvasása

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. A portál **IoT Edge** lapján kattintson az eszköz azonosítójára a IoT Edge eszközök listájáról.
2. Másolja az **elsődleges** vagy **másodlagos kapcsolatok karakterláncának**értékét.

## <a name="register-with-visual-studio-code"></a>Regisztrálás a Visual Studio Code-ban

A legtöbb műveletek végrehajtása a VS Code-ban többféle módon lehet. Ez a cikk a Explorert használja, de a parancssorban is futtathatja a lépéseket.

### <a name="prerequisites-for-visual-studio-code"></a>A Visual Studio Code előfeltételei

* [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében
* [Visual Studio Code](https://code.visualstudio.com/)
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

A Visual Studio Code-hoz készült Azure IoT Extensions használatával műveleteket hajthat végre a IoT Hub. Ezeknek a műveleteknek a működéséhez be kell jelentkeznie az Azure-fiókjába, és ki kell választania a IoT Hub.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.
1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

   ![Az Azure IoT Hub-eszközök a szakaszt kibontva](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé.
1. Válassza a **IoT hub kiválasztása**lehetőséget.
1. Ha nem jelentkezett be az Azure-fiókjába, kövesse az utasításokat.
1. Válassza ki az Azure-előfizetését.
1. Válassza ki az IoT hubnak.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>IoT Edge-eszköz létrehozása a Visual Studio Code-ban

1. A VS Code Explorerben bontsa ki az **Azure IoT hub-eszközök** szakaszt.
1. Kattintson a **...** elemre az **Azure IoT hub eszközök** szakasz fejlécében. Ha nem látja a három pontra, kattintson a, vagy vigye a kurzort a fejléc fölé.
1. Válassza a **létrehozás IoT Edge eszköz**lehetőséget.
1. A megnyíló szövegbeviteli mezőben adjon az eszköz azonosítóval.

A képernyőn a parancs eredménye látható. A rendszer kinyomtatja az eszköz adatait, beleértve a megadott **deviceId** -t, valamint azt a **ConnectionString** -t, amellyel a fizikai eszközt csatlakoztathatja az IoT hubhoz.

A képernyőn a parancs eredménye látható. A rendszer kinyomtatja az eszköz adatait, beleértve a megadott **deviceId** -t, valamint azt a **ConnectionString** -t, amellyel a fizikai eszközt csatlakoztathatja az IoT hubhoz.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>A Visual Studio Code-ban IoT Edge eszközök megtekintése

Az IoT hub-hoz csatlakozó összes eszköz megjelenik a Visual Studio Code Explorer **Azure IoT hub** szakaszában. IoT Edge eszközök a nem peremhálózati eszközöktől eltérő ikonnal vannak elkülönítve, és az a tény, hogy az **$edgeAgent** és **$edgeHub** modulokat minden IoT Edge eszközön telepítik.

![Az IoT hub IoT Edge-eszközök megtekintése](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>A Visual Studio Code-beli kapcsolatok karakterláncának beolvasása

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell.

1. Kattintson a jobb gombbal az eszköz AZONOSÍTÓJÁRA az **Azure IoT hub** szakaszban.
1. Válassza az **eszköz csatlakoztatási karakterláncának másolása**lehetőséget.

   A kapcsolati karakterláncot a vágólapra másolja.

Azt is megteheti, hogy az eszköz **információinak beolvasása** lehetőségre kattint a jobb gombbal, és megjeleníti a kimeneti ablakban található összes eszközt, beleértve a kapcsolódási karakterláncot is.

## <a name="register-with-the-azure-cli"></a>Regisztrálás az Azure CLI-vel

Az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT Edge kezelésére. Lehetővé teszi, hogy az Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok beépített kezelése. Az IoT bővítmény az Azure CLI-t az eszközök kezelésével és a teljes IoT Edge funkcióval gazdagítja.

### <a name="prerequisites-for-the-azure-cli"></a>Az Azure CLI előfeltételei

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>IoT Edge-eszköz létrehozása az Azure CLI-vel

Az az [IOT hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) paranccsal hozzon létre egy új eszköz-identitást az IOT hub-ban. Például:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétereket tartalmazza:

* **eszköz-azonosító**: adjon meg egy leíró nevet, amely egyedi az IoT hub számára.
* **hub-Name**: adja meg az IoT hub nevét.
* **Edge-enabled**: Ez a paraméter azt deklarálja, hogy az eszköz a IoT Edge-vel való használatra szolgál.

   ![az iot hub-eszközidentitást create kimenete](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge eszközök megtekintése az Azure CLI-vel

Az az [IOT hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) paranccsal megtekintheti az IOT hub összes eszközét. Például:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

A IoT Edge eszközként regisztrált eszközökön a tulajdonság képességei lesznek. a **iotEdge** értéke **true (igaz**).

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>A kapcsolatok karakterláncának beolvasása az Azure CLI-vel

Amikor készen áll az eszköz beállításához, a kapcsolati karakterláncot, amely összekapcsolja a fizikai eszköz az IoT hub az identitással kell. Az az [IOT hub Device-Identity show-kapcsolat-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) paranccsal visszaállíthatja egy adott eszköz kapcsolati karakterláncát:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

A `device-id` paraméter értéke megkülönbözteti a kis-és nagybetűket. Ne másolja a kapcsolati karakterláncot az idézőjelek közé.

## <a name="next-steps"></a>Következő lépések

Most, hogy az IoT hub-ban regisztrálta az eszköz azonosítóját, készen áll a IoT Edge futtatókörnyezet telepítésére az eszközökön. Telepítse a futtatókörnyezetet az eszköz operációs rendszere szerint:

* [Azure IoT Edge telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)
* [A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)
