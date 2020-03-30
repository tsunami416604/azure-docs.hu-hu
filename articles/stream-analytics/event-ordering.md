---
title: Eseményrendezési szabályzatok konfigurálása az Azure Stream Analytics szolgáltatáshoz
description: Ez a cikk azt ismerteti, hogyan konfigurálható még a rendezési beállítások a Stream Analytics szolgáltatásban
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461192"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Eseményrendezési szabályzatok konfigurálása az Azure Stream Analytics szolgáltatáshoz

Ez a cikk ismerteti, hogyan kell beállítani és használni a késői érkezési és nem sorrendben lévő eseményszabályzatokat az Azure Stream Analytics-ben. Ezek a házirendek csak akkor lesznek érvényben, ha a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) záradékot használja a lekérdezésben.

## <a name="event-time-and-arrival-time"></a>Az esemény ideje és az érkezés időpontja

A Stream Analytics-feladat az *eseményeket* az esemény időpontja vagy *az érkezési idő*alapján tudja feldolgozni. **Esemény/alkalmazás idő** az időbélyeg jelen esemény hasznos adat (amikor az esemény jött létre). **Érkezési idő** az időbélyeg, amikor az esemény érkezett a bemeneti forrás (Event Hubs/IoT Hub/Blob storage). 

Alapértelmezés szerint a Stream Analytics *az eseményeket az érkezési idő*szerint dolgozza fel, de a lekérdezéstimestamp [by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) záradékhasználatával *eseményidő* szerint is feldolgozhatja az eseményeket. A kései érkezésre és a sorrendben nem megfelelő irányelvek csak akkor érvényesek, ha eseményeket dolgoz fel eseményidő szerint. Mindig vegye figyelembe a forgatókönyv késésre és helyes sorrendre vonatkozó követelményeit ezen beállítások konfigurálásakor. 

## <a name="what-is-late-arrival-policy"></a>Mi a kései érkezésre vonatkozó szabályzat?

Néha az események különböző okok miatt későn érkeznek. Például egy 40 másodperces késéssel érkező esemény eseményideje = 00:10:00 és érkezési idő = 00:10:40 lesz. Ha a késői érkezési szabályzatot 15 másodpercre állítja be, a 15 másodpercnél későbbi időpontban beérkező eseményeket a szolgáltatás elveti (nem dolgozza fel a Stream Analytics), vagy módosítja az esemény idejét. A fenti példában, mivel az esemény 40 másodperccel később érkezett (több mint házirend-beállítás), az esemény ideje a késői érkezésre vonatkozó maximális időtartamhoz lesz igazítva 00:10:25-re (érkezési idő - késői érkezési politika értéke). Az alapértelmezett késői érkezési szabályzat 5 másodperc.

## <a name="what-is-out-of-order-policy"></a>Mi a sorrenden kívüli házirend? 

Az esemény is előfordulhat, hogy nem működik. Miután az esemény ideje a késői érkezési szabályzat alapján módosult, dönthet úgy is, hogy automatikusan eldobja vagy módosítja a nem sorrendben lévő eseményeket. Ha ezt a házirendet 8 másodpercre állítja, minden olyan esemény, amely nem sorrendben, de a 8 másodperces ablakon belül érkezik, eseményidő szerint lesz átrendezve. A később érkező események et eldobjuk vagy a maximális sorrenden kívüli házirend-értékhez igazítjuk. Az alapértelmezett sorrenden kívüli házirend 0 másodperc. 

## <a name="adjust-or-drop-events"></a>Események beállítása vagy eldobása

Ha az események a beállított szabályzatok alapján késve vagy sorrendben érkeznek, eldobhatja az ilyen eseményeket (amelyeket nem a Stream Analytics dolgozta fel), vagy módosíthatja az eseményidejüket.

Lássunk egy példát ezekre a politikákra működés közben.
<br> **Késői érkezésre vonatkozó szabályzat:** 15 másodperc
<br> **Sorrenden kívüli házirend:** 8 másodperc

| Eseményszám | Esemény ideje | Érkezési idő | System.Timestamp | Magyarázat |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Az esemény későn és azon kívül érkezett a tolerancia szint. Így az esemény ideje a maximális késői érkezési toleranciához igazodik.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Az esemény későn érkezett, de a tűréshatáron belül. Tehát esemény idő nem kap igazítani.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Az esemény időben érkezett. Nincs szükség kiigazításra.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Az esemény nem sorrendben, de 8 másodperces tűréshatáron belül érkezett. Tehát az esemény ideje nem igazodik. Elemzési célokra ez az esemény a 4-es számú esemény előző eseményszámának minősül.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Az esemény nem sorrendben érkezett, és 8 másodperces külső tűréshatár. Így az esemény idő beállítása a maximális sorrenden kívüli tolerancia. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Késleltethetik ezek a beállítások a feladat kimenetét? 

Igen. Alapértelmezés szerint a sorrenden kívüli házirend értéke nulla (00 perc és 00 másodperc). Ha módosítja az alapértelmezett értéket, a feladat első kimenete ezzel az értékkel (vagy nagyobb) késik. 

Ha a bemenetek egyik partíciója nem kap eseményeket, akkor a késésre vonatkozó házirend-érték kel késni kell a kimenetet. Ennek okát olvassa el az alábbi InputPartition hiba szakaszban. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>LateInputEvents üzenetek jelennek meg a tevékenységnaplómban

Ezek az üzenetek arról tájékoztatják, hogy az események későn érkeztek, és a konfigurációnak megfelelően eldobva vagy módosítva. Figyelmen kívül hagyhatja ezeket az üzeneteket, ha megfelelően konfigurálta a késői érkezési szabályzatot. 

Az üzenet például a következő: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>A tevékenységnaplóban inputpartitionNotProgressing jelenik meg

A bemeneti forrás (Event Hub/IoT Hub) valószínűleg több partíciót. Az Azure Stream Analytics csak akkor állítja elő a t1 időbélyegző kimenetét, ha az összes egyesített partíció legalább a t1 időpontban van. Tegyük fel például, hogy a lekérdezés egy két partícióval rendelkező eseményközpont-partícióról olvas. Az egyik partíció, p1, események et t1 időpontig. A másik partíció, P2, eseményeket, amíg az idő t1 + x. A kimenet ezután a t1 időpontig keletkezik. De ha van egy explicit PartitionId záradék, mindkét partíciók egymástól függetlenül haladnak. 

Ha ugyanabból a bemeneti adatfolyamból több partíciót kombinál, a késői érkezési tolerancia az a maximális időtartam, amennyi ideig minden partíció vár az új adatokra. Ha van egy partíció az Event Hubon, vagy ha az IoT Hub nem kap bemeneteket, a partíció idővonala nem halad, amíg el nem éri a késői érkezést tűréshatár. Ez késlelteti a kimenetet a késői érkezési tűréshatár. Ilyen esetekben a következő üzenet jelenhet meg:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ez az üzenet tájékoztatja, hogy legalább egy partíció a bemenet üres, és késlelteti a kimenetet a késői érkezési küszöbértéket. Ennek leküzdésére, akkor ajánlott vagy:  
1. Győződjön meg arról, hogy az Event Hub/IoT Hub összes partíciója kap bemenetet. 
2. Használja partitioni partitionid záradékot a lekérdezésben. 

## <a name="next-steps"></a>További lépések
* [Időkezelési megfontolások](stream-analytics-time-handling.md)
* [A Stream Analytics ben elérhető mérőszámok](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
