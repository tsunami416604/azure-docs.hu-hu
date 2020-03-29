---
title: Webes API-k üzembe helyezése és hívása & AZ Azure Logic Apps-ekrest-API-kat
description: Webes API-k üzembe helyezése és hívása & REST API-k at az Azure Logic Apps rendszerintegrációs munkafolyamataihoz
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790751"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Egyéni API-k üzembe helyezése és hívása munkafolyamatokból az Azure Logic Apps-ben

Miután [egyéni API-kat hoz létre](./logic-apps-create-api-app.md) a logikai alkalmazások munkafolyamataihoz, üzembe kell helyeznie az API-kat, mielőtt meghívhatna őket. Az [API-kweb-alkalmazásokként](../app-service/overview.md)is üzembe helyezhetők, de fontolja meg az API-k [API-alkalmazásokként](../app-service/app-service-web-tutorial-rest-api.md)való üzembe helyezését, amelyek megkönnyítik a munkát, amikor api-kat hoz létre, üzemeltet és használ fel a felhőben és a helyszínen. Nem kell módosítania az API-kban lévő kódot – csak telepítse a kódot egy API-alkalmazásba. Api-kat az [Azure App Service](../app-service/overview.md)platformszolgáltatásként (PaaS) üzemeltethet, amely rendkívül méretezhető, egyszerű API-üzemeltetést biztosít.

Bár bármelyik API-t meghívhat egy logikai alkalmazásból, a legjobb élmény érdekében adja hozzá az [OpenAPI (korábban Swagger) metaadatait,](https://swagger.io/specification/) amelyek az API műveleteit és paramétereit ismertetik. Ez az OpenAPI-fájl segítségével az API könnyebben integrálható, és jobban dolgozhat a logikai alkalmazásokkal.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Az API telepítése webalkalmazásként vagy API-alkalmazásként

Mielőtt meghívhatna az egyéni API-t egy logikai alkalmazásból, telepítse az API-t webalkalmazásként vagy API-alkalmazásként az Azure App Service-be. Ahhoz is, hogy az OpenAPI-fájl olvasható legyen a Logic Apps Designer számára, állítsa be az API-definíció tulajdonságait, és kapcsolja be a [több forrásból származó erőforrás-megosztást (CORS)](../app-service/overview.md) a webalkalmazásban vagy AZ API-alkalmazásban.

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a webalkalmazást vagy az API-alkalmazást.

2. A megnyíló alkalmazásmenü **API-területén**válassza az **API-definíció lehetőséget.** Állítsa be az **API-definíció helyét** az OpenAPI swagger.json fájl URL-címére.

   Az URL általában ebben a formátumban jelenik meg:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Hivatkozás az egyéni API-hoz való OpenAPI-fájlra](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Az **API csoportban**válassza a **CORS lehetőséget.** Állítsa a CORS-házirendet **az Engedélyezett eredetekhez** **"*"** értékre (engedélyezve az összeset).

   Ez a beállítás engedélyezi a Logic App Designer től érkező kérelmeket.

   ![A Logic App Designer kéréseinek engedélyezése az egyéni API-ra](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

További információ: [RESTful API üzemeltetése a CORS szolgáltatással az Azure App Service szolgáltatásban.](../app-service/app-service-web-tutorial-rest-api.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Az egyéni API hívása a logikai alkalmazás munkafolyamataiból

Az API-definíció tulajdonságainak és a CORS-nak a beállítása után az egyéni API-eseményindítók nak és műveleteknek elérhetőnek kell lenniük ahhoz, hogy a logikai alkalmazás munkafolyamatába bekerüljön. 

*  Az OpenAPI URL-címekkel rendelkező webhelyek megtekintéséhez tallózhat az előfizetéses webhelyek között a Logic Apps Designer alkalmazásban.

*  Ha a rendelkezésre álló műveleteket és bemeneteket egy OpenAPI-dokumentumra mutatva szeretné megtekinteni, használja a [HTTP + Swagger műveletet.](../connectors/connectors-native-http-swagger.md)

*  Bármely API hívásához, beleértve az OpenAPI-dokumentumot nem tartalmazó VAGY elérhető API-kat is, a [HTTP-művelettel](../connectors/connectors-native-http.md)bármikor létrehozhat egy kérelmet.

## <a name="next-steps"></a>További lépések

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)