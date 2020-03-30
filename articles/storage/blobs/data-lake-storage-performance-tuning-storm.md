---
title: 'Hangolási teljesítmény: Storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft dokumentumok'
description: Az Azure Data Lake Storage Gen2 Storm teljesítményhangolási irányelvei
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327901"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Hangolási teljesítmény: Storm, HDInsight & Azure Data Lake Storage Gen2

Az Azure Storm-topológia teljesítményének finomhangolásakor figyelembe veendő tényezők megismerése. Fontos például, hogy megértsük a spouts és a csavarok által végzett munka jellemzőit (függetlenül attól, hogy a munka I/O vagy memóriaigényes). Ez a cikk számos teljesítményhangolási irányelvet ismerteti, beleértve a gyakori problémák elhárítását.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2 fiók.** A létrehozásról a [Rövid útmutató: Tárfiók létrehozása analitikushoz](data-lake-storage-quickstart-create-account.md)című témakörben talál.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen2-fiókhoz. Lásd: [Azure Data Lake Storage Gen2 használata Az Azure HDInsight-fürtökkel.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.
* **Storm-fürt futtatása a Data Lake Storage Gen2 szolgáltatáson.** További információ: [Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen2 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen2 teljesítményhangolási útmutatócímű témakörben talál.](data-lake-storage-performance-tuning-guidance.md)   

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságának finomhangolása

Előfordulhat, hogy javíthatja a teljesítményt az I/O és a Data Lake Storage Gen2 egyidejűsítésével. A Storm topológia olyan konfigurációkkal rendelkezik, amelyek meghatározzák a párhuzamosságot:
* A munkavégző folyamatok száma (a dolgozók egyenletesen oszlanak el a virtuális gépek között).
* A spout végrehajtó példányainak száma.
* A bolt végrehajtó példányainak száma.
* A spout feladatok száma.
* A csavarfeladatok száma.

Például egy 4 virtuális géptel és 4 munkavégző folyamattal, 32 kifolyó végrehajtóval és 32 kifolyó feladattal, valamint 256 csavarvégrehajtóval és 512 csavarfeladattal rendelkező fürtön vegye figyelembe a következőket:

Minden felügyelő, amely egy feldolgozó csomópont, egyetlen feldolgozó Java virtuális gép (JVM) folyamattal rendelkezik. Ez a JVM folyamat 4 kifolyó szálat és 64 csavarmenetet kezel. Az egyes szálakon belül a feladatok egymás után futnak. Az előző konfigurációval minden spout szál 1 feladattal rendelkezik, és minden csavarszál 2 feladattal rendelkezik.

A Storm, itt vannak a különböző alkatrészek et, és hogyan befolyásolják a párhuzamosság szintjét van:
* A fő csomópont (úgynevezett Nimbus a Storm) a feladatok küldésére és kezelésére szolgál. Ezek a csomópontok nincsenek hatással a párhuzamosság mértékére.
* A felügyeleti csomópontok. A HDInsightban ez egy feldolgozócsomópont nak, az Azure virtuális gépnek felel meg.
* A dolgozói feladatok storm folyamatok futnak a virtuális gépeken. Minden dolgozói feladat egy JVM-példánynak felel meg. A Storm a munkavégző csomópontok nak a lehető legegyenletesebben osztja el a megadott munkavégző folyamatok számát.
* Spout és bolt végrehajtó példányok. Minden végrehajtó példány a dolgozókon (JVM-ek) futó szálnak felel meg.
* Vihar feladatok. Ezek olyan logikai feladatok, amelyeket ezek a szálak futtatnak. Ez nem változtatja meg a párhuzamosság szintjét, ezért ki kell értékelnie, hogy végrehajtónként több feladatra van-e szüksége vagy sem.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>A legjobb teljesítmény a Data Lake Storage Gen2-től

A Data Lake Storage Gen2 szolgáltatással végzett munka során a legjobb teljesítményt kapja, ha az alábbi módon:
* Egyesítse a kis hozzáfűzéseket nagyobb méretekre.
* Tegyen meg annyi egyidejű kérést, amennyit csak tud. Mivel minden csavarszál blokkolja az olvasásokat, azt szeretné, hogy valahol a magonként 8-12 szál tartományban legyen. Ez tartja a NIC és a CPU jól hasznosított. Egy nagyobb virtuális gép több egyidejű kérést tesz lehetővé.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy egy 8 feldolgozói csomópont-fürtddel rendelkezik egy D13v2 Azure virtuális géptel. Ez a virtuális gép 8 maggal rendelkezik, így a 8 munkavégző csomópont között összesen 64 mag van.

Tegyük fel, hogy magonként 8 csavarmenetet csinálunk. Mivel 64 magok, ez azt jelenti, szeretnénk 512 teljes bolt végrehajtó példányok (azaz szálak). Ebben az esetben tegyük fel, hogy virtuális gépenként egy JVM-mel kezdjük, és főként a JVM-en belüli szál-egyidejűséget használjuk az egyidejűség eléréséhez. Ez azt jelenti, hogy 8 feldolgozói feladatra van szükség (Egy Azure virtuális gép) és 512 csavarvégrehajtók. Ebben a konfigurációban a Storm megpróbálja elosztani a dolgozók egyenletesen a munkavégző csomópontok (más néven felügyeleti csomópontok), így minden dolgozó csomópont 1 JVM. Most a felügyelőkön belül a Storm megpróbálja a végrehajtókat egyenletesen elosztani a felügyelők között, így minden egyes felügyelő (azaz jvm) 8 szálat ad meg.

## <a name="tune-additional-parameters"></a>További paraméterek finomhangolása
Miután rendelkezik az alapvető topológiával, mérlegelheti, hogy módosítani szeretné-e valamelyik paramétert:
* **JVM-ek száma munkavégző csomópontonként.** Ha nagy adatstruktúrával (például egy keresett táblával) rendelkezik, amelyet a memóriában üzemeltet, minden JVM-nek külön másolatra van szüksége. Azt is megteheti, hogy az adatszerkezetet több szálon is használhatja, ha kevesebb JVM-et használ. A csavar I/O-ja esetében a JVM-ek száma nem okoz annyi különbséget, mint a JVM-ek között hozzáadott szálak száma. Az egyszerűség kedvéért, ez egy jó ötlet, hogy egy JVM munkavállalónként. Attól függően, hogy mit csinál a csavar, vagy milyen alkalmazás feldolgozásra van szüksége, bár előfordulhat, hogy módosítania kell ezt a számot.
* **A kifolyó végrehajtóinak száma.** Mivel az előző példa a Data Lake Storage Gen2-re való íráshoz használja a csavarokat, a spoutok száma nem közvetlenül releváns a csavar teljesítményéhez. Azonban attól függően, hogy a feldolgozás vagy az I/O történik a kifolyóban, ez egy jó ötlet, hogy beállítsa a spouts a legjobb teljesítményt. Győződjön meg arról, hogy van elég spouts, hogy képes legyen tartani a csavarok foglalt. A spoutok kimeneti sebességének meg kell egyeznie a csavarok átviteli sebességével. A tényleges konfiguráció a kifolyótól függ.
* **A feladatok száma.** Minden csavar egyetlen szálként fut. A boltonkénti további feladatok nem biztosítanak további egyidejűséget. Az egyetlen alkalom, hogy a haszon, ha a folyamat elismerve a tuple vesz nagy részét a bolt végrehajtási idő. Érdemes sok tuple-t egy nagyobb hozzáfűzésbe csoportosítani, mielőtt nyugtát küldenél a csavarról. Így a legtöbb esetben több feladat nem nyújt további előnyt.
* **Helyi vagy véletlen sorrendű csoportosítás.** Ha ez a beállítás engedélyezve van, a rendszer a tesztelő folyamaton belül lévő csavaroknak küldi a tuple-okat. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívásokat. Ez a legtöbb topológiához ajánlott.

Ez az alapvető forgatókönyv egy jó kiindulási pont. Tesztelje a saját adataival az előző paraméterek optimális teljesítmény ének eléréséhez.

## <a name="tune-the-spout"></a>A kifolyó finomhangolása

A kifolyó beállításához a következő beállításokat módosíthatja.

- **Tuple időmegtümő: topology.message.timeout.secs**. Ez a beállítás határozza meg, hogy az üzenet mennyi időt vesz igénybe a sikertelennek ítélt üzenet befejezéséhez és visszaigazolásának fogadásához.

- **Maximális memória munkavégző folyamatonként: worker.childopts**. Ezzel a beállítással további parancssori paramétereket adhat meg a Java-dolgozóknak. A leggyakrabban használt beállítás itt az XmX, amely meghatározza a JVM halommemóriához lefoglalt maximális memóriát.

- **Maximális kifolyó függőben: topology.max.spout.pending**. Ez a beállítás határozza meg, hogy a számlálószálonként bármikor hány olyan tuple-t lehet végrehajtani (a topológia egy csomópontjában még nem nyugtázható).

  Egy jó számítás az, hogy megbecsülje az egyes tuples méretét. Ezután kikell találnia, hogy mennyi memóriája van egy kifolyó szálnak. A szálhoz lefoglalt teljes memória, osztva ezzel az értékkel, megadja a maximális kifolyó függő paraméterének felső határát.

Az alapértelmezett Data Lake Storage Gen2 Storm bolt rendelkezik egy méretszinkronizálási házirend-paraméterrel (fileBufferSize), amely a paraméter beállítására használható.

Az I/O-igényes topológiákban célszerű, ha minden egyes csavarszál at a saját fájljába ír, és fájlelforgatási házirendet (fileRotationSize) állít be. Amikor a fájl elér egy bizonyos méretet, az adatfolyam automatikusan kiürítésre kerül, és új fájl kerül írásra. Az elforgatáshoz ajánlott fájlméret 1 GB.

## <a name="monitor-your-topology-in-storm"></a>A topológia figyelése a Stormban  
Atopológiája futása közben figyelheti a Storm felhasználói felületén. Itt vannak a fő paramétereket, hogy nézd meg:

* **Teljes folyamatvégrehajtási késés.** Ez az az átlagos idő, amelyalatt egy törzs a kifolyó által kibocsátott, a csavar által feldolgozott és nyugtázott.

* **Teljes csavarfolyamat-késés.** Ez az átlagos töltött idő a csavar, amíg nem kap nyugtázást.

* **A teljes csavar késést hajt végre.** Ez a csavar által a végrehajtási metódusban töltött átlagos idő.

* **A hibák száma.** Ez azon számlálók számára vonatkozik, amelyeket nem sikerült teljes mértékben feldolgozni az időtúllépést megelőzően.

* **Kapacitás.** Ez az intézkedés, hogy milyen elfoglalt a rendszer. Ha ez a szám 1, a csavarok működnek, amilyen gyorsan csak tudnak. Ha ez kevesebb, mint 1, növelje a párhuzamosság. Ha ez nagyobb, mint 1, csökkentse a párhuzamosság.

## <a name="troubleshoot-common-problems"></a>Gyakori problémák elhárítása
Íme néhány gyakori hibaelhárítási forgatókönyv.
* **Sok tuples az idő túllépik az időzést.** Tekintse meg a topológia minden csomópontján, hogy meghatározza, hol van a szűk keresztmetszet. Ennek leggyakoribb oka az, hogy a csavarok nem képesek lépést tartani a spouts. Ez azt eredményezi, hogy a belső pufferek eltömődnek, miközben a feldolgozásra várnak. Fontolja meg az időtúlérték növelését vagy a függőben lévő maximális kifolyó csökkentését.

* **Van egy nagy teljes folyamat végrehajtási késés, de egy alacsony csavar folyamat késése.** Ebben az esetben lehetséges, hogy a tuples nem ismerik el elég gyorsan. Ellenőrizze, hogy elegendő számú elismerő van-e. Egy másik lehetőség az, hogy túl sokáig várakoznak a várólistában, mielőtt a csavarok elkezdik feldolgozni őket. Csökkentse a függőben lévő maximális kifolyót.

* **Van egy nagy csavar végrehajtási késés.** Ez azt jelenti, hogy a csavar execute() metódusa túl sokáig tart. Optimalizálja a kódot, vagy tekintse meg az írási méreteket és a kiürítési viselkedést.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 szabályozás
Ha eléri a Data Lake Storage Gen2 által biztosított sávszélesség-korlátokat, előfordulhat, hogy feladathibák jelennek meg. Ellenőrizze a feladatnaplókban a szabályozási hibákat. A tároló méretének növelésével csökkentheti a párhuzamosságmértékét.    

Annak ellenőrzéséhez, hogy szabályozás van-e, engedélyezze a hibakeresési naplózást az ügyféloldalon:

1. Az **Ambari** > **Storm** > **Config** > Advanced**storm-worker-log4j**területen módosítsa ** &lt;a gyökérszintet="info"&gt; ** a root ** &lt;level="debug"&gt;parancsot.** Indítsa újra az összes csomópontot/szolgáltatást a konfiguráció érvénybe léptetéséhez.
2. A Storm topológia naplók figyelése a munkavégző csomópontokon (a&lt;/var/log/storm/worker-artifacts/ TopologyName&gt;/&lt;port&gt;/worker.log) alatt a Data Lake Storage Gen2 szabályozási kivételek.

## <a name="next-steps"></a>További lépések
További teljesítmény tuning Storm lehet hivatkozni ebben a [blogban](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Egy további futtatásához tekintse meg [ezt a GitHubon.](https://github.com/hdinsight/storm-performance-automation)
