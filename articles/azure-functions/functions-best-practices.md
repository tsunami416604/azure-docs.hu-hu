---
title: "Gyakorlati tanácsok az Azure Functions |} Microsoft Docs"
description: "Az Azure Functions ajánlott eljárásairól és mintáiról megismerése."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "az Azure functions mintázatok, ajánlott eljárás, Funkciók, Eseményfeldolgozási, webhookokkal, a dinamikus számítási, a kiszolgáló nélküli architektúrája"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 739e820a44194af984750932d6023c90fcd11e42
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>A teljesítmény-és az Azure Functions megbízhatóság

Ez a cikk útmutatást a jobb teljesítmény és megbízhatósága a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazások működik. 

## <a name="general-best-practices"></a>Általános gyakorlati tanácsok

Hogyan build és a kiszolgáló nélküli megoldások Azure Functions használatával tervezővel a bevált gyakorlat az alábbiakban.

### <a name="avoid-long-running-functions"></a>Kerülje a hosszú ideig futó funkciók

Nagyméretű, hosszú futású funkciók váratlan időtúllépési hibákat is okozhat. Egy függvény nagyméretű, sok Node.js függőségek miatt válhat. Függőségek importálását is okozhat váratlan időtúllépések eredményező megnövekedett terhelést alkalommal. Függőségek töltődnek be mindkét explicit és implicit módon. A kód által betöltött egyetlen modul töltődhet saját további modulok.  

Amikor csak lehetséges, azonosítóterületen nagy funkciók kisebb függvénynek együtt beállítja, hogy a munka, és térjen vissza a gyors válaszok. Például egy webhook vagy a HTTP-eseményindító függvény előfordulhat, hogy egy visszaigazoló választ belül bizonyos; esetében gyakori, a webhookok való egy azonnali válasz igényelnek. A HTTP-eseményindító forgalma átadhatók egy várólista eseményindító függvény által feldolgozandó egy várólistára. Ez a megközelítés lehetővé teszi a tényleges munkát késleltető, és térjen vissza az azonnali válasz.


### <a name="cross-function-communication"></a>Kereszt-függvény kommunikáció

[Tartós funkciók](durable-functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) Állapotváltások és több funkciók közötti kommunikáció kezelése épülnek.

Nem használja több funkciók integrálása tartós funkciók vagy a Logic Apps, esetén általában tárüzenetsort közötti kommunikáció függvény használata ajánlott.  A legfőbb oka tárüzenetsort olcsóbb és sokkal könnyebben kiépítéséhez. 

A tárolási várólistában lévő egyes üzenetek 64 KB méretű korlátozottak. Ha nagyobb üzenetek továbbítani funkciók között van szüksége, az Azure Service Bus várólista üzenet használható méretének legfeljebb 256 KB.

Service Bus-témakörök hasznosak, ha üzenet feldolgozás előtt szűrés van szüksége.

Az Event hubs hasznosak nagy mennyiségű kommunikáció támogatásához.


### <a name="write-functions-to-be-stateless"></a>Írást kell lennie az állapot nélküli 

Funkciók kell lennie az állapot nélküli és idempotent Ha lehetséges. Az adatok minden szükséges állapotadatokat társítani. Például egy rendelés feldolgozása valószínűleg a hozzárendelt `state` tag. Egy függvény alapján az állapotban, miközben maga a funkció továbbra is az állapot nélküli rendelés feldolgozása sikerült. 

Az Idempotent funkciók különösen az időzítő eseményindítók használata ajánlott. Például ha rendelkezik, amelyet feltétlenül naponta egyszer kell futtatni, írják, a nap folyamán bármikor ugyanaz az eredmény való futtatás. A függvény a következő módokon léphet nincs feladata egy adott napon esetén. Emellett egy korábbi futtatás nem sikerült végrehajtani, ha a következő futtatáskor kell átvételéhez, ahol abbahagyta.


### <a name="write-defensive-functions"></a>Defenzív írást

Tegyük fel, a függvény kivételt bármikor sikerült tapasztal. A funkciók tervezése folytathatja a korábbi sikertelen pontokról a következő végrehajtása közben. Vegye figyelembe a következő műveletek van szükség:

1. A lekérdezés egy db 10 000 soraihoz.
2. Hozzon létre egy üzenetsor-üzenetet az összes további feldolgozható sorok le a sort.
 
Attól függően, hogy hogyan összetett a rendszer, előfordulhat, hogy: helytelen viselkedik érintett alárendelt szolgáltatásokkal hálózati kimaradások vagy a kvóta korlátozza elérte, stb. A függvény bármikor befolyásolhatja mindegyik. Kell terveznie a Funkciók, elő kell készíteni a azt.

Hogyan reagál a kódját a után 5000 elem beszúrása a várólistára feldolgozásra hiba esetén? Nyomon követheti, hogy befejezte készletben. Ellenkező esetben előfordulhat, hogy be őket újra a következő alkalommal. Ez a munkafolyamat egy súlyos hatása lehet. 

Egy elem már megtörtént a feldolgozása, ha engedélyezi a műveletvégzés a funkciót.

Használhatja az Azure Functions platform összetevők már foglalt védelmi intézkedések előnyeit. Lásd például: **elhalt üzenetek kezelésének** dokumentációját [Azure Storage eseményindítók és kötések](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Méretezhetőség gyakorlati tanácsok

Számos tényező, amely hatással van, a függvény alkalmazás példányok méretezési hogyan. A részletek szerepelnek dokumentációját [függvény skálázás](functions-scale.md).  Az alábbiakban néhány gyakorlati tanácsot optimális méretezhetőséget, a függvény alkalmazások biztosításához.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Angol szövegben ne keverje függvény ugyanazt az alkalmazást a teszt- és éles kódot

A függvény alkalmazások funkciók osztozik az erőforrásokon. Például a memória van osztva. Egy függvény alkalmazást éles környezetben használata, nem felvétele teszt kapcsolatos funkciók, és erőforrásokat. Éles kód végrehajtása során váratlan terhelést okozhat.

Ügyeljen az éles függvény alkalmazások betöltése. Memória van átlagosan volt az alkalmazás minden függvényt.

Ha egy megosztott hivatkozott több .NET-es függvényeket, elhelyezi egy közös megosztott mappába. Az alábbi példához hasonló utasítással szerelvény hivatkozási, ha a C#-parancsfájlokkal (.csx): 

    #r "..\Shared\MyAssembly.dll". 

Ellenkező esetben egyszerűen véletlenül telepíthető több teszt verziója megegyezik a bináris parancsmagoktól eltérően működő funkciók között.

Ne használja az éles kódban részletes naplózás. Rendelkezik egy negatívan befolyásolta a teljesítményt.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kódot használja, de elkerülni hívások

Aszinkron programozás az ajánlott eljárás. Azonban mindig elkerülése hivatkozik a `Result` vagy tulajdonság hívása `Wait` metódusa egy `Task` példány. Ez a megközelítés szál Erőforrásfogyás vezethet.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Amikor csak lehetséges kötegben üzeneteket fogadni

Az Event Hubs például néhány eseményindítók engedélyezése egy köteg egyetlen meghívása az üzenetek fogadását.  Kötegelés üzenetek mennyi jobb teljesítményt rendelkezik.  A maximális mérete konfigurálható a `functions.json` a fájlt a [host.json referenciadokumentációt](functions-host-json.md)

A C# funkciók módosíthatja a típus egy szigorú típusmegadású tömbhöz.  Például ahelyett, hogy `EventData sensorEvent` a metódus aláírása sikerült `EventData[] sensorEvent`.  Egyéb nyelvek kell explicit módon állítsa a cardinality tulajdonságát a `function.json` a `many` engedélyezéséhez a kötegelés [itt látható módon](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Állomás viselkedések jobban kezelni az egyidejű konfigurálása

A `host.json` fájl, a függvény alkalmazás állomás eseményindító és futtatási viselkedés konfigurációját teszi lehetővé.  Mellett kötegelés viselkedéshez, eseményindítók több egyidejű kezelheti.  Gyakran hangolását ezek a beállítások értékei a minden példány skálájának megfelelően igényeit kiszolgálni, a meghívott funkciók segítségével.

A hosts fájl beállításokat az alkalmazáson belüli összes funkciójának alkalmazza a belül egy *egypéldányos* függvény. Például ha egy függvény alkalmazás 2 HTTP funkciók kellett, egyidejű kérelmek 25, vagy HTTP-eseményindítóval kérelem számolja a megosztott 25 egyidejű kérelmek felé.  Ha 10 példányok méretezhető függvény alkalmazást, a 2 funkciók hatékonyan engedélyezése 250 egyidejű kérelmek (10 példányok * 25 egyidejű kérelmet egy példány).

**HTTP egyidejűségi állomás beállításai**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Más gazdagép-konfigurációs beállítások található [az állomás konfigurációs dokumentum](functions-host-json.md).

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

Mivel az Azure Functions használ akkor is tisztában kell lennie az App Service-irányelvek Azure App Service-ben.
* [Minták és gyakorlatok HTTP teljesítményoptimalizálás](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
