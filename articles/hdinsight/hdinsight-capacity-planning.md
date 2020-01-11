---
title: Fürt kapacitásának megtervezése az Azure HDInsight
description: Azonosíthatja az Azure HDInsight-fürtök kapacitásának és teljesítményének tervezésével kapcsolatos legfontosabb kérdéseket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: db7b2787df68e5e9baadddc7e6e6159cfff26097
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887240"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight-fürtök kapacitásának megtervezése

A HDInsight-fürt üzembe helyezése előtt tervezze meg a kívánt kapacitást a szükséges teljesítmény és skála meghatározásával. Ez a tervezés segít optimalizálni a használhatóságot és a költségeket. Az üzembe helyezést követően egyes szektorcsoport-kapacitási döntések nem módosíthatók. Ha a teljesítmény-paraméterek módosulnak, a fürtöket a tárolt adatok elvesztése nélkül lehet kibontani és újból létrehozni.

A kapacitás megtervezésére vonatkozó legfontosabb kérdések a következők:

* Melyik földrajzi régióban érdemes telepíteni a fürtöt?
* Mekkora tárterületre van szüksége?
* Milyen típusú fürtöt kell üzembe helyezni?
* Milyen méretű és típusú virtuális gépet (VM) használ a fürtcsomópontok?
* Hány munkavégző csomópontnak kell a fürtje?

## <a name="choose-an-azure-region"></a>Válasszon egy Azure-régiót

Az Azure-régió meghatározza, hogy a fürt hol van fizikailag kiépítve. Az olvasási és írási késés csökkentése érdekében a fürtnek az adatai közelében kell lennie.

A HDInsight számos Azure-régióban elérhető. A legközelebbi régió megtalálásához tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>A tárolási hely és a méret kiválasztása

### <a name="location-of-default-storage"></a>Alapértelmezett tároló helye

Az alapértelmezett tárolónak, vagy egy Azure Storage-fióknak vagy Azure Data Lake Storagenek a fürttel azonos helyen kell lennie. Az Azure Storage minden helyen elérhető. Data Lake Storage Gen1 bizonyos régiókban érhető el – tekintse meg az aktuális [Data Lake Storage rendelkezésre állást](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Meglévő adatterületek helye

Ha már rendelkezik olyan Storage-fiókkal vagy Data Lake Storage, amely tartalmazza az adatait, és ezt a tárolót szeretné használni a fürt alapértelmezett tárolójában, akkor a fürtöt ugyanarra a helyre kell telepítenie.

### <a name="storage-size"></a>Tárterület mérete

Miután telepített egy HDInsight-fürtöt, további Azure Storage-fiókokat is csatolhat, vagy más Data Lake Storagehoz férhet hozzá. Az összes Storage-fióknak ugyanabban a helyen kell lennie, mint a fürtnek. Egy Data Lake Storage lehet egy másik helyen, de ez bizonyos adatolvasási/írási késést eredményezhet.

Az Azure Storage [kapacitása korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), míg Data Lake Storage Gen1 gyakorlatilag korlátlan.

A fürtök különböző tárolási fiókok kombinációjából is hozzáférhetnek. A tipikus példák a következők:

* Ha az adatmennyiség várhatóan túllépi az egyetlen blob Storage-tároló tárolókapacitását.
* Ha a blob-tárolóhoz való hozzáférés sebessége túllépi a sávszélesség-szabályozási küszöbértéket.
* Ha az adatfeldolgozást szeretné végezni, már feltöltött egy BLOB-tárolót a fürt számára.
* Ha a biztonsági okokból el szeretné különíteni a tárterület különböző részeit, vagy leegyszerűsíti a felügyeletet.

48 csomópontos fürt esetén 4 – 8 Storage-fiók használatát javasoljuk. Bár lehetséges, hogy a teljes tárterület már elegendő, az egyes Storage-fiókok további hálózati sávszélességet biztosítanak a számítási csomópontok számára. Ha több Storage-fiókkal rendelkezik, minden egyes Storage-fiókhoz használjon véletlenszerű nevet, előtag nélkül. A véletlenszerű elnevezések célja, hogy csökkentse a tárolási torlódások (szabályozás) és az összes fiók általános módú meghibásodásának esélyét. A jobb teljesítmény érdekében csak egy tárolót használjon Storage-fiókkal.

## <a name="choose-a-cluster-type"></a>Fürt típusának kiválasztása

A fürt típusa határozza meg a HDInsight-fürt futtatásához konfigurált munkaterhelést, például [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)vagy [Apache Spark](https://spark.apache.org/). A rendelkezésre álló fürtök típusának részletes ismertetését lásd: [Az Azure HDInsight bemutatása](hdinsight-overview.md#cluster-types-in-hdinsight). Minden egyes fürthöz tartozik egy adott központi telepítési topológia, amely a csomópontok méretére és számára vonatkozó követelményeket tartalmaz.

## <a name="choose-the-vm-size-and-type"></a>Válassza ki a virtuális gép méretét és típusát

Minden egyes fürt típusa csomópont típusú, és mindegyik csomópont típusa speciális beállításokkal rendelkezik a virtuálisgép-mérethez és-típushoz.

Az alkalmazás optimális méretének meghatározásához a fürt kapacitásának teljesítménytesztét és a jelzett méret növelését is megnövelheti. Használhat például egy szimulált munkaterhelést vagy egy *Kanári-lekérdezést*is. Szimulált számítási feladatok esetén a várt számítási feladatok különböző méretű fürtökön futnak, és a kívánt teljesítmény eléréséig fokozatosan megnő a méret. Egy Kanári-lekérdezést időnként beillesztheti a többi éles lekérdezésbe, hogy megjelenjen-e elegendő erőforrás a fürtben.

A számítási feladatok megfelelő virtuálisgép-családjának kiválasztásával kapcsolatos további információkért lásd: [a fürt megfelelő virtuálisgép-méretének kiválasztása](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>A fürt méretének kiválasztása

A fürt méretezését a virtuálisgép-csomópontok mennyisége határozza meg. Az összes fürt típusa esetén vannak olyan csomópont-típusok, amelyek egy adott léptéket és a kibővíthető csomópont-típusokat támogatják. Előfordulhat például, hogy egy fürt pontosan három [Apache ZooKeeper](https://zookeeper.apache.org/) csomópontot vagy két fő csomópontot igényel. Az elosztott módon adatfeldolgozást végző feldolgozó csomópontok további munkavégző csomópontok hozzáadásával kihasználhatják a horizontális felskálázást.

A fürt típusától függően a munkavégző csomópontok számának növelése további számítási kapacitást (például több magot) ad hozzá, de a teljes fürt számára szükséges memória teljes mennyiségét is hozzáadhatja a feldolgozás alatt álló adatmemória-tárolás támogatásához. A virtuális gép méretének és típusának megválasztásához hasonlóan a megfelelő szektorcsoportok méretezése általában empirikusan érhető el, szimulált számítási feladatokkal vagy Kanári-lekérdezések használatával.

A fürt felskálázásával teljesítheti a maximális terhelési igényeket, majd lekicsinyítheti azt, ha a további csomópontok már nem szükségesek. Az automatikus [méretezési funkció](hdinsight-autoscale-clusters.md) lehetővé teszi, hogy az előre meghatározott mérőszámok és időzítések alapján automatikusan méretezze a fürtöt. A fürtök kézi méretezésével kapcsolatos további információkért lásd: [HDInsight-fürtök méretezése](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Fürt életciklusa

A fürt élettartama után számítunk fel díjat. Ha csak bizonyos időpontokra van szükség a fürt működésének megkezdéséhez, [létrehozhat igény szerinti fürtöket Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md). Létrehozhat olyan PowerShell-parancsfájlokat is, amelyek kiépítik és törlik a fürtöt, majd az [Azure Automation](https://azure.microsoft.com/services/automation/)használatával ütemezhetik ezeket a parancsfájlokat.

> [!NOTE]  
> Fürt törlésekor az alapértelmezett Hive-metaadattár is törlődik. Ha meg szeretné őrizni a metaadattár a fürt következő újbóli létrehozásához, használjon külső metaadat-tárolót, például az Azure Database vagy az [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>A fürt feladataival kapcsolatos hibák elkülönítése

Időnként hibák merülhetnek fel több Térkép párhuzamos végrehajtása, valamint a több csomópontot tartalmazó fürt összetevőinek csökkentése miatt. A probléma elkülönítéséhez próbálja meg az elosztott tesztelést úgy, hogy egyidejűleg több feladatot futtat egyetlen feldolgozó csomópont-fürtön, majd ezt a megközelítést kiterjesztve több feladatot is futtathat egyszerre több csomópontot tartalmazó fürtökön. Egy egycsomópontos HDInsight-fürt az Azure-ban való létrehozásához használja az *Egyéni (méret, beállítások, alkalmazások)* beállítást, és használja az 1 értéket a **fürt mérete** szakaszban lévő *munkavégző csomópontok* számára, ha új fürtöt szeretne kiépíteni a portálon.

## <a name="quotas"></a>Kvóták

A célként megadott fürt virtuálisgép-méretének, méretezésének és típusának meghatározása után keresse meg az előfizetés aktuális kvóta-kapacitási korlátait. Ha eléri a kvóta korlátját, előfordulhat, hogy nem tud új fürtöket telepíteni vagy bővíteni a meglévő fürtöket további munkavégző csomópontok hozzáadásával. Az egyetlen kvóta a CPU magok kvótája, amely az egyes előfizetések régiójának szintjén található. Az előfizetés például 30 Magos korláttal rendelkezhet az USA keleti régiójában. 

Az elérhető magok vizsgálatához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Navigáljon a HDInsight-fürt **áttekintő** oldalára. 
3. A bal oldali menüben kattintson a **kvóta korlátai**elemre.

   A lap megjeleníti a használatban lévő magok számát, a rendelkezésre álló magok számát és az összes magot.

Ha kvótát kell megadnia, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
1. A lap bal alsó részén kattintson a **Súgó és támogatás** elemre.
1. Válassza az **Új támogatási kérelem** lehetőséget.
1. Az **új támogatási kérelem** oldal **alapismeretek** lapján válassza a következő beállításokat:

   - **Probléma típusa**: **szolgáltatás-és előfizetési korlátok (kvóták)**
   - **Előfizetés**: a módosítani kívánt előfizetés
   - **Kvóta típusa**: **HDInsight**

     ![Támogatási kérelem létrehozása az HDInsight Core-kvóta növeléséhez](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Válassza a **Tovább: megoldások > >** .
1. A **részletek** lapon adja meg a probléma leírását, válassza ki a probléma súlyosságát, az előnyben részesített kapcsolattartási módszert és az egyéb kötelező mezőket.
1. Válassza a **Next (tovább): Review + > > létrehozása**elemet.
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.

> [!NOTE]  
> Ha a HDInsight alapszintű kvótát egy privát régióban szeretné megemelni, [küldjön el egy engedélyezési kérelmet](https://aka.ms/canaryintwhitelist).

[Az ügyfélszolgálattól kérheti a kvóta növelését](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Vannak azonban rögzített kvóták, például egyetlen Azure-előfizetés legfeljebb 10 000 magot tartalmazhat. A korlátokkal kapcsolatos további információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök beállítása a Apache Hadoop-, Spark-, Kafka-és egyéb szolgáltatásokkal](hdinsight-hadoop-provision-linux-clusters.md): megtudhatja, hogyan állíthatja be és konfigurálhatja a fürtöket a HDInsight-ben Apache Hadoop, Spark, Kafka, Interactive kaptár, HBASE, ml Services vagy Storm használatával.
* A [fürt teljesítményének figyelése](hdinsight-key-scenarios-to-monitor.md): megismerheti a HDInsight-fürt azon főbb forgatókönyveit, amelyek hatással lehetnek a fürt kapacitására.
