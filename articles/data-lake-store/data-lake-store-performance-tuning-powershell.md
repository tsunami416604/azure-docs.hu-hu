---
title: Útmutató a Powershell használatával a Data Lake Store teljesítményhangolás |} Microsoft Docs
description: Tippek az Azure PowerShell használata a Data Lake Store teljesítményének javításával
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
ms.openlocfilehash: 7b19972ed4a75ac899a4b78b28ab36ba305a5a64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198650"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Útmutató a PowerShell használata az Azure Data Lake Store teljesítményhangolása

Ez a cikk felsorolja a tulajdonságokat, amelyeket is kell beállítani, hogy közben a PowerShell használata a Data Lake Store egy nagyobb teljesítményre van szüksége:

## <a name="performance-related-properties"></a>Teljesítménnyel kapcsolatos tulajdonságai

| Tulajdonság            | Alapértelmezett | Leírás |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ez a paraméter lehetővé teszi a párhuzamos szálak számának megadását az egyes fájlok fel- vagy letöltéséhez. Ez a szám jelenti. a maximális szálak / fájl osztják, de kevesebb szálak kaphat a forgatókönyvtől függően (például ha egy 1 KB-os fájlt feltölteni, kap egy szálat még akkor is, ha 20 szálak fel).  |
| ConcurrentFileCount | 10      | Ez a paraméter kifejezetten a mappák fel- és letöltéséhez kapcsolódik. Ez a paraméter határozza meg az egyidejűleg fel- vagy letölthető fájlok számát. Ez a szám egyidejű feltöltött, illetve egy időben letöltött fájlok maximális számát jelöli, de kevesebb párhuzamossági kaphat a forgatókönyvtől függően (például, ha két fájlt feltölteni, kapott két egyidejű fájlok feltöltését még akkor is, ha azt kérdezi, a 15). |

**Példa**

Ezzel a paranccsal fájlokat tölthet le az Azure Data Lake Store-ból a felhasználó helyi lemezére fájlonként 20 szálat és 100 egyidejű fájlt használva.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Hogyan állapítható meg ezek a tulajdonságok értékét?

Lehetséges, hogy a következő kérdésre, hogyan biztosít a teljesítménnyel kapcsolatos tulajdonságok értékről. Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A teljes szálszám meghatározása** – Kezdje a használni kívánt szálak teljes számának kiszámításával. Általános irányelvként elmondható hat szálak minden fizikai magok kell használni.

        Total thread count = total physical cores * 6

    **Példa**

    Tételezzük fel, hogy egy 16 maggal rendelkező D14 VM-en futtatja a PowerShell-parancsokat.

        Total thread count = 16 cores * 6 = 96 threads


* **2. lépés: A PerFileThreadCount kiszámítása** – A PerFileThreadCount számítását fájlok mérete alapján végezzük. 2.5 GB-nál kisebb fájlok esetén nincs szükség ennek a paraméternek módosítható, mert az alapértelmezett 10 is elegendő. 2.5 GB-nál nagyobb méretű fájlokhoz 10 szálat használják az alapja az első 2,5 GB és a fájlméret 1 szál minden további 256 MB-os növekedést hozzáadása. Ha olyan mappát másol, amelyben nagyon eltérő méretű fájlok találhatók, érdemes hasonló fájlméret alapján csoportosítani a fájlokat. Az eltérő fájlméretek az optimálisnál kisebb teljesítménnyel járhatnak. Ha nem lehetséges a hasonló fájlméret alapján történő csoportosítás, akkor a PerFileThreadCount értékét a legnagyobb fájlméret alapján kell beállítani.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Példa**

    Ha 100 és 10 GB-os 1 GB közötti fájlokat, azt használják a 10 GB-os a legnagyobb fájlméretet a egyenlet, amely a következőhöz hasonló olvashatók.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **3. lépés: Kiszámításához ConcurrentFilecount** – használja a teljes szálak száma és ConcurrentFileCount kiszámításához PerFileThreadCount alapján a következő egyenlet:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Példa**

    Az eddig használt példaértékek alapján

        96 = 40 * ConcurrentFileCount

    Tehát a **ConcurrentFileCount** értéke **2,4**, amelyet kerekíthetünk **2**-re.

## <a name="further-tuning"></a>További hangolás

Elképzelhető, hogy további hangolásra lesz szüksége, mert különböző fájlméretekkel kell dolgoznia. A fenti számítás működik jól esetén a fájlok a legtöbb vagy összes nagyobb és 10 GB-os tartományba közelebb. Ha ehelyett sok különböző, nagyon eltérő méretű fájllal rendelkezik, akkor csökkentheti a PerFileThreadCount értékét. A PerFileThreadCount értékének csökkentésével, növelhetjük a ConcurrentFileCount értékét. Ezért ha feltételezzük, hogy a fájlok többsége kisebb 5 GB-os tartományba, azt visszavonható-e a számítás:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Igen **ConcurrentFileCount** 96/20, amely 4.8, válik kerekíteni **4**.

A beállítások hangolását a **PerFileThreadCount** értékének növelésével vagy csökkentésével folytathatja a fájlméretek eloszlásától függően.

### <a name="limitation"></a>Korlátozás

* **A fájlok száma kisebb, mint a ConcurrentFileCount értéke**: Ha a feltölteni kívánt fájlok száma kisebb a **ConcurrentFileCount** kiszámított értékének, akkor csökkentse a **ConcurrentFileCount** értékét úgy, hogy az egyenlő legyen a fájlok számával. A fennmaradó szálakat a **PerFileThreadCount** értékének a növelésére használhatja.

* **Túl sok szál**: Ha túlságosan megnöveli a szálszámot a fürt méretének növelése nélkül, az rosszabb teljesítménnyel járhat. Versengési problémák adódhatnak, ha kontextusváltás történik a CPU-n.

* **Elégtelen egyidejűség**: Ha az egyidejűség nem elégséges, lehet, hogy túl kicsi a fürt. A fürt, amely több egyidejű lehetővé teszi a csomópontok száma növelhető.

* **Szabályozási hibák**: Elképzelhető, hogy szabályozási hibákat tapasztal, ha az egyidejűség túl magas. Ha szabályozás hibák merülnek fel, csökkentse az egyidejűséget vagy lépjen kapcsolatba velünk.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Store használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)

