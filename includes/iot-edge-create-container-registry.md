---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564730"
---
## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban az Azure IoT Tools bővítmény használatával hoz létre egy modult, és létrehoz egy **tároló rendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.

A tároló-lemezképek tárolására bármelyik Docker-kompatibilis beállításjegyzéket használhatja. Két népszerű Docker-beállításjegyzék-szolgáltatás [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja.

Ha még nem rendelkezik tároló-beállításjegyzékkel, kövesse az alábbi lépéseket egy új Azure-beli létrehozásához:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Földrajzi egység | Válassza ki az Önhöz legközelebb eső helyet. |
   | Rendszergazdai felhasználó | Állítsa **Engedélyezés** értékre. |
   | SKU (Cikkszám) | Válassza az **Alapszintű** lehetőséget. |

3. Kattintson a **Létrehozás** gombra.

4. Miután létrehozta a tároló-beállításjegyzéket, tallózással keresse meg, majd a bal oldali panelen válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások**területen található menüből.

5. Másolja a **bejelentkezési kiszolgáló**, a **Felhasználónév**és a **jelszó** értékeit, és mentse őket kényelmesen. Ezeket az értékeket az oktatóanyag során a tároló-beállításjegyzékhez való hozzáférés biztosításához használja.

   ![A tároló-beállításjegyzék bejelentkezési kiszolgálójának, felhasználónevének és jelszavának másolása](./media/iot-edge-create-container-registry/registry-access-key.png)