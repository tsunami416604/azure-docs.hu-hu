---
title: 'Oktatóanyag: horgonyok megosztása Azure Cosmos DB'
description: Ebből az oktatóanyagból megtudhatja, hogyan oszthatja meg az Azure térbeli horgonyok azonosítóit az Android/iOS-eszközökön a háttér-szolgáltatás és a Azure Cosmos DB segítségével.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ff431c27dd53e82f9003b658c82ceb3efb5d320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810020"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Azure térbeli horgonyok megosztása munkamenetek és eszközök között egy Azure Cosmos DB háttérrel

Ez az oktatóanyag az [Azure térbeli horgonyok munkamenetek és eszközök közötti megosztásának folytatása.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Végigvezeti Önt a folyamaton, amely néhány további képességet biztosít, hogy Azure Cosmos DB szolgáljon a háttérbeli tárolóként, miközben az Azure térbeli horgonyokat a munkamenetek és az eszközök között osztják meg.

![Az objektumok megőrzését bemutató GIF](./media/persistence.gif)

Érdemes megjegyezni, hogy bár a Unity és a Azure Cosmos DB is ebben az oktatóanyagban fog megjelenni, csupán egy példát mutat arra, hogyan oszthat meg térbeli horgonyokat az eszközök között. A felhasználó más nyelveket és háttér-technológiákat is elérhet ugyanezen cél eléréséhez.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Vegyen fel egy Azure Cosmos-adatbázist a korábban létrehozott erőforráscsoporthoz.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Másolja ki az-t `Connection String` , mert szüksége lesz rá.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>A SharingService-fájlok kisebb módosításának elvégzése

A **megoldáskezelő**megnyitásához nyissa meg a t `SharingService\Startup.cs` .

Keresse meg a `#define INMEMORY_DEMO` fájl elejét, és írja be a sort. Mentse a fájlt.

A **megoldáskezelő**megnyitásához nyissa meg a t `SharingService\appsettings.json` .

Keresse meg a `StorageConnectionString` tulajdonságot, és állítsa be úgy az értéket, hogy megegyezzen az `Connection String` [adatbázis-fiók létrehozása lépésben](#create-a-database-account)másolt értékkel. Mentse a fájlt.

Közzéteheti a megosztási szolgáltatást, és futtathatja a minta alkalmazást.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Cosmos DB használatával osztotta meg az egyes eszközökön a horgony azonosítóit. Ha többet szeretne megtudni arról, hogyan használhatók az Azure térbeli horgonyok egy új Unity HoloLens-alkalmazásban, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Új Unity HoloLens-alkalmazás indítása](./tutorial-new-unity-hololens-app.md)
