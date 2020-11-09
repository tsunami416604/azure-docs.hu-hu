---
title: Kiszolgáló nélküli SQL-készlettel feldolgozott adatfeldolgozás
description: Ez a dokumentum azt ismerteti, hogyan számítja ki az adatfeldolgozási mennyiség kiszámításakor a rendszer az adattárban lévő adatlekérdezéseket.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381201"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Az Azure szinapszis Analytics kiszolgáló nélküli SQL-készlet használatával feldolgozott adatai

A *feldolgozott* adatmennyiség a rendszer által ideiglenesen a lekérdezés futtatása közben tárolt adatmennyiség. A feldolgozott adatmennyiség a következő mennyiségekből áll:

- A tárterületről beolvasott adatok mennyisége. Ez az összeg a következőket tartalmazza:
  - Az adatgyűjtés során beolvasott adatolvasások.
  - Adatok olvasása a metaadatok olvasása közben (metaadatokat, például a parketta-t tartalmazó fájlformátumok esetében).
- A közbenső eredményekben lévő adatmennyiség. Ezeket az adatátvitelt a rendszer a csomópontok között a lekérdezés futtatása közben továbbítja. Ez magában foglalja a végpontra irányuló adatátvitelt tömörítetlen formátumban. 
- A tárterületre írt adatmennyiség. Ha a CETAS-t használja az eredményhalmaz tárolóba való exportálásához, akkor a rendszer a CETAS kiválasztása résznél a feldolgozott adatmennyiséget adja hozzá a feldolgozott adatmennyiséghez.

A fájlok a tárterületről való olvasása igen optimalizált. A folyamat a következőket használja:

- A beolvasás, amely némi terhelést eredményezhet az olvasott adatmennyiséghez képest. Ha egy lekérdezés teljes fájlt olvas, akkor nincs terhelés. Ha a fájlok részben olvashatók, például az első N lekérdezésekben, akkor egy kicsit több adat is olvasható a beolvasás használatával.
- Egy optimalizált vesszővel tagolt (CSV) elemző. Ha PARSER_VERSION = "2.0"-t használ a CSV-fájlok olvasásához, akkor a tárterületről beolvasott adatok mennyisége némileg megnő. Az optimalizált CSV-elemző párhuzamosan olvassa be a fájlokat, az azonos méretű adattömbökben. Az adattömbök nem feltétlenül tartalmaznak egész sort. Az összes sor elemzésének biztosítása érdekében az optimalizált CSV-elemző a szomszédos adattömbök kis töredékeit is beolvassa. Ez a folyamat kis mennyiségű terhelést eredményez.

## <a name="statistics"></a>Statisztika

A kiszolgáló nélküli SQL Pool lekérdezés-optimalizáló a statisztikára támaszkodik az optimális lekérdezés-végrehajtási tervek létrehozásához. A statisztikákat manuálisan is létrehozhatja. Ellenkező esetben a kiszolgáló nélküli SQL-készlet automatikusan létrehozza azokat. Mindkét esetben a statisztikák egy külön lekérdezés futtatásával jönnek létre, amely egy megadott mintavételi arányban ad vissza egy adott oszlopot. Ez a lekérdezés a feldolgozott adatmennyiséggel rendelkezik.

Ha ugyanazt vagy bármely olyan lekérdezést futtat, amely kihasználhatja a létrehozott statisztikai adatokat, akkor a statisztikákat a rendszer újból felhasználja, ha lehetséges. Nincs további feldolgozott adat a statisztikák létrehozásához.

Amikor statisztikákat hoznak létre egy Parquet oszlophoz, csak a megfelelő oszlop olvasható a fájlokból. Egy CSV-oszlop statisztikáinak létrehozásakor a teljes fájlok beolvasása és elemzése történik.

## <a name="rounding"></a>Kerekítés

A feldolgozott adatmennyiséget a rendszer a legközelebbi MB/s értékre kerekíti. Minden lekérdezés legalább 10 MB feldolgozott adatmennyiséget tartalmaz.

## <a name="what-data-processed-doesnt-include"></a>A feldolgozott adatfeldolgozási szolgáltatás nem tartalmazza

- Kiszolgálói szintű metaadatok (például bejelentkezések, szerepkörök és kiszolgálói szintű hitelesítő adatok).
- A végpontban létrehozott adatbázisok. Ezek az adatbázisok csak metaadatokat tartalmaznak (például felhasználók, szerepkörök, sémák, nézetek, beágyazott tábla értékű függvények [TVFs], tárolt eljárások, adatbázis-hatókörű hitelesítő adatok, külső adatforrások, külső fájlformátumok és külső táblák).
  - Ha séma-következtetést használ, akkor a rendszer beolvassa a fájlok töredékeit az oszlopnevek és az adattípusok kikövetkeztetve, és a feldolgozott adatmennyiséghez hozzáadja az olvasott adatmennyiséget.
- Adatdefiníciós nyelvi (DDL) utasítások, kivéve a CREATE STATISTICS utasítást, mert a megadott minta százalék alapján dolgozza fel az adatokat a tárolóból.
- Csak metaadat-lekérdezések.

## <a name="reducing-the-amount-of-data-processed"></a>A feldolgozott adatmennyiség csökkentése

Optimalizálhatja a feldolgozott adatmennyiséget, és javíthatja a teljesítményt, ha particionálja és átalakítja az adatait egy tömörített, oszlop alapú formátumba, például a parketta használatára.

## <a name="examples"></a>Példák

Képzelje el a három táblázatot.

- A population_csv táblát 5 TB-os CSV-fájl támogatja. A fájlok öt egyenlő méretű oszlopba vannak rendezve.
- A population_parquet tábla ugyanazokkal az értékekkel rendelkezik, mint a population_csv táblázat. A biztonsági mentés 1 TB-os parketta-fájlokból áll. Ez a tábla kisebb, mint az előző, mert az adattömörítési formátuma egy parketta.
- A very_small_csv táblát 100 KB-os CSV-fájl támogatja.

**1. lekérdezés** : a Sum (populáció) kiválasztása population_csv

Ez a lekérdezés teljes fájlokat olvas és elemez a sokaság oszlop értékeinek lekéréséhez. A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek populációs összegét a rendszer a csomópontok között továbbítja. A végső összeg a végpontra kerül. 

Ez a lekérdezés 5 TB-nyi adat feldolgozását és kis mennyiségű terhelést dolgoz fel a töredékek összegének átadásához.

**2. lekérdezés** : válassza a Sum (populáció) elemet population_parquet

Ha tömörített és oszlopos formátumokat (például a Parquet) kérdez le, a rendszer kevesebb adatmennyiséget olvas be, mint az 1. lekérdezésben. Ez az eredmény jelenik meg, mert a kiszolgáló nélküli SQL-készlet egyetlen tömörített oszlopot olvas a teljes fájl helyett. Ebben az esetben a 0,2 TB olvasható. (Öt egyenlő méretű oszlop 0,2 TB.) A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek populációs összegét a rendszer a csomópontok között továbbítja. A végső összeg a végpontra kerül. 

Ez a lekérdezés 0,2 TB-ot és kis mennyiségű terhelést dolgoz fel a töredékek összegének átadásához.

**3. lekérdezés** : select * from population_parquet

Ez a lekérdezés beolvassa az összes oszlopot, és az összes adatokat tömörítetlen formátumban továbbítja. Ha a tömörítési formátum 5:1, akkor a lekérdezés 6 TB-ot dolgoz fel, mert 1 TB-ot olvas, és 5 TB tömörítetlen adatokat továbbít.

**4. lekérdezés** : válassza a Count (*) elemet very_small_csv

Ez a lekérdezés teljes fájlokat olvas be. A tábla tárolási fájljainak teljes mérete 100 KB. A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek összegét a csomópontok között továbbítjuk. A végső összeg a végpontra kerül. 

Ez a lekérdezés valamivel több mint 100 KB adatmennyiséget dolgoz fel. A lekérdezésben feldolgozott adatmennyiség 10 MB-ra van kerekítve, a jelen cikk [kerekítési](#rounding) szakaszában megadott módon.

## <a name="next-steps"></a>Következő lépések

A teljesítményre vonatkozó lekérdezések optimalizálásával kapcsolatos további információkért lásd: [ajánlott eljárások kiszolgáló nélküli SQL-készlethez](best-practices-sql-on-demand.md).
