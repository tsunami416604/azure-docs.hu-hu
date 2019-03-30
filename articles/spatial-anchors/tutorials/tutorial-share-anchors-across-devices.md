---
title: Oktatóanyag – megosztás Azure térbeli horgonyok munkamenetek és eszközök |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan oszthat meg Azure térbeli Forráshorgony azonosítók között egy háttér-szolgáltatással a Unity Android vagy iOS-eszközök.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629308"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Oktatóanyag: Azure térbeli horgonyok megosztása a munkamenetek és eszközök

Ebben az oktatóanyagban, megtudhatja, hogyan használandó [Azure térbeli horgonyok](../overview.md) horgonyokat létrehozni egy munkamenet során, és keresse meg őket, vagy egy másik ugyanazon az eszközön. Ezek azonos kapcsolati alapokat is ugyanazon a helyen, és egyszerre több eszközön található.

![Adatmegőrzés](./media/persistence.gif)

Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, lesz egy alkalmazást, amely két vagy több eszközhöz is telepíthető. Egy példány által létrehozott Azure térbeli horgonyok a másokkal is megoszthatók.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Telepítse az ASP.NET Core-webalkalmazás az Azure-ban, amely a központi jellegűek, megosztására használható egy időtartam, a memóriában tárolja őket.
> * Konfigurálja a AzureSpatialAnchorsLocalSharedDemo jelenet belül a Unity-mintát, a kihasználásához a megosztás horgonyok webalkalmazás Gyorsútmutatókat.
> * Üzembe helyezése, és a egy vagy több eszközhöz történő futtatása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes okainak, bár fogja használni az ASP.NET Core-webalkalmazás és a Unity ebben az oktatóanyagban, azaz csak megjeleníthető például az Azure térbeli Forráshorgony azonosítók megosztása más eszközök között. Használhatja más nyelv és a háttér-technológiákkal cél elérése érdekében. Ezenkívül az ASP.NET Core-webalkalmazást a jelen oktatóanyagban használt maga .NET Core SDK-t 2.2-es. Fut megfelelően az Azure Web Apps szolgáltatásban rendszeres (Windows), de jelenleg nem fog működni az Azure Web Apps for Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

Nyissa meg a Visual Studiót, és nyissa meg a projekt a `Sharing\SharingServiceSample` mappát.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az ASP.NET Core-webalkalmazás Azure-ban üzembe helyezett, és ezután konfigurálását és a Unity-alkalmazás üzembe helyezését. Térbeli horgonyok létrehozott-e az alkalmazással, és megosztva azokat más eszközök használatával az ASP.NET Core-webalkalmazás.

Az ASP.NET Core-webalkalmazás fejlesztéséhez, hogy az Azure Cosmos DB tárolja a megosztott térbeli Forráshorgony azonosítók kapcsolatos további információkért folytassa a következő oktatóanyaggal. Az Azure Cosmos DB megőrzése lehetőséget biztosít az ASP.NET Core-webalkalmazás. Ennek során tehát lehetővé teszi az alkalmazás horgonyra már ma létrehozhatja első, és térjen vissza később nap találja meg ismét a horgony azonosítója a webalkalmazásban tárolt használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Cosmos DB Store horgonyokra mutató használata](./tutorial-use-cosmos-db-to-store-anchors.md)
