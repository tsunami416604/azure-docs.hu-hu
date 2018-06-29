---
title: A HDInsight - Azure ML szolgáltatások adatkörnyezet beállításai számítási |} Microsoft Docs
description: Tudja meg, hogyan különböző számítási környezet ML szolgáltatásokkal a felhasználók rendelkezésére a HDInsight-on
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 57480cef48182a56b315d7d6932883c485f5a7c8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050108"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Számítási környezet lehetőségek az ML-szolgáltatások hdinsight

Gépi tanulás szolgáltatások on Azure HDInsight szabályozza hívások végrehajtásának módját a úgy, hogy a számítási környezet. Ez a cikk ismerteti a megadása, hogyan végrehajtási párhuzamos működésű-e a élcsomópont vagy a HDInsight-fürt mag között elérhető beállítások.

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához. Egy élcsomópontot az informatikai részleg parallelized elosztott feladatai RevoScaleR a peremhálózati kiszolgáló a magokon futtassák. Is futtathatja őket a fürt csomópontjai között RevoScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

## <a name="ml-services-on-azure-hdinsight"></a>A HDInsight az Azure ML szolgáltatások
[Gépi tanulás szolgáltatások on Azure HDInsight](r-server-overview.md) a legújabb funkciókat biztosít az R-alapú használatával. Egy a HDFS-tárolóban tárolt adatokat képes használni a [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage tárolóban") tárfiókot, egy Data Lake store vagy a Linux helyi fájlrendszer. ML szolgáltatások nyílt forráskódú R épül, mivel az R-alapú alkalmazások létrehozása a 8000 + nyílt forráskódú R csomagok bármelyikét is alkalmazhatja. Használhatja a feladatok az [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), a Microsoft big data elemzés csomag ML-szolgáltatások részét képező.  

## <a name="compute-contexts-for-an-edge-node"></a>Számítási környezetek számára egy élcsomópontot
Általában a élcsomópont ML szolgáltatások fürtben futó R-parancsfájl tevékenykedhet az R parancsértelmező ezen a csomóponton. A kivételek olyan RevoScaleR függvény témakör lépéseit. A RevoScaleR hívások határozza meg, hogyan állíthatja a RevoScaleR számítási környezet számítási környezetben fusson.  Amikor az R-parancsfájl egy élcsomópontot futtatja, a számítási környezet lehetséges értékei a következők:

- szekvenciális helyi (*helyi*)
- helyi párhuzamos (*localpar*)
- Térkép csökkentése
- Spark

A *helyi* és *localpar* más-más lehetőségek csak a hogyan **rxExec** hívások végrehajtása. Mindkettő végrehajtani más rx függvényhívások párhuzamos módon összes elérhető magokon hiányában a RevoScaleR használata **numCoresToUse** beállítás, például `rxOptions(numCoresToUse=6)`. Párhuzamos végrehajtás beállítások optimális teljesítményt nyújtanak.

A következő táblázat összefoglalja a különböző számítási környezet lehetőségek beállításához hívások végrehajtásának módját:

| Számítási környezet  | Hogyan kell beállítani                      | Végrehajtási környezet                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Szekvenciális helyi | rxSetComputeContext('local')    | Párhuzamos működésű végrehajtása a magokon a biztonsági csomópont kiszolgáló kivételével Feladattervek végrehajtásának rxExec hívások |
| Helyi párhuzamos   | rxSetComputeContext('localpar') | Párhuzamos működésű végrehajtása a magokon a peremhálózati kiszolgáló |
| Spark            | RxSpark()                       | Párhuzamos működésű elosztott végrehajtásának keresztül a csomópontjai között a HDI-fürtnek |
| Térkép csökkentése       | RxHadoopMR()                    | Párhuzamos működésű elosztott végrehajtásának keresztül térkép csökkentse a csomópontok között a HDI-fürtnek |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>A számítási környezet meg vonatkozó irányelvek

Adja meg a végrehajtási párhuzamos működésű választja három lehetőség közül melyiket analytics munkáját, mérete és az adatok helye jellegétől függ. Nincs nem egyszerű képlet, amely azt ismerteti, amelyek számítási környezet használata. Vannak, azonban néhány alapelvek, amelyek segítségével a megfelelő választás, vagy legalább, szűkítheti a választási lehetőségek egy teljesítményteszt futtatása előtt. Ezek az irányadó elvek a következők:

- A Linux helyi fájlrendszer gyorsabb, mint a HDFS.
- Ismétlődő elemzések gyorsabbak, ha az adatok helyi, és ha XDF.
- Célszerű adatfolyam-kis mennyiségű szöveg adatforrásból származó adatokat. Nagyobb adatmennyiség esetén konvertálja XDF elemzés előtt.
- A terhelést növelni az másolással vagy az élcsomóponthoz elemzéshez adatfolyam válik Kezelhetetlen nagyon nagy mennyiségű adat.
- A Spark gyorsabb, mint az térkép elemzése a Hadoop csökkenti az.

Megadott alapelvek, az alábbi szakaszok nyújtanak néhány általános szabályok megoldás a számítási környezet kijelöléséhez.

### <a name="local"></a>Helyi
* Ha elemzéséhez adatmennyiség kicsi, és nem igényel ismételt elemzése, majd adatfolyamként közvetlenül be az elemző rutin használatával *helyi* vagy *localpar*.
* Ha elemzéséhez adatmennyiség a kis- és közepes méretű és ismételt elemzése szükséges, majd másolja azt a helyi fájlrendszer XDF importálja és elemezze keresztül *helyi* vagy *localpar*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Ha elemzéséhez adatok mennyisége túl nagy, majd importálni kell a Spark DataFrame használatával **RxHiveData** vagy **RxParquetData**, vagy XDF HDFS-ben (kivéve, ha probléma a tároló), és elemezze a Spark számítási használatával a környezetben.

### <a name="hadoop-map-reduce"></a>Hadoop térkép csökkentése
* A térkép csökkentheti a számítási környezetben használja, ha a Spark számítási környezet módon hibát tapasztal, mert általában lassabban működik.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Beágyazott súgójának rxSetComputeContext
További információt és példákat a RevoScaleR számítási környezeteket tekintse meg a beágyazott megkönnyíti a R rxSetComputeContext módszer, például:

    > ?rxSetComputeContext

Azt is jelentheti a [elosztott számítástechnikai áttekintése](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) a [Machine Learning Server dokumentációjában](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, adja meg, hogyan végrehajtási párhuzamos működésű-e a élcsomópont vagy a HDInsight-fürt mag között rendelkezésre álló beállításokkal kapcsolatos. A HDInsight-fürtök az ML-szolgáltatások használatával kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [A Hadoop ML-szolgáltatások áttekintése](r-server-overview.md)
* [Ismerkedés a Hadoop ML-szolgáltatások](r-server-get-started.md)
* [A HDInsight az ML-szolgáltatások az Azure tárolási lehetőségek](r-server-storage.md)

