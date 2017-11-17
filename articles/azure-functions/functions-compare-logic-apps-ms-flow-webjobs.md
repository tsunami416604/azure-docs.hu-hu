---
title: "Választhat a folyamat, a Logic Apps, a funkciók és a webjobs-feladatok |} Microsoft Docs"
description: "Összehasonlítása és a felhő integráció a Microsoft szolgáltatásaihoz, és döntse el, melyik szolgáltatásokat kell használnia."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "Microsoft folyamata, folyamata, a logic apps, az azure functions feladatokat az azure webjobs-feladatok, webjobs, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 03246846484878f7155449ad11b009aeffe8a576
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Választás a következők közül: Flow, Logic Apps, Functions és WebJobs
Ez a cikk hasonlítja össze, és kiemeli a következő szolgáltatások a Microsoft felhőben, így az összes integrációs előforduló problémák megoldásához és üzleti folyamatok automatizálása:

* [Microsoft folyamata](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Az Azure App Service webjobs-feladatok](../app-service/web-sites-create-web-jobs.md)

Ezek a szolgáltatások akkor hasznos, ha a "kapcsolása" együtt a különféle rendszerek. Azok az összes definiálhat bemeneti, a műveletek, a feltételek és a kimeneti. Futtathatja azok ütemezés vagy eseményindító. Azonban mindegyik szolgáltatáshoz egyedi előnye is van, és összehasonlítja azokat nem a kérdés, "melyik szolgáltatás az a legjobb?" de egyik "melyik szolgáltatás legjobban megfelelőt ennél a megoldásnál?" Ezek a szolgáltatások kombinációja gyakran, méretezhető, teljes körű integrációs megoldást gyorsan készíthet a legjobb módja.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Vs folyamata. Logic Apps
Is tárgyaljuk Microsoft Flow és az Azure Logic Apps együtt mert mindkettő *konfigurációs-első* integrációs szolgáltatások. Ezek megkönnyítik a folyamatok és munkafolyamatok kialakítását, és integrálhatja a különböző SaaS és vállalati alkalmazások. 

* Attribútumfolyam Logic Apps épül
* Rendelkeznek-e az azonos munkafolyamat-Tervező
* [Összekötők](../connectors/apis-list.md) , hogy az egyik munkahelyi is együttműködik a más

Folyamata lehetővé teszi az office munkavégző egyszerű integráció végrehajtásához (például lekérése az SMS fontos e-mailek) keresztül a fejlesztők és informatikai. A Logic Apps, másrészt engedélyezheti a speciális vagy kritikus fontosságú integrációja (például B2B folyamatok) ahol vállalati szintű DevOps és biztonsági eljárásokat szükség. Egy üzleti munkafolyamat összetettsége az idő haladtával egyre nő a jellemző. Ennek megfelelően kezdje a folyamatot a következő első, majd átalakíthatja a logic app, igény szerint.

Az alábbi táblázat segít meghatározni, hogy alakulásának vagy a Logic Apps esetén ajánlott az egy adott integráció.

|  | Folyamat | Logic Apps |
| --- | --- | --- |
| Célközönség |irodai dolgozók, üzleti felhasználók |A fejlesztők, az informatikai szakemberek számára |
| Forgatókönyvek |Önkiszolgáló |Kritikus fontosságú |
| Tervezési eszköz |A böngésző és a mobil alkalmazást, és csak a felhasználói felület |A böngésző és [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [nézet Code](../logic-apps/logic-apps-author-definitions.md) érhető el |
| DevOps |Ad hoc, éles környezetben fejlesztése |forrás-vezérlő, tesztelését, támogatási, és automatizálási és kezelhetősége [Azure erőforrás-kezelés](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Rendszergazdai feladatok |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://Portal.Azure.com](https://portal.azure.com) |
| Biztonság |Standard eljárásokat: [adatok közös joghatóság alá](https://wikipedia.org/wiki/Technological_Sovereignty), [titkosítását](https://wikipedia.org/wiki/Data_at_rest#Encryption) bizalmas adatokat, stb. |Biztonság Azure: [Azure biztonsági](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), stb. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Vs funkciók. WebJobs
Is tárgyaljuk Azure Functions és az Azure App Service WebJobs együtt mert mindkettő *kód-első* integrációs szolgáltatását, és a fejlesztők számára készült. Lehetővé teszik egy parancsfájl vagy futtatásához olyan kódrészletek, különféle eseményeket válaszul például [új Storage Blobsba](functions-bindings-storage.md) vagy [WebHook kérelmet](functions-bindings-http-webhook.md). Az alábbiakban azok Hasonlóságok: 

* Mindkét épül [Azure App Service](../app-service/app-service-web-overview.md) , és kihasználhatják a szolgáltatások, mint [verziókövető](../app-service/app-service-continuous-deployment.md), [hitelesítési](../app-service/app-service-authentication-overview.md), és [figyelési](../app-service/web-sites-monitor.md).
* Mindkettő szolgáltatások fejlesztői kialakításával foglalkozik.
* A szabványos parancsnyelvek és programozási nyelvek támogatása.
* NuGet és támogatja a NPM is.

Funkciók WebJobs természetes fejlődéséhez abban, hogy a webjobs-feladatok kapcsolatos ajánlott dolgot vesz igénybe, és javítja a rájuk. A fejlesztések közé tartoznak: 

* [Kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) az app model.
* Zökkenőmentes fejlesztői, teszteléséhez, és futtassa a kód a böngészőben.
* Több Azure-szolgáltatások és a 3. fél szolgáltatások beépített integrációját, például [GitHub Webhook](https://developer.github.com/webhooks/creating/).
* Fizetési-használati, nem kell fizetnie az [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Automatikus, [dinamikus méretezés](functions-scale.md).
* A meglévő ügyfeleknek az App Service-ben futó App Service-csomag (mértékben történő kihasználásához alatt használt erőforrások) továbbra is lehetséges.
* Integráció a Logic Apps.

A következő táblázat összefoglalja, funkciók és a webjobs-feladatok:

|  | Functions | WebJobs |
| --- | --- | --- |
| Méretezés |Configurationless skálázás |az App Service-csomag vertikális |
| Díjszabás |Fizetési-használati vagy része az App Service-csomag |App Service-csomag része |
| Futtatás-típus |elindul, ütemezett (időzítő indítófeltételt) |kiváltott, folyamatos, ütemezett |
| Eseményindító események |[Időzítő](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [(GitHub, Slackhez) HTTP/WebHook](functions-bindings-http-webhook.md), [az Azure App Service Mobile Apps szolgáltatásban](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [Azure Storage üzenetsorokat és blobokat](functions-bindings-storage-blob.md), [Azure Service Bus-üzenetsorok és témakörök](functions-bindings-service-bus.md) |[Az Azure Storage üzenetsorokat és blobokat](functions-bindings-storage-blob.md), [Azure Service Bus-üzenetsorok és témakörök](functions-bindings-service-bus.md) |
| A böngésző fejlesztői |Támogatott |Nem támogatott |
| C# |Támogatott |Támogatott |
| F# |Támogatott |Nem támogatott |
| JavaScript |Támogatott |Támogatott |
| Java |Előzetes verzió | Nem támogatott |
| Bash |Kísérleti |Támogatott |
| Windows-parancsfájlok (.cmd, .bat) |Kísérleti |Támogatott |
| PowerShell |Kísérleti |Támogatott |
| PHP |Kísérleti |Támogatott |
| Python |Kísérleti |Támogatott |
| TypeScript |Kísérleti |Nem támogatott |

Függvények, illetve a WebJobs használata végső soron attól függ, milyen máris az App Service. Ha egy App Service alkalmazás legyen kódtöredékek futtatásához, és ugyanabban a DevOps környezetben együtt kezelheti azokat, használja a webjobs-feladatok. A következő esetekben használja a függvényt.

* Más Azure-szolgáltatások vagy a 3. fél alkalmazásokat kódtöredékek futtatni kívánt.
* Szeretné az integrációs kód míg kezelhetők az App Service-alkalmazásokhoz.
* A kódrészleteket logikai alkalmazás a hívni kívánt. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Egymást követő, Logic Apps, és a funkciók együtt
Mint korábban már említettük a melyik szolgáltatás az Ön számára legmegfelelőbb szolgáltatás adott helyzettől függ. 

* Egyszerű üzleti optimalizálás a folyamat használja.
* Ha az integrációs forgatókönyv túl speciális folyamathoz, vagy DevOps képességek és a biztonsági megfelelőségi van szüksége, használja a Logic Apps.
* Az integrációs forgatókönyv lépése magas egyéni átalakítási vagy speciális kód szükséges, ha egy függvény írási, és a funkciót a logic App műveletet indító.

A logikai alkalmazás egy folyamat hívása. A logikai alkalmazás és a logikai alkalmazás függvény függvények is elindítható. Az integráció folyamata, a Logic Apps és a funkciók között továbbra is fennáll, időbeli javítása érdekében. Build valami egy szolgáltatás, és az egyéb szolgáltatások használatát. Válasszon az alábbi három technológiából bármely befektetési ezért megfelelőbb.

## <a name="next-steps"></a>Következő lépések
Ismerkedés az egyes szolgáltatások az első folyamata, logikai alkalmazás, függvény alkalmazás vagy webjobs-feladat létrehozásával. Kattintson az alábbi hivatkozásokra:

* [Ismerkedés a Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)
* [WebJobs üzembe helyezése Visual Studióval](../app-service/websites-dotnet-deploy-webjobs.md)

Vagy a következő hivatkozásokkal integrációs szolgáltatásokról további információ:

* [Az Azure Functions & Azure App Service kihasználva integrációs forgatókönyvek szerint Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Egyszerű tett Charles Lamanna integrációja](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [A Logic Apps élő adás](http://aka.ms/logicappslive)
* [Microsoft Flow gyakran ismételt kérdések](https://flow.microsoft.com/documentation/frequently-asked-questions/)

