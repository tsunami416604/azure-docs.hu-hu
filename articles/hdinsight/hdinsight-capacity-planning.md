---
title: Fürtkapacitás-tervezés az Azure HDInsightban
description: Azonosítsa az Azure HDInsight-fürtök kapacitás- és teljesítménytervezésének legfontosabb kérdéseit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272642"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight-fürtök kapacitástervezése

A HDInsight-fürt üzembe helyezése előtt tervezze meg a kívánt fürtkapacitást a szükséges teljesítmény és méretezés meghatározásával. Ez a tervezés segít optimalizálni mind a használhatóságot, mind a költségeket. Egyes fürtkapacitási döntések nem módosíthatók az üzembe helyezés után. Ha a teljesítményparaméterek megváltoznak, a fürt a tárolt adatok elvesztése nélkül szétszerelhető és újra létrehozható.

A legfontosabb kérdések, amelyeket a kapacitástervezéssel kapcsolatban fel kell tenni:

* Melyik földrajzi régióban kell telepítenia a fürtöt?
* Mennyi tárhelyre van szüksége?
* Milyen fürttípust kell telepíteni?
* Milyen méretű és típusú virtuális gép (VM) kell a fürtcsomópontok használni?
* Hány munkavégző csomókkal kell rendelkeznie a fürtnek?

## <a name="choose-an-azure-region"></a>Válasszon azure-régiót

Az Azure-régió határozza meg, ahol a fürt fizikailag kiépített. Az olvasások és írások késésének minimalizálása érdekében a fürtnek az adatok közelében kell lennie.

A HDInsight számos Azure-régióban elérhető. A legközelebbi régió megkereséséhez olvassa el a [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)című témakört.

## <a name="choose-storage-location-and-size"></a>A tároló helyének és méretének kiválasztása

### <a name="location-of-default-storage"></a>Alapértelmezett tárolás helye

Az alapértelmezett tárolónak, akár egy Azure Storage-fióknak, akár az Azure Data Lake Storage-nak ugyanazon a helyen kell lennie, mint a fürt. Az Azure Storage minden helyen elérhető. A Data Lake Storage Gen1 bizonyos régiókban érhető el – tekintse meg a Data Lake Storage aktuális [rendelkezésre állását.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

### <a name="location-of-existing-data"></a>A meglévő adatok helye

Ha már rendelkezik az adatokat tartalmazó tárfiókkal vagy Data Lake Storage-tárolóval, és ezt a tárolót szeretné használni a fürt alapértelmezett tárolójaként, akkor a fürtöt ugyanazon a helyen kell telepítenie.

### <a name="storage-size"></a>Tárhely mérete

Miután telepített egy HDInsight-fürtöt, további Azure Storage-fiókokat csatolhat, vagy más Data Lake Storage-hoz férhet hozzá. Az összes tárfióknak ugyanazon a helyen kell lennie, mint a fürtnek. A Data Lake Storage lehet egy másik helyen, bár ez néhány adat olvasási/írási késést eredményezhet.

Az Azure Storage rendelkezik bizonyos [kapacitáskorlátokkal,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)míg a Data Lake Storage Gen1 gyakorlatilag korlátlan.

A fürt különböző tárfiókok kombinációjához férhet hozzá. Tipikus példák:

* Ha az adatok mennyisége valószínűleg meghaladja a tárolókapacitásegyetlen blob tárolótároló.
* Ha a blob tárolóhoz való hozzáférés aránya túllépheti azt a küszöbértéket, ahol a szabályozás történik.
* Ha adatokat szeretne készíteni, már feltöltötte a fürt számára elérhető blobtárolóba.
* Ha biztonsági okokból el szeretné különíteni a tároló különböző részeit, vagy egyszerűsíteni szeretné a felügyeletet.

A jobb teljesítmény érdekében tárfiókonként csak egy tárolót használjon.

## <a name="choose-a-cluster-type"></a>Fürttípus kiválasztása

A fürttípus határozza meg a HDInsight-fürt által futtatott számítási feladatokat, például [az Apache Hadoop,](https://hadoop.apache.org/) [az Apache Storm,](https://storm.apache.org/) [az Apache Kafka](https://kafka.apache.org/)vagy az [Apache Spark.](https://spark.apache.org/) A rendelkezésre álló fürttípusok részletes leírását az Azure HDInsight – bevezetés című [témakörben tartalmazza.](hdinsight-overview.md#cluster-types-in-hdinsight) Minden fürttípus rendelkezik egy adott központi telepítési topológiával, amely a csomópontok méretére és számára vonatkozó követelményeket tartalmaz.

## <a name="choose-the-vm-size-and-type"></a>Válassza ki a virtuális gép méretét és típusát

Minden fürttípus rendelkezik egy csomó csomóponttípusok, és minden csomópont típus speciális beállításokat a virtuális gép méretét és típusát.

Az alkalmazás optimális fürtméretének meghatározásához teljesítménytesztelheti a fürtkapacitást, és növelheti a jelzett méretet. Használhat például szimulált munkaterhelést vagy *kanárilekérdezést.* Szimulált számítási feladat esetén a várt számítási feladatokkülönböző méretű fürtökön futnak, fokozatosan növelve a méretet, amíg el nem éri a kívánt teljesítményt. A kanári-lekérdezés rendszeres időközönként beszúrható a többi éles lekérdezés közé, hogy megmutassa, hogy a fürt rendelkezik-e elegendő erőforrással.

Arról, hogy miként választhatja ki a megfelelő virtuálisgép-családot a számítási feladatokhoz, [olvassa el a megfelelő virtuális gép méretének kiválasztása a fürthöz című témakört.](hdinsight-selecting-vm-size.md)

## <a name="choose-the-cluster-scale"></a>A fürtméret kiválasztása

A fürt skáláját a virtuálisgép-csomópontok mennyisége határozza meg. Minden fürttípushoz vannak olyan csomóponttípusok, amelyek adott skálával rendelkeznek, és a csomóponttípusok támogatják a horizontális felskálázást. Egy fürthöz például pontosan három [Apache ZooKeeper-csomópontra](https://zookeeper.apache.org/) vagy két főcsomópontra lehet szükség. Az elosztott módon adatfeldolgozást végző feldolgozócsomópontok további munkavégző csomópontok hozzáadásával profitálhatnak a horizontális felskálázásból.

A fürt típusától függően a munkavégző csomópontok számának növelése további számítási kapacitást (például több magot) ad hozzá, de a teljes fürt számára szükséges memória mennyisége is hozzáadódik a feldolgozott adatok memórián belüli tárolásának támogatásához. A virtuális gép méretének és típusának kiválasztásához hasonlóan a megfelelő fürtskálát általában empirikusan éri el, szimulált munkaterhelések vagy kanári lekérdezések használatával.

A fürt horizontális felskálázhatja a csúcsterhelési igények, majd a skálázás vissza, ha ezek az extra csomópontok már nincs szükség. Az [automatikus skálázás funkció](hdinsight-autoscale-clusters.md) lehetővé teszi, hogy automatikusan méretezze a fürt et előre meghatározott metrikák és időzítések alapján. A fürtök manuális méretezésével kapcsolatos további tudnivalókért olvassa el a [HDInsight-fürtök méretezése című témakört.](hdinsight-scaling-best-practices.md)

### <a name="cluster-lifecycle"></a>Fürt életciklusa

A fürt élettartama díjat számít fel. Ha csak meghatározott időpontokban van szüksége a fürt be- és futtatására, [igény szerinti fürtöket hozhat létre az Azure Data Factory használatával.](hdinsight-hadoop-create-linux-clusters-adf.md) A fürthöz kiépítő és törlő PowerShell-parancsfájlokat is létrehozhat, majd ütemezheti ezeket a parancsfájlokat az [Azure Automation](https://azure.microsoft.com/services/automation/)használatával.

> [!NOTE]  
> Fürt törlésekor az alapértelmezett Hive metatár is törlődik. A metatároló megőrzése a következő fürt újbóli létrehozása, használjon egy külső metaadat-tároló, például az Azure Database vagy [az Apache Oozie.](https://oozie.apache.org/)
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Fürtfeladat-hibák elkülönítése

Néha hibák fordulhatnak elő több leképezés párhuzamos végrehajtása miatt, és csökkenthetik a többcsomópontos fürt összetevőinek számát. A probléma elkülönítése érdekében próbálja meg az elosztott tesztelést úgy, hogy egyidejűleg több feladatot futtat egy munkavégző csomópont-fürtön, majd bontsa ki ezt a megközelítést, hogy több feladatot futtasson egyidejűleg egynél több csomópontot tartalmazó fürtökön. Egycsomópontos HDInsight-fürt létrehozásához az Azure-ban használja az *Egyéni (méret, beállítások, alkalmazások)* beállítást, és 1-es értéket használjon a **Fürtméret** szakaszban lévő *Feldolgozó-csomópontok száma* mezőben egy új fürt kiépítésekor.

## <a name="quotas"></a>Kvóták

A célfürt virtuális gépméretének, méretezésének és típusának meghatározása után ellenőrizze az előfizetés aktuális kvótakapacitás-korlátait. Ha eléri a kvótakorlátot, előfordulhat, hogy nem tud új fürtöket telepíteni, vagy a meglévő fürtöket további munkavégző csomópontok hozzáadásával bővíteni. Az egyetlen kvótakorlát a CPU-magok kvótája, amely az egyes előfizetések régió szintjén létezik. Előfordulhat például, hogy az előfizetés 30 alapvető korláttal rendelkezik az USA keleti régiójában. 

A rendelkezésre álló magok ellenőrzéséhez tegye a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg a HDInsight-fürt **Áttekintő** lapját. 
3. Kattintson a bal oldali menü **Kvótakorlátok parancsára.**

   Az oldal megjeleníti a használatban lévő magok számát, a rendelkezésre álló magok számát és az összes magot.

Ha kvótanövelést kell kérnie, tegye a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. A lap bal alsó részén válassza a **Súgó + támogatás** lehetőséget.
1. Válassza **az Új támogatási kérelem lehetőséget.**
1. Az **Új támogatási kérelem** lap **Alapjai** lapján adja meg a következő beállításokat:

   - **Probléma típusa**: **Szolgáltatási és előfizetési korlátok (kvóták)**
   - **Előfizetés**: a módosítani kívánt előfizetés
   - **Kvóta típusa**: **HDInsight**

     ![Támogatási kérelem létrehozása a HDInsight alapkvótájának növeléséhez](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Válassza a **Tovább: Megoldások >>** lehetőséget.
1. A **Részletek** lapon adja meg a probléma leírását, válassza ki a probléma súlyosságát, a kívánt kapcsolatfelvételi módot és más szükséges mezőket.
1. Válassza a **Tovább lehetőséget: Véleményezés + >>létrehozása **.
1. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás gombot.**

> [!NOTE]  
> Ha egy privát régióban növelnie kell a HDInsight alapkvótáját, [küldjön engedélyezési kérelmet.](https://aka.ms/canaryintwhitelist)

A [kvótanöveléséhez forduljon az ügyfélszolgálathoz.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

Vannak azonban bizonyos rögzített kvótakorlátok, például egy Azure-előfizetés legbőlegelhető 10 000 mag. Ezekről a korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

## <a name="next-steps"></a>További lépések

* [Fürtök beállítása a HDInsightban az Apache Hadoop, a Spark, a Kafka és még sok más](hdinsight-hadoop-provision-linux-clusters.md)segítségével: Ismerje meg, hogyan állíthatja be és konfigurálhatja a fürtöket a HDInsightban az Apache Hadoop, a Spark, a Kafka, az Interaktív Hive, a HBase, az ML-szolgáltatások vagy a Storm segítségével.
* [Fürtteljesítmény figyelése:](hdinsight-key-scenarios-to-monitor.md)Ismerje meg a HDInsight-fürt olyan kulcsfontosságú forgatókönyveit, amelyek hatással lehetnek a fürt kapacitására.
