---
title: Az R Server on Azure HDInsight bemutatása |} Microsoft Docs
description: Megtudhatja, hogyan R Server használata a HDInsight a big data elemző alkalmazások létrehozásához.
services: hdinsight
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 2106e5f0c9b6a27da3747bc67aad0937f2356873
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>R Server, és nyílt forráskódú R képességek a HDInsight bemutatása

Microsoft R Server a HDInsight-fürtök létrehozása az Azure-ban esetén érhető el, mint a központi telepítési lehetőség. Ez az új képesség biztosít adatelemzők, statisztikusok és R-t használó programozók méretezhető, igény szerinti hozzáféréssel rendelkező elosztott elemzés hdinsight platformon történő módszerek.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Fürtök méretének a projektek és az elvégzendő feladatok, és ha azok már nincs szükség majd bontva. Mivel azok Azure HDInsight részei, ezeken a fürtökön rendelkeznek vállalati szintű 24/7 támogatása, szolgáltatásiszint-szerződésben garantált 99,9 %-os üzemidőt, valamint a integrálása az Azure-ökoszisztéma más összetevői.

Az R Server on HDInsight a legújabb funkciókat az R-alapú használatával szinte bármilyen méretű, vagy az Azure Blob, vagy a Data Lake tárolási betöltött adatkészletek biztosít. R Server nyílt forráskódú R épül, mivel az R-alapú alkalmazások létrehozása kihasználhatják a 8000 + nyílt forráskódú R csomagok bármelyikét. A feladatok ScaleR, a Microsoft big data elemzés csomag részét képező R Server, az is elérhetők.

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához. Egy élcsomópontot az informatikai részleg parallelized elosztott feladatai ScaleR a peremhálózati kiszolgáló a magokon futtassák. Is futtathatja őket a fürt csomópontjai között ScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

A modellek vagy elemzések kapott eredmény letölthető az előrejelzés használja a helyszíni. Akkor is is lehet operationalized máshol Azure, különösen keresztül [Azure Machine Learning Studio](http://studio.azureml.net) [webszolgáltatás](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>A HDInsight R első lépések
R Server szerepeljenek a HDInsight-fürtöt, ki kell választani az R Server fürt típusát, az Azure portál használatával HDInsight-fürtök létrehozásakor. Az R Server-fürt típusa tartalmaz R Server, az adatok a fürt csomópontjai és egy élcsomópontot, az R Server-alapú analytics követően zónának szolgál. Lásd: [Ismerkedés az R Server on HDInsight az](r-server-get-started.md) a fürt létrehozásához útmutatást.

## <a name="learn-about-data-storage-options"></a>További tudnivalók az adatok tárolási lehetőségek
A HDInsight-fürtök a HDFS fájlrendszerében alapértelmezett tároló vagy egy Azure Storage-fiókot, vagy egy Azure Data Lake store társítható. Ez a társítás biztosítja, hogy az adatokat tölt fel a fürt, tárolás elemzési során állandó történik. Számos különböző eszköz az adatok átvitele a tárolási lehetőség választ ki, beleértve a feltöltési portálalapú létesítmény a tárfiók kezelése és a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Lehetősége van további Blob való hozzáférés hozzáadása, és a Data lake tárolja a kiépítési folyamat függetlenül használja az elsődleges tárolási lehetőség fürt során. Lásd: [Ismerkedés az R Server on HDInsight az](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) további fiókokat ad hozzá hozzáférési olvashat. Tekintse meg a kiegészítő [Azure tárolási lehetőségek R Server on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) több storage-fiókok használatával kapcsolatos további olvashatja.

Is [Azure fájlok](../../storage/files/storage-how-to-use-files-linux.md) tárolási beállításként való használatra a peremhálózati csomóponton. Az Azure Files lehetővé teszi a fájlmegosztást, amelyet a Linux fájlrendszerben létrejött az Azure Storage csatlakoztatni. HDInsight-fürt az R Serverhez adatok tárolási lehetőségek kapcsolatos további információkért lásd: [Azure Storage vonatkozó az R Server on HDInsight-fürtök beállítások](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Hozzáférés az R Server on a fürt
R Server élcsomópontjához egy böngészőben a csatlakozhat. Telepíti a rendszer alapértelmezés szerint a fürt létrehozása során. További információkért lásd: [az R Server on HDInsight használatába](r-server-get-started.md).

Akkor is csatlakozhatnak a R Server a parancssor az R-konzol eléréséhez a SSH/PuTTY használatával. 

## <a name="develop-and-run-r-scripts"></a>Fejlesztéséhez és az R parancsfájlok futtatása
Az R parancsfájlok létrehozása és futtatása használhatja a rendelkezésre álló párhuzamos működésű és elosztott rutinok mellett 8000 + nyílt forráskódú R csomagokat a ScaleR könyvtárban. R Server élcsomópont a futó parancsfájl általában az R parancsértelmező belül fut, ezen a csomóponton. A kivételek közé tartoznak a fenti lépések, amelyeket a beállított számítási környezetet ScaleR függvény Hadoop térkép csökkentése (RxHadoopMR) vagy a Spark (RxSpark). A függvény ebben az esetben egy elosztott módon fut, ezen adatok (feladat) csomópontokon a fürt társított hivatkozott adatokkal. A különböző számítási környezet beállításokkal kapcsolatos további információkért lásd: [számítási környezet lehetőségek R Server on HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Modell üzembe helyezése
Az adatok modellezési befejeződése után az új adatokat, vagy az Azure és a helyszíni előrejelzéseket készítsen a modellt üzembe. Ez a folyamat pontozási néven ismert. Pontozó teheti HDInsight, Azure Machine Learning vagy a helyszínen.

### <a name="score-in-hdinsight"></a>Pontszám a Hdinsightban
Pontozásához a hdinsight eszközben, az R függvényt, amely meghívja a modell a tárfiók betöltött új adatfájl előrejelzéseket készítsen írni. Ezután menti az előrejelzés a tárfiók. A szokásos igény szerinti élcsomópont a fürt vagy egy ütemezett feladat segítségével is futtathatja.  

### <a name="score-in-azure-machine-learning-aml"></a>Az Azure-ban pontszám számítógép-képzési (AML)
Pontszám egy AML webszolgáltatás segítségével, használja az úgynevezett nyílt forráskódú Azure Machine Learning R csomag [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) közzététele a modell Azure webszolgáltatásként. Kényelmi célokat szolgál a csomag nincs előre telepítve van a élcsomópont. Ezután a Machine Learning létesítményekben hozhat létre a webszolgáltatás felhasználói felület, és majd a webszolgáltatás hívására, pontozó igény szerint.

Válassza ezt a beállítást, ha szüksége bármely ScaleR modellobjektumokat átalakítása egyenértékű nyílt forráskódú modellobjektumokat a webszolgáltatáshoz való használatra. Használjon ScaleR kényszerítési funkciók, például `as.randomForest()` ensemble alapú modellek esetében az átalakításhoz.

### <a name="score-on-premises"></a>Helyszíni pontszám
A modell létrehozása után helyszíni pontozásához, szerializálni a modellnek az R, töltse le, deszerializálni, mert azt és kövesse az új adatok pontozása. Akkor is a korábban a leírt módszer segítségével új adatok pontozása [hdinsight pontozási](#scoring-in-hdinsight) vagy használatával [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>A fürt karbantartása
### <a name="install-and-maintain-r-packages"></a>Telepítheti és karbantarthatja R csomagok
Az R csomagokat használhat a legtöbb élcsomópont óta az R parancsfájlok nincs legtöbb lépések szükségesek. Élcsomópont további R csomagok telepítéséhez használhatja a szokásos `install.packages()` R. metódus

Ha rutinok a ScaleR könyvtárból a fürtön csak használ, nem általában telepítendő további R csomagokat az adatok csomópontján. Azonban szükség lehet további csomagok használatát támogató **rxExec** vagy **RxDataStep** végrehajtási adatok csomópontjára.

Ezekben az esetekben a további csomagok telepíthetők a parancsfájlművelet a fürt létrehozása után. További információkért lásd: [HDInsight fürtök létrehozásával R Server](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Hadoop-leképezés csökkentése memória beállításainak módosítása
Egy fürt által elérhető R Server, amikor egy térképen csökkentse feladat fut memória mennyiségének módosítása kell módosítani. A fürt módosításához használja az Apache Ambari felhasználói felület, amely a fürt számára az Azure portál panel keresztül érhető el. A fürt felhasználói felülete Ambari elérésével kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

Akkor is módosíthatja a Hadoop kapcsolók használatával hívásában R Server rendelkezésre álló memória mennyisége **RxHadoopMR** az alábbiak szerint:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Fürt méretezése
Egy meglévő fürthöz is méretezhető felfelé vagy lefelé a portálon keresztül. Skálázással, a további kapacitást, amelyek hasznosak lehetnek a nagyobb feldolgozási feladatok áttekintésével felmérheti, vagy méretezheti vissza egy fürt tétlen állapotában. A fürt méretezése kapcsolatos útmutatásért lásd: [kezelése HDInsight-fürtök](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A rendszer karbantartása
Operációsrendszer-javítások és egyéb frissítés alkalmazásához karbantartást végeznek a HDInsight-fürtök a mögöttes Linux virtuális gépek munkaidőn kívüli során. Általában karbantartási történik, 3:30-kor (a virtuális gép számára a helyi ideje alapján), minden hétfőjén és csütörtökén. Úgy, hogy a fürt egyszerre több mint egy negyedéve nincs hatással lesznek a frissítések megtörténik.  

Mivel az átjárócsomópontokkal redundáns, és nem minden adatcsomópontokat érintettek, bármely ez idő alatt futó feladatok lelassíthatják. Ezek továbbra is fusson lefusson, azonban. Bármely egyéni szoftvert vagy a helyi adatok, amelyekhez őrzi meg karbantartási eseményekből keresztül, kivéve, ha végzetes hiba lép fel, amely egy fürt újjáépítése szükséges.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>További tudnivalók: IDE beállítások R Server on HDInsight-fürt
HDInsight-fürt Linux élcsomópont az R-alapú elemzése követően időzónáját. HDInsight újabb verzióiban a peremhálózati csomóponton Rstudióból kiszolgáló alapértelmezett telepítést biztosítanak, egy webböngésző-alapú IDE. Egy IDE a fejlesztési Rstudióból kiszolgáló használni és az R parancsfájlok végrehajtását lehet jelentősen hatékonyabb, mint az R-konzol használata.

Egy másik teljes IDE lehetőség egy asztali IDE telepítse, és a fürt elérni egy távoli térkép csökkentése vagy a Spark számítási környezet használata. A Microsoft a választható lehetőségek [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) Rstudióból és Walware tartozó Eclipse-alapú [StatET](http://www.walware.de/goto/statet).

Végül, az R Server konzolra élcsomópont begépelésével érhetik el **R** SSH vagy PuTY segítségével történő kapcsolódás után Linux parancs parancssorba. Ha a konzol felhasználói felületén, célszerű egy szövegszerkesztőben R-parancsfájl fejlesztési futtassa egy másik ablakban, és Kivágás, és illessze be a parancsfájl szakaszok a R konzolba, igény szerint.

## <a name="learn-about-pricing"></a>További tudnivalók díjszabása
A díjak, az R Server a HDInsight-fürtök rendelt hasonlóan a szabványos HDInsight-fürtök díja van felépítve. Az alapul szolgáló virtuális gépek méretezési a neve, adatokat és peremhálózati csomópontok, azonban kiegészül a core órás uplift alapulnak. HDInsight árazással kapcsolatos további információért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>További lépések
R Server használata a HDInsight-fürtökkel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Bevezetés az R Server on HDInsight használatába](r-server-get-started.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](r-server-compute-contexts.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](r-server-storage.md)
