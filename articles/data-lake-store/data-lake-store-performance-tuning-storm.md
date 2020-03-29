---
title: Az Azure Data Lake Storage Gen1 Storm teljesítményhangolására vonatkozó irányelvei | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen1 Storm teljesítményhangolási irányelvei
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436477"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítményhangolási útmutató a Storm hoz a HDInsight és az Azure Data Lake Storage Gen1 szolgáltatáshoz

Az Azure Storm-topológia teljesítményének finomhangolásakor figyelembe veendő tényezők megismerése. Fontos például, hogy megértsük a spouts és a csavarok által végzett munka jellemzőit (függetlenül attól, hogy a munka I/O vagy memóriaigényes). Ez a cikk számos teljesítményhangolási irányelvet ismerteti, beleértve a gyakori problémák elhárítását.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1 fiók.** Az Azure Data Lake Storage Gen1 – [Első lépések az Azure Data Lake Storage Gen1 című](data-lake-store-get-started-portal.md)témakörben található.
* **Egy Azure HDInsight-fürt,** amely egy Data Lake Storage Gen1-fiókhoz fér hozzá. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört.](data-lake-store-hdinsight-hadoop-use-portal.md) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.
* **Storm-fürt futtatása a Data Lake Storage Gen1 szolgáltatáson.** További információ: [Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen1 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen1 teljesítményhangolási útmutatócímű témakörben talál.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságának finomhangolása

Előfordulhat, hogy javíthatja a teljesítményt az I/O és a Data Lake Storage Gen1 egyidejűsűségének növelésével. A Storm topológia olyan konfigurációkkal rendelkezik, amelyek meghatározzák a párhuzamosságot:
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

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>A legjobb teljesítmény a Data Lake Storage Gen1-től

A Data Lake Storage Gen1 szolgáltatással végzett munka során a legjobb teljesítményt kapja, ha az alábbi módon:
* Egyesítse a kis hozzáfűzést nagyobb méretekre (ideális esetben 4 MB).
* Tegyen meg annyi egyidejű kérést, amennyit csak tud. Mivel minden csavarszál blokkolja az olvasásokat, azt szeretné, hogy valahol a magonként 8-12 szál tartományban legyen. Ez tartja a NIC és a CPU jól hasznosított. Egy nagyobb virtuális gép több egyidejű kérést tesz lehetővé.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy egy 8 feldolgozói csomópont-fürtddel rendelkezik egy D13v2 Azure virtuális géptel. Ez a virtuális gép 8 maggal rendelkezik, így a 8 munkavégző csomópont között összesen 64 mag van.

Tegyük fel, hogy magonként 8 csavarmenetet csinálunk. Mivel 64 magok, ez azt jelenti, szeretnénk 512 teljes bolt végrehajtó példányok (azaz szálak). Ebben az esetben tegyük fel, hogy virtuális gépenként egy JVM-mel kezdjük, és főként a JVM-en belüli szál-egyidejűséget használjuk az egyidejűség eléréséhez. Ez azt jelenti, hogy 8 feldolgozói feladatra van szükség (Egy Azure virtuális gép) és 512 csavarvégrehajtók. Ebben a konfigurációban a Storm megpróbálja elosztani a dolgozók egyenletesen a munkavégző csomópontok (más néven felügyeleti csomópontok), így minden dolgozó csomópont 1 JVM. Most a felügyelőkön belül a Storm megpróbálja a végrehajtókat egyenletesen elosztani a felügyelők között, így minden egyes felügyelő (azaz jvm) 8 szálat ad meg.

## <a name="tune-additional-parameters"></a>További paraméterek finomhangolása
Miután rendelkezik az alapvető topológiával, mérlegelheti, hogy módosítani szeretné-e valamelyik paramétert:
* **JVM-ek száma munkavégző csomópontonként.** Ha nagy adatstruktúrával (például egy keresett táblával) rendelkezik, amelyet a memóriában üzemeltet, minden JVM-nek külön másolatra van szüksége. Azt is megteheti, hogy az adatszerkezetet több szálon is használhatja, ha kevesebb JVM-et használ. A csavar I/O-ja esetében a JVM-ek száma nem okoz annyi különbséget, mint a JVM-ek között hozzáadott szálak száma. Az egyszerűség kedvéért, ez egy jó ötlet, hogy egy JVM munkavállalónként. Attól függően, hogy mit csinál a csavar, vagy milyen alkalmazás feldolgozásra van szüksége, bár előfordulhat, hogy módosítania kell ezt a számot.
* **A kifolyó végrehajtóinak száma.** Mivel az előző példa a Data Lake Storage Gen1-re való íráshoz használja a csavarokat, a spoutok száma nem közvetlenül releváns a csavar teljesítményéhez. Azonban attól függően, hogy a feldolgozás vagy az I/O történik a kifolyóban, ez egy jó ötlet, hogy beállítsa a spouts a legjobb teljesítményt. Győződjön meg arról, hogy van elég spouts, hogy képes legyen tartani a csavarok foglalt. A spoutok kimeneti sebességének meg kell egyeznie a csavarok átviteli sebességével. A tényleges konfiguráció a kifolyótól függ.
* **A feladatok száma.** Minden csavar egyetlen szálként fut. A boltonkénti további feladatok nem biztosítanak további egyidejűséget. Az egyetlen alkalom, hogy a haszon, ha a folyamat elismerve a tuple vesz nagy részét a bolt végrehajtási idő. Érdemes sok tuple-t egy nagyobb hozzáfűzésbe csoportosítani, mielőtt nyugtát küldenél a csavarról. Így a legtöbb esetben több feladat nem nyújt további előnyt.
* **Helyi vagy véletlen sorrendű csoportosítás.** Ha ez a beállítás engedélyezve van, a rendszer a tesztelő folyamaton belül lévő csavaroknak küldi a tuple-okat. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívásokat. Ez a legtöbb topológiához ajánlott.

Ez az alapvető forgatókönyv egy jó kiindulási pont. Tesztelje a saját adataival az előző paraméterek optimális teljesítmény ének eléréséhez.

## <a name="tune-the-spout"></a>A kifolyó finomhangolása

A kifolyó beállításához a következő beállításokat módosíthatja.

- **Tuple időmegtümő: topology.message.timeout.secs**. Ez a beállítás határozza meg, hogy az üzenet mennyi időt vesz igénybe a sikertelennek ítélt üzenet befejezéséhez és visszaigazolásának fogadásához.

- **Maximális memória munkavégző folyamatonként: worker.childopts**. Ezzel a beállítással további parancssori paramétereket adhat meg a Java-dolgozóknak. A leggyakrabban használt beállítás itt az XmX, amely meghatározza a JVM halommemóriához lefoglalt maximális memóriát.

- **Maximális kifolyó függőben: topology.max.spout.pending**. Ez a beállítás határozza meg, hogy a számlálószálonként bármikor hány olyan tuple-t lehet végrehajtani (a topológia egy csomópontjában még nem nyugtázható).

  Egy jó számítás az, hogy megbecsülje az egyes tuples méretét. Ezután kikell találnia, hogy mennyi memóriája van egy kifolyó szálnak. A szálhoz lefoglalt teljes memória, osztva ezzel az értékkel, megadja a maximális kifolyó függő paraméterének felső határát.

## <a name="tune-the-bolt"></a>Állítsa be a csavart
Amikor a Data Lake Storage Gen1 szolgáltatásba ír, állítsa a méretszinkronizálási házirendet (az ügyféloldalon lévő puffert) 4 MB-ra. A kiürítés vagy hsync() csak akkor történik meg, ha a puffer mérete az ezen az értéken. A Data Lake Storage Gen1 illesztőprogramja a feldolgozó virtuális gép automatikusan elvégzi ezt a pufferelést, kivéve, ha explicit módon hajt végre egy hsync().

Az alapértelmezett Data Lake Storage Gen1 storm bolt rendelkezik egy méretszinkronizálási házirend paraméterrel (fileBufferSize), amely a paraméter beállítására használható.

Az I/O-igényes topológiákban célszerű, ha minden egyes csavarszál at a saját fájljába ír, és fájlelforgatási házirendet (fileRotationSize) állít be. Amikor a fájl elér egy bizonyos méretet, az adatfolyam automatikusan kiürítésre kerül, és új fájl kerül írásra. Az elforgatáshoz ajánlott fájlméret 1 GB.

### <a name="handle-tuple-data"></a>Hangadatok kezelése

A Storm, a kifolyó tartja a tuple, amíg azt kifejezetten elismerte a bolt. Ha a bolt egy-egy támszor, de még nem nyugtázta, a spout előfordulhat, hogy nem maradt meg a Data Lake Storage Gen1 háttérrendszer. A nyomor nyugtázása után a spout garantáltan megőrzi a boltot, majd törölheti a forrásadatokat bármilyen forrásból, ahonnan olvas.  

A data lake storage gen1 legjobb teljesítménye érdekében a boltpuffer 4 MB-os trom-uple adatokkal rendelkezik. Ezután írjon a Data Lake Storage Gen1 háttérrendszerébe egy 4 MB-os írási műveletként. Miután az adatok sikeresen megírták az áruházba (a hflush()hívásával), a csavar nyugtázza az adatokat a spout. Ez az, amit a példa csavar szállított itt nem. Az is elfogadható, hogy a hflush() hívás meghívása és a tuples nyugtázása előtt nagyobb számú tuple-t tartson. Ez azonban növeli a tuples a repülés, hogy a kifolyó kell tartania, és ezért növeli a szükséges memória egy JVM.However, this increases the number of tuples in flight that the spout needs to hold, and therefore increases the amount of memory required per JVM.

> [!NOTE]
> Az alkalmazások más nem teljesítési okok miatt gyakrabban (4 MB-nál kisebb adatméretben) kell nyugtáznia a törzseket. Ez azonban hatással lehet az I/O átviteli a tároló háttérrendszerére. Gondosan mérjük meg ezt a kompromisszumot a csavar I/O teljesítményével.

Ha a bejövő tuples nem magas, így a 4 MB-os puffer kitöltése hosszú időt vesz igénybe, fontolja meg ennek mérséklését:
* A csavarok számának csökkentése, így kevesebb puffert kell kitölteni.
* Időalapú vagy számlálásalapú szabályzattal rendelkezik, ahol a hflush() minden x flush- vagy y ezredmásodpercben aktiválódik, és az eddig felhalmozott tuple-ok visszaigazolásra kerülnek.

Vegye figyelembe, hogy az átviteli sebesség ebben az esetben alacsonyabb, de az események lassú ütemével a maximális átviteli sebesség egyébként sem a legnagyobb cél. Ezek a megoldások segítenek csökkenteni a teljes időt, hogy egy megbúvó áramlik át a boltba. Ez akkor is fontos lehet, ha valós idejű folyamatot szeretne, még alacsony eseményaránnyal is. Azt is vegye figyelembe, hogy ha a bejövő nagy szakítósebesség alacsony, módosítsa a topology.message.timeout_secs paramétert, hogy a tuples ne időtúllépést, amíg pufferelés vagy feldolgozás történik.

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

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1 szabályozás
Ha eléri a Data Lake Storage Gen1 által biztosított sávszélesség-korlátokat, előfordulhat, hogy feladathibák jelennek meg. Ellenőrizze a feladatnaplókban a szabályozási hibákat. A tároló méretének növelésével csökkentheti a párhuzamosságmértékét.    

Annak ellenőrzéséhez, hogy szabályozás van-e, engedélyezze a hibakeresési naplózást az ügyféloldalon:

1. Az **Ambari** > **Storm** > **Config** > Advanced**storm-worker-log4j**területen módosítsa ** &lt;a gyökérszintet="info"&gt; ** a root ** &lt;level="debug"&gt;parancsot.** Indítsa újra az összes csomópontot/szolgáltatást a konfiguráció érvénybe léptetéséhez.
2. A Storm topológia naplók figyelése a munkavégző csomópontokon (a Data&lt;Lake Storage&gt;/&lt;&gt;Gen1 szabályozása kivételek /var/log/storm/worker-artifacts/ TopologyName port /worker.log) alatt.

## <a name="next-steps"></a>További lépések
További teljesítmény tuning Storm lehet hivatkozni ebben a [blogban](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Egy további futtatásához tekintse meg [ezt a GitHubon.](https://github.com/hdinsight/storm-performance-automation)
