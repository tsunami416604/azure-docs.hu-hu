---
title: BLOB Storage-események küldése webes végpontnak – sablon
description: A blob Storage-fiók létrehozásához használja a Azure Event Grid és egy Azure Resource Manager sablont, és fizessen elő eseményeket. Küldje el az eseményeket egy webhookba. "
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 86dc7a4ed05ceae5c7a641ffef23bd75ec48ceea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605549"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>BLOB Storage-események átirányítása a webes végpontra Azure Resource Manager sablon használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben egy **Azure Resource Manager sablonnal** hoz létre blob Storage-fiókot, előfizethet az adott blob-tároló eseményeire, és elindítja az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A Blob Storage-eseményekre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

    [Üzembe helyezés az Azure-ban](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Event Grid-előfizetéssel rendelkező Storage-fiók létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)származik.

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [**"Microsoft. Storage/storageAccounts/Providers/eventSubscriptions**](/azure/templates/microsoft.eventgrid/eventsubscriptions): hozzon létre egy Azure Event Grid-előfizetést a Storage-fiókhoz.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. A következő hivatkozásra kattintva jelentkezzen be az Azure-ba, és nyisson meg egy sablont. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Adja meg a **végpontot**: adja meg a webalkalmazás URL- `api/updates` címét, és adja hozzá a Kezdőlap URL-címét.
3. Válassza a **vásárlás** lehetőséget a sablon telepítéséhez.

  A Azure Portal a sablon üzembe helyezéséhez használja a rendszer. Használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)találhat további Azure Event Grid sablon mintákat.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

![Előfizetési esemény megtekintése](./media/blob-event-quickstart-portal/view-subscription-event.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

A Blob Storage-hoz egy eseményt egy fájl feltöltésével aktiválhat. A fájlnak nem kell tartalommal rendelkeznie. A cikkek feltételezik, hogy van egy testfile.txt fájlja, de bármilyen fájlt használhat.

Amikor feltölti a fájlt az Azure Blob Storage-ba, Event Grid üzenetet küld a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumú, és egy vagy több eseményből álló tömböt tartalmaz. A következő példában a JSON-üzenet egyetlen eseményt tartalmazó tömböt tartalmaz. Tekintse meg a webalkalmazását, és észreveheti, hogy az fogadott egy blob által létrehozott eseményt.

![Eredmények megtekintése](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [törölje az erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>További lépések

Azure Resource Manager-sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Azure Resource Manager dokumentáció](/azure/azure-resource-manager)
* [Erőforrások definiálása Azure Resource Manager-sablonokban](/azure/templates/)
* [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
