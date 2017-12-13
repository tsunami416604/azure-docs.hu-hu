---
title: "Az Azure Functions áttekintése | Microsoft Docs"
description: "Megtudhatja, hogyan optimalizálhat aszinkron számítási feladatokat percek alatt az Azure Functions használatával."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="an-introduction-to-azure-functions"></a>Az Azure Functions bemutatása  
Az Azure Functions megoldással egyszerűen futtathatók kisebb kódrészletek, más néven „függvények”, a felhőben. Elég, ha a szóban forgó problémára vonatkozó kódot megírja, nem kell egy egész alkalmazással vagy futtató infrastruktúrával bajlódnia. A függvények használatával még hatékonyabbá válhat a fejlesztés, amelyhez tetszőleges nyelvet használhat, legyen az akár a C#, az F#, a Node.js, a Java vagy a PHP. Csak annyi időért kell fizetnie, amennyit a kódja fut, a szükség szerinti méretezést pedig rábízhatja az Azure szolgáltatásra. Az Azure Functions segítségével [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazások fejleszthetők a Microsoft Azure-on.

A témakor általános áttekintést nyújt az Azure Functions szolgáltatásról. Ha szeretne rögtön a Functions használatának első lépéseihez ugrani, kezdje [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md) című résszel. Ha a Functions szolgáltatással kapcsolatos további műszaki információkat keres, lépjen a [fejlesztői segédanyagok](functions-reference.md) részhez.

## <a name="features"></a>Szolgáltatások
A Functions néhány főbb jellemzője:

* **Nyelvválasztás** – C#, F# vagy JavaScript használatával írhat függvényeket. További lehetőségekért lásd a [támogatott nyelveket](supported-languages.md).
* **Használatalapú fizetési modell** – Csak annyi időért kell fizetnie, amennyit a kódja fut. Tekintse meg a Használatalapú futtatási csomag lehetőséget a [díjszabás szakaszban](#pricing).  
* **Saját függőségek használata** – A Functions a NuGetet és az NPM-et is támogatja, így Ön a kedvenc könyvtárait használhatja.  
* **Beépített biztonság** – a HTTP-eseményindítókkal aktivált függvényeket olyan OAuth-szolgáltatókkal védheti, mint az Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiókok.  
* **Egyszerűsített integráció** – Könnyedén kihasználhatja az Azure-szolgáltatások és szolgáltatásként nyújtott szoftverek (SaaS) lehetőségeit. Erre vonatkozó példákat az [integrációs szakaszban](#integrations) talál.  
* **Rugalmas fejlesztés** – A függvényeket közvetlenül a portálon írhatja meg, vagy beállíthat folyamatos integrációt, és a kódját a [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), a [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md) vagy egyéb [támogatott fejlesztőeszközök](../app-service/app-service-deploy-local-git.md) segítségével helyezheti üzembe.  
* **Nyílt forráskód** – A Functions futtatókörnyezete nyílt forráskódú, és [elérhető a GitHubon](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Mire használhatom a Functions szolgáltatást?
A Functions kiválóan alkalmas adatok feldolgozására, rendszerek integrálására, az eszközök internetes hálózatával (IoT) való munkavégzésre, és egyszerű API-k és mikroszolgáltatások létrehozására. Fontolja meg a Functions használatát olyan műveletekhez, mint a rendszerképek vagy megrendelések feldolgozása, a fájlkarbantartás, illetve az ütemezés szerint futtatni kívánt feladatokhoz. 

A Functions sablonokkal segít megtenni az első lépéseket a legfontosabb forgatókönyvek, köztük az alábbiak esetében:

* **HTTPTrigger** – Kód végrehajtásának aktiválása egy HTTP-kéréssel. Példaként tekintse meg [az első függvény létrehozását](functions-create-first-azure-function.md).
* **TimerTrigger** – Törlés vagy egyéb kötegelt feladatok végrehajtása egy előre meghatározott menetrend szerint. Példaként tekintse meg az [időzítő által aktivált függvények létrehozását](functions-create-scheduled-function.md).
* **GitHub webhook** – Válaszadás a Github-adattárakban történő eseményekre. Példaként tekintse meg a [GitHub-webhookok által aktivált függvények létrehozását](functions-create-github-webhook-triggered-function.md).
* **Általános webhook** – Bármely webhookokat támogató szolgáltatás felől érkező webhook HTTP-kérések feldolgozása. Példaként tekintse meg az [általános webhookok által aktivált függvények létrehozását](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** – Azure Cosmos DB-dokumentumok feldolgozása, egy NoSQL-adatbázisban található gyűjteményekhez való hozzáadáskor vagy a frissítéskor. Példaként tekintse meg az [Azure Cosmos DB által aktivált függvények létrehozását](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** – Azure Storage-blobok feldolgozása tárolókhoz való hozzáadáskor. Ez a függvény rendszerképek átméretezéséhez használható. További információért lásd [a blobtárolók kötéseit](functions-bindings-storage-blob.md).
* **QueueTrigger** – Válaszadás az Azure Storage üzenetsorába beérkező üzenetekre. Példaként tekintse meg az [Azure Queue Storage által aktivált függvények létrehozását](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** – Válaszadás egy Azure Eseményközponthoz kézbesített eseményekre. Ez különösen hasznos az alkalmazások felépítésében, a felhasználói élmények vagy munkafolyamatok feldolgozásában és az eszközök internetes hálózatát (IoT) érintő forgatókönyvekben. További információért lásd [az Event Hubs kötéseit](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz az üzenetsor figyelésével. További információért lásd [a Service Bus kötéseit](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz témakörökre való feliratkozással. További információért lásd [a Service Bus kötéseit](functions-bindings-service-bus.md).

Az Azure Functions támogatja az *eseményindítókat*, amelyek egy kód végrehajtásának elindítását szolgáló módszerek, és a *kötéseket*, amelyek a be- és kimeneti adatok kódolásának leegyszerűsítését szolgáló módszerek. Az Azure Functions által biztosított eseményindítók és kötések részletes leírásáért lásd: [Azure Functions triggers and bindings developer reference](functions-triggers-bindings.md) (Az Azure Functions eseményindítóinak és kötéseinek fejlesztői segédanyagai).

## <a name="integrations"></a>Integráció
Az Azure Functions számos Azure-szolgáltatással és harmadik féltől származó szolgáltatással integrálható. Ezek a szolgáltatások a függvény aktiválására és végrehajtásának megkezdésére, vagy a kódhoz tartozó be- és kimenetként is használhatók. Az Azure Functions a következő szolgáltatásokkal való integrációt támogatja:

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps (táblák)
* Azure Notification Hubs
* Azure Service Bus (üzenetsorok és témakörök)
* Azure Storage (blob, üzenetsorok és táblák) 
* GitHub (webhookok)
* Helyszíni szolgáltatások (a Service Bus használatával)
* Twilio (SMS-üzenetek)

## <a name="pricing"></a>Mennyibe kerül a Functions szolgáltatás használata?
Az Azure Functions kétféle díjszabási csomaggal érhető el. Válassza azt, amelyik leginkább megfelel az igényeinek: 

* **Használatalapú csomag** – A függvény futásakor az Azure biztosít minden szükséges számítási erőforrást. Önnek nem kell foglalkoznia az erőforrás-kezeléssel, és csak annyi időért kell fizetnie, amennyit a kódja fut. 
* **App Service-csomag** – Függvényeit ugyanúgy futtathatja, ahogy a webes, mobilos és API-alkalmazásait. Ha már más alkalmazásokhoz is használja az App Service szolgáltatást, ugyanabban a csomagban további költségek nélkül futtathatja a függvényeit. 

További információt a szolgáltatási csomagokról [az Azure Functions szolgáltatási csomagok áttekintésében](functions-scale.md) talál. Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Következő lépések
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)  
  Lásson azonnal munkához, és hozza létre az első függvényét az Azure Functions gyorsindítójával. 
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Részletesebb műszaki információkat tartalmaz az Azure Functions futtatókörnyezettel kapcsolatban, valamint segédanyagokat függvények kódolásához, illetve eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában. 
* [További információ az Azure App Service szolgáltatásról](../app-service/app-service-web-overview.md)  
  Az Azure Functions az Azure App Service használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 

