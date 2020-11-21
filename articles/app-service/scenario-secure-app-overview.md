---
title: Oktatóanyag – biztonságos Webalkalmazás létrehozása Azure App Serviceon | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre webalkalmazásokat Azure App Service használatával, hogyan engedélyezheti a hitelesítést, meghívja az Azure Storage-t, és meghívja a Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef7782c68746d4c20df5a9ae5e27ffca3e60efbe
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024126"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Oktatóanyag: hitelesítés engedélyezése App Service-és hozzáférés-tárolóban és Microsoft Graph

Ez az oktatóanyag egy általános alkalmazási forgatókönyvet ismertet, amelyben a következőket sajátíthatja el:

- [Konfigurálhatja a webalkalmazások hitelesítését](scenario-secure-app-authentication-app-service.md) , és korlátozhatja a felhasználók hozzáférését a szervezetben. Lásd: a diagramon.
- Felügyelt identitások használatával biztonságosan hozzáférhet a webalkalmazáshoz az [Azure Storage](scenario-secure-app-access-storage.md) szolgáltatásban. Lásd: B a diagramon.
- Hozzáférés Microsoft Graph a [bejelentkezett felhasználóhoz](scenario-secure-app-access-microsoft-graph-as-user.md) vagy [a webalkalmazáshoz](scenario-secure-app-access-microsoft-graph-as-app.md) a felügyelt identitások használatával. Lásd: C a diagramon.
- [Törölje az](scenario-secure-app-clean-up-resources.md) oktatóanyaghoz létrehozott erőforrásokat.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="A Microsoft Identity platform alkalmazási forgatókönyveit bemutató ábra." border="false":::

A kezdéshez megtudhatja, hogyan engedélyezheti a hitelesítést egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Webes alkalmazás hitelesítésének konfigurálása](scenario-secure-app-authentication-app-service.md)
