---
title: A fürtök kapacitástervezése az Azure HDInsight
description: Adjon meg egy HDInsight-fürtöt, a kapacitás és teljesítmény hogyan.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: hrasheed
ms.openlocfilehash: c8ca936220bf1f4d7f38858c0e09e332cd474077
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193858"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight-fürtök kapacitástervezése

Egy HDInsight-fürt üzembe helyezése előtt tervezze meg a kívánt fürt kapacitás szükséges teljesítmény és méretezés által. A tervezési segítségével optimalizálhatja a használhatóság és a költségek. Néhány fürt kapacitásának döntések üzembe helyezés után nem módosítható. Ha módosítja a teljesítmény-paraméterek, a fürt szétszerelés, és újra létrehozza a tárolt adatok elvesztése nélkül.

A kulcs kérdéseket tehet fel és a kapacitástervezés a következők:

* Mely földrajzi régióban kell üzembe helyezni a fürt?
* Mennyi tárhelyre van szüksége?
* Fürt típusának meg kell telepíteni?
* Milyen méretű és a virtuális gép (VM) típusú célszerű a fürtcsomópontok használni?
* Hány feldolgozó csomóponttal kell rendelkeznie a fürt?

## <a name="choose-an-azure-region"></a>Egy Azure-régió kiválasztása

Az Azure-régió határozza meg, ahol a fürt fizikailag van kiépítve. Az olvasási és írási késés minimalizálása érdekében a fürt az adatok közel kell lennie.

HDInsight számos Azure-régióban érhető el. Keresse meg a legközelebbi régió, tekintse meg a *HDInsight* bejegyzés alatt *Analytics* a [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Válassza ki a tároló helye és mérete

### <a name="location-of-default-storage"></a>Alapértelmezett tároló helye

Az alapértelmezett tároló, egy Azure Storage-fiókot vagy egy Azure Data Lake Store a fürt ugyanazon a helyen kell lennie. Az Azure Storage minden helyeken érhető el. Data Lake Store Gen1 érhető el egyes régiókban – tekintse meg a jelenlegi Data Lake Store rendelkezésre állási csoportban *tárolási* a [Azure termékek rendelkezésre álló régiók szerint](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>A meglévő adatok helye

Ha már rendelkezik, a storage-fiók vagy a Data Lake Store az Ön adatait tartalmazó, és szeretné használni ezt a tárolót a fürt alapértelmezett tárolóként, majd telepítenie kell a fürt adott azonos helyen található.

### <a name="storage-size"></a>Tároló mérete

Miután egy üzembe helyezett HDInsight-fürtöt, további Azure Storage-fiókokat csatlakoztathat vagy egyéb Data Lake Store eléréséhez. A storage-fiókok a fürt ugyanazon a helyen kell lennie. Bár ez vezethet be bizonyos adatok olvasási/írási késés egy Data Lake Store egy másik helyre is lehet.

Az Azure Storage rendelkezik néhány [kapacitáskorlátait](../azure-subscription-service-limits.md#storage-limits), míg a Data Lake Store Gen1 gyakorlatilag korlátlan.

A fürt különböző tárfiókokban kombinációját férhetnek hozzá. Tipikus példák:

* Ha az adatok mennyisége valószínű, hogy egy blob storage-tároló tárolási kapacitásának túllépésére.
* Ha a blob-tárolóba való hozzáférés sebessége meghaladhatja a küszöbértéket, ha szabályozás fordul elő.
* Ha azt szeretné, hogy az adatok, már feltöltötte a fürt számára elérhetővé blob-tárolóba.
* Mikor érdemes elkülöníteni a tárolási, biztonsági okokból különböző részeit, vagy amelyekkel leegyszerűsíthető a felügyelet.

48 csomópontos fürt esetén javasoljuk, hogy a tárfiókok 4 – 8. Már elegendő tárterület teljes lehet, bár egyes tárfiók további hálózati sávszélességet biztosít a számítási csomópontok. Ha több tárfiók, minden olyan tárfiókhoz előtag nélkül véletlenszerű nevet használhat. Véletlenszerű elnevezési célját összes fiók csökkenthető a tárolási szűk keresztmetszetek (szabályozás) vagy a közös módú hibák esélyét. A jobb teljesítmény érdekében használja a storage-fiókonként csak egy tároló.

## <a name="choose-a-cluster-type"></a>Fürt típusának kiválasztása

Fürt típusa határozza meg a számítási feladatok futnak, például a HDInsight-fürt van konfigurálva [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/), vagy [ Az Apache Spark](https://spark.apache.org/). Az elérhető fürttípusok részletes ismertetését lásd: [Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Mindegyik fürttípus rendelkezik egy adott központi telepítési topológiában, amely megfelel a követelményeknek és a csomópontok számát.

## <a name="choose-the-vm-size-and-type"></a>Válassza ki a virtuális gép méretét és típusát

Mindegyik fürttípus csomóponttípusok készletével rendelkezik, és mindegyik csomóponttípus van konkrét beállításokat a virtuális gép méretét és típusát.

Az optimális fürt méretének meghatározásához az alkalmazás, fürtkapacitás becslésére, és a jelzett méretének növeléséhez. Például használhat egy szimulált számítási feladat vagy egy *tesztcsoportos lekérdezés*. Egy szimulált számítási feladat, futtatja a várható számítási feladatok különböző méretű fürtök esetén a fokozatosan növekvő mérete, amíg el nem a kívánt teljesítményt. Canary lekérdezés többek között a más éles lekérdezések show-e a fürt elegendő erőforrással rendelkezik-e rendszeresen lehet beszúrni.

A virtuális gép méretét és típusát határozza meg a Processzor feldolgozási, a RAM-méretétől és a hálózati késés:

* CPU: A Virtuálisgép-méret előírja a magok számát. A több mag, annál nagyobb mértékű párhuzamos számítási csomópontokra érheti el. Néhány virtuális gép típusa is gyorsabb magok.

* MEMÓRIA: A virtuális gép méretét is előírja a virtuális Gépet a rendelkezésre álló RAM mennyiségét. A számítási feladatokhoz, amely tárolja az adatokat a feldolgozáshoz memória az ahelyett, hogy ellenőrizze a lemezről való olvasása, a feldolgozó csomópontokat, rendelkezik elég memória a az adatokat.

* Hálózat: A legtöbb fürt esetében a fürt által feldolgozott adatokat nem a helyi lemezen, hanem egy külső tárolási szolgáltatás, például a Data Lake Store vagy az Azure Storage van. Vegye figyelembe a hálózati sávszélesség és a csomópont virtuális gép és a storage szolgáltatás közötti átviteli sebesség. A virtuális gép számára elérhető sávszélesség általában nagyobb méretű egyenes arányban növekszik. További információkért lásd: [Virtuálisgép mérete – áttekintés](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Válassza ki a fürt vertikális

Egy fürthöz méretezés a virtuális gép csomópontjainak mennyiségét határozza meg. Az összes fürt esetében vannak, amelyek egy adott méretezési csomóponttípusok és csomóponttípus, amely támogatja a horizontális felskálázást. Ha például egy fürt igényelhetnek pontosan három [Apache ZooKeeper](https://zookeeper.apache.org/) csomópontok vagy két fő csomópont ingyenes. Feldolgozó csomópontokat, amelyeket a adatfeldolgozási elosztott módon is kihasználhatják a felskálázás, adja hozzá a további feldolgozó csomópontokat.

A fürt típusától függően a munkavégző csomópontok számának növelése ad hozzá a számítási kapacitás (például a több mag), de előfordulhat, hogy a feldolgozott adatok tárolása a memóriában támogatásához az egész fürt számára szükséges memória teljes mennyiségétől is hozzáadhat. Csakúgy, mint a kiválasztott virtuális gép méretét és típusát, a fürt megfelelő méretezés kiválasztása általában elérte tapasztalati, szimulált számítási feladatokat vagy a tesztcsoportos lekérdezések használatával.

Ki lehet terjeszteni a terhelés megnövekedett igényeket kell kielégíteni, majd horizontális le, amikor már nincs szükség további csomópontokat a fürthöz. További információkért lásd: [méretezési HDInsight-fürtök](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Fürt életciklusa

A fürt élettartama díjkötelesek. Ha csak vannak, hogy kell-e a fürt felfelé és a futó megadott időpontok, [Azure Data Factory segítségével igény szerinti fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-adf.md). PowerShell-parancsfájlok, amelyek kiépítése, és törölje a fürtöt is létrehozhat, és majd ütemezze ezeket a szkripteket használatával [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> A fürt törlésekor az alapértelmezett Hive-metaadattár is törlődik. A következő fürt újbóli létrehozásához a metaadattár fennáll, használja a külső metaadat-tárral, például az Azure Database vagy [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Fürt feladathibák elkülönítése

Időnként hibák több maps párhuzamos végrehajtása miatt fordulhat elő, és csökkentse az összetevők a több csomópontos a fürt. A problémát, próbálja meg egyidejűleg futó elosztott tesztelése a segítségével több feladat egy egy csomópontos fürtre, majd ezt a megközelítést több feladat egyidejű futtatását több csomópontot tartalmazó fürtök bontsa ki. Az Azure-ban egy egy csomópontos HDInsight-fürt létrehozásához használja a *speciális* lehetőséget.

Egy egy csomópontos fejlesztési környezet telepítése a helyi számítógépen is, és tesztelje a megoldást itt. Hortonworks Hadoop-alapú megoldások egy egycsomópontos helyi fejlesztési környezetet, amely hasznos kezdeti, megvalósíthatósági, fejlesztési és tesztelési biztosít. További információkért lásd: [hortonworks – tesztkörnyezet](https://hortonworks.com/products/hortonworks-sandbox/).

Helyi egycsomópontos fürtre a probléma azonosításához futtassa újra a sikertelen feladatokat, és módosítsa a bemeneti adatokat, vagy kisebb adatkészletek használata. Ezen feladatok futtatásának módját, a platform és az alkalmazás típusától függ.

## <a name="quotas"></a>Kvóták

Után, amely meghatározza, hogy a fürt virtuális célgép méretét, a méretezési csoport és a típus, ellenőrizze az előfizetés az aktuális kvóta kapacitásának korlátjáig. Ha egyenlege eléri a kvótát, nem lehet új fürtök üzembe helyezése, vagy a meglévő fürtök horizontális további munkavégző csomópontok hozzáadásával. A csak kvótahatár: a Processzormagok kvóta, amely létezik a régió szintjén minden egyes előfizetés esetén. Ha például az előfizetés lehet magkorlátja 30 az USA keleti régiójában. Ha a kvóta növelésére van szüksége, kövesse az alábbi lépéseket:

1. Az Azure Portal megnyitása
1. Kattintson a **Súgó és támogatás** az oldal bal alsó részén.
1. Kattintson a **új támogatási kérelem**.
1. Az a **új támogatási kérelem** lap **alapjai** lapra, válassza ki a következő beállításokat:
    - **Probléma típusa**: **Szolgáltatás és az előfizetések korlátai (kvóták)**
    - **Előfizetés**: a módosítani kívánt előfizetés
    - **Kvóta típusa**: **HDInsight**
    
    ![Hozzon létre egy támogatási kérést a HDInsight magkvóta növeléséhez](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Kattintson a **Tovább**gombra.
1. Az a **részletek** lapon adja meg a probléma leírását, válassza ki a probléma súlyosságát, és válassza ki a kívánt kapcsolattartási formát.
1. Kattintson a **tovább: Felülvizsgálat + létrehozás**.
1. Az a **felülvizsgálat + létrehozása** lapra, majd **létrehozás**.

> [!Note]
> Ha szeretne egy privát régióban a HDInsight magkvóta növeléséhez [be egy engedélyezési kérést](https://aka.ms/canaryintwhitelist).

Is [kérje egy kvótájának növelését az ügyfélszolgálattól](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Azonban néhány rögzített kvóta korlátozva van, például egy Azure-előfizetéssel is rendelkezik, legfeljebb 10 000 magok. Ezek a korlátok a részletekért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>További lépések

* [Az Apache Hadoop, Spark, Kafka és több HDInsight-fürtök beállítása](hdinsight-hadoop-provision-linux-clusters.md): Megtudhatja, hogyan állíthatja be, és a HDInsight Apache Hadoop, Spark, Kafka, interaktív Hive, HBase, Machine Learning-szolgáltatások vagy a Storm-fürtök konfigurálása.
* [Fürtteljesítmény monitorozása](hdinsight-key-scenarios-to-monitor.md): További információ a HDInsight-fürthöz, amelyek hatással lehetnek a fürt kapacitásának figyelése főbb forgatókönyvek megvalósítását.
