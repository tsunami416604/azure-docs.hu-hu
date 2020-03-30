---
title: Az ML-szolgáltatások környezeti beállításainak számítása a HDInsightban – Azure
description: Ismerje meg a HDInsight ML-szolgáltatást használó felhasználók számára elérhető különböző számítási környezetbeállításokat
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660256"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Az ML-szolgáltatások környezeti beállításainak számítási környezete a HDInsightban

Az Azure HDInsight ML-szolgáltatásai a számítási környezet beállításával szabályozzák a hívások végrehajtását. Ez a cikk ismerteti azokat a beállításokat, amelyek rendelkezésre állnak annak meghatározására, hogy a végrehajtás párhuzamos-e a peremhálózati csomópont vagy a HDInsight-fürt magjai között.

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való csatlakozáshoz és az R-parancsfájlok futtatásához. Egy peremhálózati csomópont esetén lehetősége van a RevoScaleR párhuzamos elosztott függvényeinek futtatására a peremhálózati csomópont-kiszolgáló magjain. A RevoScaleR Hadoop-térképcsökkentés vagy az Apache Spark számítási környezeteinek használatával is futtathatja őket a fürt csomópontjain.

## <a name="ml-services-on-azure-hdinsight"></a>Ml-szolgáltatások az Azure HDInsightban

[Az Azure HDInsight ML-szolgáltatásai](r-server-overview.md) az R-alapú elemzések legújabb funkcióit biztosítják. Az Apache Hadoop HDFS-tárolóban az [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") storage-fiókban, a Data Lake Store-ban vagy a helyi Linux-fájlrendszerben tárolt adatokat használhatja. Mivel az ML Services nyílt forráskódú R-re épül, az Ön által készített R-alapú alkalmazások a 8000+ nyílt forráskódú R csomagok bármelyikét alkalmazhatják. Ők is használhatják a rutinok [RevoScaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)a Microsoft big data-elemzési csomag, amely tartalmazza az ML-szolgáltatások.  

## <a name="compute-contexts-for-an-edge-node"></a>Peremhálózati csomópont környezetének számítása

Általában egy R-parancsfájl, amely fut az ML Services-fürt a peremhálózati csomóponton fut az R értelmező az adott csomóponton. A kivételek azok a lépések, amelyek meghívják a RevoScaleR függvényt. A RevoScaleR-hívások olyan számítási környezetben futnak, amelyet a RevoScaleR számítási környezet beállításának módját határozza meg.  Amikor az R-parancsfájlt peremcsomópontról futtatja, a számítási környezet lehetséges értékei a következők:

- helyi szekvenciális *(helyi*)
- helyi párhuzamos (*localpar*)
- Térkép csökkentése
- Spark

A *helyi* és *a localpar* beállítások csak az **rxExec** hívások végrehajtása módjában különböznek. A RevoScaleR **numCoresToUse** beállítás használatával például `rxOptions(numCoresToUse=6)`a többi rx-függvényhíváspárhuzamosan hajtják végre az összes rendelkezésre álló magot, kivéve, ha másként nincs megadva. A párhuzamos végrehajtási lehetőségek optimális teljesítményt nyújtanak.

Az alábbi táblázat összefoglalja a különböző számítási környezet beállításait a hívások végrehajtásának beállításához:

| Számítási környezet  | Hogyan kell beállítani                      | Végrehajtási környezet                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Helyi szekvenciális | rxSetComputeContext('local')    | Párhuzamos végrehajtás a peremhálózati csomópont-kiszolgáló magjai között, kivéve az rxExec hívásokat, amelyek sorozatosan hajthatók végre |
| Helyi párhuzamos   | rxSetComputeContext('localpar') | Párhuzamos végrehajtás a peremcsomópont-kiszolgáló magjai között |
| Spark            | RxSpark()                       | Párhuzamosan elosztott végrehajtás a Sparkon keresztül a HDI-fürt csomópontjain |
| Térkép csökkentése       | RxHadoopMR()                    | Párhuzamos elosztott végrehajtás a Map Reduce segítségével a HDI-fürt csomópontjain |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>A számítási környezetel kapcsolatos döntés irányelvei

A párhuzamos végrehajtást biztosító három lehetőség közül melyik függ az elemzési munka jellegétől, méretétől és az adatok helyétől. Nincs egyszerű képlet, amely megmondja, hogy melyik számítási környezetet használja. Vannak azonban olyan vezérelvek, amelyek segíthetnek a helyes választás meghozatalában, vagy legalábbis segítenek leszűkíteni a választási lehetőségeket, mielőtt lefuttatna egy viszonyítási alaptesztet. Ezek az irányadó elvek a következők:

- A helyi Linux fájlrendszer gyorsabb, mint a HDFS.
- Az ismételt elemzések gyorsabbak, ha az adatok helyiek, és ha Az XDF-ben vannak.
- Célszerű kis mennyiségű adatot streamelni szöveges adatforrásból. Ha az adatmennyiség nagyobb, az elemzés előtt konvertálja XDF-re.
- Az adatok nak a peremhálózati csomópontra történő másolása vagy streamelése az elemzéshez nem kezelhető a nagyon nagy mennyiségű adat esetében.
- Az ApacheSpark gyorsabb, mint a Map Reduce elemzéshadoopban.

Tekintettel ezekre az elvekre, a következő szakaszok kínálnak néhány általános ökölszabály kiválasztásához számítási környezetben.

### <a name="local"></a>Helyi

- Ha az elemzandó adatok mennyisége kicsi, és nem igényel ismételt elemzést, akkor közvetlenül az elemzési rutinba továbbítsa a *helyi* vagy *localpar*használatával.
- Ha az elemzendő adatok mennyisége kicsi vagy közepes méretű, és ismételt elemzést igényel, másolja a helyi fájlrendszerbe, importálja az XDF-be, és elemezze *helyi* vagy *localpar*keresztül .

### <a name="apache-spark"></a>Apache Spark

- Ha az elemzandó adatok mennyisége nagy, importálja azt egy Spark DataFrame-be **Az RxHiveData** vagy **rxParquetData**használatával, vagy az XDF-be a HDFS-ben (kivéve, ha a tárolás probléma), és elemezze azt a Spark számítási környezetben.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop térkép csökkentése

- Csak akkor használja a Map Reduce számítási környezetet, ha a Spark számítási környezetével leküzdhetetlen problémával találkozik, mivel általában lassabb.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Inline súgó az rxSetComputeContext-en
További információt és példákat a RevoScaleR számítási környezetek, lásd a szövegközi súgó R az rxSetComputeContext módszer, például:

    > ?rxSetComputeContext

A [Machine Learning Server dokumentációjában](https://docs.microsoft.com/machine-learning-server/)található [Elosztott számítástechnika áttekintése](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) is megtekinthető.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhet a lehetőségekkel, amelyek azt a datálják, hogy a végrehajtás párhuzamos-e a peremhálózati csomópont vagy a HDInsight-fürt magjai között. Az ML-szolgáltatások HDInsight-fürtökkel való használatáról az alábbi témakörökben olvashat bővebben:

- [Az Apache Hadoop ML-szolgáltatásainak áttekintése](r-server-overview.md)
- [Az Azure Storage lehetőségei az ML-szolgáltatásokhoz a HDInsightban](r-server-storage.md)
