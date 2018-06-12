---
title: Központi telepítése, és a webes API-k & REST API-k hívja meg az Azure Logic Apps |} Microsoft Docs
description: Központi telepítése, és hívja meg a webes API-k & REST API-k a rendszer az Azure Logic Apps integrációs munkafolyamatok
keywords: webes API-k, a REST API-k, a összekötőket, a munkafolyamatok, a rendszer integrációja, hitelesítéshez
services: logic-apps
author: stepsic-microsoft-com
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e808a463beb312df6ee2f8fc4378f72755dcdf33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299007"
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Központi telepítése, és hívja meg egyedi API-kat a logic app munkafolyamatok

Miután [hozzon létre egyéni API-k](./logic-apps-create-api-app.md) használható a logic app munkafolyamatokban, telepítenie kell az API-kat is hívása előtt. Telepítheti az API-k [webalkalmazások](../app-service/app-service-web-overview.md), de az API-k érdemes megfontolni [API-alkalmazások](../app-service/app-service-web-tutorial-rest-api.md), amelyek egyszerűbbé teszik a projekt felépítéséhez, tárolására és felhasználása a felhőben és helyszíni API-k. Nincs módosíthatja bármely kódot az API-k – csupán telepítse kódját egy API-alkalmazásba. Az API-kat tárolhatja a [Azure App Service](../app-service/app-service-web-overview.md), a platform,--szolgáltatás (PaaS) nyújtó jól skálázható, egyszerű API-t futtató biztosít.

Bár a legjobb élmény API-k a logic App hívhatják meg, vegye fel [OpenAPI (korábban Swagger) metaadatok](http://swagger.io/specification/) , amely az API-műveleteket és paramétereket ismerteti. A OpenAPI fájl segítségével könnyebben integrálhatja, és együttműködését a logic apps az API.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Az API-webalkalmazást vagy API-alkalmazás központi telepítése

Az egyéni API-t a logikai alkalmazás hívása előtt telepítse az API-webalkalmazást vagy API-alkalmazás az Azure App Service. Emellett ahhoz, hogy a fájl a Logic Apps Designer által is olvasható OpenAPI, API definition tulajdonságainak beállításához és kapcsolja be a [eltérő eredetű erőforrások megosztása (CORS)](../app-service/app-service-web-overview.md) a webes alkalmazás vagy API-alkalmazásba.

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a webalkalmazás vagy API-alkalmazásba.

2. Az alkalmazás menüjében, amely megnyitja a **API**, válassza a **API-definíció**. Állítsa be a **API definition hely** a OpenAPI swagger.json fájl URL-címet.

   Általában az URL-cím jelenik meg, a következő formátumban: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Az egyéni API-OpenAPI fájl csatolása](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. A **API**, válassza a **CORS**. Állítsa be a CORS-házirend a **engedélyezett eredeteket** való **"*"** (összes engedélyezése).

   Ez a beállítás lehetővé teszi a kérelmek az Logic App-tervezőből.

   ![A Logic App Designer kérések engedélyezése az egyéni API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

További információkért lásd: [futtatni egy RESTful API-t az Azure App Service CORS](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Az egyéni API-t hívja a logic app munkafolyamatok

Az API-definíció tulajdonságait és a CORS beállítása után az egyéni API-eseményindítók és műveletek közé tartozik a logic app munkafolyamat is elérhetőnek kell lennie. 

*  A Logic Apps-tervezőben az előfizetés webhelyek tallózással OpenAPI URL-címek webhelyeket megtekintéséhez.

*  Egy OpenAPI dokumentum húzni elérhető műveletek és a bemeneti adatok megtekintéséhez használja a [HTTP + Swagger műveleti](../connectors/connectors-native-http-swagger.md).

*  API-k, beleértve az API-k, amelyek nem rendelkeznek, vagy tehetnek közzé, egy OpenAPI dokumentum hívására bármikor létrehozhat egy kérelmet a [HTTP-művelet](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>További lépések

* [Egyéni összekötő áttekintése](../logic-apps/custom-connector-overview.md)