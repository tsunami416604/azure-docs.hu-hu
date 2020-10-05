---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 11b9c553573d9e6188ba634b4cb966d6a9b850b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947264"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Azure kommunikációs erőforrás létrehozása

Azure kommunikációs szolgáltatások erőforrásának létrehozásához először jelentkezzen be a [Azure Portalba](https://portal.azure.com). A lap bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Képernyőfelvétel: az erőforrás létrehozása gomb kiemelése a Azure Portalban.":::

Adja meg a **kommunikációt** a **piactér** vagy a portál tetején található keresési sávon.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Képernyőfelvétel: az erőforrás létrehozása gomb kiemelése a Azure Portalban.":::

Válassza az eredmények között a **kommunikációs szolgáltatások** elemet, majd kattintson a **Hozzáadás**gombra.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Képernyőfelvétel: az erőforrás létrehozása gomb kiemelése a Azure Portalban.":::

Most már konfigurálhatja a kommunikációs szolgáltatások erőforrását. A létrehozási folyamat első lapján a következőt kell megadnia:

* Az előfizetés
* Az erőforráscsoport (létrehozhat egy újat, vagy kiválaszthat egy meglévő erőforráscsoportot)
* A kommunikációs szolgáltatások erőforrásának neve
* Az a földrajzi hely, amelyhez az erőforrás társítva lesz

A következő lépésben címkéket rendelhet hozzá az erőforráshoz. Az Azure-erőforrások rendszerezéséhez címkéket is használhat. A címkékkel kapcsolatos további információkért tekintse meg az [erőforrás-címkézési dokumentációt](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources) .

Végezetül áttekintheti a konfigurációt, és **létrehozhatja** az erőforrást. Vegye figyelembe, hogy az üzembe helyezés eltarthat néhány percig.

## <a name="manage-your-communication-services-resource"></a>A kommunikációs szolgáltatások erőforrásának kezelése

A kommunikációs szolgáltatások erőforrásának kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg és válassza ki az **Azure kommunikációs szolgáltatásokat**.

A **kommunikációs szolgáltatások** lapon válassza ki az erőforrás nevét.

Az erőforrás **Áttekintés** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. Az erőforrás oldal bal oldali menüjében további konfigurációs beállításokat találhat.