---
title: "Esemény sorrendjét és az Azure Stream Analytics késedelmesség kezelése |} Microsoft Docs"
description: "Tudnivalók a Stream Analytics-soron vagy késői események adatfolyamban működéséről."
keywords: "nem megfelelő sorrendben, késői, események"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: f9854172e08785676a7804433d9a559e623a7b05
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Az Azure Stream Analytics esemény rendelés szempont

## <a name="understand-arrival-time-and-application-time"></a>Ismerje meg, érkezésének ideje és kérelem ideje.

Minden esemény historikus adatfolyam események időbélyeg kapja. Az Azure Stream Analytics Timestamp típusú eseményekhez érkezésének ideje vagy alkalmazás idő rendeli hozzá. A "System.Timestamp" oszlop van rendelve az esemény időbélyegzője. Érkezésének ideje hozzá van rendelve a bemeneti forrásnál, ha az esemény eléri a forrás. Érkezési ideje EventEnqueuedTime Eseményközpont bemeneti és [blob utolsó módosítás időpontja](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) blob bemeneti. Alkalmazási hozzá van rendelve, ha az esemény jön létre, és a tartalom egy részét. Alkalmazás ideje eseményeket feldolgozását, használja a "Időbélyeg által" záradék a select lekérdezés. Hiányzik a "Időbélyeg által" záradék, ha érkezésének ideje eseményeket dolgoznak fel. Érkezésének ideje elérhető EventEnqueuedTime tulajdonság használatával az event hubs és a blob bemeneti BlobLastModified tulajdonság használatával. Az Azure Stream Analytics a Timestamp típusú sorrendben kimenetet, és néhány beállítás nem megfelelő sorrendben adatok kezelésére szolgál.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Az Azure Stream Analytics kezelésére vonatkozó több adatfolyam

Az Azure Stream Analytics-feladat egyesíti, beleértve néhány esetben több ütemtervet származó események

* A több partíciót állít elő. Lekérdezések, amelyek nem rendelkeznek egy explicit "partíció által PartitionId" kellene kombinálhatja a partícióról származó események.
* A UNION két vagy több különböző bemeneti forrásból.
* Csatlakozás a bemeneti forrásból.

Olyan esetekben, ahol több ütemtervet a rendszer kombinálja, Azure Stream Analytics a művelet létrehoz egy Timestamp típusú kimeneti *t1* csak követően a rendszer kombinálja összes forrás legalább időpontban *t1*.
Például, ha a lekérdezés olvas egy *Eseményközpont* lemezpartíción, két partíció, és a partíció *P1* események rendelkezik időpontig *t1* és más partíció  *P2* események rendelkezik időpontig *t1 + x*, kimeneti hozzák időpontig *t1*.
Azonban ha merült fel egy explicit *"Partíció által PartitionId"* záradék, mind a partíciók megfelelően egymástól függetlenül.
Késő érkezés tolerancia beállítás az adatok az egyes partíciók kezelésére szolgál.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Késő érkezés tűréshatár és nem megfelelő sorrendben tolerancia beállítása
Bemeneti adatfolyamot, amelyek nincsenek sorrendben vagy szerepelnek:
* Rendezett (és ezért **késleltetett**).
* A felhasználó által megadott házirendnek megfelelően igazítva a rendszer.

A Stream Analytics eltűr késői és üzemen kívüli események által feldolgozásakor **alkalmazási**. A következő beállítások érhetők el a a **esemény rendelési** beállítás Azure-portálon: 

![Stream Analytics eseménykezelésnek](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**Késő érkezés tolerancia**
* A beállítás akkor alkalmazható, csak akkor, ha alkalmazási általi feldolgozás, ellenkező esetben figyelmen kívül.
* Ez a maximális érkezésének ideje és kérelem időpont között. Ha alkalmazás idő előtt (érkezési idő - késő érkezés ablak), akkor időre van beállítva (érkezési - késő érkezés ablakban)
* Több partíciót az ugyanazon bemeneti adatfolyamból vagy több bemeneti adatfolyam együttesen együtt, késő érkezés tolerancia az a maximális időtartam minden partíció megvárja-e az új adatokat. 

Késő érkezés ablak röviden, a maximális késleltetési az eseménygenerálás és a bemeneti forrásnál esemény fogadását között.
Késő érkezés tolerancia alapján módosítás először történik, és nem megfelelő sorrendben történik mellett. A **System.Timestamp** oszlop lesz a végső időbélyeg az esemény rendelt.

**Nem megfelelő sorrendben tolerancia**
* Az események sorrendje nem, de a "üzemen kívüli tűrési" halmazában érkező **által időbélyeg átrendezésekor**. 
* Legkésőbb tolerancia érkező események **eldobni vagy módosítani**.
    * **Igazítva**: igazítva érkezett meg a legújabb elfogadható idő jelennek meg. 
    * **Eldobott**: vetve.

"Üzemen kívüli tűrési" belül fogadott események sorrendjének módosításához a lekérdezés eredménye **által üzemen kívüli tűrési késleltetett**.

**Példa**

* Késő érkezés tolerancia = 10 perc<br/>
* Rendelés tolerancia kívül = 3 perc<br/>
* Feldolgozási idő szerint<br/>
* Események:
   * Esemény 1 _alkalmazási_ 00:00:00 = _érkezésének ideje_ = 00:10:01, _System.Timestamp_ 00:00:01, mert igazítva = (_érkezésének ideje_  -  _Alkalmazási_) nagyobb, mint késő érkezés tolerancia.
   * Esemény 2 _alkalmazási_ = 00:00:01, _érkezésének ideje_ = 00:10:01, _System.Timestamp_ = 00:00:01, nem beállítani, mert a kérelem ideje a késő érkezés belül ablak.
   * Esemény 3 _alkalmazási_ 00:10:00 = _érkezésének ideje_ 00:10:02 = _System.Timestamp_ 00:10:00, nem módosul, mert alkalmazások idő késő érkezés ablakban = .
   * Esemény 4 _alkalmazási_ 00:09:00 = _érkezésének ideje_ 00:10:03 = _System.Timestamp_ = 00:09:00, elfogadott eredeti Timestamp értékkel rendelkező alkalmazás ideje területén belül rendelés tűréshatáron.
   * Esemény 5 _alkalmazási_ 00:06:00 = _érkezésének ideje_ 00:10:04 = _System.Timestamp_ 00:07:00, beállítani, mert a kérelem ideje régebbi, mint a nem megfelelő sorrendben = tolerancia.

## <a name="practical-considerations"></a>Gyakorlati szempontok
A fent említett *késő érkezés tolerancia* az alkalmazás és érkezési idő maximális különbsége.
Is amikor alkalmazás feldolgozási idő, újabb, mint a konfigurált események *késő érkezés tolerancia* előtt módosulnak a *üzemen kívüli tolerancia* beállítás lesz érvényes. Igen soron kívüli hatályos késő érkezés tűréshatár és nem megfelelő sorrendben tolerancia minimális.

Üzemen kívüli események belül adatfolyam fordulhat elő, beleértve, oknál
* A küldők között óraeltérés.
* Változó késés küldő és a bemeneti forrás között.

Késő érkezés történik, többek között, oknál
* Feladók a batch-, és küldje el a események időtartamra később, az időszak után.
* Az esemény feladó küldése és fogadása az esemény bemeneti forrás közötti késleltetés.

Konfigurálása közben *késő érkezés tolerancia* és *üzemen kívüli tolerancia* egy adott feladat, a nézetet, a késési követelményekkel, és fent tényezőket érdemes figyelembe venni.

Az alábbiakban néhány példa

### <a name="example-1"></a>1. példa: 
A lekérdezés "Partíció által PartitionId" záradékot tartalmaz, és belül egyetlen partícióra, az események küldhetők szinkron küldési módszerekkel. Aszinkron küldés módszerek blokk, amíg az események küldhetők.
Ebben az esetben nem megfelelő sorrendben értéke nulla események explicit megerősítés elküldése a következő esemény előtt a sorrendben vannak elküldve. Késő érkezés az esemény létrehozása és küldése az esemény + a küldő és a bemeneti forrás közötti maximális késleltetés maximális késleltetés

### <a name="example-2"></a>2. példa
A lekérdezés "Partíció által PartitionId" záradékot tartalmaz, és belül egyetlen partícióra, az események küldhetők aszinkron küldés metódussal. Aszinkron küldés módszerek is kezdeményezhető több küld egy időben, ami üzemen kívüli események okozhat.
Ebben az esetben nem megfelelő sorrendben és a késői érkezési az esemény létrehozása és küldése az esemény + a küldő és a bemeneti forrás közötti maximális késleltetés legalább maximális késleltetés.

### <a name="example-3"></a>3. példa:
Lekérdezés nem rendelkezik "Partíció által PartitionId", és legalább két partíció.
A beállítás ugyanaz, mint a 2. példa. Azonban az egyik partíciója adatok késleltetheti-e a kimenet további * késő érkezés tolerancia "ablak.

## <a name="to-summarize"></a>Összefoglalásképpen
* Késő érkezés tűréshatár és nem megfelelő sorrendben ablak konfigurálni kell a nézetet, késésre vonatkozó követelmény alapján, és is figyelembe kell venni az események küldhetők hogyan.
* Javasoljuk, hogy nem megfelelő sorrendben tolerancia értéke kisebb a késő érkezés tolerancia.
* Több ütemtervet egyesítésekor adatokat az adatforrások és a partíciók az egyik hiánya késleltetheti-e a kimenet egy további késő érkezés tűrési által.

## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések
* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
