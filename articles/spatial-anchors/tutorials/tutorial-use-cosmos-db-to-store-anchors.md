---
title: Oktatóanyag – Azure térbeli horgonyok megosztása munkamenetek és eszközök között egy Azure Cosmos DB háttérrel | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan oszthatja meg az Azure térbeli horgonyok azonosítóit az Android/iOS-eszközökön a háttér-szolgáltatás és a Azure Cosmos DB segítségével.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882167"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Azure térbeli horgonyok megosztása munkamenetek és eszközök között egy Azure Cosmos DB háttérrel

Ez az oktatóanyag az [Azure térbeli horgonyok munkamenetek és eszközök közötti megosztásának folytatása.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Végigvezeti Önt a folyamaton, amely néhány további képességet biztosít, hogy Azure Cosmos DB szolgáljon a háttérbeli tárolóként, miközben az Azure térbeli horgonyokat a munkamenetek és az eszközök között osztják meg.

![Az objektumok megőrzését bemutató GIF](./media/persistence.gif)

Érdemes megjegyezni, hogy bár a Unity és a Azure Cosmos DB is ebben az oktatóanyagban fog megjelenni, csupán egy példát mutat arra, hogyan oszthat meg térbeli horgonyokat az eszközök között. A felhasználó más nyelveket és háttér-technológiákat is elérhet ugyanezen cél eléréséhez. Emellett az oktatóanyagban használt ASP.NET Core webalkalmazáshoz a .NET Core 2,2 SDK szükséges. A Windows Web Apps működik, de jelenleg nem fut Web Apps Linux rendszeren.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Vegyen fel egy Azure Cosmos-adatbázist a korábban létrehozott erőforráscsoporthoz. 

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Másolja a `Connection String`, mert szüksége lesz rá.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>A SharingService-fájlok kisebb módosításának elvégzése

**Megoldáskezelő**nyissa meg a `SharingService\Startup.cs`.

Keresse meg az `#define INMEMORY_DEMO` a fájl tetején, és írja be a megjegyzést. Mentse a fájlt.

**Megoldáskezelő**nyissa meg a `SharingService\appsettings.json`.

Keresse meg a `StorageConnectionString` tulajdonságot, és állítsa be az értéket úgy, hogy megegyezzen az [adatbázis-fiók létrehozása lépésben](#create-a-database-account)átmásolt `Connection String` értékkel. Mentse a fájlt.

Közzéteheti a megosztási szolgáltatást, és futtathatja a minta alkalmazást.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure Cosmos DB használatával osztotta meg az egyes eszközökön a horgony azonosítóit. Ha többet szeretne megtudni arról, hogyan használhatók az Azure térbeli horgonyok egy új Unity HoloLens-alkalmazásban, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Új Unity HoloLens-alkalmazás indítása](./tutorial-new-unity-hololens-app.md)
