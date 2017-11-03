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
ms.openlocfilehash: 41f26a4b03a6431aaad21bda6336b8840d2d923f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="an-introduction-to-azure-functions"></a>Az Azure Functions bemutatása  
Az Azure Functions megoldással egyszerűen futtathatók kisebb kódrészletek, más néven „függvények”, a felhőben. Elég, ha a szóban forgó problémára vonatkozó kódot megírja, nem kell egy egész alkalmazással vagy futtató infrastruktúrával bajlódnia. Funkciók is legyen még hatékonyabbá, és a fejlesztési nyelvi szerkesztőprogramban, például a C#, F #, Node.js, Java, vagy a PHP is használhatja. Csak annyi időért kell fizetnie, amennyit a kódja fut, a szükség szerinti méretezést pedig rábízhatja az Azure szolgáltatásra. Az Azure Functions lehetővé teszi, hogy fejlesztése [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazások a Microsoft Azure-on.

A témakor általános áttekintést nyújt az Azure Functions szolgáltatásról. Ha szeretne, hogyan Ismerkedés a Funkciók, kezdje [az első Azure-függvény létrehozása](functions-create-first-azure-function.md). Ha a Functions szolgáltatással kapcsolatos további műszaki információkat keres, lépjen a [fejlesztői segédanyagok](functions-reference.md) részhez.

## <a name="features"></a>Szolgáltatások
Az alábbiakban néhány kulcsszolgáltatása funkciók:

* **Nyelvválasztás** – C#, F #, Node.js, Java, PHP, batch, bash, vagy végrehajtható fájlok használatával írhat függvényeket.
* **Használatalapú fizetési modell** – Csak annyi időért kell fizetnie, amennyit a kódja fut. Tekintse meg a Használatalapú futtatási csomag lehetőséget a [díjszabás szakaszban](#pricing).  
* **Saját függőségek használata** – A Functions a NuGetet és az NPM-et is támogatja, így Ön a kedvenc könyvtárait használhatja.  
* **Beépített biztonság** – a HTTP-eseményindítókkal aktivált függvényeket olyan OAuth-szolgáltatókkal védheti, mint az Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiókok.  
* **Egyszerűsített integráció** – Könnyedén kihasználhatja az Azure-szolgáltatások és szolgáltatásként nyújtott szoftverek (SaaS) lehetőségeit. Erre vonatkozó példákat az [integrációs szakaszban](#integrations) talál.  
* **Rugalmas fejlesztés** – a függvényeket közvetlenül a portálon, vagy beállíthat folyamatos integrációt és keresztül a kód telepítésére [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md), és más [támogatott Fejlesztőeszközök](../app-service/app-service-deploy-local-git.md).  
* **Nyílt forráskód** – A Functions futtatókörnyezete nyílt forráskódú, és [elérhető a GitHubon](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Mire használhatom a Functions szolgáltatást?
Functions kiválóan alkalmas adatok feldolgozására, rendszerek, az internetes hálózatával (IoT) használata, és egyszerű API-k és mikroszolgáltatások felépítése integrálása. Fontolja meg a Functions használatát olyan műveletekhez, mint a rendszerképek vagy megrendelések feldolgozása, a fájlkarbantartás, illetve az ütemezés szerint futtatni kívánt feladatokhoz. 

A Functions sablonokkal segít megtenni az első lépéseket a legfontosabb forgatókönyvek, köztük az alábbiak esetében:

* **HTTPTrigger** – Kód végrehajtásának aktiválása egy HTTP-kéréssel. Egy vonatkozó példáért lásd: [hozzon létre az első függvényét](functions-create-first-azure-function.md).
* **TimerTrigger** – Törlés vagy egyéb kötegelt feladatok végrehajtása egy előre meghatározott menetrend szerint. Egy vonatkozó példáért lásd: [időzítő által indított függvény létrehozása](functions-create-scheduled-function.md).
* **GitHub webhook** – Válaszadás a Github-adattárakban történő eseményekre. Egy vonatkozó példáért lásd: [hozzon létre egy függvényt a GitHub webhook által indított](functions-create-github-webhook-triggered-function.md).
* **Általános webhook** – Bármely webhookokat támogató szolgáltatás felől érkező webhook HTTP-kérések feldolgozása. Egy vonatkozó példáért lásd: [hozzon létre egy függvényt egy általános webhook által indított](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** -folyamat Azure Cosmos DB dokumentumok hozzáadásakor vagy gyűjteményében egy NoSQL-adatbázis frissítése. Egy vonatkozó példáért lásd: [hozzon létre egy Azure Cosmos DB által indított függvényt](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** – Azure Storage-blobok feldolgozása tárolókhoz való hozzáadáskor. Ez a függvény rendszerképek átméretezéséhez használható. További információkért lásd: [Blob-tároló kötések](functions-bindings-storage-blob.md).
* **QueueTrigger** – Válaszadás az Azure Storage üzenetsorába beérkező üzenetekre. Egy vonatkozó példáért lásd: [hozzon létre egy Azure Queue storage által indított függvényt](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** – Válaszadás egy Azure Eseményközponthoz kézbesített eseményekre. Ez különösen hasznos az alkalmazások felépítésében, a felhasználói élmények vagy munkafolyamatok feldolgozásában és az eszközök internetes hálózatát (IoT) érintő forgatókönyvekben. További információkért lásd: [Event Hubs kötések](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz az üzenetsor figyelésével. További információkért lásd: [Service Bus kötések](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz témakörökre való feliratkozással. További információkért lásd: [Service Bus kötések](functions-bindings-service-bus.md).

Az Azure Functions támogatja az *eseményindítókat*, amelyek egy kód végrehajtásának elindítását szolgáló módszerek, és a *kötéseket*, amelyek a be- és kimeneti adatok kódolásának leegyszerűsítését szolgáló módszerek. Az Azure Functions által biztosított eseményindítók és kötések részletes leírásáért lásd: [Azure Functions triggers and bindings developer reference](functions-triggers-bindings.md) (Az Azure Functions eseményindítóinak és kötéseinek fejlesztői segédanyagai).

## <a name="integrations"></a>Integráció
Az Azure Functions számos Azure-szolgáltatással és harmadik féltől származó szolgáltatással integrálható. Ezek a szolgáltatások a függvény aktiválására és végrehajtásának megkezdésére, vagy a kódhoz tartozó be- és kimenetként is használhatók. A következő szolgáltatásokkal való integrációt az Azure Functions támogatja:

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
Az Azure Functions kétféle díjszabási csomaggal érhető rendelkezik. Válassza ki azt, amelyik leginkább megfelel az igényeinek: 

* **Használatalapú csomag** – A függvény futásakor az Azure biztosít minden szükséges számítási erőforrást. Önnek nem kell foglalkoznia az erőforrás-kezeléssel, és csak annyi időért kell fizetnie, amennyit a kódja fut. 
* **App Service-csomag** – Függvényeit ugyanúgy futtathatja, ahogy a webes, mobilos és API-alkalmazásait. Ha már más alkalmazásokhoz is használja az App Service szolgáltatást, ugyanabban a csomagban további költségek nélkül futtathatja a függvényeit. 

Üzemeltetési tervek kapcsolatos további információkért lásd: [üzemeltetési terv összehasonlítása az Azure Functions](functions-scale.md). Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/).

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
  Az Azure Functions használja Azure App Service alapfunkciókat például a központi telepítések, a környezeti változókat és a diagnosztika. 

