---
title: Az Azure Data Lake Storage Gen2 Storm teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen2 Storm teljesítmény-finomhangolási útmutató
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 2401c74b55df78014a2f642b5166b4cf0017d87d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076207"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a Storm on HDInsight és az Azure Data Lake Storage Gen2

Ismerje meg, amelyek érdemes figyelembe venni, egy Azure Storm-topológia teljesítményének hangolása. Például fontos tudni, hogy a spoutok és a boltok (a munkahelyi e i/o- vagy memóriaigényes) által végzett munka jellemzőit. Ez a cikk ismerteti a széles körű teljesítmény-finomhangolási útmutató, beleértve a gyakori hibák elhárítása.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen2 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [a rövid útmutató: Hozzon létre egy tárfiókot fiókra a elemzési](data-lake-storage-quickstart-create-account.md).
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen2-fiókot. Lásd: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.
* **Egy Storm-fürt futtatása a Data Lake Storage Gen2**. További információkért lásd: [HDInsight alatt futó Stormmal](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Teljesítmény-finomhangolási útmutató a Data Lake Storage Gen2**.  Az általános teljesítmény fogalmak, lásd: [Data Lake Storage Gen2 teljesítményének hangolása útmutatója](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságát hangolása

Előfordulhat, hogy növelje az egyidejűség és a Data Lake Storage Gen2 a i/o teljesítmény javítása érdekében. Storm-topológia párhuzamosságát meghatározó konfigurációk készletével rendelkezik:
* (A dolgozók egyenlően vannak elosztva a virtuális gépeket) a munkavégző folyamatok száma.
* Spout végrehajtó példányok száma.
* Bolt végrehajtó példányok száma.
* Spout feladatok száma.
* Bolt feladatok száma.

Ha például 4 virtuális gépet, és 4 munkavégző folyamatok, 32 spout végrehajtóval és 32 spout feladatokat, és a 256 bolt végrehajtóval és 512 bolt feladatok fürtön, vegye figyelembe a következőket:

Minden egyes felügyelő, amely egy munkavégző csomópont, egy egyetlen feldolgozói folyamat a Java virtuális gép (JVM) rendelkezik. A JVM folyamatról 4 spout a szálak és a 64 bolt szál kezeli. Mindegyik szál belül feladatok egymás után futnak. Az előző konfigurációval mindegyik spout szál 1 feladat, pedig minden egyes bolt szál 2 feladatok.

A Storm az alábbiakban a különböző összetevők érintett, és azok rendelkezik párhuzamosság szintje:
* A (Storm Nimbus elnevezésű) fő csomópontot és feladatok kezelése szolgál. Ezek a csomópontok semmilyen hatást a párhuzamosság foka nem.
* A felügyeleti csomópontok. A HDInsight ez felel meg az Azure virtuális gép munkavégző csomópont.
* A feldolgozó feladatok a virtuális gépeken futó Storm folyamatokat. Mindegyik feldolgozó tevékenység felel meg a JVM-példányra. A Storm osztja el a kell adnia a feldolgozó csomópontok lehető legegyenletesebben feldolgozó folyamatok számát.
* Spout és boltok végrehajtó példányok. A szál a feldolgozók (JVMs) belül futó minden végrehajtó példány felel meg.
* A Storm-feladatokat. Ezek a logikai feladatokat, hogy minden egyes szálait futtassa. Ez nem változtatja, párhuzamosság szintjét, így kell kiértékelni, ha több feladatot végrehajtó kiszolgálónként kell-e.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>A legjobb teljesítményt beolvasása a Data Lake Storage Gen2

Ha a Data Lake Storage Gen2 dolgozik, a lehető legjobb teljesítményt kap Ha tegye a következőket:
* Coalesce, nagyobb méretű hozzáfűzi a kisméretű.
* Hajtsa végre, sok egyidejű kérés, amennyit csak lehet. Mindegyik bolt szál blokkolása olvasási állapotát, mert Ön szeretné valahol magonként 8 – 12 szálak tartományán. Ezt követi a hálózati adapter és a Processzor felhasznált is. Nagyobb méretű virtuális gép lehetővé teszi több egyidejű kéréseket.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy egy 8 feldolgozó csomópontot tartalmazó fürt D13v2 Azure virtuális gépen. Ez a virtuális gép rendelkezik 8 mag, így többek között a 8 feldolgozó csomópontot kell 64 magok száma összesen.

Tegyük fel, magonként 8 bolt szál végzünk. A megadott 64 mag, azt jelenti, hogy szeretnénk 512 teljes bolt végrehajtó példányok (vagyis a szál). Ebben az esetben tegyük fel, hogy virtuális gépenként több JVM kezdődnie, és főként használja a hozzászóláslánc egyidejűségi belül a JVM párhuzamosság elérése érdekében. Ez azt jelenti, 8 feldolgozó feladatokat (egy Azure virtuális gépenként), és 512 bolt végrehajtóval van szükségünk. Ebben a konfigurációban a Storm megpróbálja terjeszteni a dolgozók munkavégző csomópontok (más néven a felügyeleti csomópontok) közötti egyenletes jogosultságot ad az egyes feldolgozó csomópontok 1 JVM. Most a felettesi belül Storm próbál a végrehajtóval felügyelőt közötti egyenletes elosztása, így minden egyes felügyeleti (vagyis a JVM) 8 szálait minden.

## <a name="tune-additional-parameters"></a>További paraméterek hangolása
Miután az alapszintű topológiát, érdemes lehet hogy szeretne-e a Teljesítménybeállítások paraméterek egyikét:
* **JVMs száma feldolgozó csomópontonként.** Ha egy nagy mennyiségű adat struktúra (például egy keresési táblázat) a memóriában, minden egyes JVM gazdagép igényel külön példányt. Másik lehetőségként használhatja az adatok struktúrája számos szálakon Ha kevesebb JVMs. A bolt i/o JVMs száma nem derül lehető különbség a között ezek JVMs hozzáadott szálak számának. Az egyszerűség kedvéért célszerű worker kiszolgálónként egy JVM rendelkezik. Mi a bolt végez, illetve milyen alkalmazás feldolgozása, attól függően szükséges, azonban szükség lehet módosítani ezt a számot.
* **Spout végrehajtóval száma.** Mivel az előző példában a Data Lake Storage Gen2-re írására boltok használ, a spoutok az értéke nem közvetlenül kapcsolódik a bolt teljesítményét. Azonban feldolgozás vagy i/o a spout történik az igényelt kreditmennyiség függvényében, egy célszerű a spoutok a legjobb teljesítmény hangolására. Győződjön meg arról, hogy rendelkezik-e elegendő spoutok tudják tartani a boltok foglalt. A különböző kimeneti mértéke meg kell egyeznie a boltok átviteli kapacitást. A tényleges konfiguráció a spout függ.
* **Feladatok száma.** Minden egyes bolt egyetlen szálon fut. További feladatok / bolt nem ad meg semmilyen további egyidejűségi. Csak azok az előnyök, ha a rekord bosszankodnak, a folyamat vesz igénybe a bolt végrehajtási ideje nagy részét. Célszerű a csoporthoz be egy nagyobb számos rekordok hozzáfűzése nyugtázása a bolt történő elküldése előtt. Így a legtöbb esetben több feladat semmilyen további előnyt biztosítanak.
* **Helyi vagy shuffle csoportosítást.** Ha ez a beállítás engedélyezve van, az azonos feldolgozó folyamaton belül boltok rekordokat tartalmazó érkeznek. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívások. Ez a legtöbb topológiák ajánlott.

Ez a alapvető forgatókönyv egy jó kiindulási pont. Tesztelje a saját adataival, a fenti paraméterek az optimális teljesítmény érdekében módosítania.

## <a name="tune-the-spout"></a>A spout hangolása

A következő beállításokat a spout hangolásához módosíthatja.

- **Rekord időtúllépés: topology.message.timeout.secs**. Ez a beállítás meghatározza, hogy egy üzenetet, majd nyugtázása, szükséges idő előtt, nem sikerült.

- **Munkavégző folyamatok maximális memória: worker.childopts**. Ez a beállítás lehetővé teszi a Java Worker további parancssori paraméterek megadását. A leggyakrabban használt beállítás itt XmX, amely megadja, hogy a JVM-halommemória számára lefoglalt maximális memória.

- **Függőben lévő maximális spout: topology.max.spout.pending**. Ez a beállítás határozza meg, hogy a flight (a topológia összes csomópontjának jelenleg még nem nyugtázott) spout szálanként bármikor rekordok számát.

  Egy jó számítási tennie, hogy a rekordok méretének becslése. Ezt követően döntse el, hogy mennyi memóriát egy spout szál rendelkezik. A teljes memória egy olyan hozzászólásláncra, és elosztja ezt az értéket, lefoglalt kell biztosítanak a felső határérték a maximális spout függőben lévő paraméter.

Az alapértelmezett Data Lake Storage Gen2 Storm bolt paramétereinek mérete szinkronizálási házirend (fileBufferSize) ezt a paramétert hangolására használható.

I/O-igényes topológia esetén, egy célszerű mindegyik a saját fájl írása bolt szál lesz, és használatával úgy beállítani a fájl rotációja (fileRotationSize). A fájl bizonyos méretet elér, amikor a stream automatikusan ki van ürítve, és a egy új fájl beíródik. A rotációja javasolt fájl mérete 1 GB.

## <a name="monitor-your-topology-in-storm"></a>A topológia a Storm figyelése  
A topológia futása közben figyelheti a Storm felhasználói felületén. A fő paramétereket, és tekintse meg a következők:

* **Teljes folyamat-végrehajtás késése.** Ez az egy rekordot a spout által kibocsátott, a bolt által feldolgozott, és arra vonatkozik, átlagos idő.

* **Teljes bolt folyamat késleltetésére.** Ez a, amíg nem kap nyugtázása a rekord, a bolt által eltöltött átlagos időt.

* **Teljes bolt késése hajtható végre.** Ez az az execute metódus a bolt által eltöltött átlagos időt.

* **Hibák száma.** Ez vonatkozik, amelyek nem tudta teljesen feldolgozni azokat időkorlátjának lejártáig rekordok számát.

* **A kapacitás.** Ez a mérték, hogyan foglalt a rendszer. Ha ez a szám 1, a boltok gyors akkor is működik. Ha kevesebb mint 1, növelheti a párhuzamosságot. Ha nagyobb, mint 1, csökkentheti a párhuzamosság.

## <a name="troubleshoot-common-problems"></a>Gyakori hibák elhárítása
Az alábbiakban néhány gyakori hibaelhárítási forgatókönyveket.
* **Számos rekordokat tartalmazó időkorlátja.** Tekintse meg annak megállapításához, ahol a szűk keresztmetszetet a topológia összes csomópontján. Ennek leggyakoribb oka az, hogy a boltok nem érhetők el a különböző tartani. Ez belső puffer eltömődés közben feldolgozásra váró rekordok vezet. Fontolja meg az időtúllépés értéke növelésével vagy csökkentésével a maximális spout függőben van.

* **Nincs olyan magas teljes folyamat végrehajtása késés, de egy folyamat alacsony bolt késése.** Ebben az esetben is lehet, hogy a rekordok felsorolásának nincs folyamatban ismernek elég gyorsan. Ellenőrizze, hogy nincsenek-e elegendő számú acknowledgers. Egy másik lehetőség az, hogy azok vannak várakozik a túl hosszú ahhoz a boltok feldolgozását. Csökkentheti a maximális spout függőben van.

* **Van egy nagy bolt késése hajtható végre.** Ez azt jelenti, hogy az execute() metódust a bolt-túl sokáig tart. Optimalizálhatja a kódot, vagy írási méretek tekintse meg és viselkedés ürítése.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2-szabályozás
Ha eléri a Data Lake Storage Gen2 által nyújtott sávszélesség korlátai, megjelenhet tevékenységhibákat okozna. Tekintse meg a feladat naplókat szabályozási hibákat tapasztal. A párhuzamosság tároló méretének növelésével csökkenthető.    

Annak ellenőrzéséhez, hogy a, szabályozott első, a hibakeresési ügyféloldali naplózás engedélyezése:

1. A **Ambari** > **Storm** > **Config** > **storm-feldolgozó – log4j speciális**, módosítása **&lt;gyökér szintű = "info"&gt;** való  **&lt;gyökér szintű = "debug"&gt;**. Indítsa újra az összes a csomópontok/szolgáltatást a konfigurációjának érvénybe léptetéséhez.
2. A figyelő a Storm-topológia bejelentkezik a munkavégző csomópontok (/var/log/storm/worker-artifacts alatt /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) a Data Lake Storage Gen2 szabályozási kivételeket.

## <a name="next-steps"></a>További lépések
További teljesítményhangolás, a Storm lehet hivatkozni [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Egy további példa futtatásához: [erre a Githubon](https://github.com/hdinsight/storm-performance-automation).
