---
title: Kiszolgáló nélküli SQL-készlettel feldolgozott adatfeldolgozás
description: Ez a dokumentum azt ismerteti, hogyan történik az adatfeldolgozások kiszámítása az Azure Storage-ban kiszolgáló nélküli SQL-készlettel történő lekérdezéskor.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424221"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Kiszolgáló nélküli SQL-készlettel feldolgozott adatfeldolgozás az Azure szinapszis Analyticsben

A feldolgozott adatmennyiség a lekérdezés végrehajtása során ideiglenesen a rendszeren tárolt adatmennyiség, amely a következőkből áll:

- A tárterületről beolvasott adatok mennyisége – ide tartoznak a következők:
  - Az adatgyűjtés során olvasott adatmennyiség
  - A metaadatok olvasásakor olvasott adatok mennyisége (metaadatokat (például a Parkettaot tartalmazó fájlformátumok)
- Adatok mennyisége közbenső eredményekben – a csomópontok között továbbított adatok a lekérdezés végrehajtása során, beleértve a végpontra irányuló adatátvitelt tömörítetlen formátumban. 
- A Storage-ba írt adat mennyisége – ha a CETAS-t használja az eredményhalmaz tárterületre való exportálásához, a rendszer kiírja a felszámított bájtok és a CETAS kiválasztása részében feldolgozott adatmennyiséget.

A fájlok a tárolóból való olvasása igen optimalizált, és a következőket használja:

- Előre beolvasás – ami kis terhelést eredményezhet a beolvasott adat mennyiségéhez képest. Ha egy lekérdezés teljes fájlt olvas, nem lesz terhelés. Ha a fájlok részben olvashatók, például az első N lekérdezésekben, egy kicsit több adat lesz beolvasva.
- Optimalizált CSV-elemző – ha PARSER_VERSION = "2.0"-t használ a CSV-fájlok olvasásához, akkor a tárterületről beolvasott adatok valamivel nagyobb mennyiségű adatmennyiséget eredményeznek.  Az optimalizált CSV-elemző párhuzamosan, azonos méretű adattömbökben olvassa be a fájlokat. Nincs garancia arra, hogy az adattömbök teljes sorokat fogja tartalmazni. Az összes sor elemzésének biztosítása érdekében a rendszer a szomszédos adattömbök kis töredékeit is beolvassa, és egy kis mennyiségű terhelést ad hozzá.

## <a name="statistics"></a>Statisztika

A kiszolgáló nélküli SQL Pool lekérdezés-optimalizáló a statisztikára támaszkodik az optimális lekérdezés-végrehajtási tervek létrehozásához. A statisztikákat manuálisan is létrehozhatja, vagy a kiszolgáló nélküli SQL-készlet automatikusan létrehozza őket. Mindkét esetben a statisztikák egy külön lekérdezés végrehajtásával jönnek létre, amely egy adott oszlopot ad vissza a megadott mintavételi arányban. Ez a lekérdezés a feldolgozott adatmennyiséggel rendelkezik.

Ha ugyanezt vagy bármely olyan lekérdezést futtat, amely kihasználja a létrehozott statisztikai adatokat, a statisztikákat a rendszer újból felhasználja, ha lehetséges, és a statisztikák létrehozásakor nem lesz feldolgozva további adat.

A Parquet oszlop statisztikáinak létrehozásakor a rendszer csak a megfelelő oszlopot olvassa a fájlokból. A CSV-oszlopok statisztikáinak létrehozása a teljes fájlok olvasását és elemzését eredményezi.

## <a name="rounding"></a>Kerekítés

A feldolgozott adatmennyiséget a rendszer a lekérdezéssel megnövelt MB-ra kerekíti, legalább 10 MB adatfeldolgozással.

## <a name="what-is-not-included-in-data-processed"></a>Mit nem tartalmaz a feldolgozott adatfeldolgozás

- Kiszolgálói szintű metaadatok (például bejelentkezések, szerepkörök, kiszolgálói szintű hitelesítő adatok)
- A végpontban létrehozott adatbázisok csak metaadatokat tartalmaznak (például felhasználók, szerepkörök, sémák, nézetek, beágyazott TVFs, tárolt eljárások, adatbázis-hatókörű hitelesítő adatok, külső adatforrások, külső fájlformátumok, külső táblák)
  - Ha séma-következtetést használ, a rendszer beolvassa a fájlok töredékeit az oszlopnevek és az adattípusok kikövetkeztetés céljából.
- DDL-utasítások a LÉTREHOZÁSi statisztikák kivételével, mivel a megadott minta százaléka alapján dolgozza fel az adatokat a tárolóból
- Csak metaadat-lekérdezések

## <a name="reduce-amount-of-data-processed"></a>A feldolgozott adatmennyiség csökkentése

Optimalizálhatja a feldolgozott adatmennyiséget, és jobb teljesítményt érhet el, ha particionálja és átalakítja az adatait egy tömörített, oszlopos formátumba, például a parketta használatára.

## <a name="examples"></a>Példák

Tegyük fel, hogy két táblázat létezik, amelyek mindegyike azonos adattal rendelkezik öt egyenlő méretű oszlopban:

- population_csv a tábla 5 TB CSV-fájllal támogatott
- population_parquet 1 TB-os parketta-fájlokkal támogatott tábla – ez a tábla kisebb, mint az előző, mivel a parketta tömörített adatmennyiséget tartalmaz
- very_small_csv 100 KB-os CSV-fájlokkal támogatott tábla

**Lekérdezés #1** : válassza a Sum (populáció) elemet a population_csv

Ez a lekérdezés teljes fájlokat olvas és elemez a populáció oszlop értékeinek lekéréséhez. A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek sokasága a csomópontok között lesz átadva, és a végső összeg a végpontra kerül át. Ez a lekérdezés 5 TB adatmennyiséget és kisebb terhelést fog feldolgozni a töredékek összegének átadásához.

**Lekérdezés #2** : válassza a Sum (populáció) elemet a population_parquet

A tömörített és oszlopos formátumok (például a parketta) lekérdezése kevesebb adatolvasást eredményez, mint az előző lekérdezésben, mivel a kiszolgáló nélküli SQL-készlet egyetlen tömörített oszlopot fog olvasni a teljes fájl helyett. Ebben az esetben a 0,2 TB beolvasható (öt egyenlő méretű oszlop, 0,2 TB). A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek sokasága a csomópontok között lesz átadva, és a végső összeg a végpontra kerül át. Ez a lekérdezés 0,2 TB-ot és egy kisebb terhelést fog feldolgozni a töredékek összegének átadásához.

**Lekérdezés #3** : select * from population_parquet

Ez a lekérdezés beolvassa az összes oszlopot, és az összes adatátvitelt tömörítetlen formátumban továbbítja. Ha a tömörítési formátum 5:1, akkor a 6 TB-ot fogja feldolgozni, mivel az 1 TB + 5 TB-os, tömörítetlen adatok átvitelét fogja olvasni.

**Lekérdezés #4** : válassza a Count (*) elemet a very_small_csv

A lekérdezés teljes fájlokat olvas. A tábla tárolási fájljainak teljes mérete 100 KB. A csomópontok feldolgozzák ennek a táblának a töredékeit, az egyes töredékek összegét a rendszer a csomópontok között továbbítja, és a végső összeg a végpontra kerül. Ez a lekérdezés valamivel több mint 100 KB adatmennyiséget fog feldolgozni. A lekérdezésben feldolgozott adatmennyiség 10 MB-ra lesz kerekítve a [kerekítés](#rounding)során.

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan optimalizálhatja a teljesítmény-lekérdezéseket, tekintse meg a [kiszolgáló nélküli SQL-készlet ajánlott eljárásait](best-practices-sql-on-demand.md).
