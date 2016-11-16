---
title: "Az Azure Functions áttekintése | Microsoft Docs"
description: "Megtudhatja, hogyan optimalizálhat aszinkron számítási feladatokat percek alatt az Azure Functions használatával."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/29/2016
ms.author: cfowler;mahender;glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 601c5a61023fe1565dc0f8e22d9826ae5705fe22


---
# <a name="azure-functions-overview"></a>Az Azure Functions áttekintése
Az Azure Functions megoldással egyszerűen futtathatók kisebb kódrészletek, más néven „függvények”, a felhőben. Elég, ha a szóban forgó problémára vonatkozó kódot megírja, nem kell egy egész alkalmazással vagy futtató infrastruktúrával bajlódnia. Ezáltal még hatékonyabbá válhat a fejlesztés, amelyhez tetszőleges nyelvet használhat, legyen az akár a C#, F#, a Node.js, a Python vagy a PHP. Csak annyi időért kell fizetnie, amennyit a kódja fut, a szükség szerinti méretezést pedig rábízhatja az Azure szolgáltatásra.

A témakor általános áttekintést nyújt az Azure Functions szolgáltatásról. Ha szeretne rögtön az Azure Functions használatának első lépéseihez ugrani, kezdje [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md) résszel. Ha a Functions szolgáltatással kapcsolatos további műszaki információkat keres, lépjen a [fejlesztői segédanyagok](functions-reference.md) részhez.

## <a name="features"></a>Szolgáltatások
Az Azure Functions néhány főbb jellemzője:

* **Nyelvválasztás** – C#, F# Node.js, Python, PHP, Batch, Bash, Java vagy bármilyen végrehajtható formátum használatával írhat függvényeket.
* **Használatalapú fizetési modell** – Csak annyi időért kell fizetnie, amennyit a kódja fut. Lásd az App Service-csomag lehetőséget lent, a [díjszabás szakaszban](#pricing).  
* **Saját függőségek használata** – A Functions a NuGetet és az NPM-et is támogatja, így Ön a kedvenc könyvtárait használhatja.  
* **Beépített biztonság** – a HTTP-eseményindítókkal aktivált függvényeket olyan OAuth-szolgáltatókkal védheti, mint az Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiókok.  
* **Egyszerűsített integráció** – Könnyedén kihasználhatja az Azure-szolgáltatások és szolgáltatásként nyújtott szoftverek (SaaS) lehetőségeit. Erre vonatkozó példákat lentebb, az [integrációs szakaszban](#integrations) talál.  
* **Rugalmas fejlesztés** – A függvényeket közvetlenül a portálon írhatja meg, vagy beállíthat folyamatos integrációt, és a kódját a GitHub, a Visual Studio Team Services vagy egyéb [támogatott fejlesztőeszközök](../app-service-web/web-sites-deploy.md#deploy-using-an-ide) segítségével helyezheti üzembe.  
* **Nyílt forráskód** – A Functions futtatókörnyezete nyílt forráskódú, és [elérhető a GitHubon](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Mire használhatom a Functions szolgáltatást?
Az Azure Functions kiválóan alkalmas adatok feldolgozására, rendszerek integrálására, az eszközök internetes hálózatával (IoT) való munkavégzésre, és egyszerű API-k és mikroszolgáltatások létrehozására. Fontolja meg a Functions használatát olyan műveletek esetében, mint a rendszerképek vagy megrendelések feldolgozása, a fájlkarbantartás, a háttérszálon futtatni kívánt hosszú távú feladatok vagy bármilyen ütemezve futtatni kívánt feladat. 

A Functions sablonokkal segít megtenni az első lépéseket a legfontosabb forgatókönyvek, köztük az alábbiak esetében:

* **BlobTrigger** – Azure Storage-blobok feldolgozása tárolókhoz való hozzáadáskor. Ez rendszerképek átméretezéséhez használható.
* **EventHubTrigger** – Válaszadás egy Azure Eseményközponthoz kézbesített eseményekre. Ez különösen hasznos az alkalmazások felépítésében, a felhasználói élmények vagy munkafolyamatok feldolgozásában és az eszközök internetes hálózatát (IoT) érintő forgatókönyvekben.
* **Általános webhook** – Bármely webhookokat támogató szolgáltatás felől érkező webhook HTTP-kérések feldolgozása.
* **GitHub webhook** – Válaszadás a Github-adattárakban történő eseményekre. Egy vonatkozó példáért lásd: [Create a webhook or API function](functions-create-a-web-hook-or-api-function.md) (Egy webhook vagy API-függvény létrehozása).
* **HTTPTrigger** – Kód végrehajtásának aktiválása egy HTTP-kéréssel.
* **QueueTrigger** – Válaszadás az Azure Storage üzenetsorába beérkező üzenetekre. Egy vonatkozó példáért lásd: [Create an Azure Function which binds to an Azure service](functions-create-an-azure-connected-function.md) (Egy Azure-szolgáltatáshoz kötődő Azure-függvény létrehozása).
* **ServiceBusQueueTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz az üzenetsor figyelésével. 
* **ServiceBusTopicTrigger** – Kód csatlakoztatása egyéb Azure-szolgáltatásokhoz vagy helyi szolgáltatásokhoz témakörökre való feliratkozással. 
* **TimerTrigger** – Törlés vagy egyéb kötegelt feladatok végrehajtása egy előre meghatározott menetrend szerint. Egy vonatkozó példáért lásd: [Create an event processing function](functions-create-an-event-processing-function.md) (Eseményfeldolgozó függvény létrehozása).

Az Azure Functions támogatja az *eseményindítókat*, amelyek egy kód végrehajtásának elindítását szolgáló módszerek, és a *kötéseket*, amelyek a be- és kimeneti adatok kódolásának leegyszerűsítését szolgáló módszerek. Az Azure Functions által biztosított eseményindítók és kötések részletes leírásáért lásd: [Azure Functions triggers and bindings developer reference](functions-triggers-bindings.md) (Az Azure Functions eseményindítóinak és kötéseinek fejlesztői segédanyagai).

## <a name="a-nameintegrationsaintegrations"></a><a name="integrations"></a>Integráció
Az Azure Functions számos Azure-szolgáltatással és harmadik féltől származó szolgáltatással integrálható. Ezek a függvény aktiválására és végrehajtásának megkezdésére, vagy a kódhoz tartozó be- és kimenetként is használhatók. Az Azure Functions a következő szolgáltatásokkal való integrációt támogatja. 

* Azure DocumentDB
* Azure Event Hubs 
* Azure Mobile Apps (táblák)
* Azure Notification Hubs
* Azure Service Bus (üzenetsorok és témakörök)
* Azure Storage (blob, üzenetsorok és táblák) 
* GitHub (webhookok)
* Helyszíni szolgáltatások (a Service Bus használatával)

## <a name="a-namepricingahow-much-does-functions-cost"></a><a name="pricing"></a>Mennyibe kerül a Functions szolgáltatás használata?
Az Azure Functions kétféle díjszabási csomaggal érhető el – válassza azt, amelyik leginkább megfelel az igényeinek: 

* **Dinamikus futtatási csomag** – A függvény futásakor az Azure biztosít minden szükséges számítási erőforrást. Önnek nem kell foglalkoznia az erőforrás-kezeléssel, és csak annyi időért kell fizetnie, amennyit a kódja fut. Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](/pricing/details/functions). 
* **App Service-csomag** – Függvényeit ugyanúgy futtathatja, ahogy a webes, mobilos és API-alkalmazásait. Ha már más alkalmazásokhoz is használja az App Service szolgáltatást, ugyanabban a csomagban további költségek nélkül futtathatja a függvényeit. További részletekért lásd [az App Service díjszabási oldalát](/pricing/details/app-service/).

A függvények méretezésével kapcsolatos további információkért lásd: [Az Azure Functions méretezése](functions-scale.md).

## <a name="next-steps"></a>Következő lépések
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)  
  Lásson azonnal munkához, és hozza létre az első függvényét az Azure Functions gyorsindítójával. 
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Részletesebb műszaki információkat tartalmaz az Azure Functions futtatókörnyezettel kapcsolatban, valamint segédanyagokat függvények kódolásához, illetve eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Részletezi az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat, köztük a dinamikus szolgáltatáscsomagot, és segít a megfelelő csomag kiválasztásában. 
* [További információ az Azure App Service szolgáltatásról](../app-service/app-service-value-prop-what-is.md)  
  Az Azure Functions az Azure App Service platform használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 




<!--HONumber=Nov16_HO2-->


