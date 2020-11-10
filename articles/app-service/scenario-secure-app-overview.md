---
title: Oktatóanyag – biztonságos Webalkalmazás létrehozása Azure App Serviceon | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre webalkalmazásokat a Azure App Service használatával, engedélyezheti a hitelesítést, meghívja az Azure Storage-t, és meghívja a Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428877"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Oktatóanyag: hitelesítés engedélyezése App Service-és hozzáférés-tárolóban és Microsoft Graph

Ez az oktatóanyag egy általános alkalmazási forgatókönyvet ismertet, amely a következőket ismerteti:

- [(A) konfigurálja a webalkalmazások hitelesítését](scenario-secure-app-authentication-app-service.md) , és korlátozza a hozzáférést a szervezet felhasználóihoz.
- (B) a webalkalmazás nevében [biztonságosan férhet hozzá az Azure Storage](scenario-secure-app-access-storage.md) szolgáltatáshoz a felügyelt identitások használatával.
- (C) a Microsoft Graph a [bejelentkezett felhasználó](scenario-secure-app-access-microsoft-graph-as-user.md) nevében vagy a [webalkalmazás](scenario-secure-app-access-microsoft-graph-as-app.md) nevében, felügyelt identitások használatával férhetnek hozzá.
- [Törölje az](scenario-secure-app-clean-up-resources.md) oktatóanyaghoz létrehozott erőforrásokat.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Alkalmazás-forgatókönyvek a Microsoft Identity platformon" border="false":::

A kezdéshez megtudhatja, hogyan engedélyezheti a hitelesítést egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Webes alkalmazás hitelesítésének konfigurálása](scenario-secure-app-authentication-app-service.md)
