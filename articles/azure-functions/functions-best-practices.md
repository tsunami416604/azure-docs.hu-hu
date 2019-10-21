---
title: Ajánlott eljárások a Azure Functionshoz | Microsoft Docs
description: A Azure Functions ajánlott eljárásainak és mintáinak megismerése.
author: ggailey777
manager: gwallace
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad7bdfd3abc4d3b4b672f5471ea826d4cef0f3fc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596888"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Azure Functions teljesítményének és megbízhatóságának optimalizálása

Ez a cikk útmutatást nyújt a [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli Function-alkalmazások teljesítményének és megbízhatóságának javításához.  

## <a name="general-best-practices"></a>Általános ajánlott eljárások

Az alábbiakban az ajánlott eljárások azt ismertetik, hogyan hozhat létre és alakíthat ki kiszolgáló nélküli megoldásokat Azure Functions használatával.

### <a name="avoid-long-running-functions"></a>A hosszan futó függvények elkerülése

A nagyméretű, hosszan futó függvények váratlan időtúllépési problémákhoz vezethetnek. Ha többet szeretne megtudni egy adott üzemeltetési csomag időtúllépéséről, tekintse meg a [Function app timeout időtartamát](functions-scale.md#timeout). 

A függvények sok Node. js-függőség miatt nagy méretűek lehetnek. A függőségek importálása nagyobb betöltési időt is okozhat, ami váratlan időtúllépéseket eredményezhet. A függőségeket explicit módon és implicit módon kell betölteni. A kód által betöltött egyetlen modul a saját további moduljait is betöltheti. 

Amikor csak lehetséges, a nagyméretű függvények újrabontása kisebb functions-készletekbe, amelyek együtt működnek, és a válaszokat gyorsan adják vissza. Előfordulhat például, hogy egy webhook vagy egy HTTP trigger függvény egy bizonyos időkorláton belül visszaigazolási választ kér. gyakori, hogy a webhookok azonnali választ igényelnek. A HTTP-trigger hasznos adatait átadhatja egy, a várólista-trigger függvény által feldolgozandó várólistába. Ezzel a megközelítéssel késleltetheti a tényleges munkát, és azonnali választ adhat vissza.


### <a name="cross-function-communication"></a>Cross Function kommunikáció

A [Durable functions](durable/durable-functions-overview.md) és a [Azure Logic apps](../logic-apps/logic-apps-overview.md) az állapot-váltások és a több függvény közötti kommunikáció kezelésére készült.

Ha nem használ Durable Functions vagy Logic Apps több függvénybe való integrálásra, általában ajánlott a tárolási várólisták használata a több funkciós kommunikációhoz.  A fő ok a tárolási várólisták olcsóbb és sokkal könnyebben kiépíthető. 

A tárolási várólistán lévő egyes üzenetek mérete 64 KB-ra van korlátozva. Ha nagyobb üzeneteket kell átadnia a függvények között, egy Azure Service Bus üzenetsor használható a standard szinten 256 KB-ig, a prémium szinten legfeljebb 1 MB-ra.

Service Bus témakörök akkor hasznosak, ha a feldolgozás előtt üzenet-szűrésre van szükség.

Az Event hubok hasznosak a nagy mennyiségű kommunikáció támogatásához.


### <a name="write-functions-to-be-stateless"></a>Az írási függvények állapot nélküliek lesznek 

A függvények állapot nélküliek és idempotens, ha lehetséges. Társítson minden szükséges állapotinformációkat az adataihoz. Például egy feldolgozás alatt álló megrendelés valószínűleg társítva van egy `state` taggal. Egy függvény az adott állapot alapján feldolgozhat egy rendelést, miközben maga a függvény állapota változatlan marad. 

A idempotens functions használata különösen ajánlott időzítő eseményindítókkal. Ha például olyan dolog van, amely naponta egyszer kell futnia, írja meg, hogy a nap folyamán bármikor fusson ugyanazzal az eredménnyel. A függvény akkor léphet ki, ha egy adott nap nem működik. Ha egy korábbi Futtatás nem fejeződött be, a következő futtatásnak kell megadnia, ahol abbahagyta.


### <a name="write-defensive-functions"></a>Védelmi függvények írása

Tegyük fel, hogy a függvényt bármikor felmerülhet. Tervezze meg a függvényeket úgy, hogy a következő végrehajtás során egy korábbi sikertelen pontról folytassanak. Vegyünk egy olyan forgatókönyvet, amely a következő műveleteket igényli:

1. Egy adatbázis 10 000-sorainak lekérdezése.
2. Hozzon létre egy üzenetsor-üzenetet az egyes sorokhoz a sor további feldolgozásához.
 
Attól függően, hogy milyen összetett a rendszer, lehetséges, hogy: a kapcsolódó alsóbb szintű szolgáltatások rosszul viselkednek, a hálózati kimaradások, vagy a kvóta korlátai is megszülettek. Ezek mindegyike bármikor hatással lehet a függvényre. Meg kell terveznie a feladatait.

Hogyan reagál a kód, ha hiba lép fel az elemek 5 000-es beszúrását követően a feldolgozáshoz? Egy megadott készlet elemeinek nyomon követése. Ellenkező esetben a következő alkalommal is beszúrhatja őket. Ez komoly hatással lehet a munkahelyi folyamatra. 

Ha egy üzenetsor-elem már fel lett dolgozva, a függvény nem lehet op.

Használja ki a Azure Functions platformon használt összetevőkhöz már megadott védelmi mértékeket. Tekintse meg például az [Azure Storage üzenetsor-eseményindítók és-kötések](functions-bindings-storage-queue.md#trigger---poison-messages)dokumentációjában található **méreg üzenetsor-üzeneteinek kezelését** ismertető részt. 

## <a name="scalability-best-practices"></a>Méretezhetőség – ajánlott eljárások

Számos tényező befolyásolja a Function app-méretezési példányait. A részletek a [függvények skálázására](functions-scale.md)szolgáló dokumentációban találhatók.  Az alábbiakban néhány ajánlott eljárás használható a functions-alkalmazások optimális méretezhetőségének biztosításához.

### <a name="share-and-manage-connections"></a>Kapcsolatok megosztása és kezelése

Ha lehetséges, használja újra a külső erőforrásokhoz való kapcsolódást.  Lásd: [kapcsolatok kezelése Azure Functionsban](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne keverje a tesztet és a termelési kódot ugyanabban a Function alkalmazásban

Functions-megosztási erőforrásokon belüli függvények. A memória például meg van osztva. Ha éles környezetben használ egy Function alkalmazást, ne adjon hozzá teszttel kapcsolatos függvényeket és erőforrásokat. Váratlan terhelést okozhat a termelési kód végrehajtása során.

Ügyeljen arra, hogy milyen terhelést kell betöltenie az üzemi függvények alkalmazásaiba. A memória átlaga az alkalmazás egyes funkciói között.

Ha a megosztott szerelvény több .NET-függvényben is szerepel, akkor egy közös megosztott mappában kell elhelyeznie. Az alábbi példához hasonló utasítással hivatkozhat a szerelvényre, ha C# parancsfájlokat (. CSX) használ: 

    #r "..\Shared\MyAssembly.dll". 

Ellenkező esetben a függvények közötti eltérő működést biztosító bináris fájlok véletlen üzembe helyezése könnyen elvégezhető.

Ne használja a részletes naplózást az éles kódban. Negatív hatással van a teljesítményre.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kód használata, de ne blokkolja a hívásokat

Az aszinkron programozás ajánlott eljárás. Azonban mindig ne hivatkozzon a `Result` tulajdonságra, vagy hívja meg a `Wait` metódust egy `Task` példányon. Ez a módszer a szál kimerülését eredményezheti.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Üzenetek fogadása kötegben, amikor csak lehetséges

Egyes eseményindítók, például az Event hub lehetővé teszik egy köteg üzenet fogadását egyetlen meghíváskor.  A kötegelt üzenetek sokkal jobb teljesítményt biztosítanak.  A `host.json` fájlban a maximális batch-méretet a [Host. JSON dokumentációjában](functions-host-json.md) részletesen konfigurálhatja.

A C# függvények esetében a típust erősen gépelt tömbre módosíthatja.  Például ahelyett, hogy `EventData sensorEvent` a metódus aláírását `EventData[] sensorEvent`.  Más nyelvek esetében explicit módon be kell állítania a `function.json` kardinális tulajdonságot a `many` értékre, hogy az [itt látható módon](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)engedélyezze a kötegelt feldolgozást.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>A gazdagép viselkedésének konfigurálása a Egyidejűség jobb kezelésére

A Function app `host.json` fájlja lehetővé teszi a gazdagép-futtatókörnyezet és az aktiválási viselkedés konfigurációját.  A Batch-viselkedésen kívül számos eseményindító esetében is kezelheti a párhuzamosságot.  Az ilyen beállításokban szereplő értékek gyakran módosítják az egyes példányok megfelelő méretezését a meghívott függvények igényei szerint.

A Hosts fájlban lévő beállítások a függvény *egyetlen példányán* belül az alkalmazáson belüli összes függvényre érvényesek. Ha például 2 HTTP-függvénnyel rendelkező Function-alkalmazással és egyidejű kéréssel 25-re van beállítva, akkor a HTTP-triggerre irányuló kérelem a közös 25 egyidejű kérelemre is beleszámít.  Ha a Function alkalmazás 10 példányra van méretezve, a 2 függvény gyakorlatilag lehetővé teszi a 250 egyidejű kérések használatát (10 példány * 25 egyidejű kérelem/példány).

**HTTP-egyidejűségi gazdagép beállításai**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

[A gazdagép konfigurációs dokumentumában](functions-host-json.md)más gazdagép-konfigurációs beállítások is megtalálhatók.

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

* [Kapcsolatok kezelése a Azure Functionsban](manage-connections.md)
* [Azure App Service ajánlott eljárások](../app-service/app-service-best-practices.md)
