---
title: 'Oktatóanyag: horgonyok megosztása a munkamenetek és az eszközök között'
description: Ebből az oktatóanyagból megtudhatja, hogyan oszthatja meg az Azure térbeli horgony azonosítóit az Android/iOS-eszközök között egy háttér-szolgáltatással rendelkező egységben.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 440d8af17bccaf8d3fcb92f65e5d91ed969aec31
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971321"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Oktatóanyag: térbeli horgonyok megosztása a munkamenetek és az eszközök között

Az Azure térbeli horgonyok egy platformfüggetlen fejlesztői szolgáltatás, amellyel vegyes valóságú élmények hozhatók létre olyan objektumok használatával, amelyek a helyükön maradnak az eszközökön az idő múlásával. 

Ebben az oktatóanyagban az [Azure térbeli horgonyait](../overview.md) használva horgonyokat hozhat létre egy munkamenet során, majd megkeresheti őket ugyanazon az eszközön vagy egy másikon. Ugyanazokat a horgonyokat több eszköz is megtalálhatja ugyanazon a helyen és egyszerre.

![A mobileszköz használatával létrehozott és a nap folyamán egy másik eszközzel használt térbeli horgonyokat bemutató animáció.](./media/persistence.gif)


Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Helyezzen üzembe egy ASP.NET Core webalkalmazást az Azure-ban, amellyel megoszthatja a horgonyokat, és a memóriában tárolhatja a horgonyokat egy adott időszakra vonatkozóan.
> * A gyors üzembe helyezési pontokon belül konfigurálja a AzureSpatialAnchorsLocalSharedDemo jelenetet, hogy kihasználhassa a megosztási horgonyok webalkalmazás előnyeit.
> * A horgonyokat üzembe helyezheti és futtathatja egy vagy több eszközön.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> Ebben az oktatóanyagban egy Unity és egy ASP.NET Core webalkalmazást fog használni, de az itt látható megközelítés csak azt mutatja be, hogyan oszthat meg más eszközökön az Azure térbeli horgonyok azonosítóit. Ugyanezen cél elérése érdekében más nyelveket és háttér-technológiákat is használhat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>A minta projekt letöltése

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Nyissa meg a Visual studiót, majd nyissa meg a projektet a *Sharing\SharingServiceSample* mappában.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Létre kell hoznia egy erőforráscsoportot és egy App Service tervet, mielőtt telepítené a szolgáltatást a Visual Studio Code-ban.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Lépjen a <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>, majd jelentkezzen be az Azure-előfizetésbe.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el az erőforráscsoport **myResourceGroup**, majd kattintson **az OK gombra**.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

A **üzemeltetési terv konfigurálása** panelen használja az alábbi beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service terv neve |
| Hely | USA nyugati régiója | Az az adatközpont, amelyen a webalkalmazás fut |
| Méret | Ingyenes | Az üzemeltetési funkciókat meghatározó [díjszabási](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) csomag |

Válassza az **OK** lehetőséget.

Nyissa meg a Visual Studio Code-ot, majd nyissa meg a projektet a *Sharing\SharingServiceSample* mappában. 

A megosztási szolgáltatás Visual Studio code-on keresztüli üzembe helyezéséhez kövesse az <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">ASP.net Core alkalmazás közzététele az Azure-ban Visual Studio Code</a>-ban című témakör utasításait. Kezdje a "Megnyitás a Visual Studio Code-ban" című szakaszban. Ne hozzon létre egy másik ASP.NET-projektet az előző lépésben leírtak szerint, mert már van egy üzembe helyezett és közzétett projekt: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>A minta alkalmazás üzembe helyezése

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy ASP.NET Core webalkalmazást az Azure-ban, és egy Unity-alkalmazást konfigurált és telepített. A térbeli horgonyok az alkalmazással lettek létrehozva, és a ASP.NET Core webalkalmazás használatával megosztva más eszközökkel.

Javíthatja ASP.NET Core webalkalmazását úgy, hogy az Azure Cosmos DB használatával megtartja a megosztott térbeli horgonyok azonosítóinak tárolóját. Azure Cosmos DB támogatás hozzáadásával a ASP.NET Core webalkalmazás létrehoz egy horgonyt még ma. Ezt követően a webalkalmazásban tárolt Anchor-azonosító használatával később is visszatérhet az alkalmazás, hogy újra megkeresse a horgonyt.

> [!div class="nextstepaction"]
> [A Azure Cosmos DB használata a horgonyok tárolásához](./tutorial-use-cosmos-db-to-store-anchors.md)

