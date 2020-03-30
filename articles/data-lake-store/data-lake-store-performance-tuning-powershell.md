---
title: Azure Data Lake Storage Gen1 teljesítményhangolása – PowerShell
description: Tippek a teljesítmény növeléséhez az Azure PowerShell azure Data Lake Storage Gen1 használatával történő használata esetén.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904578"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Teljesítményhangolási útmutató a PowerShell azure Data Lake Storage Gen1 használatával kapcsolatban

Ez a cikk ismerteti azokat a tulajdonságokat, amelyek et finomhangolása, hogy jobb teljesítményt a PowerShell használata közben a Data Lake Storage Gen1 használata.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>A teljesítménnyel kapcsolatos tulajdonságok

| Tulajdonság            | Alapértelmezett | Leírás |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ez a paraméter lehetővé teszi a párhuzamos szálak számának megadását az egyes fájlok fel- vagy letöltéséhez. Ez a szám a fájlonként lefoglalható maximális szálakat jelöli, de a forgatókönyvtől függően kevesebb szálat kaphat (például ha 1 KB-os fájlt tölt fel, akkor egy szálat kap, még akkor is, ha 20 szálat kér).  |
| ConcurrentFileCount | 10      | Ez a paraméter kifejezetten a mappák fel- és letöltéséhez kapcsolódik. Ez a paraméter határozza meg az egyidejűleg fel- vagy letölthető fájlok számát. Ez a szám az egyszerre feltölthető vagy letölthető egyidejű fájlok maximális számát jelöli, de a forgatókönyvtől függően kevesebb egyidejűséget kaphat (például ha két fájlt tölt fel, akkor két egyidejű feltöltést kap, még akkor is, ha kéri 15 esetén). |

**Példa:**

Ez a parancs fájlokat tölt le a Data Lake Storage Gen1 szolgáltatásból a felhasználó helyi meghajtójára fájlonként 20 szál és 100 egyidejű fájl használatával.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>A tulajdonságértékek meghatározása

A következő kérdés, hogy miként határozhatja meg, hogy milyen értéket biztosítson a teljesítménnyel kapcsolatos tulajdonságokhoz. Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: Határozza meg a teljes szálszám** - Kezdje a teljes szálszám kiszámításával. Általános iránymutatásként minden fizikai maghoz hat szálat kell használni.

    `Total thread count = total physical cores * 6`

    **Példa:**

    Tételezzük fel, hogy egy 16 maggal rendelkező D14 VM-en futtatja a PowerShell-parancsokat.

    `Total thread count = 16 cores * 6 = 96 threads`

* **2. lépés: A PerFileThreadCount kiszámítása** - A PerFileThreadCount-ot a fájlok mérete alapján számítjuk ki. A 2,5 GB-nál kisebb fájlok esetében nincs szükség a paraméter módosítására, mert a 10-es alapértelmezett érték elegendő. A 2,5 GB-nál nagyobb fájlok esetében az első 2,5 GB alapként 10 szálat kell használni, és 1 szálat kell hozzáadni minden további 256 MB-os fájlméret-növekedéshez. Ha olyan mappát másol, amelyben nagyon eltérő méretű fájlok találhatók, érdemes hasonló fájlméret alapján csoportosítani a fájlokat. Az eltérő fájlméretek az optimálisnál kisebb teljesítménnyel járhatnak. Ha nem lehetséges a hasonló fájlméret alapján történő csoportosítás, akkor a PerFileThreadCount értékét a legnagyobb fájlméret alapján kell beállítani.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Példa:**

    Feltételezve, hogy 100 fájlja van 1 GB-tól 10 GB-ig, a 10 GB-ot használjuk a legnagyobb fájlméretként az egyenlethez, amely a következőképpen olvasható.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **3. lépés: KoncurrentFilecount számítása** – A teljes szálszám és a PerFileThreadCount segítségével a ConcurrentFileCount kiszámítása a következő egyenlet alapján:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Példa:**

    Az eddig használt példaértékek alapján

    `96 = 40 * ConcurrentFileCount`

    Tehát a **ConcurrentFileCount** értéke **2,4**, amelyet kerekíthetünk **2**-re.

## <a name="further-tuning"></a>További hangolás

Elképzelhető, hogy további hangolásra lesz szüksége, mert különböző fájlméretekkel kell dolgoznia. Az előző számítás akkor működik jól, ha az összes vagy a legtöbb fájl nagyobb és közelebb van a 10 GB-os tartományhoz. Ha ehelyett sok különböző, nagyon eltérő méretű fájllal rendelkezik, akkor csökkentheti a PerFileThreadCount értékét. A PerFileThreadCount értékének csökkentésével, növelhetjük a ConcurrentFileCount értékét. Tehát, ha feltételezzük, hogy a legtöbb fájlunk kisebb az 5 GB-os tartományban, újra eltudjuk végezni a számításunkat:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Tehát **a ConcurrentFileCount** 96/20 lesz, ami 4,8, **4-re**kerekítve.

A beállítások hangolását a **PerFileThreadCount** értékének növelésével vagy csökkentésével folytathatja a fájlméretek eloszlásától függően.

### <a name="limitation"></a>Korlátozás

* **A fájlok száma kisebb, mint a ConcurrentFileCount értéke**: Ha a feltölteni kívánt fájlok száma kisebb a **ConcurrentFileCount** kiszámított értékének, akkor csökkentse a **ConcurrentFileCount** értékét úgy, hogy az egyenlő legyen a fájlok számával. A fennmaradó szálakat a **PerFileThreadCount** értékének a növelésére használhatja.

* **Túl sok szál**: Ha túlságosan megnöveli a szálszámot a fürt méretének növelése nélkül, az rosszabb teljesítménnyel járhat. Versengési problémák adódhatnak, ha kontextusváltás történik a CPU-n.

* **Elégtelen egyidejűség**: Ha az egyidejűség nem elégséges, lehet, hogy túl kicsi a fürt. Növelheti a csomópontok számát a fürtben, ami több egyidejűséget biztosít.

* **Szabályozási hibák**: Elképzelhető, hogy szabályozási hibákat tapasztal, ha az egyidejűség túl magas. Ha szabályozás hibák merülnek fel, csökkentse az egyidejűséget vagy lépjen kapcsolatba velünk.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)

