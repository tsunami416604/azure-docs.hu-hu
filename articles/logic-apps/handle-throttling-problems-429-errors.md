---
title: Szabályozási problémák vagy '429 – Túl sok kérés– hibák kezelése)
description: A sávszélesség-szabályozási problémák vagy a "HTTP 429 Túl sok kérelem" hibák megkerülése az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272678"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Szabályozási problémák (429 – "Túl sok kérelem" hiba) kezelése az Azure Logic Apps alkalmazásban

Az [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)a logikai alkalmazás a ["HTTP 429 túl sok kérelem" hibaüzenetet](https://developer.mozilla.org/docs/Web/HTTP/Status/429) ad vissza, ha szabályozást tapasztal, ami akkor történik, ha a kérelmek száma meghaladja a cél képes kezelni egy adott idő alatt. Szabályozás a problémák, például a késleltetett adatfeldolgozás, csökkentett teljesítménysebesség és a hibák, például meghaladja a megadott újrapróbálkozási házirend.

![Szabályozás az SQL Server-összekötőben](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Az alábbiakban a logikai alkalmazás által tapasztalt gyakori szabályozási típusokat ismertetik:

* [Logikai alkalmazás](#logic-app-throttling)
* [Csatlakozó](#connector-throttling)
* [Célszolgáltatás vagy -rendszer](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Logikai alkalmazás szabályozása

Az Azure Logic Apps szolgáltatás saját [átviteli korláttal rendelkezik.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Így ha a logikai alkalmazás túllépi ezeket a korlátokat, a logikai alkalmazás erőforrás lesz szabályozva, nem csak egy adott példány vagy futtatás.

A szabályozással kapcsolatos események ezen a szinten, ellenőrizze a logikai alkalmazás **Metrikák** ablaktáblában az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás **menüjében**válassza a Figyelés csoport **Metrikák ( Metrikák)** lehetőséget.

1. A **Diagram címe csoportban**válassza **a Metrika hozzáadása** lehetőséget, hogy egy másik mutatót adjon hozzá a meglévőhöz.

1. Az első **metrikasávmecsiklata** lista válassza **a Művelet szabályozása események**lehetőséget. A második **metrikasávmecsikla** listájában válassza az **Eseményindító szabályozott események**lehetőséget.

A szabályozás ezen a szinten történő kezeléséhez a következő lehetőségek közül választhat:

* Korlátozza az egyidejűleg futtatható logikai alkalmazáspéldányok számát.

  Alapértelmezés szerint, ha a logikai alkalmazás eseményindító feltétele egyszerre többször is teljesül, a logikai alkalmazás több eseményindító példánya egyidejűleg vagy *párhuzamosan*fut. Ez a viselkedés azt jelenti, hogy minden eseményindító példány aktiválódik, mielőtt az előző munkafolyamat-példány futása befejeződik.

  Bár az egyidejűleg futtatható eseményindítópéldányok alapértelmezett száma [korlátlan](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), korlátozhatja ezt a számot [az eseményindító egyidejűségi beállításának bekapcsolásával,](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)és szükség esetén az alapértelmezett értéktől eltérő korlátot is választhat.

* Nagy átviteli üzemmód engedélyezése.

  A logikai alkalmazások alapértelmezett korláttal rendelkeznek [az 5 perces gördülő időközön futtatható műveletek számára.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Ha ezt a korlátot a műveletek maximális számára szeretné növelni, kapcsolja be a [nagy átviteli üzemmódot](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) a logikai alkalmazásban.

* Tiltsa le a tömbkötegelést ("felosztva") viselkedést az eseményindítókban.

  Ha egy eseményindító egy tömböt ad vissza a feldolgozandó fennmaradó munkafolyamat-műveletekhez, az eseményindító [ **Felosztása beállítás** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) felosztja a tömbelemeket, és elindítja az egyes tömbelemek munkafolyamat-példányát, így gyakorlatilag több egyidejű futtatást indít el a [ **Felosztás a** korlátig.](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) A szabályozás szabályozásának szabályozásához kapcsolja ki a **Felosztás a** viselkedést, és a logikai alkalmazás egyetlen hívással dolgozza fel a teljes tömböt, és ne kezeljen egyetlen elemet hívásonként.

* A műveletek et kisebb logikai alkalmazásokba kell újraelszámítani.

  Ahogy korábban említettük, a logikai alkalmazás csak egy [alapértelmezett számú művelet, amely fut egy 5 perces időszakban.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Bár a korlát ot [a nagy átviteli sebességű mód](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)engedélyezésével növelheti, azt is mérlegelheti, hogy szeretné-e a logikai alkalmazás műveletekkisebb logikai alkalmazásokra való bontását, hogy az egyes logikai alkalmazásokban futó műveletek száma a korlát alatt maradjon. Így csökkentheti a terhet egy logikai alkalmazás-erőforrás, és a terhelés több logikai alkalmazások között oszlatása. Ez a megoldás jobban működik olyan műveletekhez, amelyek nagy adatkészleteket kezelnek, vagy olyan sok egyidejűleg futó műveletet, hurok ismétlést vagy műveletet hajtanak végre az egyes ciklusismétléseken belül, amelyek meghaladják a végrehajtási korlátot.

  Ez a logikai alkalmazás például minden munkát megtesz, hogy táblákat szerezzen be egy SQL Server-adatbázisból, és lekéri a sorokat az egyes táblákból. A **Minden** ciklushoz párhuzamosan végighalad az egyes táblázatokon, így a **Sorok begetése** művelet az egyes táblázatok sorait adja vissza. A táblákban lévő adatmennyiség alapján ezek a műveletek meghaladhatják a műveletvégrehajtás végrehajtási korlátját.

  ![Logikai alkalmazás "előtte" refactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  A refactoring után a logikai alkalmazás most antól egy szülő és gyermek logikai alkalmazás. A szülő lekéri a táblákat az SQL Serverből, majd meghívja az egyes táblákhoz a gyermekek et a sorok lekéréséhez:

  ![Logikai alkalmazás létrehozása egyetlen művelethez](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  A szülőlogikai alkalmazás által megnevezett gyermeklogikai alkalmazás a következő sorok lekéréséhez az egyes táblázatokhoz:

  ![Másik logikai alkalmazás létrehozása egy második művelethez](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Csatlakozó szabályozása

Minden egyes összekötő saját szabályozási korlátokkal rendelkezik, amelyek az összekötő műszaki referenciaoldalán találhatók. Például az [Azure Service Bus-összekötő](https://docs.microsoft.com/connectors/servicebus/) rendelkezik egy szabályozási korlát, amely lehetővé teszi, hogy legfeljebb 6000 hívások percenként, míg az SQL Server-összekötő [rendelkezik szabályozási korlátok, amelyek a művelet típusától függően változnak.](https://docs.microsoft.com/connectors/sql/)

Egyes eseményindítók és műveletek, például a HTTP, rendelkezik egy ["újrapróbálkozási házirend",](../logic-apps/logic-apps-exception-handling.md#retry-policies) amely az [újrapróbálkozási házirend korlátok](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) alapján a kivételkezelés megvalósítása. Ez a házirend határozza meg, hogy az eseményindító vagy művelet újrapróbálkozik-e a kéréssel, ha az eredeti kérelem sikertelen, vagy időtúllépése van, és 408, 429 vagy 5xx választ eredményez. Így ha a szabályozás elindul, és 429-es hibát ad vissza, a Logic Apps az újrapróbálkozási szabályzatot követi, ahol támogatott.

Ha meg szeretné tudni, hogy egy eseményindító vagy művelet támogatja-e az újrapróbálkozási házirendeket, ellenőrizze az eseményindító vagy a művelet beállításait. Az eseményindító vagy a művelet újrapróbálkozási kísérleteinek megtekintéséhez nyissa meg a logikai alkalmazás futtatási előzményeit, válassza ki az áttekinteni kívánt futtatást, és bontsa ki az eseményindítót vagy műveletet a bemenetek, kimenetek és az újrapróbálkozások részleteinek megtekintéséhez:

![A művelet futtatási előzményeinek, újrapróbálkozásainak, bemenetei és kimenetei megtekintése](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Bár az újrapróbálkozási előzmények hibainformációkat tartalmaznak, előfordulhat, hogy nem tudja megkülönböztetni az összekötő szabályozását és [a célszabályozást.](#destination-throttling) Ebben az esetben előfordulhat, hogy át kell tekintenie a válasz adatait, vagy el kell végeznie néhány szabályozási időköz-számításokat a forrás azonosításához.

A globális, több-bérlős Azure Logic Apps szolgáltatás logikai alkalmazásai esetében a szabályozás a *kapcsolat* szintjén történik. Így például az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)futó logikai alkalmazások esetében a szabályozás továbbra is megtörténik a nem ISE-kapcsolatok esetében, mert azok a globális, több-bérlős Logic Apps szolgáltatásban futnak. Az ISE-összekötők által létrehozott ISE-kapcsolatok azonban nem szabályozottak, mert az ISE-ben futnak.

A szabályozás ezen a szinten történő kezeléséhez a következő lehetőségek közül választhat:

* Állítson be több kapcsolatot egyetlen művelethez, hogy a logikai alkalmazás feldolgozza az adatokat feldolgozásra.

  Ebben a beállításban fontolja meg, hogy eloszthatja-e a számítási feladatot úgy, hogy egy művelet kéréseit több kapcsolatközött osztja el ugyanahhoz a célhoz ugyanazzal a hitelesítő adatokkal.

  Tegyük fel például, hogy a logikai alkalmazás táblákat kap le egy SQL Server-adatbázisból, majd lekéri a sorokat az egyes táblákból. A feldolgozandó sorok száma alapján több kapcsolatot és több et használhat **Minden** ciklusban a sorok teljes számának felosztásához kisebb készletekre feldolgozásra. Ez a forgatókönyv két **hurkot** használ a sorok teljes számának kettéosztásához. Az első **Minden** ciklushoz egy olyan kifejezést használ, amely az első felét kapja. A másik **minden** ciklushoz egy másik kifejezést használ, amely a második felét kapja, például:<p>

    * 1. kifejezés: A `take()` függvény egy gyűjtemény elejét kapja. További információt a [ **`take()`** funkcióban](workflow-definition-language-functions-reference.md#take)talál.

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 2. kifejezés: A `skip()` függvény eltávolítja a gyűjtemény elejét, és visszaadja az összes többi elemet. További információt a [ **`skip()`** funkcióban](workflow-definition-language-functions-reference.md#skip)talál.

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Íme egy vizuális példa, amely bemutatja, hogyan használhatja ezeket a kifejezéseket:

    ![Több kapcsolat létrehozása és használata egyetlen művelethez](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Minden művelethez hozzon létre egy másik kapcsolatot.

  Ebben a beállításban fontolja meg, hogy eloszthatja-e a számítási feladatokat az egyes műveletek kéréseinek a saját kapcsolaton keresztül történő szétosztásával, még akkor is, ha a műveletek ugyanahhoz a szolgáltatáshoz vagy rendszerhez kapcsolódnak, és ugyanazokat a hitelesítő adatokat használják.

  Tegyük fel például, hogy a logikai alkalmazás lekéri a táblákat egy SQL Server-adatbázisból, és lekéri az egyes táblák minden sorát. Külön kapcsolatokat használhat, hogy a táblák beszerzése egy kapcsolatot használjon, míg az egyes sorok beszerzése egy másik kapcsolatot használ.

  ![Minden művelethez más-más kapcsolatok létrehozása és használata](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Módosítsa az egyidejűséget vagy a párhuzamosságt egy ["Minden hez" cikluson](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Alapértelmezés szerint a "Minden" ciklus ismétlések futnak egy időben akár az [egyidejűségi korlátot](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha van egy összekötő, amely egyre szabályozott belül egy "Minden" hurok, csökkentheti a párhuzamosan futó ciklus ismétlések számát. További információt az alábbi témakörökben talál:
  
  * ["Minden" hurkok - változás egyidejűség vagy fuss egymás után](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Munkafolyamat-definíció nyelvi séma – minden ciklushoz](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Munkafolyamat-definíció nyelvi séma – a "Minden hez" ciklus egyidejűsítésének módosítása](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Munkafolyamat-definíció nyelvi séma – "Minden hez" hurkok futtatása egymás után](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Célszolgáltatás vagy rendszerszabályozás

Míg az összekötő saját sávszélesség-szabályozási korlátokkal rendelkezik, a célszolgáltatás vagy az összekötő által megnevezett rendszer is rendelkezhet szabályozási korlátokkal. A Microsoft Exchange Server egyes API-k például szigorúbb szabályozási korlátokkal rendelkeznek, mint az Office 365 Outlook-összekötő.

Alapértelmezés szerint a logikai alkalmazás példányai és a példányokon belüli hurkok vagy ágak *párhuzamosan*futnak. Ez a viselkedés azt jelenti, hogy több példány is meghívhat egy-egy végpontot egyszerre. Minden példány nem ismeri a másik létezését, így a sikertelen műveletek újrapróbálkozási kísérlete [olyan versenyfeltételeket](https://en.wikipedia.org/wiki/Race_condition) hozhat létre, ahol egyszerre több hívás próbál futni, de a sikeres ség érdekében ezeknek a hívásoknak meg kell érkezniük a célszolgáltatáshoz vagy -rendszerhez, mielőtt a szabályozás megtörténik.

Tegyük fel például, hogy van egy 100 elemből tükkelést tartalmazó tömbje. A "minden hez" ciklust a tömbön keresztül kell végigvezetni, és be kell kapcsolnia a ciklus egyidejűségi vezérlését, így a párhuzamos ismétlések számát 20-ra vagy az [aktuális alapértelmezett korlátra](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)korlátozhatja . A cikluson belül egy művelet beszúr egy elemet a tömbből egy SQL Server-adatbázisba, amely másodpercenként csak 15 hívást engedélyez. Ebben a forgatókönyvben a szabályozási probléma, mert az újrapróbálkozások hátraléka kiépítése, és soha nem kap futtatni.

Ez a táblázat azt az ütemtervet ismerteti, hogy mi történik a ciklusban, ha a művelet újrapróbálkozási időköze 1 másodperc:

| Időpont | Futó műveletek száma | Sikertelen műveletek száma | Várakozási ismétlések száma |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 másodperc | 20 lapka | 5 nem, az SQL-korlát miatt | 5 újrapróbálkozás |
| T + 0,5 másodperc | 15 lapka, a korábbi 5 újrapróbálkozásvárakozás miatt | Mind a 15 sikertelen, mivel a korábbi SQL-korlát még érvényben van további 0,5 másodpercig | 20 újrapróbálkozás <br>(előző 5 + 15 új) |
| T + 1 másodperc | 20 lapka | 5 sikertelen és előző 20 újrapróbálkozás az SQL-korlát miatt | 25 újrapróbálkozás (előző 20 + 5 új)
|||||

A szabályozás ezen a szinten történő kezeléséhez a következő lehetőségek közül választhat:

* Hozzon létre logikai alkalmazásokat úgy, hogy mindegyik egyetlen műveletet kezeljen.

  * Folytatva a példa SQL Server forgatókönyv ebben a szakaszban, létrehozhat egy logikai alkalmazást, amely tömbelemeket helyez egy várólistába, például egy [Azure Service Bus-várólista.](../connectors/connectors-create-api-servicebus.md) Ezután hozzon létre egy másik logikai alkalmazást, amely csak a beszúrási műveletet hajtja végre a várólistában lévő minden elemhez. Így csak egy logikai alkalmazáspéldány fut egy adott időpontban, amely vagy befejezi a beszúrási műveletet, és a várólista következő elemére lép, vagy a példány 429 hibát kap, de nem kísérli meg a nem produktív újrapróbálkozásokat.

  * Hozzon létre egy szülő logikai alkalmazást, amely minden művelethez gyermek- vagy beágyazott logikai alkalmazást hív meg. Ha a szülőnek különböző gyermekalkalmazásokat kell hívnia a szülő eredménye alapján, használhat feltételműveletet, vagy válthat olyan műveletet, amely meghatározza, hogy melyik gyermekalkalmazást hívja meg. Ez a minta segíthet a hívások és műveletek számának csökkentésében.

    Tegyük fel például, hogy két logikai alkalmazással rendelkezik, amelyek mindegyike rendelkezik egy lekérdezési eseményindítóval, amely percenként ellenőrzi az e-mail fiókját adott tárgyban, például a "Sikeres" vagy a "Hiba". Ez a beállítás óránként 120 hívást eredményez. Ehelyett ha létrehoz egy szülő logikai alkalmazást, amely percenként lekérdezi, de meghívja a gyermek logikai alkalmazást, amely az alapján fut, hogy a tárgy "Sikeres" vagy "Sikertelen", akkor a lekérdezési ellenőrzések számát a felére, vagy 60-ra csökkenti ebben az esetben.

* Kötegelt feldolgozás beállítása.

  Ha a célszolgáltatás támogatja a kötegelt műveleteket, a szabályozást a cikkek csoportokban vagy kötegekben történő feldolgozásával, nem pedig külön-külön is megoldhatja. A kötegelt feldolgozási megoldás megvalósításához hozzon létre egy "batch receiver" logikai alkalmazást és egy "kötegküldő" logikai alkalmazást. A kötegküldő addig gyűjti az üzeneteket vagy elemeket, amíg a megadott feltételek nem teljesülnek, majd ezeket az üzeneteket vagy elemeket egyetlen csoportba küldi. A kötegfogadó elfogadja a csoportot, és feldolgozza ezeket az üzeneteket vagy elemeket. További információt a [Csoportokban lévő folyamatküldések kötegelt feldolgozása](../logic-apps/logic-apps-batch-process-send-receive-messages.md)című témakörben talál.

* Használja a webhook-verziók eseményindítók és műveletek, nem pedig a lekérdezési verziók.

  Hogy miért? A lekérdezési eseményindító továbbra is ellenőrzi a célszolgáltatás vagy a rendszer adott időközönként. Egy nagyon gyakori intervallum, például minden másodpercben, szabályozási problémákat okozhat. Azonban egy webhook eseményindító vagy művelet, például [a HTTP Webhook,](../connectors/connectors-native-webhook.md)csak egyetlen hívást hoz létre a célszolgáltatásvagy rendszer, amely történik az előfizetés időpontjában, és kéri, hogy a cél értesíti az eseményindító vagy művelet csak akkor, ha egy esemény történik. Így az eseményindítónak vagy műveletnek nem kell folyamatosan ellenőriznie a célt.
  
  Így ha a célszolgáltatás vagy a rendszer támogatja a webhookokat, vagy olyan összekötőt biztosít, amely webhook-verzióval rendelkezik, ez a beállítás jobb, mint a lekérdezési verzió használata. A webhook-eseményindítók és -műveletek `ApiConnectionWebhook` azonosításához ellenőrizze, hogy rendelkeznek-e típussal, vagy hogy nem szükséges-e ismétlődést megadniuk. További információ: [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) and [APIConnectionWebhook action](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps korlátairól és konfigurációjáról](../logic-apps/logic-apps-limits-and-config.md)
