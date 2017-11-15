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
ms.openlocfilehash: d59ef16de433ac9691f6996eab2bf56f056feb88
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>A teljesítmény-és az Azure Functions megbízhatóság

Ez a cikk útmutatást a jobb teljesítmény és megbízhatósága a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazások működik. 


## <a name="avoid-long-running-functions"></a>Kerülje a hosszú ideig futó funkciók

Nagyméretű, hosszú futású funkciók váratlan időtúllépési hibákat is okozhat. Egy függvény nagyméretű, sok Node.js függőségek miatt válhat. Függőségek importálását is okozhat váratlan időtúllépések eredményező megnövekedett terhelést alkalommal. Függőségek töltődnek be mindkét explicit és implicit módon. A kód által betöltött egyetlen modul töltődhet saját további modulok.  

Amikor csak lehetséges, azonosítóterületen nagy funkciók kisebb függvénynek együtt beállítja, hogy a munka, és térjen vissza a gyors válaszok. Például egy webhook vagy a HTTP-eseményindító függvény előfordulhat, hogy egy visszaigazoló választ belül bizonyos; esetében gyakori, a webhookok való egy azonnali válasz igényelnek. A HTTP-eseményindító forgalma átadhatók egy várólista eseményindító függvény által feldolgozandó egy várólistára. Ez a megközelítés lehetővé teszi a tényleges munkát késleltető, és térjen vissza az azonnali válasz.


## <a name="cross-function-communication"></a>Kereszt-függvény kommunikáció

Több funkciók integrálása, esetén általában tárüzenetsort közötti kommunikáció függvény használata ajánlott.  A legfőbb oka tárüzenetsort olcsóbb és sokkal könnyebben kiépítéséhez. 

A tárolási várólistában lévő egyes üzenetek 64 KB méretű korlátozottak. Ha nagyobb üzenetek továbbítani funkciók között van szüksége, az Azure Service Bus várólista üzenet használható méretének legfeljebb 256 KB.

Service Bus-témakörök hasznosak, ha üzenet feldolgozás előtt szűrés van szüksége.

Az Event hubs hasznosak nagy mennyiségű kommunikáció támogatásához.


## <a name="write-functions-to-be-stateless"></a>Írást kell lennie az állapot nélküli 

Funkciók kell lennie az állapot nélküli és idempotent Ha lehetséges. Az adatok minden szükséges állapotadatokat társítani. Például egy rendelés feldolgozása valószínűleg a hozzárendelt `state` tag. Egy függvény alapján az állapotban, miközben maga a funkció továbbra is az állapot nélküli rendelés feldolgozása sikerült. 

Az Idempotent funkciók különösen az időzítő eseményindítók használata ajánlott. Például ha rendelkezik, amelyet feltétlenül naponta egyszer kell futtatni, írják, a nap folyamán bármikor ugyanaz az eredmény való futtatás. A függvény a következő módokon léphet nincs feladata egy adott napon esetén. Emellett egy korábbi futtatás nem sikerült végrehajtani, ha a következő futtatáskor kell átvételéhez, ahol abbahagyta.


## <a name="write-defensive-functions"></a>Defenzív írást

Tegyük fel, a függvény kivételt bármikor sikerült tapasztal. A funkciók tervezése folytathatja a korábbi sikertelen pontokról a következő végrehajtása közben. Vegye figyelembe a következő műveletek van szükség:

1. A lekérdezés egy db 10 000 soraihoz.
2. Hozzon létre egy üzenetsor-üzenetet az összes további feldolgozható sorok le a sort.
 
Attól függően, hogy hogyan összetett a rendszer, előfordulhat, hogy: helytelen viselkedik érintett alárendelt szolgáltatásokkal hálózati kimaradások vagy a kvóta korlátozza elérte, stb. A függvény bármikor befolyásolhatja mindegyik. Kell terveznie a Funkciók, elő kell készíteni a azt.

Hogyan reagál a kódját a után 5000 elem beszúrása a várólistára feldolgozásra hiba esetén? Nyomon követheti, hogy befejezte készletben. Ellenkező esetben előfordulhat, hogy be őket újra a következő alkalommal. Ez a munkafolyamat egy súlyos hatása lehet. 

Egy elem már megtörtént a feldolgozása, ha engedélyezi a műveletvégzés a funkciót.

Használhatja az Azure Functions platform összetevők már foglalt védelmi intézkedések előnyeit. Lásd például: **elhalt üzenetek kezelésének** dokumentációját [Azure Storage eseményindítók és kötések](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Angol szövegben ne keverje függvény ugyanazt az alkalmazást a teszt- és éles kódot

A függvény alkalmazások funkciók osztozik az erőforrásokon. Például a memória van osztva. Egy függvény alkalmazást éles környezetben használata, nem felvétele teszt kapcsolatos funkciók, és erőforrásokat. Éles kód végrehajtása során váratlan terhelést okozhat.

Ügyeljen az éles függvény alkalmazások betöltése. Memória van átlagosan volt az alkalmazás minden függvényt.

Ha egy megosztott hivatkozott több .NET-es függvényeket, elhelyezi egy közös megosztott mappába. Az alábbi példához hasonló utasítással szerelvény hivatkozik: 

    #r "..\Shared\MyAssembly.dll". 

Ellenkező esetben egyszerűen véletlenül telepíthető több teszt verziója megegyezik a bináris parancsmagoktól eltérően működő funkciók között.

Ne használja az éles kódban részletes naplózás. Rendelkezik egy negatívan befolyásolta a teljesítményt.



## <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kódot használja, de elkerülni hívások

Aszinkron programozás az ajánlott eljárás. Azonban mindig elkerülése hivatkozik a `Result` vagy tulajdonság hívása `Wait` metódusa egy `Task` példány. Ez a megközelítés szál Erőforrásfogyás vezethet.


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

Mivel az Azure Functions használ akkor is tisztában kell lennie az App Service-irányelvek Azure App Service-ben.
* [Minták és gyakorlatok HTTP teljesítményoptimalizálás](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
