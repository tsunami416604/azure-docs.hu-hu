---
title: "API Apps-ismertető | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan fejleszthet, üzemeltethet és használhat fel RESTful API-kat az Azure App Service segítségével."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: 60049a16-8159-47aa-a34b-110be0d8dab6
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 62b1541414543fc947886374424dadee09759323


---
# <a name="api-apps-overview"></a>Az API Apps áttekintése
Az Azure App Service-ben futtatott API-alkalmazások olyan funkciókat kínálnak, amelyek megkönnyítik a felhőben és helyileg tárolt API-k fejlesztését, üzemeltetését és felhasználását. Az API-alkalmazások vállalati szintű biztonságot, egyszerű hozzáférés-vezérlést, hibrid csatlakoztathatóságot, automatikus SDK-készítést és a [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) szolgáltatással való zökkenőmentes integrációt biztosítanak.

Az [Azure App Service](../app-service/app-service-value-prop-what-is.md) egy teljes körűen felügyelt platform webes, mobil és integrációs célokra. Az API Apps egyike az [Azure App Service](../app-service/app-service-value-prop-what-is.md) által kínált négy alkalmazástípusnak.

![Alkalmazástípusok Az Azure App Service szolgáltatásban.](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## <a name="why-use-api-apps"></a>Miért érdemes az API Appst választani?
Az API Apps által nyújtott főbb szolgáltatások:

* **Saját API-k használata meglévő formájukban** – nem kell megváltoztatnia a meglévő API-jai kódját ahhoz, hogy kihasználhassa az API Apps előnyeit – csupán telepítse kódját egy API-alkalmazásba. Az API minden olyan nyelvet vagy keretrendszert használhat, amelyet az App Service támogat. Ilyenek például az ASP.NET és a C#, a Java, a PHP, a Node.js és a Python.
* **Egyszerű felhasználás** – a [Swagger API-metaadatok](http://swagger.io/) beépített támogatása ügyfelek széles köre számára teszi az API-kat egyszerűen felhasználhatóvá.  Automatikusan generálhat ügyféloldali kódot az API-jainak nyelvek széles választékában, például C#, Java és JavaScript nyelven. A kód módosítása nélkül, könnyen beállítható [CORS](app-service-api-cors-consume-javascript.md). További információ: [App Service API Apps-metaadatok API-k feltárásához és kód létrehozásához](app-service-api-metadata.md), valamint [JavaScript-alapú API-alkalmazások felhasználása a CORS használatával](app-service-api-cors-consume-javascript.md). 
* **Egyszerű hozzáférés-vezérlés** – védje meg API-alkalmazásait a hitelesítés nélküli hozzáféréstől anélkül, hogy a kódon változtatnia kellene. A beépített hitelesítési szolgáltatások biztonságos hozzáférést tesznek lehetővé az API-khoz más szolgáltatások vagy felhasználói ügyfelek számára. A támogatott identitásszolgáltatók közé tartozik az Azure Active Directory, a Facebook, a Twitter, a Google, valamint a Microsoft-fiókok. Az ügyfelek használhatják az Active Directory Authentication Libraryt (ADAL) vagy a Mobile Apps SDK-t. További információ: [Az API Apps hitelesítése és engedélyezése az Azure App Service-ben](app-service-api-authentication.md).
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik az API-alkalmazások létrehozásának, üzembe helyezésének, felhasználásának, hibakeresésének és felügyeletének folyamatát. További információ: [A .NET-keretrendszerhez készült Azure SDK 2.8.1-es verziójának bejelentése](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).
* **Integráció a Logic Apps szolgáltatással** – az Ön által készített API-alkalmazások az [App Service Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) számára is felhasználhatók.  További információ: [Az App Service-ben üzemeltetett egyéni API használata a Logic Apps szolgáltatással](../logic-apps/logic-apps-custom-hosted-api.md), valamint [Az új, 2015-08-01-es sémaverzió előzetese](../logic-apps/logic-apps-schema-2015-08-01.md).

Emellett az API-alkalmazások a [Web Apps](../app-service-web/app-service-web-overview.md) és a [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) által nyújtott szolgáltatások előnyeit is kihasználhatják. Ez fordítva is igaz: ha webalkalmazást vagy mobilalkalmazást használ az API üzemeltetéséhez, a web- vagy mobilalkalmazás kihasználhatja az API Apps által nyújtott szolgáltatások előnyeit, mint például a Swagger-metaadatokat az ügyfélkód generálásához, valamint a CORS-t a tartományközi böngészőalapú hozzáféréshez. Az egyetlen különbség a három alkalmazástípus (API-, web- és mobilalkalmazások) között a nevük, valamint az Azure Portalon használt ikonjuk.

## <a name="whats-the-difference-between-api-apps-and-azure-api-management"></a>Mi a különbség az API Apps és az Azure API Management között?
Az API Apps és az [Azure API Management](../api-management/api-management-key-concepts.md) egymást kiegészítő szolgáltatások.

* Az API Management az API-k felügyeletére szolgál. Ha egy API-t  az API Management kezelőfelületére helyez, figyelni és szabályozni tudja annak használatát, módosíthatja a bemenetet és a kimenetet, összesíthet több API-t egy végpontban és így tovább. A felügyelt API-kat bárhol lehet üzemeltetni.
* Az API Apps az API-k üzemeltetésére szolgál. Ez a szolgáltatás olyan funkciókat foglal magába, amelyek egyszerűbbé teszik az API-k fejlesztését és felhasználást, viszont nem biztosítja azokat a figyelési, szabályozási, módosítási és összesítési funkciókat, amelyek az API Managementben megtalálhatók. Ha nincs szüksége az API Management szolgáltatásaira, üzemeltethet API-kat API-alkalmazások segítségével, az API Management használata nélkül.

Az alábbi diagram az API Management használatát mutatja be API-alkalmazások útján, illetve máshol üzemeltetett API-k esetében.

![Az Azure API Management és az API Apps](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Az API Management és az API Apps néhány szolgáltatása hasonló feladatokat lát el.  Mindkét szolgáltatás képes például a CORS-támogatás automatizálására. Amennyiben a két szolgáltatást együtt használja, az API Management-et érdemes a CORS-hoz használni, mivel az szolgál az API-alkalmazások kezelőfelületeként. 

## <a name="getting-started"></a>Első lépések
Első lépésként telepítsen egy mintakódot az API Apps szolgáltatásban, ehhez segítségül tekintse meg az Ön által előnyben részesített keretrendszerre vonatkozó oktatóanyagunkat.

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Az API-alkalmazásokkal kapcsolatos kérdéseit az [API Apps fórumán](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps) teheti fel. 




<!--HONumber=Jan17_HO3-->


