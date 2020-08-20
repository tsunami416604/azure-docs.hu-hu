---
title: API-támogatás az Azure statikus Web Appsban Azure Functions
description: Az Azure statikus Web Apps által támogatott API-funkciók ismertetése
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 09daffa74ccd279c8187391ba3b86063aed7d204
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607026"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-támogatás az Azure statikus Web Apps előzetes verziójában Azure Functions

Az Azure statikus Web Apps [Azure functions](../azure-functions/functions-overview.md)használatával biztosít kiszolgáló nélküli API-végpontokat. A Azure Functions, az API-k igény szerint dinamikusan méretezhetők, és a következő funkciókkal rendelkeznek:

- **Integrált biztonság** közvetlen hozzáféréssel a felhasználói [hitelesítéshez és a szerepköralapú engedélyezési](user-information.md) adathoz.
- **Zökkenőmentes útválasztás** , amely lehetővé teszi, hogy az _API_ -útvonal biztonságosan elérhető legyen a webalkalmazás számára anélkül, hogy egyéni CORS szabályokat kellene megadnia.
- **Azure functions** v3 kompatibilis a Node.js 12 rendszerrel.
- **Http-eseményindítók** és kimeneti kötések.

## <a name="configuration"></a>Konfiguráció

Az API-végpontok az _API_ -útvonalon keresztül érhetők el a webalkalmazás számára. Amíg ez az útvonal kijavítva van, megadhatja azt a mappát, ahol a társított Azure Functions alkalmazást találja. Ezt a helyet a tárház _. GitHub/munkafolyamatok_ mappájában található [munkafolyamat YAML fájl szerkesztésével](github-actions-workflow.md#build-and-deploy) módosíthatja.

## <a name="constraints"></a>Korlátozások

Az Azure statikus Web Apps Azure Functionson keresztül biztosít API-t. A Azure Functions képességei olyan funkciókra összpontosítanak, amelyek lehetővé teszik, hogy API-t hozzon létre egy webalkalmazáshoz, és lehetővé tegye a webalkalmazás számára a biztonságos kapcsolódást az API-hoz. Ezek a funkciók bizonyos korlátozásokkal rendelkeznek, beleértve a következőket:

- Az API-útvonal előtagjának _API_-nak kell lennie.
- Az API functions alkalmazásnak JavaScript-ben kell lennie.
- Az API functions útválasztási szabályai csak az [átirányítások](routes.md#redirects) támogatását és [a szerepkörökkel rendelkező útvonalak biztonságossá](routes.md#securing-routes-with-roles)tételét támogatják.
- Az eseményindítók [http](../azure-functions/functions-bindings-http-webhook.md)-re korlátozódnak.
  - A bemeneti és kimeneti [kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) támogatottak.
- A naplók csak akkor érhetők el, ha [Application Insightst](../azure-functions/functions-monitoring.md) ad hozzá a functions-alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
