---
title: API-támogatás az Azure statikus Web Appsban Azure Functions
description: Az Azure statikus Web Apps által támogatott API-funkciók ismertetése
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4fa3acf0e6cc767aeee4504bbc4df382a75e256b
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758758"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-támogatás az Azure statikus Web Apps előzetes verziójában Azure Functions

Az Azure statikus Web Apps [Azure functions](../azure-functions/functions-overview.md)használatával biztosít kiszolgáló nélküli API-végpontokat. A Azure Functions, az API-k igény szerint dinamikusan méretezhetők, és a következő funkciókkal rendelkeznek:

- **Integrált biztonság** közvetlen hozzáféréssel a felhasználói [hitelesítéshez és a szerepköralapú engedélyezési](user-information.md) adathoz.
- **Zökkenőmentes útválasztás** , amely lehetővé teszi, hogy az _API_ -útvonal biztonságosan elérhető legyen a webalkalmazás számára anélkül, hogy egyéni CORS szabályokat kellene megadnia.
- **Azure functions** v3 kompatibilis a Node. js 12-mel.
- **Http-eseményindítók** és kimeneti kötések.

## <a name="configuration"></a>Konfiguráció

Az API-végpontok az _API_ -útvonalon keresztül érhetők el a webalkalmazás számára. Amíg ez az útvonal kijavítva van, megadhatja azt a mappát, ahol a társított Azure Functions alkalmazást találja. Ezt a helyet a tárház _. GitHub/munkafolyamatok_ mappájában található [munkafolyamat YAML fájl szerkesztésével](github-actions-workflow.md#build-and-deploy) módosíthatja.

## <a name="constraints"></a>Korlátozások

Az Azure statikus Web Apps Azure Functionson keresztül biztosít API-t. A Azure Functions képességei olyan funkciókra összpontosítanak, amelyek lehetővé teszik, hogy API-t hozzon létre egy webalkalmazáshoz, és lehetővé tegye a webalkalmazás számára a biztonságos kapcsolódást az API-hoz. Ezek a funkciók bizonyos korlátozásokkal rendelkeznek, beleértve a következőket:

- Az API-útvonal előtagjának _API_-nak kell lennie.
- Az API functions alkalmazásnak JavaScript-ben kell lennie.
- Az eseményindítók és kötések a [http](../azure-functions/functions-bindings-http-webhook.md)-re korlátozódnak.
  - Az összes többi [Azure functions eseményindító és kötés](../azure-functions/functions-triggers-bindings.md#supported-bindings) a kimeneti kötések kivételével korlátozott.
- A naplók csak akkor érhetők el, ha [Application Insightst](../azure-functions/functions-monitoring.md) ad hozzá a functions-alkalmazáshoz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
