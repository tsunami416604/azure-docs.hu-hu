---
title: Számítási környezeti beállítások a Machine Learning-szolgáltatásokhoz a HDInsight – Azure
description: További információ a különböző számítási környezeti beállítások a Machine Learning-szolgáltatásokkal a felhasználók rendelkezésére a HDInsight
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 98c92638d66e3ac1b4b537c0f1f8b70000aa85e9
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617564"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Számítási környezeti beállítások a HDInsight a Machine Learning-szolgáltatások

Machine Learning-szolgáltatások az Azure HDInsight a hívások a számítási környezet beállításával végrehajtásának módját szabályozza. Ez a cikk ismerteti a számára adja meg, hogyan végrehajtási párhuzamosíthatók-e a peremhálózati csomópont vagy a HDInsight-fürt mag között elérhető beállítások.

A fürt élcsomópontjához kényelmes megoldás az R-szkriptek futtatása és a fürthöz való csatlakozáshoz. Az élcsomópont lehetősége van a futó párhuzamos működésű elosztott funkcióit RevoScaleR a Processzormagok száma, a peremhálózati kiszolgáló között. Is futtathatja őket a fürt csomópontjai között használatával a RevoScaleR a Hadoop Mapreduce vagy a Spark számítási környezetek.

## <a name="ml-services-on-azure-hdinsight"></a>Machine Learning-szolgáltatások az Azure HDInsight
[Machine Learning-szolgáltatások az Azure HDInsight](r-server-overview.md) a legújabb funkciókat kínál az R-alapú elemzés. Egy a HDFS-tárolóban tárolt adatokat képes használni a [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob storage") tárfiók, egy Data Lake store vagy a Linux helyi fájlrendszer. Mivel nyílt forráskódú R, Machine Learning-szolgáltatások beépített, az R-alapú alkalmazásokat hoz létre a 8000-es + nyílt forráskódú R-csomagok is alkalmazható. Is használhatják a rutinokat a [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), a Microsoft big data analytics csomagot, amely a Machine Learning-szolgáltatások részét képezi.  

## <a name="compute-contexts-for-an-edge-node"></a>Számítási környezetek az élcsomópont
R-szkriptet az élcsomópont a Machine Learning szolgáltatások a fürtben futó általában az R-értelmező belül futtatja, a csomóponton. A kivétel ezeket a lépéseket, amelyek RevoScaleR függvény hívása. A RevoScaleR hívások állíthatja be, hogy a RevoScaleR számítási környezet által meghatározott számítási környezetben futnak.  Ha futtatja az R-szkriptet az élcsomóponthoz, a számítási környezet lehetséges értékei a következők:

- egymást követő helyi (*helyi*)
- helyi párhuzamos (*localpar*)
- Mapreduce
- Spark

A *helyi* és *localpar* beállítások eltérőek a csak a hogyan **rxExec** hívások végrehajtása. Mindkettő végrehajtani más rx függvényhívások párhuzamos módon között az összes rendelkezésre álló magok hacsak nincs másképpen megadva a RevoScaleR használatán keresztül **numCoresToUse** beállítás, például `rxOptions(numCoresToUse=6)`. Párhuzamos végrehajtási beállítások optimális teljesítményt nyújtanak.

A következő táblázat összefoglalja a különböző számítási környezeti beállítások beállítása a hívások végrehajtásának módját:

| Számítási környezet  | Hogyan állítható be                      | Végrehajtási környezet                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Helyi szekvenciális | rxSetComputeContext('local')    | A peremhálózati csomópont kivételével rxExec hívás, amely tárolókonfigurációhoz tevékenységében kiszolgáló között a Processzormagok száma párhuzamos végrehajtása |
| Helyi párhuzamos   | rxSetComputeContext('localpar') | A peremhálózati kiszolgáló között a Processzormagok száma párhuzamos végrehajtása |
| Spark            | RxSpark()                       | A HDI-fürt csomópontjai között keresztül a Spark elosztott végrehajtása párhuzamos működésű |
| Mapreduce       | RxHadoopMR()                    | A HDI-fürt csomópontjai között keresztül Mapreduce elosztott végrehajtása párhuzamos működésű |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Meghatározza az egy számítási környezet irányelvek

Adja meg a párhuzamos végrehajtási választott három lehetőség közül melyiket az elemzési munka, mérete és az adatok helye jellegétől függ. Nincs nincs egyszerű képlet, amely arra kéri, amely számítási környezet használata. Vannak azonban bizonyos alapelvek, amelyek segítségével győződjön meg arról, a megfelelő választás, vagy legalább, szűkítheti a választási lehetőségek a teljesítményteszt futtatása előtt. Ezek az alapelvek irányítsa a következők:

- A Linux helyi fájlrendszer az gyorsabb, mint a HDFS.
- Ismétlődő elemzések gyorsabbak, ha az adatok helyi, és ha az XDF van.
- Célszerű adatfolyam kis mennyiségű adatot egy szöveges adatforrásból. Nagyobb adatmennyiség esetén átalakíthatja XDF elemzés előtt.
- Kezelhetetlen nagyon nagy mennyiségű adat válik, másolása, illetve az adatok elemzéséhez az élcsomóponthoz streamelési járó többletterhelést.
- A Spark, a gyorsabb, mint a Mapreduce a Hadoop elemzés céljából.

Adja meg ezeket az alapelveket, az alábbi szakaszok nyújtanak néhány általános szabályokat a legjobb megoldás a számítási környezet kiválasztása.

### <a name="local"></a>Helyi
* Ha elemzéséhez adatmennyiség kicsi, és nem igényel ismételt elemzése, majd, adatfolyamként közvetlenül az analysis rutin használatával *helyi* vagy *localpar*.
* Ha ismétlődő elemzése szükséges elemzéséhez adatmennyiség kis vagy közepes méretű, majd másolja azt a helyi fájlrendszerben, azok importálása az XDF és elemezheti a testre keresztül *helyi* vagy *localpar*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Ha nagy adatmennyiség elemzését, majd importálni kell a Spark DataFrame használatával **RxHiveData** vagy **RxParquetData**, vagy a HDFS-ben XDF (kivéve, ha a tároló a hiba), és elemezheti a Spark számítási a környezet.

### <a name="hadoop-map-reduce"></a>Hadoop Mapreduce
* A Mapreduce számítási környezetet használja, ha általánosságban lassabb lesz, mivel a Spark számítási környezet megoldhatatlan hibát tapasztal.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Beágyazott segítséget rxSetComputeContext
További információkat és példákat RevoScaleR számítási környezetek lásd: a beágyazott megkönnyítő R rxSetComputeContext módszer, például:

    > ?rxSetComputeContext

Emellett olvassa el a [elosztott számítástechnikai áttekintése](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) a [Machine Learning-kiszolgáló dokumentációja](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a beállításokat, adja meg, hogyan végrehajtási párhuzamosíthatók-e között a peremhálózati csomópont vagy a HDInsight-fürt magok számára elérhető. Machine Learning-szolgáltatások és a HDInsight-fürtök együttes használatával kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Hadoop Machine Learning-szolgáltatások áttekintése](r-server-overview.md)
* [Machine Learning-szolgáltatások Hadoop használatának első lépései](r-server-get-started.md)
* [A HDInsight Machine Learning-szolgáltatásokhoz az Azure tárolási lehetőségek](r-server-storage.md)

