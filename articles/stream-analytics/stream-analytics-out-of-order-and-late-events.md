---
title: Esemény és az Azure Stream Analytics késedelmesség kezelése
description: Ez a cikk ismerteti, hogyan kezeli a Stream Analytics a soron vagy késői események adatfolyamban.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: e407a95d3ac858ea7180a75f9fbfc399860ad378
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30912015"
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Az Azure Stream Analytics esemény rendelés kapcsolatos szempontok

## <a name="arrival-time-and-application-time"></a>Érkezésének ideje és kérelem ideje

A historikus adatfolyam események minden esemény hozzá van rendelve az időbélyegző. Az Azure Stream Analytics időbélyegzőt minden esemény érkezési idő vagy alkalmazás idő segítségével rendeli. A **System.Timestamp** oszlopnak van rendelve az esemény időbélyege. 

Érkezésének ideje hozzá van rendelve a bemeneti forrásnál, ha az esemény eléri a forrás. Érkezésének ideje használatával végezheti el a **EventEnqueuedTime** tulajdonsága event hub bemeneti és a használatával a [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) blob bemeneti tulajdonság. 

Alkalmazási hozzá van rendelve, ha az esemény jön létre, és a tartalom egy részét. Alkalmazás ideje eseményeket feldolgozását, használja a **időbélyeg által** a select lekérdezés záradékot. Ha a **által időbélyeg** záradék hiányzik, érkezésének ideje eseményeket dolgoznak fel. 

Az Azure Stream Analytics időbélyegző sorrendben kimenetet, és beállítások soron adatok kezelésére szolgál.


## <a name="handling-of-multiple-streams"></a>Több adatfolyam kezelése

Egy Azure Stream Analytics-feladat egyesíti a következőhöz hasonló esetekben több ütemtervet származó események:

* A több partíciót állít elő. Lekérdezések, amelyek nem rendelkeznek explicit **PartitionID partíció** záradékban kell alakítania a partícióról származó események.
* A UNION két vagy több különböző bemeneti forrásból.
* Csatlakozás a bemeneti forrásból.

Olyan esetekben, ahol több ütemtervet a rendszer kombinálja, Azure Stream Analytics időbélyeg a kimenetet hoz létre *t1* csak követően a rendszer kombinálja összes forrás legalább időpontban *t1*. Tegyük fel például, hogy a lekérdezés két partíciókkal event hub partíción olvassa be. A partíciók egyik *P1*, események rendelkezik időpontig *t1*. A többi partíció *P2*, események rendelkezik időpontig *t1 + x*. Kimeneti majd előállított időpontig *t1*. De ha nincs explicit **PartitionID partíció** záradék, mind a partíciók előrehaladás egymástól függetlenül.

Késő érkezés tolerancia beállítást az adatok az egyes partíciók kezelésére szolgál.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Késő érkezés tűréshatár és soron tolerancia beállítása
Bemeneti adatfolyamot, amelyek nincsenek sorrendben vagy szerepelnek:
* Rendezett (és ezért késleltetett)
* A rendszer a felhasználó által megadott házirendnek megfelelően igazítva

A Stream Analytics eltűr késői és soron események alkalmazás időpontjára feldolgozásakor van. A következő beállítások érhetők el a a **esemény rendelési** lehetőséget az Azure-portálon: 

![Stream Analytics eseménykezelésnek](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Késő érkezés tolerancia
Késő érkezés tolerancia csak akkor alkalmazási idő még feldolgozás esetén alkalmazható. Ellenkező esetben a beállítást a rendszer figyelmen kívül hagyja.

Késő érkezés tolerancia érkezésének ideje és kérelem idő közötti legnagyobb különbség. Ha egy esemény érkezik a késő érkezés tolerancia később (például alkalmazás idő *app_t* < érkezésének ideje *arr_t* -késő érkezés házirend tolerancia *late_t*), a esemény módosul a késő érkezés tűréshatáron maximális (*arr_t* - *late_t*). A késő érkezés ablak az eseménygenerálás és a bemeneti forrásnál esemény fogadását maximális késleltetés. 

Több partíciót az ugyanazon bemeneti adatfolyamból vagy több bemeneti adatfolyam együttes használatakor késő érkezés tolerancia az a maximális időt, amely minden partíció megvárja-e az új adatokat. 

Késő érkezés tolerancia alapján módosítás először történik. Soron tolerancia alapján módosítása ezután történik. A **System.Timestamp** oszlopnak van rendelve az esemény végső időbélyegzőjét.

### <a name="out-of-order-tolerance"></a>Soron tolerancia
Az események sorrendje nem megérkezni, hanem a készlet soron tűrési belül rendezi újra időbélyeg által. A tűrési később érkező események vagy szerepelnek:
* **Igazítva**: igazítva érkezett meg a legújabb elfogadható idő jelennek meg. 
* **Eldobott**: vetve.

A Stream Analytics átrendezi a soron tűrési belül fogadott események, amikor a lekérdezés kimenetét a soron tűrési által késleltetett.

### <a name="early-events"></a>Korai események
Alkalmazás időpontjára feldolgozásakor események, amelyek alkalmazás érték előre a érkezési idő több mint 5 perc vagy eldobni vagy módosítani a konfigurációs beállításnak megfelelően.

### <a name="example"></a>Példa

* Késő érkezés tolerancia = 10 perc<br/>
* Soron tolerancia = 3 perc<br/>
* Feldolgozási idő szerint<br/>
* Események:
   1. **Alkalmazási** 00:00:00 = **érkezésének ideje** = 00:10:01, **System.Timestamp** 00:00:01, mert igazítva = (**érkezésének ideje - alkalmazási**) van több, mint a késő érkezés tolerancia.
   2. **Alkalmazási** = 00:00:01, **érkezésének ideje** = 00:10:01, **System.Timestamp** = 00:00:01, mert alkalmazási késő érkezés ablakban nem módosul.
   3. **Alkalmazási idő** 00:10:00 = **érkezésének ideje** 00:10:02 = **System.Timestamp** = 00:10:00, mert alkalmazási késő érkezés ablakban nem módosul.
   4. **Alkalmazási** 00:09:00 = **érkezésének ideje** 00:10:03 = **System.Timestamp** = 00:09:00, az eredeti időbélyeg fogadhatók el, mert a kérelem ideje a-soron belül van tolerancia.
   5. **Alkalmazási** 00:06:00 = **érkezésének ideje** 00:10:04 = **System.Timestamp** 00:07:00, beállítani, mert a kérelem ideje régebbi, mint a soron tolerancia =.

### <a name="practical-considerations"></a>Gyakorlati szempontok
A korábban említett késő érkezés tolerancia az alkalmazás és érkezési idő közötti legnagyobb különbség. Alkalmazás által éppen feldolgozása, eseményeket, amelyek újabb, mint a megadott késő érkezés tűrést módosul a soron tolerancia beállítás alkalmazása előtt. Igen soron kívüli hatályos késő érkezés tűréshatár és soron tolerancia minimális.

Az adatfolyam belül soron események okai:
* Az óra eltérésére a küldők között.
* Változó késés a küldő és a bemeneti forrás között.

Késő érkezés okai például:
* Feladók kötegelés és az események küldése az az időtartam alatt később, az időszak után.
* Az esemény feladó küldése és fogadása az esemény a bemeneti forrás közötti késleltetés.

Konfigurálja a késő érkezés tűréshatár és soron tolerancia egy adott feladat, vegye figyelembe helyességét, késési követelményekkel és az előző tényezőket.

Az alábbiakban néhány példa.

#### <a name="example-1"></a>1. példa 
A lekérdezés tartalmaz egy **PartitionID partíció** záradékban. Belül egyetlen partícióra az események küldhetők szinkron küldési módszerek segítségével. Aszinkron küldés módszerek blokk, amíg az események küldhetők.

Ebben az esetben nem megfelelő sorrendben oka nulla események küldése fel explicit megerősítő, a következő esemény elküldése előtt. Késő érkezés az esemény létrehozása és küldése az eseményt, valamint a küldő és a bemeneti forrás közötti maximális késleltetés maximális késleltetés.

#### <a name="example-2"></a>2. példa
A lekérdezés tartalmaz egy **PartitionID partíció** záradékban. Belül egyetlen partícióra az események küldhetők aszinkron küldés módszerek segítségével. Aszinkron küldés módszerek több küld egy időben, amelyek soron események esetén is kezdeményezhető.

Ebben az esetben nem megfelelő sorrendben és a késői érkezési legalább az esemény létrehozása és küldése az eseményt, valamint a küldő és a bemeneti forrás közötti maximális késleltetés maximális késleltetés.

#### <a name="example-3"></a>3. példa
A lekérdezés nem rendelkezik egy **PartitionID partíció** záradék, és legalább két partíció.

Konfigurációs megegyezik a 2. példa. Azonban az egyik partíciója adatok késleltetheti a kimenet egy további késő érkezés tűrési által.

## <a name="handling-event-producers-with-differing-timelines"></a>Eltérő ütemtervet tartalmazó esemény gyártók kezelése
Egyetlen bemeneti esemény adatfolyam gyakran több esemény-gyártók, például az egyes eszközökről származó eseményeket tartalmazza. Ezek az események sorrendje a korábban tárgyalt okok miatt előfordulhat, hogy érkeznek. A következő használati helyzetekben bár lehet, hogy a rendellenességeket esemény gyártók között nagy, egyetlen termelő eseményeiben rendellenességeket kis (vagy még nem létezik).

Az Azure Stream Analytics soron események kezelésére vonatkozó általános mechanizmust biztosít. Ilyen mechanizmusokat eredményez a feldolgozási késedelmeket (való várakozás közben a rendszer eléréséhez straggling események), eldobott vagy módosul az események, vagy mindkettőt.

Még a sok esetben a kívánt lekérdezést végrehajtja különböző esemény gyártók események egymástól függetlenül. Például, előfordulhat, hogy lehet összesítése egy ablak, minden eszközhöz események. Ebben az esetben nincs szükség a kimeneti, amely megfelel a más esemény gyártók szinkronizálásához várakozás közben egy esemény készítő késleltetése. Ez azt jelenti nincs szükség a gyártók között eltérésére idő kezelésére. Figyelmen kívül hagyhatja azt.

Természetesen ez azt jelenti, hogy a kimeneti eseményekben maguk nem megfelelő sorrendben az időbélyegzőjüket tekintetében. Az alsóbb rétegbeli fogyasztói az ilyen viselkedést kezelésére képesnek kell lennie. Azonban minden esemény kimenet helyességéről.

Az Azure Stream Analytics segítségével valósítja meg ezt a funkciót a [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) záradékban.

## <a name="summary"></a>Összegzés
* Késő érkezés tűréshatár és a helyességét, és késésre vonatkozó követelmény alapján soron ablak konfigurálása. Is figyelembe venni, hogyan kerülnek az eseményeket.
* Azt javasoljuk, hogy soron tolerancia értéke kisebb a késő érkezés tolerancia.
* Több ütemtervet egyesítésekor éppen adatokat az adatforrások és a partíciók az egyik hiánya késleltetheti-e a kimenet egy további késő érkezés tűrési által.

## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
