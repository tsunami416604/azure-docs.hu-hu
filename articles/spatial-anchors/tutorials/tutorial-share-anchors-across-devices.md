---
title: 'Oktatóanyag: horgonyok megosztása a munkamenetek és az eszközök között'
description: Ebből az oktatóanyagból megtudhatja, hogyan oszthatja meg az Azure térbeli horgony azonosítóit az Android/iOS-eszközök között egy háttér-szolgáltatással rendelkező egységben.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0029fcf8e9efdea529212a7cca49cc8660c623f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276965"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Oktatóanyag: Azure térbeli horgonyok megosztása a munkamenetek és az eszközök között

Ebből az oktatóanyagból megtudhatja, hogyan használhatók az [Azure térbeli horgonyok](../overview.md) egy adott munkamenetben, majd megkereshetik őket ugyanazon az eszközön vagy egy másikon. Ugyanezeket a horgonyokat több eszköz is elhelyezheti ugyanazon a helyen és egyidejűleg.

![Kitartás](./media/persistence.gif)

Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan alkalmazással fog rendelkezni, amely két vagy több eszközre is telepíthető. Az egyik példány által létrehozott Azure térbeli horgonyok megoszthatók másokkal.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Helyezzen üzembe egy ASP.NET Core webalkalmazást az Azure-ban, amellyel megoszthatja a horgonyokat, és tárolhatja őket a memóriában egy ideig.
> * A gyors üzembe helyezési pontokon belül konfigurálja a AzureSpatialAnchorsLocalSharedDemo jelenetet, hogy kihasználhassa a megosztási horgonyok webalkalmazás előnyeit.
> * Egy vagy több eszköz üzembe helyezése és futtatása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes megjegyezni, hogy bár ebben az oktatóanyagban az Unity és egy ASP.NET Core webalkalmazást használ, csak egy példát mutat be, hogy miként oszthat meg Azure térbeli horgony-azonosítókat más eszközökön. Ugyanezen cél elérése érdekében más nyelveket és háttér-technológiákat is használhat. Az oktatóanyagban használt ASP.NET Core webalkalmazás a .NET Core 2,2 SDK-val való függőséggel is rendelkezik. Normál Azure-Web Apps (Windows rendszeren) fut, de jelenleg nem fog működni az Azure Web Apps Linux rendszeren.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>A minta projekt letöltése

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Nyissa meg a Visual studiót, és nyissa meg a projektet a `Sharing\SharingServiceSample` mappában.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

A szolgáltatás a VS Code-ban való üzembe helyezése előtt létre kell hoznia egy erőforráscsoportot és egy App Service tervet.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Navigáljon a <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a> , és jelentkezzen be az Azure-előfizetésbe.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet.

A **üzemeltetési terv konfigurálása** párbeszédpanelen használja a következő beállításokat:

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| MySharingServicePlan | Az App Service-csomag neve. |
| Hely | USA nyugati régiója | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | Az üzemeltetési funkciókat meghatározó [díjszabási](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) csomag. |

Kattintson az **OK** gombra.

Nyissa meg a Visual Studio Code-ot, és nyissa meg a projektet a `Sharing\SharingServiceSample` mappában. <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">Ezt az oktatóanyagot</a> követve üzembe helyezheti a megosztási szolgáltatást a Visual Studio Code használatával. A "Megnyitás a Visual Studio Code-ban" című szakaszban leírtak szerint járjon el. Ne hozzon létre egy másik MVC-projektet a fenti lépésben leírtak szerint, mivel már rendelkezik az üzembe helyezni és közzétenni kívánt projekttel – a SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>A minta alkalmazás üzembe helyezése

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy ASP.NET Core webalkalmazást az Azure-ban, majd konfigurálta és telepítette az Unity alkalmazást. A térbeli horgonyok az alkalmazással lettek létrehozva, és más eszközökkel lettek megosztva a ASP.NET Core webalkalmazás használatával.

Javíthatja ASP.NET Core webalkalmazását úgy, hogy az Azure Cosmos DB használatával megtartja a megosztott térbeli horgony azonosítóinak tárolóját. Azure Cosmos DB támogatás hozzáadásával lehetővé válik, hogy a ASP.NET Core webalkalmazás egy horgonyt hozzon létre még ma, és térjen vissza később a webalkalmazásban tárolt Anchor-azonosító használatával.

> [!div class="nextstepaction"]
> [A horgonyok tárolása az Azure Cosmo DB használatával](./tutorial-use-cosmos-db-to-store-anchors.md)

