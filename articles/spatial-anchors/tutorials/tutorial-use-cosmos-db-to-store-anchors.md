---
title: Oktatóanyag – a munkamenetek és az Azure térbeli horgonyok és a egy Azure Cosmos DB háttérbeli eszközzel megosztása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan Azure térbeli Forráshorgony azonosítók eszközök a Unity-nél egy háttér-szolgáltatással és az Azure Cosmos DB közötti megosztásához.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752137"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Munkamenetek és eszközök az Azure térbeli horgonyok és a egy Azure Cosmos DB háttér-megosztás

Ez az oktatóanyag bemutatja, hogyan használható [Azure térbeli horgonyok](../overview.md) való:

1. Egy munkamenet horgonyokat létrehozni, és keresse meg azokat az azonos vagy eltérő eszközön egy másik munkamenetben. Például az egy másik napot.
2. Létrehozni, amely egyszerre több eszközök ugyanazon a helyen található.

![Adatmegőrzés](./media/persistence.gif)

[Az Azure térbeli horgonyok](../overview.md) platformfüggetlen fejlesztői szolgáltatás lehetővé teszi, hogy hozzon létre a vegyes valóságon alapuló felhasználói élményt alakíthat ki eszközök idővel helyükre kivonatuk objektumok. Ha elkészült, lesz egy alkalmazást, amely két vagy több eszközhöz is telepíthető. Egy példány által létrehozott Azure térbeli horgonyok megosztja a azonosítókról másoknak a Cosmos DB használatával.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Telepítse az ASP.NET Core-webalkalmazás az Azure-ban, amelyek segítségével megoszthatja a központi jellegűek, Cosmos DB-ben tárolja őket.
> * Konfigurálja a AzureSpatialAnchorsLocalSharedDemo jelenet belül a Unity-mintát, a kihasználásához a megosztás horgonyok webalkalmazás Gyorsútmutatókat.
> * Üzembe helyezése, és a egy vagy több eszközhöz történő futtatása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes okainak, bár fogja használni az Azure Cosmos DB és a Unity ebben az oktatóanyagban, azaz csak megjeleníthető például az Azure térbeli Forráshorgony azonosítók megosztása más eszközök között. Lehetőség van felhasználó más nyelv és a egy cél elérése érdekében a háttér-technológiákkal. Ezenkívül az ASP.NET Core-webalkalmazást a jelen oktatóanyagban használt maga .NET Core SDK-t 2.2-es. Fut megfelelően az Azure Web Apps szolgáltatásban rendszeres (Windows), de jelenleg nem fog működni az Azure Web Apps for Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Jegyezze fel a `Connection String` módon lentebb használandó.

## <a name="deploy-your-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

Nyissa meg a Visual Studiót, és nyissa meg a projekt a `Sharing\SharingServiceSample` mappát.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Konfigurálja a szolgáltatást, hogy használja a Cosmos DB

Az a **Megoldáskezelőben**, nyissa meg `SharingService\Startup.cs`.

Keresse meg a `#define INMEMORY_DEMO` a fájl elején. sor, és hozzászólhat próbálhatja ki. Mentse a fájlt.

Az a **Megoldáskezelőben**, nyissa meg `SharingService\appsettings.json`.

Keresse meg a `StorageConnectionString` tulajdonságot, és állítsa be az lehet az `Connection String` , hogy a szóban tudomásul vette a [hozzon létre egy adatbázis-fiók lépés](#create-a-database-account). Mentse a fájlt.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Cosmos DB felhasználta megosztani a forráshorgony azonosítók különböző eszközökön. Az Azure térbeli horgonyok kódtárral kapcsolatos további tudnivalókért továbbra is az útmutató, hogyan hozhat létre, és keresse meg a központi jellegűek.

> [!div class="nextstepaction"]
> [Hozzon létre, és keresse meg a horgonyok Azure térbeli horgonyok használatával](../create-locate-anchors-overview.md)
