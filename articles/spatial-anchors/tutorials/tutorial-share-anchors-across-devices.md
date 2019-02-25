---
title: Oktatóanyag – a munkamenetek és eszközök az Azure térbeli horgonyok megosztása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan oszthat meg Azure térbeli Forráshorgony azonosítók egy háttér-szolgáltatással a Unity-nél eszközök között.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752140"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Oktatóanyag: Munkamenetek és eszközök az Azure térbeli horgonyok megosztása

Ez az oktatóanyag bemutatja, hogyan használható [Azure térbeli horgonyok](../overview.md) való:

1. Egy munkamenet horgonyokat létrehozni, és keresse meg azokat az azonos vagy eltérő eszközön egy másik munkamenetben. Például az egy másik napot.
2. Létrehozni, amely egyszerre több eszközök ugyanazon a helyen található.

![Adatmegőrzés](./media/persistence.gif)

Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, lesz egy alkalmazást, amely két vagy több eszközhöz is telepíthető. Egy példány által létrehozott Azure térbeli horgonyok a másokkal is megoszthatók.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Telepítse az ASP.NET Core-webalkalmazás az Azure-ban, amely a központi jellegűek, megosztására használható egy időtartam, a memóriában tárolja őket.
> * Konfigurálja a AzureSpatialAnchorsLocalSharedDemo jelenet belül a Unity-mintát, a kihasználásához a megosztás horgonyok webalkalmazás Gyorsútmutatókat.
> * Üzembe helyezése, és a egy vagy több eszközhöz történő futtatása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Érdemes okainak, bár fogja használni az ASP.NET Core-webalkalmazás és a Unity ebben az oktatóanyagban, azaz csak megjeleníthető például az Azure térbeli Forráshorgony azonosítók megosztása más eszközök között. Lehetőség van felhasználó más nyelv és a egy cél elérése érdekében a háttér-technológiákkal. Ezenkívül az ASP.NET Core-webalkalmazást a jelen oktatóanyagban használt maga .NET Core SDK-t 2.2-es. Fut megfelelően az Azure Web Apps szolgáltatásban rendszeres (Windows), de jelenleg nem fog működni az Azure Web Apps for Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="deploy-your-sharing-anchors-service"></a>A megosztási horgonyok szolgáltatás üzembe helyezése

Nyissa meg a Visual Studiót, és nyissa meg a projekt a `Sharing\SharingServiceSample` mappát.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az ASP.NET Core-webalkalmazás Azure-ban üzembe helyezett, és ezután konfigurálását és a Unity-alkalmazás üzembe helyezését. Térbeli horgonyok létrehozott-e az alkalmazással, és megosztva azokat más eszközök használatával az ASP.NET Core-webalkalmazás. Az ASP.NET Core-webalkalmazás fejlesztéséhez, így a Cosmos DB használatával tárolja a megosztott térbeli horgonyok kapcsolatos további információkért folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: A Cosmos DB Store horgonyokra mutató használata](./tutorial-use-cosmos-db-to-store-anchors.md)
