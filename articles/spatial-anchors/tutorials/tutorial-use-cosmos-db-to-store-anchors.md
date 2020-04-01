---
title: 'Oktatóanyag: Horgonyok megosztása az Azure Cosmos DB-vel'
description: Ebben az oktatóanyagban megtudhatja, hogyan oszthatja meg az Azure Spatial Anchors-azonosítókat androidos/iOS-eszközökön unity ben egy háttérszolgáltatással és az Azure Cosmos DB-vel.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615153"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Az Azure Spatial Anchors megosztása munkamenetek és eszközök között egy Azure Cosmos DB háttérprogrammal

Ez az oktatóanyag az [Azure Spatial Anchors munkamenetek és eszközök közötti megosztásának folytatása.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Ez végigvezeti a folyamaton, hogy még néhány képesség hozzáadásával az Azure Cosmos DB szolgáljon a háttér-tároló, miközben az Azure térbeli horgonyok megosztása a munkamenetek és eszközök között.

![GIF szemléltető objektumperzió](./media/persistence.gif)

Érdemes megjegyezni, hogy bár a Unity és az Azure Cosmos DB ebben az oktatóanyagban a Unity és az Azure Cosmos DB használatával fog részt venni, csak egy példa arra, hogyan oszthatja meg a térbeli horgonyok azonosítóit az eszközök között. Ugyanannak a célnak a elérése érdekében más nyelveket és háttértechnológiákat is használhat. Emellett az oktatóanyagban használt ASP.NET Core webalkalmazáshoz a .NET Core 2.2 SDK szükséges. Jól fut a Windows webalkalmazásokon, de jelenleg nem fut a Linux-webalkalmazásokon.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Adjon hozzá egy Azure Cosmos-adatbázist a korábban létrehozott erőforráscsoporthoz.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Másolja `Connection String` a, mert szüksége lesz rá.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>A SharingService-fájlok kisebb módosítása

A **Megoldáskezelőben**nyissa meg a programot. `SharingService\Startup.cs`

Keresse `#define INMEMORY_DEMO` meg a fájl tetején, és megjegyzést, hogy a sort ki. Mentse a fájlt.

A **Megoldáskezelőben**nyissa meg a programot. `SharingService\appsettings.json`

Keresse `StorageConnectionString` meg a tulajdonságot, és állítsa `Connection String` be, hogy az érték megegyezik az [adatbázisfiók létrehozása lépésben](#create-a-database-account)másolt értékkel. Mentse a fájlt.

A megosztási szolgáltatást újra közzéteheti, és futtathatja a mintaalkalmazást.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unity-20193"></a>Egység 2019.3

A változások megszakadása miatt a Unity 2019.3 jelenleg nem támogatott. Kérjük, használja a Unity 2019.1 vagy 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Cosmos DB használatával osztotta meg a horgonyazonosítókat az eszközök között. Ha többet szeretne megtudni arról, hogyan használhatja az Azure Spatial Anchors alkalmazást egy új Unity HoloLens alkalmazásban, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Új Unity HoloLens-alkalmazás indítása](./tutorial-new-unity-hololens-app.md)
