---
title: Új Azure IoT Edge-eszköz regisztrálása | Microsoft dokumentumok
description: Az Azure CLI IoT-bővítményének használatával regisztrálhat egy új IoT Edge-eszközt, és lekérheti a kapcsolati karakterláncot
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235722"
---
# <a name="register-an-azure-iot-edge-device"></a>Azure IoT Edge-eszköz regisztrálása

Mielőtt az IoT-eszközöket az Azure IoT Edge-el használhatja, regisztrálnia kell őket az IoT hubon. Az eszköz regisztrálása után lekérheti a kapcsolati karakterláncot az IoT Edge-munkaterhelések beállításához.

A regisztráció az alábbi eszközök egyikével választhat:

* [Regisztráljon egy eszközt az Azure Portalon,](#register-in-the-azure-portal) ha egy grafikus felhasználói felületet szeretne létrehozni, megtekinteni és kezelni az Azure-erőforrásoklétrehozásához, megtekintéséhez és kezeléséhez.
* [Regisztráljon egy eszközt a Visual Studio-kóddal,](#register-with-visual-studio-code) ha az Azure IoT-erőforrásokat ugyanazon a helyen szeretné kezelni, ahol az IoT-megoldásokat fejleszti.
* [Regisztráljon egy eszközt az Azure CLI-vel,](#register-with-the-azure-cli) ha az Azure-erőforrások kezeléséhez parancssori eszközöket szeretne, vagy szeretné automatizálni a feladatokat.

## <a name="register-in-the-azure-portal"></a>Regisztráció az Azure portalon

Az Azure Portalon az összes regisztrációs feladatot elvégezheti.

### <a name="prerequisites-for-the-azure-portal"></a>Az Azure Portal előfeltételei

Ingyenes vagy szabványos [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>IoT Edge-eszköz létrehozása az Azure Portalon

Az Azure Portalon az IoT Hubban az IoT Edge-eszközök külön jönnek létre és kezelhetők az IOT-eszközöktől, amelyek nincsenek elérhetők.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.
2. A bal oldali ablaktáblában válassza a menü **IoT Edge parancsát.**
3. Válassza **az IoT Edge-eszköz hozzáadása lehetőséget.**
4. Adjon meg egy leíró eszközazonosítót. Az alapértelmezett beállításokkal automatikusan generálhatja a hitelesítési kulcsokat, és csatlakoztathatja az új eszközt a központi eszközhöz.
5. Kattintson a **Mentés** gombra.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge-eszközök megtekintése az Azure Portalon

Az IoT-központhoz csatlakozó összes peremhálózati eszköz megjelenik az **IoT Edge** oldalon.

![Az IoT Edge-eszközök megtekintése az IoT hubban](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>A kapcsolati karakterlánc lekérése az Azure Portalon

Amikor készen áll az eszköz beállítására, szüksége van a kapcsolati karakterláncra, amely összeköti a fizikai eszközt az IoT hubidentitásával.

1. A portál **IoT Edge** lapján kattintson az eszközazonosítóra az IoT Edge-eszközök listájából.
2. Másolja az **elsődleges kapcsolati karakterlánc** vagy a másodlagos **kapcsolati karakterlánc**értékét.

## <a name="register-with-visual-studio-code"></a>Regisztráció a Visual Studio kódjával

A VS-kód ban a legtöbb művelet et többféleképpen is elvégezheti. Ez a cikk az Intézőt használja, de a parancspaletta segítségével is futtathatja a lépéseket.

### <a name="prerequisites-for-visual-studio-code"></a>A Visual Studio-kód előfeltételei

* [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében
* [Visual Studio kód](https://code.visualstudio.com/)
* [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio-kódhoz

### <a name="sign-in-to-access-your-iot-hub"></a>Bejelentkezés az IoT-központ eléréséhez

Az Azure IoT-bővítmények a Visual Studio-kód műveletek et az IoT Hub műveletek végrehajtásához. Ahhoz, hogy ezek a műveletek működjenek, be kell jelentkeznie az Azure-fiókjába, és ki kell választania az IoT Hubot.

1. A Visual Studio-kódban nyissa meg az **Intéző** nézetet.
1. Az Intéző alján bontsa ki az **Azure IoT Hub** szakaszt.

   ![Az Azure IoT Hub-eszközök szakasz bővítése](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kattintson a **...** az **Azure IoT Hub** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy mutasson a fejlécre, vagy mutasson rá.
1. Válassza **az IoT-központ kiválasztása**lehetőséget.
1. Ha nincs bejelentkezve az Azure-fiókjába, kövesse az utasításokat.
1. Válassza ki az Azure-előfizetését.
1. Válassza ki az IoT-központot.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>IoT Edge-eszköz létrehozása a Visual Studio-kóddal

1. A VS Code Explorer bontsa ki az **Azure IoT Hub-eszközök** szakaszban.
1. Kattintson a **...** az **Azure IoT Hub devices** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy mutasson a fejlécre, vagy mutasson rá.
1. Válassza **az IoT Edge-eszköz létrehozása lehetőséget.**
1. A megnyíló szövegmezőben adjon azonosítót az eszköznek.

A kimeneti képernyőn megjelenik a parancs eredménye. Az eszköz adatok nyomtatása, amely tartalmazza a megadott **eszközazonosítót,** és a **connectionString,** amely segítségével csatlakoztathatja a fizikai eszköz az IoT hub.

A kimeneti képernyőn megjelenik a parancs eredménye. Az eszköz adatok nyomtatása, amely tartalmazza a megadott **eszközazonosítót,** és a **connectionString,** amely segítségével csatlakoztathatja a fizikai eszköz az IoT hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>IoT Edge-eszközök megtekintése a Visual Studio-kóddal

Az IoT-központhoz csatlakozó összes eszköz a Visual Studio Code Explorer **Azure IoT** Hub-szakaszában található. Az IoT Edge-eszközök megkülönböztethetők a nem peremhálózati eszközöktől egy másik ikonnal, és az a tény, hogy a **$edgeAgent** és **$edgeHub** modulok minden IoT Edge-eszközre telepítve vannak.

![Az IoT Edge-eszközök megtekintése az IoT hubban](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>A kapcsolati karakterlánc beolvasása a Visual Studio-kóddal

Amikor készen áll az eszköz beállítására, szüksége van a kapcsolati karakterláncra, amely összeköti a fizikai eszközt az IoT hubidentitásával.

1. Kattintson a jobb gombbal az eszköz azonosítójára az **Azure IoT Hub** szakaszban.
1. Válassza **az Eszközkapcsolati karakterlánc másolása lehetőséget**.

   A kapcsolati karakterlánc átmásolva lesz a vágólapra.

A jobb gombbal elérhető menü **Eszközadatok beszerezése** parancsát is választhatja az összes eszközinformáció megtekintéséhez, beleértve a kapcsolati karakterláncot is a kimeneti ablakban.

## <a name="register-with-the-azure-cli"></a>Regisztráció az Azure CLI-vel

Az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, platformfüggetlen parancssori eszköz az Azure-erőforrások, például az IoT Edge kezelésére. Lehetővé teszi az Azure IoT Hub-erőforrások, az eszközkiépítési szolgáltatáspéldányok és a csatolt hubok kezelését a dobozból. Az IoT-bővítmény olyan funkciókkal gazdagítja az Azure CLI-t, mint az eszközkezelés és a teljes IoT Edge-képesség.

### <a name="prerequisites-for-the-azure-cli"></a>Az Azure CLI előfeltételei

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI IoT-bővítménye.](https://github.com/Azure/azure-iot-cli-extension)

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>IoT Edge-eszköz létrehozása az Azure CLI-vel

Az [aziot hub-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) paranccsal új eszközidentitást hozhat létre az IoT-központban. Példa:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétert tartalmaz:

* **device-id:** Adjon meg egy leíró nevet, amely egyedi az IoT hub.
* **hub-name**: Adja meg az IoT hub nevét.
* **edge-enabled**: Ez a paraméter deklarálja, hogy az eszköz az IoT Edge-hez használható.

   ![az iot hub eszközidentitás létrehozása kimenet](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge-eszközök megtekintése az Azure CLI-vel

Az [az iot hub-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) paranccsal megtekintheti az IoT hub összes eszközét. Példa:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Minden olyan eszköz, amely IoT Edge-eszközként van regisztrálva, a **capabilities.iotEdge** tulajdonság **értéke true**lesz.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>A kapcsolati karakterlánc lekérése az Azure CLI-vel

Amikor készen áll az eszköz beállítására, szüksége van a kapcsolati karakterláncra, amely összeköti a fizikai eszközt az IoT hubidentitásával. Az [az iot hub-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) paranccsal adja vissza egyetlen eszköz kapcsolati karakterláncát:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

A paraméter `device-id` értéke a kis- és nagybetűk et is figyelembe véve. Ne másolja az idézőjeleket a kapcsolati karakterlánc köré.

## <a name="next-steps"></a>További lépések

Most, hogy regisztrált egy eszközidentitást az IoT hubon, készen áll az IoT Edge futásidejű telepítésére az eszközökre. Telepítse a futásidőt az eszköz operációs rendszerének megfelelően:

* [Az Azure IoT Edge telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)
* [Az Azure IoT Edge futásidejű telepítésélinuxra](how-to-install-iot-edge-linux.md)
