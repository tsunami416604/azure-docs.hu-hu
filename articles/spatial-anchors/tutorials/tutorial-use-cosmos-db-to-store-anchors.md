---
title: Oktatóanyag – a munkamenetek és eszközök az Azure térbeli horgonyok és a egy Azure Cosmos DB-háttérrendszer megosztása |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan Azure térbeli horgonyok azonosítók megosztása egy háttér-Service-szel és az Azure Cosmos DB a Unity Android vagy iOS-eszközök.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286397"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Oktatóanyag: Munkamenetek és eszközök az Azure térbeli horgonyok és a egy Azure Cosmos DB-háttérrendszer megosztása

Ebben az oktatóanyagban, megtudhatja, hogyan használandó [Azure térbeli horgonyok](../overview.md) való:

- Horgonyokat létrehozni egy munkamenet során, és keresse meg azokat a munkamenetben egy másik, ugyanarra az eszközre, illetve egy másikat. A második munkamenet Előfordulhat például, egy másik napon.
- Létrehozni, amely ugyanazon a helyen, és egyszerre több eszközön is elhelyezhetők.

![GIF szemléltető objektum adatmegőrzés](./media/persistence.gif)

[Az Azure térbeli horgonyok](../overview.md) platformfüggetlen fejlesztői szolgáltatás, amely segítségével hozzon létre vegyes valóság tapasztalatok objektumok helyükre kivonatuk eszközök idővel. Ha elkészült, lesz egy alkalmazást, amely két vagy több eszközhöz is telepíthető. Egy példány által létrehozott térbeli horgonyok fog másokkal a azonosítókról az Azure Cosmos DB használatával.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Telepítse az ASP.NET Core-webalkalmazás az Azure-ban, amely a központi jellegűek, megosztására használható az Azure Cosmos DB tárolja őket.
> * Adja meg a AzureSpatialAnchorsLocalSharedDemo jelenet kihasználásához a megosztás horgonyok webalkalmazást az Azure-gyorssablonok a Unity minta.
> * Egy vagy több eszköz telepítenek egy alkalmazást, és futtathatja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes megjegyezni, hogy, bár fogja használni az Azure Cosmos DB és a Unity ebben az oktatóanyagban, azaz csak, amelyek különböző eszközökön térbeli horgonyok azonosítók megosztása egy példát. Lehetőség van felhasználó más nyelv és a egy cél elérése érdekében a háttér-technológiákkal. Az ASP.NET Core-webalkalmazás a jelen oktatóanyagban használt is, a .NET Core SDK 2.2-es van szükség. Rendben fut a Web Apps for Windows, de nem Linux rendszeren jelenleg futnak webalkalmazásokat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Másolás a `Connection String` mert, szüksége lesz rá.

## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

Nyissa meg a Visual Studióban, és a nyissa meg a projektre a a `Sharing\SharingServiceSample` mappát.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurálja a szolgáltatást, hogy az Azure Cosmos DB-adatbázist használja

A **Megoldáskezelőben**, nyissa meg `SharingService\Startup.cs`.

Keresse meg `#define INMEMORY_DEMO` a fájl- és megjegyzés, amely ki sor elején. Mentse a fájlt.

A **Megoldáskezelőben**, nyissa meg `SharingService\appsettings.json`.

Keresse meg a `StorageConnectionString` tulajdonságot, és adja meg kell egyeznie a `Connection String` érték, amely a másolt a [hozzon létre egy adatbázis-fiók lépés](#create-a-database-account). Mentse a fájlt.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Cosmos DB felhasználta megosztani a forráshorgony azonosítók különböző eszközökön. Az Azure térbeli horgonyok kódtárral kapcsolatos további tudnivalókért továbbra is az útmutató, hogyan hozhat létre, és keresse meg a központi jellegűek.

> [!div class="nextstepaction"]
> [Hozzon létre, és keresse meg a horgonyok Azure térbeli horgonyok használatával](../create-locate-anchors-overview.md)
