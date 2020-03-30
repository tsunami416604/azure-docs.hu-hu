---
title: Blob-tárolási események küldése a webvégpontra – sablon
description: Az Azure Event Grid és egy Azure Resource Manager-sablon használatával blobtár-fiókot hozhat létre, és előfizethet az eseményekre. Küldje el az eseményeket egy Webhookba.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 343ed57c87ea6df5db4cde0978132af31419f905
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78303341"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Blob-tárolási események útvonala a webvégpontba az Azure Resource Manager-sablon használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben egy **Azure Resource Manager-sablon** használatával hozzon létre egy Blob storage-fiókot, előfizet az adott blobstorage-eseményekre, és eseményt indít az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A Blob Storage-eseményekre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

    [Üzembe helyezés az Azure-ban](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Tárfiók létrehozása Event Grid-előfizetéssel

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

A sablonban két Azure-erőforrás van definiálva:

* [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [**"Microsoft.Storage/storageAccounts/providers/eventSubscriptions**](/azure/templates/microsoft.eventgrid/eventsubscriptions): hozzon létre egy Azure Event Grid-előfizetést a tárfiókhoz.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi hivatkozást az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy key vault és egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json"><img src="./media/blob-event-quickstart-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Adja meg a **végpontot:** adja meg `api/updates` a webalkalmazás URL-címét, és adja hozzá a kezdőlap URL-címéhez.
3. A sablon üzembe helyezéséhez válassza a **Vásárlás** lehetőséget.

  Az Azure Portal itt a sablon üzembe helyezéséhez. Használhatja az Azure PowerShell, az Azure CLI és a REST API-t is. További telepítési módszerekről a [Sablonok telepítése ..](../azure-resource-manager/templates/deploy-powershell.md)

> [!NOTE]
> További Azure Event Grid-sablonmintákat [itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)talál.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

![Előfizetési esemény megtekintése](./media/blob-event-quickstart-portal/view-subscription-event.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

A Blob Storage-hoz egy eseményt egy fájl feltöltésével aktiválhat. A fájlnak nem kell tartalommal rendelkeznie. A cikkek feltételezik, hogy van egy testfile.txt fájlja, de bármilyen fájlt használhat.

Amikor feltölti a fájlt az Azure Blob storage,Event Grid üzenetet küld a végpont a beállított előfizetéskor. Az üzenet JSON formátumú, és egy vagy több eseményt tartalmazó tömböt tartalmaz. A következő példában a JSON-üzenet egy tömböt tartalmaz egy eseménnyel. Tekintse meg a webalkalmazását, és észreveheti, hogy az fogadott egy blob által létrehozott eseményt.

![Eredmények megtekintése](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [törölje az erőforráscsoportot.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
)

## <a name="next-steps"></a>További lépések

Az Azure Resource Manager-sablonokról az alábbi cikkekben olvashat bővebben:

* [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager)
* [Erőforrások definiálása az Azure Resource Manager-sablonokban](/azure/templates/)
* [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
