---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 0c43c6dcced94225e9ab9ae903535ce74286ad9a
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406714"
---
## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ebben az oktatóanyagban az Azure IoT Tools bővítmény használatával hoz létre egy modult, és létrehoz egy **tároló rendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.

A tároló-lemezképek tárolására bármelyik Docker-kompatibilis beállításjegyzéket használhatja. Két népszerű Docker-beállításjegyzék-szolgáltatás [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja.

Ha még nem rendelkezik tároló-beállításjegyzékkel, kövesse az alábbi lépéseket egy új Azure-beli létrehozásához:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Termékváltozat | Válassza az **Alapszintű** lehetőséget. |

3. Kattintson a **Létrehozás** gombra.

4. Miután létrehozta a tároló-beállításjegyzéket, tallózással keresse meg, majd a bal oldali panelen válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások**területen található menüből. 

5. Ide kattintva engedélyezheti a rendszergazda felhasználó számára a tároló-beállításjegyzék **felhasználónevének** és **jelszavának** megtekintését.

6. Másolja a **bejelentkezési kiszolgáló**, a **Felhasználónév**és a **jelszó** értékeit, és mentse őket kényelmesen. Ezeket az értékeket az oktatóanyag során a tároló-beállításjegyzékhez való hozzáférés biztosításához használja.

   ![A tároló-beállításjegyzék bejelentkezési kiszolgálójának, felhasználónevének és jelszavának másolása](./media/iot-edge-create-container-registry/registry-access-key.png)