---
title: "Adatkörnyezet beállításai R Server on HDInsight - Azure számítási |} Microsoft Docs"
description: "További tudnivalók a különböző számítási környezetben lehetőség legyen elérhető a felhasználók az R Server a HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: e15a263879ddc0edabc301201019f4209eee1058
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Számítási környezet lehetőségek R Server on HDInsight

A Microsoft az R Server on Azure HDInsight szabályozza hívások úgy, hogy a számítási környezet végrehajtásának módját. Ez a cikk ismerteti a megadása, hogyan végrehajtási párhuzamos működésű-e a élcsomópont vagy a HDInsight-fürt mag között elérhető beállítások.

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához. Egy élcsomópontot az informatikai részleg parallelized elosztott feladatai ScaleR a peremhálózati kiszolgáló a magokon futtassák. Is futtathatja őket a fürt csomópontjai között ScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>A Microsoft az R Server on Azure HDInsight
[A Microsoft az R Server on Azure HDInsight](r-server-overview.md) a legújabb funkciókat biztosít az R-alapú használatával. Egy a HDFS-tárolóban tárolt adatokat képes használni a [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage tárolóban") tárfiókot, egy Data Lake store vagy a Linux helyi fájlrendszer. R Server nyílt forráskódú R épül, mivel az R-alapú alkalmazások létrehozása a 8000 + nyílt forráskódú R csomagok bármelyikét alkalmazhatja. Használhatja a feladatok az [RevoScaleR](https://msdn.microsoft.com/microsoft-r/scaler/scaler), a Microsoft big data elemzés csomag R Server részét képező.  

## <a name="compute-contexts-for-an-edge-node"></a>Számítási környezetek számára egy élcsomópontot
Az R Server rendszeren futó élcsomópont R-parancsfájl általában az R parancsértelmező belül fut, ezen a csomóponton. A kivételek olyan ScaleR függvény témakör lépéseit. A ScaleR hívások határozza meg, hogyan állíthatja a ScaleR számítási környezet számítási környezetben fusson.  Amikor az R-parancsfájl egy élcsomópontot futtatja, a számítási környezet lehetséges értékei a következők:

- szekvenciális helyi (*"local"*)
- helyi párhuzamos (*"localpar"*)
- Térkép csökkentése
- Spark

A *"local"* és *"localpar"* más-más lehetőségek csak a hogyan **rxExec** hívások végrehajtása. Mindkettő végrehajtani más rx függvényhívások párhuzamos módon összes elérhető magokon hiányában a ScaleR használata **numCoresToUse** beállítás, például `rxOptions(numCoresToUse=6)`. Párhuzamos végrehajtás beállítások optimális teljesítményt nyújtanak.

A következő táblázat összefoglalja a különböző számítási környezet lehetőségek beállításához hívások végrehajtásának módját:

| Számítási környezet  | Hogyan kell beállítani                      | A végrehajtási környezet                        |
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
* Ha elemzéséhez adatmennyiség kicsi, és nem igényel ismételt elemzése, majd adatfolyamként közvetlenül be az elemző rutin használatával *"local"* vagy *"localpar"*.
* Ha elemzéséhez adatmennyiség a kis- és közepes méretű és ismételt elemzése szükséges, majd másolja azt a helyi fájlrendszer XDF importálja és elemezze keresztül *"local"* vagy *"localpar"*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Ha elemzéséhez adatok mennyisége túl nagy, majd importálni kell a Spark DataFrame használatával **RxHiveData** vagy **RxParquetData**, vagy a HDFS-ben XDF (kivéve, ha probléma a tároló), és elemezze a Spark számítási környezetet használ.

### <a name="hadoop-map-reduce"></a>Hadoop térkép csökkentése
* A térkép csökkentheti a számítási környezetben használja, ha a Spark számítási környezet módon hibát tapasztal, mert általában lassabban működik.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Beágyazott súgójának rxSetComputeContext
További információt és példákat a ScaleR számítási környezeteket tekintse meg a beágyazott megkönnyíti a R rxSetComputeContext módszer, például:

    > ?rxSetComputeContext

Is hivatkozhat a "[ScaleR elosztott számítástechnikai útmutató](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)", amely megtalálható a a [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server, az MSDN Webhelyén") könyvtár.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, adja meg, hogyan végrehajtási párhuzamos működésű-e a élcsomópont vagy a HDInsight-fürt mag között rendelkezésre álló beállításokkal kapcsolatos. R Server használata a HDInsight-fürtökkel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [R Server, a Hadoop – áttekintés](r-server-overview.md)
* [R Server a Hadoop első lépései](r-server-get-started.md)
* [Rstudióból kiszolgáló felvétele a HDInsight (Ha nincs hozzáadva a fürt létrehozása során)](r-server-install-r-studio.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](r-server-storage.md)

