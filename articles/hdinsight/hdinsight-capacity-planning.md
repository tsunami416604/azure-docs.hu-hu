---
title: Az Azure HDInsight fürt kapacitástervezési |} Microsoft Docs
description: Megtudhatja, hogyan adhatja meg a kapacitást és teljesítményt a HDInsight-fürtöt.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: ce8764e79d24d36eb3b162fb5f3f688b3e9fab3f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>A HDInsight-fürtök kapacitástervezését

A HDInsight-fürt telepítése előtt tervezze meg a kívánt fürt kapacitás a szükséges teljesítményt és a skála. A tervezési segítségével optimalizálhatja a használhatóság és a költségeket. Néhány fürt kapacitás kérdésekben telepítés után nem módosítható. Ha módosítja a teljesítmény-paraméterek, a fürtök szétszerelés, és újból létrehozza a tárolt adatok elvesztése nélkül.

Kérje meg a kapacitástervezés fontos kérdések a következők:

* Mely földrajzi régióban kell telepítenie a fürtöt?
* Mennyi tárhelyre van szüksége?
* Fürt típusa kell, hogy telepíteni?
* Milyen mérete és a virtuális gép (VM) típusa a fürtcsomópontok használandó?
* Hány munkavégző csomópontokhoz legyen a fürt?

## <a name="choose-an-azure-region"></a>Válassza ki a kívánt Azure-régiót

Az Azure-régió határozza meg, ahol a fürt fizikailag lett kiépítve. Az olvasások és írások késését minimalizálása érdekében a fürt az adatok közel kell lennie.

HDInsight számos Azure-régiók érhető el. A legközelebbi régiót, lásd: a *HDInsight Linux* bejegyzésre az *adatok + analitika* a [Azure termékek elérhető régiónként](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Válassza ki a tároló helye és mérete

### <a name="location-of-default-storage"></a>Alapértelmezett tároló helyét

Az alapértelmezett tároló, vagy egy Azure Storage-fiók, vagy az Azure Data Lake Store, a fürt ugyanott kell lennie. Az Azure Storage összes helyeken érhető el. Data Lake Store érhető el bizonyos területeken – tekintse meg az aktuális Data Lake Store a rendelkezésre állási *tárolási* a [Azure termékek elérhető régiónként](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>A meglévő adatok helye

Ha már rendelkezik, egy tárfiókhoz vagy a Data Lake Store az adatokat tartalmazó, és szeretné, hogy ez a tároló használja a fürt alapértelmezett tárolására, majd telepítenie kell a fürthöz, hogy ugyanazon a helyen.

### <a name="storage-size"></a>Tároló mérete

Miután telepített egy HDInsight-fürt, további Azure Storage-fiókokat csatlakoztathat vagy más Lake hozzáférést. A storage-fiókok és a fürt ugyanazon a helyen kell lennie. A Data Lake Store lehet egy másik helyre, bár ez vezethet be néhány olvasási/írási adatkésleltetést.

Az Azure Storage rendelkezik néhány [kapacitáskorlátait](../azure-subscription-service-limits.md#storage-limits), amíg a Data Lake Store gyakorlatilag korlátlan.

A fürt másik storage-fiókok kombinációjával érheti el. Gyakori példák a következők:

* Amikor az adatok mennyisége valószínű hosszabb legyen, mint a tárolási kapacitás egy blob storage-tároló.
* Ha blobtárolóba való hozzáférés sebessége előfordulhat, hogy lépheti túl a küszöbértéket, ha sávszélesség-szabályozás következik be.
* Ha azt szeretné, hogy adatok, már feltöltött elérhető legyen a fürt egy blob-tárolóba.
* Ha azt szeretné elkülöníteni a tároló, biztonsági okokból különböző részei, vagy amelyekkel leegyszerűsíthető a felügyelet.

48 csomópontos fürt ajánlott 4 – 8 storage-fiókok. Habár bizonyos már elegendő tárhelyet, minden tárfiók további hálózati sávszélességet biztosít a számítási csomópontok. Ha egyszerre több tárfiókot, minden tárfiók előtag nélkül véletlenszerű nevet használni. Véletlenszerű elnevezési céljának összes fiók csökkenti a tárolási szűk keresztmetszetek (sávszélesség-szabályozás) vagy a közös módú hibák esélyét. A jobb teljesítmény érdekében használjon tárolási fiókonként csak egy tároló.

## <a name="choose-a-cluster-type"></a>Válassza ki a fürt típusa

A fürt típusa meghatározza, hogy a munkaterhelés, a HDInsight-fürt futtatásához, például a Hadoop, Storm, Kafka vagy Spark van konfigurálva. A rendelkezésre álló fürttípusok részletes ismertetését lásd: [Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Minden egyes fürttípus rendelkezik egy adott központi telepítési topológia, amely tartalmazza az követelményei a csomópontok száma és mérete.

## <a name="choose-the-vm-size-and-type"></a>Válassza ki a Virtuálisgép-méretet és típusa

Minden egyes fürttípus tartozik egy csomóponttípusok, és minden csomópont a virtuális gép méretét, és írja be az ismertetett lehetőségek rendelkezik.

Területen megállapíthatja, hogy az optimális fürtméret alkalmazás elvégez egy teljesítménytesztet fürt kapacitás, és növelje meg a méretet jelöli. Például használhatja a szimulált alkalmazások és szolgáltatások, vagy egy *Kanári lekérdezés*. A szimulált alkalmazások és szolgáltatások, az Ön alkalmazásokat és szolgáltatásokat futtathatnak a várt különböző méretű fürtökön fokozatosan méretének növelését, amíg el nem a kívánt teljesítményt. Kanári lekérdezés csak azután lehet beszúrni rendszeresen között a más éles lekérdezések jelenjen-e a fürt elegendő erőforrással rendelkezik-e.

A virtuális gép méretét és típusát Processzor tápellátáshoz, a RAM memória méretét és a hálózati késés határozza meg:

* CPU: A virtuális gép méretét határozzák meg, hogy az magok száma. A több mag, párhuzamos számítási minél nagyobb fokú minden csomópont érhető el. Néhány virtuális gép típust is gyorsabb magok.

* Memória: A Virtuálisgép-méretet is határozzák meg, hogy a virtuális gép rendelkezésre álló RAM mennyiségét. Az adatok tárolása a feldolgozás memória, nem pedig a lemezről, olvasási biztosítása a feldolgozó csomópontok munkaterhelések rendelkezik elegendő memóriával az adatok.

* Hálózati: A legtöbb fürt esetében, a fürt által feldolgozott adata nem a helyi lemezen, hanem egy külső tároló szolgáltatás, például a Data Lake Store vagy az Azure Storage. Vegye figyelembe a hálózati sávszélesség és a teljesítményt a csomópont virtuális gép és a tárolási szolgáltatás között. A virtuális gépek rendelkezésre álló sávszélesség általában növeli a nagyobb méretű. További információkért lásd: [virtuális gép méretének áttekintése](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Válassza ki a fürt méretezése

A fürt méretezése a virtuális gép csomópontjainak mennyisége határozza meg. Minden fürt esetében nincsenek csomóponttípusok, amelyek egy adott méretezési és csomóponttípus, amely támogatja a kibővített. A fürt például szükség lehet pontosan három ZooKeeper csomópontok vagy a Head két csomópont. Munkavégző csomópontokhoz, amelyek az adatfeldolgozás elosztott módon is kihasználhatja a munkavégző csomópontok hozzáadásával kiterjesztése.

Attól függően, hogy a fürt típusa feldolgozó csomópontok számának növelésével ad hozzá a további számítási kapacitás (például a több mag), de előfordulhat, hogy a feldolgozott adatok tárolása a memóriában támogatásához az egész fürt számára szükséges memória teljes mennyiségétől is hozzáadhat. Csakúgy, mint a virtuális gép méretét, és írja be a választott, a fürt megfelelő méretezés kiválasztása általában elérésekor empirically, a szimulált munkaterhelések vagy Kanári lekérdezések.

Ki lehet terjeszteni a fürt csúcs terhelés követelményeinek, majd vertikális le, ha azokat a további csomópontokat, már nem szükséges. További információkért lásd: [méretezési HDInsight-fürtök](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Fürt életciklusa

Van szó, a fürt élettartamát. Ha csak adott, hogy kell-e a fürt fel és fut, akkor [Azure Data Factory igény-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-adf.md). Is létrehozhat a PowerShell-parancsfájlok, amelyek kiépíteni, és törölje a fürtöt, és majd ütemezze a parancsfájlok használatával [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> A fürt törlésekor az alapértelmezett Hive metaadattárhoz is törlődik. A következő fürt újbóli létrehozása a metaadattárhoz megőrizni, egy külső metaadat-tároló, például az Azure-adatbázis vagy Oozie használja.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Különítse el a fürt hibák: feladathiba

Egyes esetekben hibák fordul elő, mivel a párhuzamos végrehajtás több leképezés és csökkentse az összetevők több csomópontos fürt esetén. A problémát, próbálja meg egyidejűleg futó elosztott tesztelése a segítségével több feladat egy egy csomópontos fürtre, majd ezt a megközelítést több feladat egyidejű futtatását egynél több csomópontot tartalmazó fürtök bontsa ki. Az Azure-ban egy egy csomópontos HDInsight-fürt létrehozásához használja a *speciális* lehetőséget.

Is egy egy csomópontos fejlesztési környezet telepítése a helyi számítógépen, és tesztelje a megoldás nincs. Hortonworks egycsomópontos helyi fejlesztési környezetet a Hadoop-alapú megoldások, amelyek hasznos, a koncepció igazolása kezdeti fejlesztési és tesztelési kínál. További információkért lásd: [Hortonworks védőfal](http://hortonworks.com/products/hortonworks-sandbox/).

Egy egy csomópontos helyi fürtön a probléma azonosításához futtassa újra a meghiúsult feladatot, és módosítsa a bemeneti adatok, vagy használjon kisebb adatkészletekre. Ezek a feladatok futtatásának módját, a platform és alkalmazás függ.

## <a name="quotas"></a>Kvóták

Után annak meghatározásához, hogy a cél fürtnek Virtuálisgép-méretet, méretezési, és írja be, ellenőrizze az aktuális kapacitás kvótakorlát az előfizetéséhez. Ha eléri a kvótát, nem lehet új fürtök üzembe helyezésekor, vagy további feldolgozó csomópontok hozzáadásával meglévő fürtök kiterjesztése. A leggyakoribb kvótakorlátot elérte a CPU-magokat kvótát, hogy létezik-e az előfizetés, a régiót és a virtuális gép adatsorozat szintjén. Azt jelzi, például az előfizetés lehet-e a Virtuálisgép-példányok 200 core teljes korlátozni, az adott régióban 30 core korlátozni, és 30 core korlátozni. Is [forduljon a támogatási szolgálathoz a kvóta növelését](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Azonban néhány rögzített kvóta korlátozott, például egy Azure-előfizetéssel, legfeljebb 10 000 magok lehet. Ezek a korlátozások a részletekért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>További lépések

* [Állítsa be a HDInsight Hadoop, Spark, Kafka és több fürt](hdinsight-hadoop-provision-linux-clusters.md): megtudhatja, hogyan telepítheti és konfigurálhatja a fürtök hdinsight Hadoop, Spark, Kafka, interaktív struktúra, HBase, R Server, vagy a Storm.
* [Fürt teljesítményének figyelése](hdinsight-key-scenarios-to-monitor.md): további információk a HDInsight-fürthöz, amelyek hatással lehetnek a fürt kapacitásának figyelése főbb forgatókönyvek.
