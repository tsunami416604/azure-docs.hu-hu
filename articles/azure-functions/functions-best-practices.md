---
title: Ajánlott eljárások az Azure Functions |} A Microsoft Docs
description: Ismerje meg, ajánlott eljárások és minták az Azure Functions szolgáltatáshoz.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: az Azure functions, minták, ajánlott eljárás, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbb21e63a25d6e357287f2f91181f2326850a898
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952851"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>A teljesítmény-és az Azure Functions megbízhatóság

Ez a cikk nyújt útmutatást a teljesítményének és megbízhatóságának javítása érdekében a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazások működik. 

## <a name="general-best-practices"></a>Általános ajánlott eljárások

Az alábbi táblázat az ajánlott eljárásokat, hogyan hozhat létre, és az Azure Functions használatával kiszolgáló nélküli megoldások tervezhet.

### <a name="avoid-long-running-functions"></a>Kerülje a hosszú ideig futó függvények

Nagyméretű, hosszú lefutású funkciók váratlan időtúllépés problémákat okozhat. Egy függvény nagy számos Node.js-függőségek miatt válhat. Függőségek importálása megnövekedett betöltési időt, amelyek váratlan időtúllépéseket is okozhatnak. Függőségek töltődnek be mindkét explicit és implicit módon. A kód által betöltött egyetlen modul betöltése előfordulhat, hogy a saját további modulok.  

Amikor csak lehetséges, újrabontása nagy funkciók kisebb függvény együtt beállítja, hogy a munkahelyi és a válaszok gyors. Ha például egy webhooknak vagy egy HTTP által aktivált függvény szükség lehet egy bizonyos időkorláton; belül nyugtázása választ szokás a webhookok, az azonnali válasz igényelnek. A HTTP-eseményindító hasznos egy üzenetsorba üzenetsor eseményindító függvény által feldolgozandó adhat át. Ez a megközelítés lehetővé teszi a tényleges munka késleltetése, és azonnal választ adja vissza.


### <a name="cross-function-communication"></a>Adatbázisközi függvény kommunikáció

[Durable Functions](durable-functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-overview.md) beépített állapotváltásra és több functions közötti kommunikáció kezelését.

Ha nem használ több függvények integrálása Durable Functions vagy a Logic Apps, általában közötti kommunikáció függvény a storage-üzenetsorok használata ajánlott.  A fő oka, tároló-üzenetsorok olcsóbb, és sokkal egyszerűbb üzembe helyezni. 

Az üzenetsor-tárolóba az egyes üzenetek mérete legfeljebb 64 KB-os korlátozott. Kell továbbítani tudja a nagyobb üzenetek funkciók között, ha egy Azure Service Bus üzenetsor felhasználható támogatja az üzenet mérete a Standard szintű, legfeljebb 256 KB-os és a prémium szintű legfeljebb 1 MB.

Service Bus-témakörök hasznosak, ha szüksége van az üzenetek szűrése feldolgozás előtt.

Az Event hubs hasznosak, nagy mennyiségű kommunikáció támogatásához.


### <a name="write-functions-to-be-stateless"></a>Állapot nélkülire kell függvények írása 

Függvények állapotmentesnek kell lennie, és ha lehetséges idempotens. Minden szükséges állapotinformációkat társítása az adatokat. Például egy megrendelés feldolgozása folyamatban valószínűleg egy társított `state` tag. Egy függvény alapján adott állapotot, amíg magát a függvény marad az állapot nélküli egy megrendelés feldolgozása sikerült. 

Idempotens funkciók különösen az időzítő eseményindító használata ajánlott. Például ha valami feltétlenül naponta egyszer kell futtatni, írja, ugyanazokat az eredményeket az a nap folyamán bármikor futtathatja azt. A függvény egy adott napja nem munka esetén kiléphet. Emellett ha egy korábbi futtatása nem sikerült befejezni, a következő futtatáskor kell vegyen fel, ahol abbahagyta.


### <a name="write-defensive-functions"></a>Védelmi vonalként függvények írása

Tegyük fel, a függvény kivételt bármikor sikerült észlel. Tervezése az funkciók egy adott múltbeli időpontból sikertelen a következő végrehajtása közben is használhatja. Érdemes lehet szükség a következő műveleteket:

1. 10 000 sort az adatbázis lekérdezése.
2. Hozzon létre üzenetsori üzenet összes feldolgozni további sorokat le a sort.
 
Attól függően, hogyan összetett a rendszert van, előfordulhat, hogy rendelkezik: érintett alárendelt szolgáltatásokkal rosszul viselkedik, hálózati kimaradások vagy a kvóta korlátozza elérte, stb. A függvény bármikor befolyásolhatja ezek mindegyikét. A függvények, elő kell készíteni, a Tervező kell.

Hogyan reagál a kódját a után azok az elemek 5000 beszúrása egy üzenetsorba feldolgozási hiba esetén? Egy csoportot, amely végzett az elemek nyomon követése Ellenkező esetben előfordulhat, hogy be őket ismét legközelebb. Ez lehet egy komoly hatással a munkafolyamatot. 

Egy elem feldolgozása már, ha engedélyezi a függvényre kattintva műveletvégzés lehet.

Használhatja az Azure Functions platformon összetevők nyújtott védelmi intézkedéseket előnyeit. Lásd a **ártalmas üzenetek kezelése** dokumentációját [Azure Storage-üzenetsor eseményindítók és kötések](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Méretezhetőség ajánlott eljárások

Nincsenek hatással a módját a függvényalkalmazás példánya méretezése tényező. A részletek a dokumentációban szerepelnek [függvény méretezése](functions-scale.md).  Az alábbiakban néhány ajánlott eljárást annak biztosítása érdekében a függvényalkalmazás optimális méretezhetőségét.

### <a name="share-and-manage-connections"></a>Megosztás és a kapcsolatok kezelése

Újra felhasználhatja a külső erőforrásokhoz, amikor csak lehetséges.  Lásd: [kezelése az Azure Functions kapcsolatok](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne keverje a tesztelési és éles kód ugyanaz a függvényalkalmazás

Függvényalkalmazás-függvényei ossza meg erőforrásait. Ha például memória van osztva. Ha éles környezetben használja a függvényalkalmazást, nem hozzáadni teszt kapcsolatos funkciók és erőforrások. Éles kód végrehajtása során váratlan többletterhelést okozhat.

Legyen óvatos a termelési függvényalkalmazások betöltése. Memória van átlagolja az egyes függvények az alkalmazásban.

Ha egy megosztott hivatkozott több .NET-es függvényeket, helyezzen egy közös megosztott mappába. Az alábbi példához hasonló utasítást a szerelvény hivatkozhat, ha szkriptekkel C# (.csx): 

    #r "..\Shared\MyAssembly.dll". 

Ellenkező esetben ördöngösség véletlenül üzembe helyezése több teszt verziója ugyanaz a bináris másképp viselkednek a funkciók között.

Ne használja az éles kódban részletes naplózás. Rendelkezik csökkenti a teljesítményt.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kóddal, de elkerülni hívások

Aszinkron programozás az ajánlott eljárás. Azonban mindig elkerülése hivatkozik a `Result` tulajdonság, vagy a hívó `Wait` metódust egy `Task` példány. Ez a megközelítés szál Erőforrásfogyás vezethet.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Üzenetek fogadása, amikor csak lehetséges, a batch szolgáltatásban

Néhány eseményindítók, például Eseményközpontnak engedélyezése egy üzenetköteget egy egyszeri hívás fogadása.  Sokkal jobb teljesítményt üzenetek kötegelése rendelkezik.  Beállíthatja, hogy az a maximális kötegméret a `host.json` leírt módon fájlt a [host.json dokumentációja](functions-host-json.md)

C#-függvényekre módosíthatja a típust egy szigorú típusmegadású tömbhöz.  Például, nem pedig `EventData sensorEvent` a podpis metody lehet `EventData[] sensorEvent`.  Más nyelven kell explicit módon beállíthat egy cardinality tulajdonsága a `function.json` való `many` , és engedélyezze a kötegelés [itt látható módon](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Gazdagép viselkedésmódok egyidejűségi a hatékonyabb hibakezelést konfigurálása

A `host.json` fájlt a függvényalkalmazás a gazdagép-futtatókörnyezet és az eseményindító jelenségeket konfigurálását teszi lehetővé.  Kötegelés viselkedéseket, mellett eseményindítók számos egyidejű kezelheti.  Gyakran módosításával ezek a beállítások értékei a meghívott függvényeket az igényeinek megfelelően a minden példány méretezési segítségével.

A hosts fájl a beállításokat az alkalmazáson belüli összes függvényt alkalmazza a belül egy *egypéldányos* a függvény. Például ha egy függvényalkalmazáshoz 2 HTTP-függvényekkel kellett, egyidejű kérelmek 25, vagy HTTP-eseményindító kérést lenne beleszámít a megosztott 25 párhuzamos kérés.  Ha a függvényalkalmazás 10 példányra van ellátva, a 2-funkciók hatékonyan engedélyezése 250 egyidejű kérelmek (10 példányra * 25 párhuzamos kérés példányonként).

**HTTP egyidejűségi gazdagép beállításai**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Más gazdagép-konfigurációs beállítások található [az állomás konfigurációs dokumentum](functions-host-json.md).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions kapcsolatok kezelése](manage-connections.md)
* [Azure App Service – ajánlott eljárások](../app-service/app-service-best-practices.md)
