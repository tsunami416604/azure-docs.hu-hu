---
title: Teljesítmény-finomhangolási útmutató a Powershell használata az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Tippek az Azure PowerShell használata az Azure Data Lake Storage Gen1 teljesítményének növelése
services: data-lake-store
documentationcenter: ''
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: fff26406b036edeb48371b89f7e585160ddc58e0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123317"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a PowerShell használata az Azure Data Lake Storage Gen1

Ez a cikk felsorolja a tulajdonságokat, amelyek megfelelő hangolásával a jobb teljesítmény érdekében az Azure Data Lake Storage Gen1 dolgozhat a PowerShell használata során:

## <a name="performance-related-properties"></a>Teljesítménnyel kapcsolatos tulajdonságok

| Tulajdonság            | Alapértelmezett | Leírás |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ez a paraméter lehetővé teszi a párhuzamos szálak számának megadását az egyes fájlok fel- vagy letöltéséhez. Ez a szám jelöli, a maximális szálak / fájl kiosztható, de kevesebb szálat a forgatókönyvtől függően előfordulhat, hogy kap (Ha például egy 1 KB-os fájlt tölt fel, ha kap egy szálat akkor is, ha 20-at kér).  |
| ConcurrentFileCount | 10      | Ez a paraméter kifejezetten a mappák fel- és letöltéséhez kapcsolódik. Ez a paraméter határozza meg az egyidejűleg fel- vagy letölthető fájlok számát. Ez a szám jelöli az egyidejűleg fel- vagy egy adott időpontban letöltött fájlok maximális számát, de kisebb egyidejűséget a forgatókönyvtől függően előfordulhat, hogy kap (Ha például két fájlt tölt fel, ha kap két egyidejű fájlok feltöltése még akkor is, ha azt kérdezi, a 15). |

**Példa**

Ez a parancs letölti fájlt a Data Lake Storage Gen1 fájl- és 100 egyidejű fájlt 20 szálat használ a felhasználó helyi lemezére.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Storage Gen1 account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Hogyan állapítható meg ezek a tulajdonságok értékét?

A következő kérdésben, lehetséges, hogy, hogyan határozza meg, milyen értéket adja meg a teljesítménnyel kapcsolatos tulajdonságok. Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A teljes szálszám meghatározása** – Kezdje a használni kívánt szálak teljes számának kiszámításával. Vegye figyelembe 6 szálat használjon minden egyes fizikai maghoz.

        Total thread count = total physical cores * 6

    **Példa**

    Tételezzük fel, hogy egy 16 maggal rendelkező D14 VM-en futtatja a PowerShell-parancsokat.

        Total thread count = 16 cores * 6 = 96 threads


* **2. lépés: A PerFileThreadCount kiszámítása** – A PerFileThreadCount számítását fájlok mérete alapján végezzük. 2,5 GB-nál kisebb fájlok esetén hiba esetén nem kell módosítani ezt a paramétert, mert a rendszer az alapértelmezett 10 is elegendő. A 2,5 GB-nál nagyobb fájlok esetében 10 szálat használja alapként az első 2,5 GB és a fájlméret minden további 256 MB-os növekedést 1 szálat hozzáadása. Ha olyan mappát másol, amelyben nagyon eltérő méretű fájlok találhatók, érdemes hasonló fájlméret alapján csoportosítani a fájlokat. Az eltérő fájlméretek az optimálisnál kisebb teljesítménnyel járhatnak. Ha nem lehetséges a hasonló fájlméret alapján történő csoportosítás, akkor a PerFileThreadCount értékét a legnagyobb fájlméret alapján kell beállítani.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Példa**

    Feltételezve, hogy 1 GB-ból 10 GB-os és 100 fájllal rendelkezik, használunk a 10 GB-os, a legnagyobb fájlméret egyenlet, amely a következőhöz hasonló olvashatja.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **3. lépés: A ConcurrentFilecount kiszámítása** – használja a teljes szálszámot és a perfilethreadcount értékét a ConcurrentFileCount kiszámításához az alábbi egyenlet alapján:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Példa**

    Az eddig használt példaértékek alapján

        96 = 40 * ConcurrentFileCount

    Tehát a **ConcurrentFileCount** értéke **2,4**, amelyet kerekíthetünk **2**-re.

## <a name="further-tuning"></a>További hangolás

Elképzelhető, hogy további hangolásra lesz szüksége, mert különböző fájlméretekkel kell dolgoznia. A fenti számítás jól működik, ha az összes vagy a fájlok a legtöbb e nagyobb, a 10 GB-os tartomány közelebb. Ha ehelyett sok különböző, nagyon eltérő méretű fájllal rendelkezik, akkor csökkentheti a PerFileThreadCount értékét. A PerFileThreadCount értékének csökkentésével, növelhetjük a ConcurrentFileCount értékét. Tehát ha feltételezzük, hogy a legtöbb kisebbek 5 GB-os tartományba, azt is ismétlése végrehajthatjuk a számítást:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Tehát **ConcurrentFileCount** 96/20, amely 4.8, lesz kerekítve **4**.

A beállítások hangolását a **PerFileThreadCount** értékének növelésével vagy csökkentésével folytathatja a fájlméretek eloszlásától függően.

### <a name="limitation"></a>Korlátozás

* **A fájlok száma kisebb, mint a ConcurrentFileCount értéke**: Ha a feltölteni kívánt fájlok száma kisebb a **ConcurrentFileCount** kiszámított értékének, akkor csökkentse a **ConcurrentFileCount** értékét úgy, hogy az egyenlő legyen a fájlok számával. A fennmaradó szálakat a **PerFileThreadCount** értékének a növelésére használhatja.

* **Túl sok szál**: Ha túlságosan megnöveli a szálszámot a fürt méretének növelése nélkül, az rosszabb teljesítménnyel járhat. Versengési problémák adódhatnak, ha kontextusváltás történik a CPU-n.

* **Elégtelen egyidejűség**: Ha az egyidejűség nem elégséges, lehet, hogy túl kicsi a fürt. Növelheti a csomópontok számát a fürtben, ami több egyidejűséget biztosít.

* **Szabályozási hibák**: Elképzelhető, hogy szabályozási hibákat tapasztal, ha az egyidejűség túl magas. Ha szabályozás hibák merülnek fel, csökkentse az egyidejűséget vagy lépjen kapcsolatba velünk.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

