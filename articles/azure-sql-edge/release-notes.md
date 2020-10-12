---
title: Az Azure SQL Edge kibocsátási megjegyzései
description: A kibocsátási megjegyzések részletesen ismertetik az Azure SQL Edge-lemezképek újdonságait vagy változásait
keywords: kibocsátási megjegyzések – SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: afd78acadf133a9f128eec402eba9d0eed51b8e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284482"
---
# <a name="azure-sql-edge-release-notes"></a>Az Azure SQL Edge kibocsátási megjegyzései 

Ez a cikk ismerteti az újdonságokat és az Azure SQL Edge minden új buildjét.

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge – 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>SQL Engine Build száma – 15.0.2000.1552

### <a name="whats-new"></a>Újdonságok
1. Ubuntu 18,04-alapú tároló rendszerképek. 
2. A és `IGNORE NULL` a `RESPECT NULL` függvények támogatása `LAST_VALUE()` `FIRST_VALUE()` . 
3. Megbízhatósági változások a ONNX-vel való ELŐREJELZÉShez.
4. Adatmegőrzési szabályzaton alapuló tisztítás támogatása.
   - A hibaelhárításhoz szükséges adatmegőrzési feladat a gyűrű pufferének támogatása.
5. Új funkció támogatása 
   - Gyors helyreállítás
   - Lekérdezések automatikus finomhangolása
   - Párhuzamos végrehajtási forgatókönyvek engedélyezése
6. Energiatakarékos az alacsony energiaellátású üzemmód fejlesztése
7. A streaming új funkcióinak támogatása 
   - [Pillanatkép-Windows](https://docs.microsoft.com/stream-analytics-query/snapshot-window-azure-stream-analytics) : az új ablak típusa lehetővé teszi, hogy a pontos időpontra érkező események csoportosítsák. 
   - A [TopOne](https://docs.microsoft.com/stream-analytics-query/topone-azure-stream-analytics) és a [CollectTop](https://docs.microsoft.com/stream-analytics-query/collecttop-azure-stream-analytics) analitikai függvényként való engedélyezése lehetővé teszi, hogy a választott oszlop által rendezett rekordokat visszaadja, anélkül, hogy egy ablakhoz kellene tartoznia. 
   - A [MATCH_RECOGNIZE](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics)fejlesztése. 

### <a name="fixes"></a>Javítások
1. További hibaüzenetek és részletek a TSQL adatfolyam-továbbítási műveleteivel kapcsolatos hibák elhárításához. 
2. Az akkumulátor élettartamának az üresjárati módban való megőrzésének újdonságai. 
3. TSQL streaming Engine-javítások: 
   - Leállított folyamatos átviteli feladatok karbantartása 
   - Javítások a honosítási és a Unicode-kezelési javításokhoz
   - Javíthatja a TSQL-adatfolyamok hibakeresését, lehetővé téve a felhasználók számára, hogy lekérdezzek a feladatokról get_streaming_job.
4. Adatmegőrzési házirend alapú tisztítás
   - Az adatmegőrzési szabályzat létrehozásának és a tisztítási forgatókönyveknek a javításai.
5. Javítja a háttérben futó időzítő feladatait, hogy az alacsony energiaellátású üzemmód számára növelje az energiamegtakarítást.

### <a name="known-issues"></a>Ismert problémák 
1. Date_Bucket T-SQL függvény nem használható számított oszlopban.


## <a name="ctp-23"></a>CTP 2,3
### <a name="sql-engine-build-number---15020001549"></a>SQL Engine Build száma – 15.0.2000.1549
### <a name="whats-new"></a>Újdonságok
1. Egyéni eredetek támogatása a Date_Bucket () függvényben. 
2. A BacPac-fájlok támogatása az SQL-telepítés részeként.
3. Adatmegőrzési szabályzaton alapuló tisztítás támogatása.      
   - DDL-támogatás adatmegőrzési szabályzat engedélyezéséhez 
   - Tárolt eljárások és a háttérben végzett törlési feladat törlése
   - Kibővített események a karbantartási feladatok figyeléséhez

### <a name="fixes"></a>Javítások
1. További hibaüzenetek és részletek a TSQL adatfolyam-továbbítási műveleteivel kapcsolatos hibák elhárításához. 
2. Az akkumulátor élettartamának az üresjárati módban való megőrzésének újdonságai. 
3. TSQL streaming Engine-javítások: 
   - Az elakadt vízjel-probléma kijavítása a alstreamben lévő ugráló ablakmal 
   - Javítsa ki a keretrendszer kivétel-kezelését, hogy a rendszer a felhasználó által végrehajtható hiba miatt begyűjtse


## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>SQL Engine Build száma – 15.0.2000.1546
### <a name="whats-new"></a>Újdonságok
1. Nem legfelső szintű tárolók támogatása 
2. A használati és diagnosztikai adatok gyűjtésének támogatása 
3. A T-SQL streaming frissítései
   - Unicode-karakterek támogatása a stream-objektumok neveinél

### <a name="fixes"></a>Javítások
1. A T-SQL streaming frissítései
   - Folyamatok tisztításának fejlesztése
   - A naplózás és a diagnosztika fejlesztése
2. Az adatfeldolgozás teljesítményének növelése

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>SQL Engine Build száma – 15.0.2000.1545
### <a name="fixes"></a>Javítások
1. Javítsa ki az ELŐREJELZÉSt a ONNX-modellekkel az CPUID-probléma ARM-ben való kezeléséhez 
2. Javítás a TSQL-adatfolyam indításakor a hiba elérési útjának kezelésére 
3. Javítsa ki a küszöbértékek helytelen értékét a feladatok metrikájában, ha nincs adat. 
4. Javítsa ki a kimeneti adapterrel kapcsolatos problémát, ha az adapter változó sémát tartalmaz a kötegek között.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>SQL Engine Build száma – 15.0.2000.1401
### <a name="whats-new"></a>Újdonságok
1.  A terméknév frissítve az "Azure SQL Edge" névre
1.  Date_bucket függvény

    i.  A dátum, idő és DateTime típus támogatása
3.  ELŐREJELZÉS a ONNX
    
    i.  ONNX szükséges FUTTATÓKÖRNYEZETi paraméter 
    
4.  TSQL streaming-támogatás (korlátozott előzetes verzió) 
 
### <a name="known-issues"></a>Ismert problémák

1. <b>Probléma:</b> Lehetséges hibák a dacpac alkalmazás indításakor az időzítési probléma miatt.

    <b>Áthidaló megoldás:</b> SQL Server vagy tároló újraindítása újra megpróbálja alkalmazni a dacpac, és ki kell javítania a problémát
### <a name="request-support"></a>Támogatás kérése
1. A támogatást a [támogatási oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)kérheti le.

4. Győződjön meg arról, hogy a következő mezők vannak kiválasztva: 
    * Probléma típusa – technikai 
    * Szolgáltatás – IoT Edge
    * Probléma típusa – a probléma egy IoT Edge modulhoz kapcsolódik
    * Probléma altípusa – Azure SQL Edge

   ![Példa támogatási jegyre](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>SQL Engine Build száma – 15.0.2000.1331
### <a name="whats-new"></a>Újdonságok
1. Date_bucket függvény
    
    i. DateTimeOffset-típus támogatása
2. ELŐREJELZÉS ONNX-modellekkel

    i. nvarchar-támogatás
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>SQL Engine Build száma – 15.0.2000.1247
### <a name="whats-new"></a>Újdonságok
1.  ELŐREJELZÉS ONNX-modellekkel
 
    i.  Varchar-támogatás
    
    ii. Áttelepítés a ONNX Runtime 1,0-es verziójára 
2.  Szolgáltatások támogatása – a következő funkciók engedélyezettek:

    i.   CDC-támogatás

    ii.  Előzmények táblázat tömörítéssel

    iii. Nagyobb méretezési tényező a naplóban olvasás előtt

    iv.  Batch Mode ES szűrő pushdown

    v.   Előre beolvasott optimalizálások
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>SQL Engine Build száma – 15.0.2000.1147
### <a name="whats-new"></a>Újdonságok
1. Azure IOT-portál üzembe helyezése 

    i.   Az AMD64-és ARM-lemezképek üzembe helyezésének támogatása

    ii.  A folyamatos átviteli feladatok létrehozásának támogatása

    iii. Dacpac üzembe helyezése
2. ELŐREJELZÉS ONNX-modellekkel

    i. Numerikus típusú támogatás
3. Szolgáltatások támogatása – a következő funkciók engedélyezettek:

    i.  Pushdown összesítése az oszlopok tárolására

    ii. Merry-go-round vizsgálatok
4. A helyigény és a memóriahasználat csökkentése
