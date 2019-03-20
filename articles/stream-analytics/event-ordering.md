---
title: Az Azure Stream Analytics eseményrendezési szabályzatok konfigurálása
description: Ez a cikk bemutatja, hogyan kell konfigurálni, még akkor is a Stream Analytics a beállítások rendezése
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190533"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Az Azure Stream Analytics eseményrendezési szabályzatok konfigurálása

Ez a cikk bemutatja, hogyan beállítania és használnia késedelmes beérkezés és out soron kívüli esemény házirendek az Azure Stream Analytics szolgáltatásban. Ezek a szabályzatok érvényesek használata esetén csak a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) záradék szerepel a lekérdezésben.

## <a name="event-time-and-arrival-time"></a>Esemény időpontja és érkezési ideje

A Stream Analytics-feladat vagy alapján esemény feldolgozására képes *esemény időpontja* vagy *érkezési ideje*. **Esemény/alkalmazás idő** van jelen eseménytartalom timestamp (ha az esemény jött létre). **Érkezési ideje** történő küldés időbélyegzője legyen akkor, ha az esemény érkezett, a bemeneti forrásnál (Event Hubs/IoT Hub és Blob storage). 

Alapértelmezés szerint a Stream Analytics események feldolgozza *érkezési ideje*, de Ön által eseményeinek feldolgozása *esemény időpontja* használatával [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) záradék a lekérdezés. Késedelmes érkezési és out soron kívüli szabályzatok olyan alkalmazható, csak ha az esemény időpontja szerint eseményeket dolgoz fel. Ezek a beállítások konfigurálásakor, fontolja meg a forgatókönyv követelményei késés és helyességét. 

## <a name="what-is-late-arrival-policy"></a>Mi késői érkezési szabályzat?

Néha esemény érkezik késői különböző okok miatt. Például egy eseményt, amely 40 másodperc későn érkező események időt vesz igénybe = 00:10:00 és érkezési idő = 00:10:40. Ha a késedelmes érkezési házirend 15 másodperc, egy újabb, mint 15 másodperc vagy dobja el a rendszer (nem dolgozza fel a Stream Analytics), vagy az esemény időpontját helyesbítették szerveződő eseményre. A fenti példában az esemény érkezett 40 másodperc késői (több, mint a házirend beállítása), mert az esemény időpontját módosul a maximális késedelmes érkezési házirend 00:10:25-kor (érkezési idő – késői érkezési értéke). Alapértelmezett késői érkezési házirend érték 5 másodperc.

## <a name="what-is-out-of-order-policy"></a>Mi out soron kívüli szabályzat? 

Esemény előfordulhat, hogy érkezik sorrendben is. Esemény időtartam a késedelmes érkezési házirend alapján beállított, az automatikusan dobja el vagy módosíthatja az események, amelyek ki soron kívüli választhatja. Ha ez a házirend 8 másodperc, a más eseményeket, amelyek üzemen kívüli, de a 8-második időszakon belül érkezik esemény időpontja szerint rendezi. Érkező később fogja lehet vagy eldobott vagy módosított események maximális out soron kívüli házirend értékre. Alapértelmezett out soron kívüli házirend érték 0 másodperc. 

## <a name="adjust-or-drop-events"></a>Állítsa be, vagy dobja el az események

Ha esemény érkezik késő vagy soron kívüli ki a konfigurált házirendek alapján, dobja el az ilyen események (nem dolgozza fel a Stream Analytics), vagy azok igazított esemény ideje.

Ossza meg velünk meg ezeket a szabályzatokat, a művelet egy példát.
<br> **Késedelmes érkezési házirend:** 15 másodperc
<br> **Kimenő soron kívüli házirend:** 8 másodperc

| Esemény száma | Esemény időpontja | Érkezés időpontja | System.Timestamp | Magyarázat |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Esemény késői és külső tűréshatáron megérkezett. Esemény időpontja így késői érkezési megengedett lekérdezi igazítani.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Esemény érkezett, késői, de tűréshatáron belül. Így az esemény idő nem igazodik beolvasása.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Esemény időpontja érkezett. Nincs szükség illesztését.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Esemény érkezett out soron kívüli, de a 8 másodperces tűréshatáron belül vannak. Tehát esemény időpontja nem get igazodik. Elemzési céllal ezt az eseményt megelőző 4-es esemény számot fogja tekinteni.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Esemény 8 másodperces out érkezési sorrendjét és a külső tolerancia megérkezett. Esemény időpontja tehát a soron kívüli out tolerancia maximális módosul. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Ezek a beállítások késleltetheti-e a feladat kimenetét? 

Igen. Alapértelmezés szerint ki soron kívüli szabályzatbeállítás értéke 0 (00 perc és 00 másodperc). Ha megváltoztatja az alapértelmezett, a feladat első kimenete késleltetett az érték (vagy nagyobb). 

Ha nem kap, a partíciókat a bemeneteket az egyik események, a kimeneti kell késleltetni a késedelmes érkezési értéke számíthat. Ebből a cikkből megtudhatja, olvassa el az alábbi InputPartition hiba szakaszt. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>LateInputEvents üzenetek láthatók a tevékenységnaplóban

Ezek az üzenetek be, hogy rendelkezik későn érkező és a rendszer akár eldobott vagy módosított események a konfigurációjának megfelelően jelennek meg. Ha megfelelően konfigurált késői érkezési házirend, kihagyhatja ezeket az üzeneteket. 

Ez az üzenet példája: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>InputPartitionNotProgressing láthatók a tevékenységnaplóban

A bemeneti forrás (Event Hub és IoT Hub) valószínűleg több partíciót tartalmaz. Az Azure Stream Analytics stamp t1 időpontban a kimenetet hoz létre, csak azután a partíciókat, hogy a rendszer kombinálja vannak, legalább a t1 időpontban. Tegyük fel például, hogy a lekérdezés olvas be egy event hub-partícióról, amely két partícióval rendelkezik. Amíg a t1 időpontban események egyik partíciója, P1, rendelkezik. A többi partíció, P2, amíg a t1 időpontban + x események rendelkezik. Kimeneti majd előállított, amíg a t1 időpontban. De ha egy explicit Partition by PartitionId záradékot, mind a partíciók egymástól függetlenül halad. 

Az ugyanazon bemeneti streamből több partíciót együttesen a késedelmes érkezési tolerancia esetén a legnagyobb, hogy mennyi ideig minden partíció megvárja, amíg az új adatok. Ha több partíciót az Event hub, vagy az IoT Hub bemenetei között nem kap, ha az adott partíció ütemtervét amíg nem halad eléri a késedelmes érkezési tolerancia küszöbértéket. Ez késlelteti a kimeneti által a késedelmes érkezési tolerancia küszöbértéket. Ezekben az esetekben a következő üzenetet láthatja:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ez az üzenet tájékoztatja, hogy legalább egy partíciót a bemeneti adatok üres, és a késedelmes érkezési küszöbértéket által a kimeneti késleltetni fogja. Ez áthidalható, ajánlott, hogy mindkét: 1. Győződjön meg arról, fogadja az Event Hub és IoT hub összes partíciót. 2. Használja a Partition by PartitionID záradékot a lekérdezésben. 

## <a name="next-steps"></a>További lépések
* [Idő kezelési kapcsolatos szempontok](stream-analytics-time-handling.md)
* [A metrikák elérhető a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
