---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564730"
---
## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban az Azure IoT Tools-bővítmény használatával hozzon létre egy modult, és hozzon létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.

A Tárolórendszerképek tárolására bármely Docker-kompatibilis beállításjegyzéket használhat. Két népszerű Docker-beállításjegyzék-szolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub.](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) Ez az oktatóanyag az Azure Container Registryt használja.

Ha még nem rendelkezik tárolóbeállításjegyzékkel, az alábbi lépésekkel hozhat létre újat az Azure-ban:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Rendszergazdai felhasználó | Állítsa **Engedélyezés** értékre. |
   | SKU | Válassza az **Alapszintű** lehetőséget. |

3. Kattintson a **Létrehozás** gombra.

4. A tárolóbeállításjegyzék létrehozása után tallózással keresse meg, és a bal oldali ablaktáblából válassza a Beállítások menü **Hozzáférési kulcsok elemének Hozzáférési gombjait.** **Settings**

5. Másolja a **bejelentkezési kiszolgáló,** **a felhasználónév**és **a jelszó** értékeit, és mentse őket egy kényelmes helyre. Ezeket az értékeket használja az oktatóanyagban, hogy hozzáférést biztosítson a tároló beállításjegyzékéhez.

   ![Bejelentkezési kiszolgáló, felhasználónév és jelszó másolása a tárolóbeállításjegyzékhez](./media/iot-edge-create-container-registry/registry-access-key.png)