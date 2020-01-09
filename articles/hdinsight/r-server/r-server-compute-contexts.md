---
title: Számítási környezeti lehetőségek a HDInsight ML-szolgáltatásaihoz – Azure
description: Ismerje meg a különböző számítási környezeti lehetőségeket, amelyek a HDInsight ML-szolgáltatásaival rendelkező felhasználók számára elérhetők
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660256"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Számítási környezeti beállítások a HDInsight ML-szolgáltatásaihoz

Az Azure HDInsight ML-szolgáltatásai vezérlik a hívások végrehajtását a számítási környezet beállításával. Ez a cikk azokat a lehetőségeket ismerteti, amelyekkel megadható, hogy a végrehajtás hogyan legyen párhuzamos a peremhálózati csomópont vagy a HDInsight-fürt magjai között.

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához. A peremhálózati csomópontok esetében lehetősége van a RevoScaleR párhuzamosan elosztott funkcióinak futtatására a peremhálózati csomópont-kiszolgáló magjai között. Ezeket a fürtök csomópontjain is futtathatja a RevoScaleR Hadoop-leképezésével, és csökkentheti vagy Apache Spark a számítási környezeteket.

## <a name="ml-services-on-azure-hdinsight"></a>ML szolgáltatások az Azure HDInsight

Az [Azure HDInsight ml-szolgáltatásai](r-server-overview.md) az R-alapú elemzések legújabb képességeit biztosítják. Az [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") Storage-fiók, a Data Lake Store vagy a helyi Linux fájlrendszer Apache Hadoop HDFS tárolt adatait is használhatja. Mivel a ML-szolgáltatások nyílt forráskódú R-re épülnek, az Ön által létrehozott R-alapú alkalmazások a 8000-es és a nyílt forráskódú R-csomagok bármelyikét alkalmazhatják. Emellett a [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), a Microsoft Big Data Analytics-csomagjában található rutinokat is használhatják, amelyek a ml-szolgáltatások részét képezik.  

## <a name="compute-contexts-for-an-edge-node"></a>Az Edge-csomópont számítási környezete

Általánosságban elmondható, hogy egy, az Edge-csomóponton található ML Services-fürtön futó R-szkript az adott csomóponton az R-tolmácson belül fut. A kivételek a RevoScaleR függvényt meghívó lépések. A RevoScaleR-hívások olyan számítási környezetben futnak, amelyet a RevoScaleR számítási környezetének beállítása határoz meg.  Ha az R-szkriptet egy peremhálózati csomópontról futtatja, a számítási környezet lehetséges értékei a következők:

- helyi szekvenciális (*helyi*)
- helyi párhuzamos (*localpar*)
- Leképezés csökkentése
- Spark

A *helyi* és a *localpar* beállítások csak a **rxExec** -hívások végrehajtásának módjában különböznek. Mindkettő más Rx-függvény hívásokat párhuzamosan hajtja végre az összes rendelkezésre álló magot illetően, kivéve, ha az RevoScaleR **numCoresToUse** kapcsoló használatával másképp van megadva, például `rxOptions(numCoresToUse=6)`. A párhuzamos végrehajtási lehetőségek optimális teljesítményt nyújtanak.

A következő táblázat összefoglalja a különböző számítási környezeti beállításokat a hívások végrehajtásának beállításához:

| Számítási környezet  | A beállítás módja                      | Végrehajtási környezet                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Helyi szekvenciális | rxSetComputeContext ("local")    | Párhuzamos végrehajtás a peremhálózati csomópont-kiszolgáló magjai között, a rxExec-hívások kivételével, amelyeket a rendszer a sorosan hajt végre. |
| Helyi párhuzamos   | rxSetComputeContext('localpar') | Párhuzamos végrehajtás a peremhálózati csomópont-kiszolgáló magjai között |
| Spark            | RxSpark ()                       | Párhuzamos elosztott végrehajtás az HDI-fürt csomópontjain keresztül a Sparkon keresztül |
| Leképezés csökkentése       | RxHadoopMR()                    | A leképezésen keresztüli párhuzamos elosztott végrehajtás a HDI-fürt csomópontjain is csökken |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Útmutató a számítási környezetek döntéséhez

A párhuzamos végrehajtást biztosító három lehetőség közül választhat az elemzési munka természetétől, a mérettől és az adatok helyétől függően. Nincs egyszerű képlet, amely azt jelzi, hogy milyen számítási kontextust kell használni. Vannak azonban olyan irányadó alapelvek, amelyek segíthetnek a megfelelő választásban, vagy legalábbis a teljesítményteszt futtatása előtt segítenek leszűkíteni a döntéseket. Ezek az irányadó alapelvek a következők:

- A helyi Linux-fájlrendszer gyorsabb, mint a HDFS.
- Az ismétlődő elemzések gyorsabbak, ha az adatterületek helyiek, és ha a XDF van.
- Érdemes kis mennyiségű adat átvitelét egy szöveges adatforrásból. Ha az adatmennyiség nagyobb, akkor az elemzés előtt alakítsa át a XDF.
- Az adatoknak az Edge-csomópontba való másolása vagy továbbítása a nagy mennyiségű adathoz nem lesz kezelhető.
- A ApacheSpark gyorsabb, mint a leképezések csökkentése az Hadoop-ben végzett elemzésekhez.

Ezen alapelvek alapján a következő szakaszokban a számítási környezet kiválasztására vonatkozó általános szabályok nyújtanak segítséget.

### <a name="local"></a>Helyi

- Ha az elemezni kívánt adat mennyisége kicsi, és nem igényel ismétlődő elemzést, akkor közvetlenül az elemzési rutinba továbbítja a *helyi* vagy *localpar*használatával.
- Ha az elemzett adat mennyisége kis vagy közepes méretű, és ismétlődő elemzést igényel, akkor másolja a helyi fájlrendszerbe, importálja a XDF, és elemezze a *helyi* vagy *localpar*keresztül.

### <a name="apache-spark"></a>Apache Spark

- Ha az elemezni kívánt adatok mennyisége nagy, akkor importálja azt egy Spark-DataFrame **RxHiveData** vagy **RxParquetData**használatával, vagy XDF a HDFS-ben (kivéve, ha a tár problémát jelent), és elemezni szeretné a Spark számítási környezet használatával.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Térkép csökkentése

- A Térkép használata csak akkor csökkentse a számítási környezetet, ha a Spark számítási környezettel egy leküzdhetetlen probléma merül fel, mivel általában lassabb.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Beágyazott Súgó a rxSetComputeContext
További információ és példák a RevoScaleR számítási környezetekre: a rxSetComputeContext metódus inline súgója az R-ben, például:

    > ?rxSetComputeContext

A [Machine learning Server dokumentációjának](https://docs.microsoft.com/machine-learning-server/) [elosztott számítástechnikai áttekintését](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) is megtekintheti.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogy milyen lehetőségek állnak rendelkezésre annak megadásához, hogy a végrehajtás hogyan és hogyan legyen párhuzamos a peremhálózati csomópont vagy a HDInsight-fürt magjai között. Ha többet szeretne megtudni a HDInsight-fürtökkel rendelkező ML-szolgáltatások használatáról, tekintse meg a következő témaköröket:

- [A Apache Hadoop ML-szolgáltatásainak áttekintése](r-server-overview.md)
- [Azure Storage-beállítások a HDInsight ML-szolgáltatásaihoz](r-server-storage.md)
