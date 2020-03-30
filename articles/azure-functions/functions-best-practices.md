---
title: Gyakorlati tanácsok az Azure Functionshez
description: Ismerje meg az Azure Functions ajánlott eljárásokat és mintákat.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277777"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Az Azure Functions teljesítményének és megbízhatóságának optimalizálása

Ez a cikk útmutatást nyújt a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) függvényalkalmazások teljesítményének és megbízhatóságának javításához.  

## <a name="general-best-practices"></a>Általános ajánlott eljárások

Az alábbiakban gyakorlati tanácsok az Azure Functions használatával a kiszolgáló nélküli megoldások létrehozásának és megtervezésének ajánlott eljárásai.

### <a name="avoid-long-running-functions"></a>Kerülje a hosszú ideig futó funkciókat

A nagy, hosszan futó függvények váratlan időmegfutási problémákat okozhatnak. Ha többet szeretne megtudni egy adott tárhelycsomag időhatárolóiról, olvassa el a [függvényalkalmazás idő-megutazásának időtartamát.](functions-scale.md#timeout) 

Egy függvény sok Node.js függőség miatt válhat nagydá. A függőségek importálása megnövekedett betöltési időket is okozhat, amelyek nem várt időtúlképeket eredményezhetnek. A függőségek explicit és implicit módon is betöltődnek. A kód által betöltött egyetlen modul betöltheti a saját további moduljait. 

Amikor csak lehetséges, a nagy függvényeket kisebb függvénykészletekké kell újraelszámítani, amelyek együttműködnek, és gyorsan adják vissza a válaszokat. Például egy webhook vagy HTTP eseményindító függvény szükség lehet egy nyugtázás választ egy bizonyos határidőn belül; gyakori, hogy a webhooks azonnali választ igényel. A HTTP-eseményindító hasznos adatát átadhatja egy várólistába, amelyet egy várólista-eseményindító-függvény nek kell feldolgoznia. Ez a megközelítés lehetővé teszi, hogy elhalasztja a tényleges munkát, és azonnali választ ad vissza.


### <a name="cross-function-communication"></a>Keresztfunkciós kommunikáció

[A durable functions](durable/durable-functions-overview.md) és [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) az állapotátmenetek és a több funkció közötti kommunikáció kezelésére készült.

Ha nem használ tartós függvényeket vagy logikai alkalmazásokat több funkcióval való integrációhoz, akkor a legjobb, ha tárolósorokat használ a funkciók közötti kommunikációhoz. Ennek fő oka az, hogy a tárolási várólisták olcsóbbak és sokkal könnyebben kiépíthetőek, mint más tárolási lehetőségek. 

A tárolósorban lévő egyes üzenetek mérete 64 KB. Ha nagyobb üzeneteket kell átadnia a függvények között, az Azure Service Bus-várólistát a standard szinten legfeljebb 256 KB-os, a prémium szinten pedig legfeljebb 1 MB méretű üzenetek támogatásához lehet használni.

A Service Bus témakörei akkor hasznosak, ha a feldolgozás előtt üzenetszűrésre van szüksége.

Az eseményközpontok hasznosak a nagy mennyiségű kommunikáció támogatásához.


### <a name="write-functions-to-be-stateless"></a>Állapotnélküli írási függvények 

A függvényeknek állapot nélkülinek és idempotensnek kell lenniük, ha lehetséges. Társítson minden szükséges állapotinformációt az adataihoz. Például egy feldolgozott rendelésnek valószínűleg `state` van egy társított tagja. Egy függvény feldolgozhat egy rendelést az adott állapot alapján, miközben maga a függvény állapot nélküli marad. 

Idempotent funkciók különösen ajánlott időzítő eseményindítók. Például, ha van valami, hogy feltétlenül kell futtatni naponta egyszer, írja meg, így futtatható bármikor a nap folyamán ugyanazt az eredményt. A funkció akkor léphet ki, ha egy adott napon nincs munka. Akkor is, ha egy korábbi futtatás nem fejeződött be, a következő futtatásnak ott kell tartania, ahol abbahagyta.


### <a name="write-defensive-functions"></a>Védelmi függvények írása

Tegyük fel, hogy a függvény bármikor kivételt kaphat. Tervezze meg a funkciókat azzal a képességgel, hogy a következő végrehajtás során egy korábbi hibapontról folytathassa. Vegyünk egy forgatókönyvet, amely a következő műveleteket igényli:

1. Adatbázis 10 000 sorának lekérdezése.
2. Hozzon létre egy várólista-üzenetet minden egyes sorhoz, hogy a sorban lejjebb haladjon.
 
Attól függően, hogy mennyire összetett a rendszer, előfordulhat, hogy: részt downstream szolgáltatások rosszul viselkednek, hálózati kimaradások, vagy kvóta korlátok at elért, stb. Mindezek bármikor befolyásolhatják a funkciót. Meg kell terveznie a funkcióit, hogy felkészüljön rá.

Hogyan reagál a kód, ha hiba történik, miután 5000 ilyen elemet beillesztett egy várólistába feldolgozásra? A befejezett készlet elemeinek nyomon követése. Ellenkező esetben előfordulhat, hogy legközelebb ismét beilleszti őket. Ez a kettős beillesztés komoly hatással lehet a munkafolyamatra, ezért [a funkciókat idempotenssé teheti.](functions-idempotent.md) 

Ha egy várólistaelem már fel lett dolgozva, engedélyezze, hogy a függvény műveletmentes legyen.

Használja ki az Azure Functions platformon használt összetevőkre már biztosított védelmi intézkedéseket. Például **lásd: Méreg várólista üzenetek kezelése** a dokumentációban az [Azure Storage Queue eseményindítók és kötések.](functions-bindings-storage-queue-trigger.md#poison-messages) 

## <a name="scalability-best-practices"></a>Méretezhetőségi gyakorlati tanácsok

Számos tényező, amely befolyásolja, hogyan példányai a függvényalkalmazás méretezése. A részleteket a [függvényméretezés](functions-scale.md)dokumentációja tartalmazza.  Az alábbiakban néhány ajánlott eljárás a függvényalkalmazások optimális méretezhetőségének biztosítása érdekében.

### <a name="share-and-manage-connections"></a>Kapcsolatok megosztása és kezelése

Amikor csak lehetséges, használja fel újra a külső erőforrásokhoz való kapcsolatokat. [Megtudhatja, hogyan kezelheti a kapcsolatokat az Azure Functions ben.](./manage-connections.md)

### <a name="avoid-sharing-storage-accounts"></a>A tárfiókok megosztásának elkerülése

Amikor létrehoz egy függvényalkalmazást, hozzá kell rendelnie egy tárfiókhoz. A tárfiók-kapcsolat az [AzureWebJobsStorage alkalmazásbeállításban](./functions-app-settings.md#azurewebjobsstorage)marad meg. 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne keverje a teszt- és éleskódot ugyanabban a függvényalkalmazásban

Függvényalkalmazáson belüli függvények erőforrásokat osztanak meg. Például a memória meg van osztva. Ha éles környezetben használ függvényalkalmazást, ne adjon hozzá teszttel kapcsolatos függvényeket és erőforrásokat. Ez váratlan többletterhelést okozhat a termelési kód végrehajtása során.

Ügyeljen arra, hogy mit töltsön be az éles funkciós alkalmazásokba. A memóriát az alkalmazás egyes függvényei átlagzják.

Ha több .NET függvényben hivatkozott megosztott kódösszeállítással rendelkezik, helyezze azt egy közös megosztott mappába. Ellenkező esetben véletlenül ugyanannak a bináris fájlnak több olyan verzióját is telepítheti, amelyek eltérően viselkednek a függvények között.

Ne használjon részletes naplózást az éles kódban, amely negatív hatással van a teljesítményre.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kód használata, de a hívások blokkolásának elkerülése

Az aszinkron programozás ajánlott eljárás, különösen az I/O-műveletek blokkolása esetén.

A C#-ban mindig ne `Result` hivatkozzon a tulajdonságra vagy a metódus hívására `Wait` egy `Task` példányon. Ez a megközelítés a menetek kimerüléséhez vezethet.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Több munkavégző folyamat használata

Alapértelmezés szerint a Functions állomáspéldányai egyetlen munkavégző folyamatot használnak. A teljesítmény javítása érdekében, különösen az egyszálas runtimes, például a Python, használja a [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) a munkavégző folyamatok száma számának növelése gazdagépenként (legfeljebb 10). Az Azure Functions ezután megpróbálja egyenletesen elosztani az egyidejű függvénymeghívásokat ezek között a dolgozók között. 

A FUNCTIONS_WORKER_PROCESS_COUNT minden állomásra vonatkozik, amelyet a Functions létrehoz, amikor az alkalmazást az igényeknek megfelelően skálázhatja. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Üzenetek fogadása kötegelt formában, amikor csak lehetséges

Egyes eseményindítók, például az Event Hub lehetővé teszik az üzenetek kötegének fogadását egyetlen meghívással.  Az üzenetek kötegelése sokkal jobb teljesítményt nyújt.  A fájlmaximális kötegméretét a `host.json` [host.json referenciadokumentációban](functions-host-json.md) részletezett módon állíthatja be.

C# függvények esetén a típust erősen beírt tömbre módosíthatja.  Például, ahelyett, hogy `EventData sensorEvent` a `EventData[] sensorEvent`módszer aláírása lehet .  Más nyelvek esetében explicit módon be kell állítania a `function.json` `many` számosság tulajdonságát a kívánt sorrendben ahhoz, hogy engedélyezze a kötegelést [az itt látható módon.](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Az állomás viselkedésének konfigurálása az egyidejűség jobb kezeléséhez

A `host.json` függvényalkalmazásban lévő fájl lehetővé teszi a gazdagép futásidejű és eseményindító viselkedésének konfigurálását.  A kötegelési viselkedések mellett számos eseményindító egyidejűségét is kezelheti. Gyakran ezek a beállítások módosítása segíthet minden példány méretezése a meghívott függvények igényeinek megfelelően.

A host.json fájl beállításai az alkalmazás on-val kapcsolatos összes funkcióra vonatkoznak, a függvény *egyetlen példányán* belül. Ha például egy függvényalkalmazás két HTTP-függvényt és [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 25-re beállított kérelmeket, a HTTP-eseményindítóra vonatkozó kérés beleszámít a megosztott 25 egyidejű kérelembe.  Ha a függvényalkalmazás 10 példányra van méretezve, a két függvény hatékonyan 250 egyidejű kérelmet engedélyez (10 példány * 25 egyidejű kérelem példányonként). 

A többi állomáskonfigurációs beállítás megtalálható a [host.json konfigurációs cikkben.](functions-host-json.md)

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Kapcsolatok kezelése az Azure Functionsben](manage-connections.md)
* [Az Azure App Service gyakorlati tanácsok](../app-service/app-service-best-practices.md)
