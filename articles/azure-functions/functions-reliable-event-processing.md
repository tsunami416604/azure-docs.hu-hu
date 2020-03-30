---
title: Az Azure Functions megbízható eseményfeldolgozása
description: Az Azure Functions ben hiányzó Eseményközpont-üzenetek elkerülése
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561867"
---
# <a name="azure-functions-reliable-event-processing"></a>Az Azure Functions megbízható eseményfeldolgozása

Az eseményfeldolgozás a kiszolgáló nélküli architektúrával kapcsolatos leggyakoribb forgatókönyvek egyike. Ez a cikk ismerteti, hogyan hozhat létre egy megbízható üzenetprocesszor t, az Azure Functions az üzenetek elvesztésének elkerülése érdekében.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Az elosztott rendszerekben az eseményfolyamok kihívásai

Vegyünk egy olyan rendszert, amely másodpercenként 100 esemény állandó sebességgel küld eseményeket. Ilyen ütemben, perceken belül több párhuzamos függvénypéldányok is fogyasztanak a bejövő 100 események másodpercenként.

Azonban a következő kevésbé optimális feltételek bármelyike lehetséges:

- Mi történik, ha az esemény közzétevője sérült eseményt küld?
- Mi a teendő, ha a Functions-példány nem kezelt kivételekkel találkozik?
- Mi történik, ha egy downstream rendszer offline állapotba kerül?

Hogyan kezeli ezeket a helyzeteket, miközben megőrzi az alkalmazás átviteli?

A várólisták kal a megbízható üzenetküldés természetesen jön. Ha egy Functions eseményindítóval párosítja, a függvény zárolást hoz létre a várólistaüzenetben. Ha a feldolgozás sikertelen, a zárolás felszabadul, hogy egy másik példány újra megpróbálja a feldolgozást. A feldolgozás addig folytatódik, amíg az üzenet kiértékelése sikeres nem lesz, vagy hozzá nem adódik egy méregvárólistához.

Még akkor is, ha egyetlen üzenet üzenet marad egy újrapróbálkozási ciklusban, más párhuzamos végrehajtások továbbra is a fennmaradó üzenetek várólistába állításának törlését. Az eredmény az, hogy a teljes átviteli nem befolyásolja egy rossz üzenet. A tárolási várólisták azonban nem garantálják a rendezést, és nincsenek optimalizálva az Event Hubs által igényelt nagy átviteli igényekre.

Ezzel szemben az Azure Event Hubs nem tartalmaz zárolási koncepciót. Annak érdekében, hogy a funkciók, például a nagy átviteli sebességű, több fogyasztói csoportok és a visszajátszás-képesség, Event Hubs események viselkednek, mint egy videó lejátszó. Az események et partíciónként az adatfolyam egyetlen pontjáról olvassa be a rendszer. A mutatóból előre vagy hátra olvashatunk arról a helyről, de az események feldolgozásához át kell helyeznie a mutatót.

Ha hibák lépnek fel egy adatfolyamban, ha úgy dönt, hogy a mutatót ugyanazon a helyen tartja, az eseményfeldolgozás blokkolva lesz, amíg a mutató nem lesz előre. Más szóval, ha a mutató leáll az egyetlen esemény feldolgozásával kapcsolatos problémák kezelésére, a feldolgozatlan események felhalmozódnak.

Az Azure Functions elkerüli a holtpontokat azáltal, hogy az adatfolyam mutatóját a sikeres vagy sikertelen től függetlenül előreviszi. Mivel a mutató folyamatosan halad, a funkcióknak megfelelően kell kezelnie a hibákat.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Hogyan használja fel az Azure Functions az Event Hubs-eseményeket?

Az Azure Functions az Event Hub-eseményeket használja fel a következő lépések en való kerékpározás közben:

1. A mutató jön létre, és megmarad az Azure Storage-ban az eseményközpont minden egyes partíciójára.
2. Amikor új üzenetek érkeznek (alapértelmezés szerint kötegben), az állomás megpróbálja elindítani a függvényt az üzenetek kötegével.
3. Ha a függvény befejezi a végrehajtást (kivétellel vagy kivétel nélkül), a mutató előrelép, és egy ellenőrzőpont kerül mentésre a tárfiókba.
4. Ha a feltételek megakadályozzák a függvény végrehajtásának befejezését, az állomás nem tudja továbbhaladni a mutatót. Ha a mutató nem speciális, majd később ellenőrzi a végén feldolgozása ugyanazokat az üzeneteket.
5. 2–4.

Ez a viselkedés néhány fontos pontot tár fel:

- *A nem kezelt kivételek az üzenetek elvesztését okozhatják.* A kivételt eredményező végrehajtások továbbra is a mutató előrehaladását eredményezik.
- *A funkciók legalább egyszeri szállítást garantálnak.* Előfordulhat, hogy a kódnak és a függő rendszereknek figyelembe kell [venniük azt a tényt, hogy ugyanaz az üzenet kétszer is érkezhet.](./functions-idempotent.md)

## <a name="handling-exceptions"></a>Kivételek kezelése

Általános szabály, hogy minden függvénynek tartalmaznia kell egy [try/catch blokkot](./functions-bindings-error-pages.md) a legmagasabb szintű kódon. Pontosabban az Event Hubs eseményeket használó `catch` összes függvénynek rendelkeznie kell egy blokkkal. Így egy kivétel esetén a catch blokk kezeli a hibát, mielőtt a mutató haladna.

### <a name="retry-mechanisms-and-policies"></a>Újrapróbálkozási mechanizmusok és házirendek

Néhány kivétel átmeneti jellegű, és nem jelenik meg újra, ha egy műveletet próbál nak újra pillanatokkal később. Ez az oka annak, hogy az első lépés mindig a művelet újrapróbálkozása. Írhatna újrafeldolgozási szabályokat saját maga, de annyira általánosak, hogy számos eszköz áll rendelkezésre. Ezek a könyvtárak használatával robusztus újrapróbálkozási házirendek definiálhatók, amelyek szintén segíthetnek a feldolgozási sorrend megőrzésében.

A hibakezelési kódtárak bevezetése a függvényekben lehetővé teszi az alapvető és a speciális újrapróbálkozási házirendek meghatározását. Például megvalósíthat egy olyan házirendet, amely a következő szabályokkal illusztrált munkafolyamatot követi:

- Próbáljon meg háromszor beszúrni egy üzenetet (potenciálisan az újrapróbálkozások közötti késleltetéssel).
- Ha az összes újrapróbálkozás oka egy hiba, majd adjon hozzá egy üzenetet egy várólistába, így a feldolgozás folytatódhat az adatfolyamon.
- A sérült vagy feldolgozatlan üzeneteket később kezeli a program.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) egy példa a rugalmasság és az átmeneti hibakezelési könyvtár C# alkalmazások.

Ha előre megfelelt C# osztálytárak, [kivételszűrők](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) lehetővé teszik, hogy kódot futtatni, ha nem kezelt kivétel fordul elő.

A kivételszűrők használatát szemléltető minták az [Azure WebJobs SDK-tárházban](https://github.com/Azure/azure-webjobs-sdk/wiki) érhetők el.

## <a name="non-exception-errors"></a>Nem kivételhibák

Egyes problémák akkor is felmerülnek, ha nincs jelen hiba. Vegyünk például egy olyan hibát, amely egy végrehajtás közepén fordul elő. Ebben az esetben, ha egy függvény végrehajtása nem fejeződik be, az eltolási mutató soha nem halad előre. Ha a mutató nem halad előre, akkor a sikertelen végrehajtás után futó példányok továbbra is ugyanazokat az üzeneteket olvassák. Ez a helyzet "legalább egyszer" garanciát jelent.

Annak biztosítéka, hogy minden üzenet feldolgozása legalább egyszer azt jelenti, hogy egyes üzenetek egynél többször is feldolgozhatók. A funkcióalkalmazásoknak tisztában kell lenniük ezzel a lehetőséggel, és az [idempotencia elveire kell épülniük.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Végrehajtás leállítása és újraindítása

Bár néhány hiba elfogadható, mi van, ha az alkalmazás jelentős hibákat tapasztal? Előfordulhat, hogy le szeretné állítani az események indítását, amíg a rendszer el nem éri a kifogástalan állapotot. Miután a lehetőséget, hogy szünet elad feldolgozás gyakran érhető el egy megszakító minta. Az áramkör-megszakító minta lehetővé teszi, hogy az alkalmazás "megtörje az eseményfolyamat áramkörét", és egy későbbi időpontban folytatódjon.

Egy eseményfolyamatban az áramkör-megszakító megvalósításához két darabszükséges:

- Megosztott állapot az összes példányban az áramkör állapotának nyomon követéséhez és figyeléséhez
- Fő folyamat, amely képes kezelni a kapcsolati állapotot (nyitott vagy zárt)

A megvalósítás részletei eltérőek lehetnek, de az állapot megosztásához szükség van egy tárolómechanizmusra. Dönthet úgy, hogy az Azure Storage-ban, a Redis-gyorsítótárban vagy bármely más, a függvények gyűjteménye által elérhető fiókban tárolja az állapotot.

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) vagy [a tartós entitások](./durable/durable-functions-overview.md) természetes illeszkedést biztosítanak a munkafolyamat és a körállapot kezeléséhez. Más szolgáltatások is működhetnek, de ebben a példában logikai alkalmazásokat használnak. A logikai alkalmazások használatával szüneteltetheti és újraindíthatja a függvény végrehajtását, így az áramkör-megszakító minta megvalósításához szükséges vezérlő.

### <a name="define-a-failure-threshold-across-instances"></a>Hibaküszöbérték definiálása példányok között

Több példány egyidejű feldolgozása események egyidejű figyelembevétele érdekében a megosztott külső állapot megőrzése szükséges az áramkör állapotának figyeléséhez.

Egy olyan szabály, amelyet esetleg alkalmaz, a következőket kényszerítheti ki:

- Ha 30 másodpercen belül több mint 100 esetleges hiba történik az összes példányban, akkor szakítsa meg az áramkört, és állítsa le az új üzenetek indítását.

A megvalósítás részletei az igényeinek megfelelően változnak, de általában létrehozhat egy olyan rendszert, amely:

1. A tárfiók (Azure Storage, Redis stb.) naplózási hibáinak naplózása
1. Új hiba naplózásakor ellenőrizze a gördülőszám, hogy a küszöbérték teljesül-e (például több mint 100 az elmúlt 30 másodpercben).
1. Ha a küszöbérték teljesül, akkor egy eseményt az Azure Event Grid értesíti a rendszer a kapcsolatbontást.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>A körállapot kezelése az Azure Logic-alkalmazásokkal

A következő leírás kiemeli az egyik módja, hogy hozzon létre egy Azure Logic App a Functions alkalmazás feldolgozásának leállításához.

Az Azure Logic Apps beépített összekötőkkel rendelkezik a különböző szolgáltatásokhoz, állapotalapú vezénylési funkciókkal rendelkezik, és természetes választás a körállapot kezeléséhez. Észlelése után a kapcsolatmegszakad, hozhat létre egy logikai alkalmazást a következő munkafolyamat megvalósításához:

1. Eseményrács-munkafolyamat aktiválása és az Azure-függvény leállítása (az Azure Resource-összekötővel)
1. Értesítési e-mail küldése, amely lehetőséget tartalmaz a munkafolyamat újraindítására

Az e-mail címzettje megvizsgálhatja az áramkör állapotát, és adott esetben az értesítési e-mailben található hivatkozáson keresztül újraindíthatja az áramkört. Ahogy a munkafolyamat újraindítja a függvényt, az üzenetek feldolgozása az utolsó Event Hub ellenőrzőpontról történik.

Ezzel a megközelítéssel nem vesznek el üzenetek, az összes üzenet feldolgozása sorrendben, és megszakíthatja az áramkört, amíg szükséges.

## <a name="resources"></a>Források

- [Megbízható eseményfeldolgozási minták](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Functions megszakító](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [Az Azure Functions hibakezelése](./functions-bindings-error-pages.md)
- [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](./functions-twitter-email.md)
