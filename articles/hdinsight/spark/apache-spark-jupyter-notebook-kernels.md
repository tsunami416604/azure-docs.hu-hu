---
title: Kernelek Jupyter notebookhoz a Spark-fürtökön az Azure HDInsight
description: Ismerje meg a PySpark, a PySpark3 és a Spark kerneleit az Azure HDInsight-alapú Spark-fürtökkel elérhető Jupyter notebookhoz.
keywords: jupyter notebook a Sparkban, jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 41cb27096782f525a531f38efda539c065fa4c72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163601"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernelek Jupyter notebookhoz Apache Spark-fürtökön az Azure HDInsight

A HDInsight Spark-fürtök olyan kerneleket biztosítanak, amelyeket a Jupyter notebookon használhat a [Apache Spark](https://spark.apache.org/) az alkalmazások teszteléséhez. A kernel egy olyan program, amely futtatja és értelmezi a kódot. A három kernel a következők:

- **PySpark** – a Python2-ben írt alkalmazások esetében.
- **PySpark3** – a Python3-ben írt alkalmazások esetében.
- **Spark** – a Scala-ben írt alkalmazások esetében.

Ebből a cikkből megtudhatja, hogyan használhatja ezeket a rendszermagokat és a használatának előnyeit.

## <a name="prerequisites"></a>Előfeltételek

Egy Apache Spark-fürt a HDInsight-ben. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Jupyter-jegyzetfüzet létrehozása a Spark HDInsight

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Spark-fürtöt.  Lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) az utasításokhoz. Megnyílik az **Áttekintés** nézet.

2. Az **Áttekintés** nézet **fürt irányítópultok** listájában válassza a **Jupyter notebook**elemet. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![Jupyter notebook on Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook a Sparkban")
  
   > [!NOTE]  
   > A Jupyter-jegyzetfüzetet a Spark-fürtön is elérheti, ha megnyitja a következő URL-címet a böngészőben. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Válassza az **új**lehetőséget, majd válassza ki a **Pyspark**, a **PySpark3**vagy a **Spark** elemet egy jegyzetfüzet létrehozásához. Használja a PySpark kernelt a Scala-alkalmazásokhoz, a Python2-alkalmazásokhoz és a PySpark3 kernelhez a Python3-alkalmazásokhoz.

    ![Kernelek Jupyter notebookhoz a Sparkban](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernelek Jupyter notebookhoz a Sparkban")

4. Megnyílik egy jegyzetfüzet a kiválasztott kernelrel.

## <a name="benefits-of-using-the-kernels"></a>A kernelek használatának előnyei

Íme néhány előnye az új kernelek használatában a Spark HDInsight-fürtökön futó Jupyter notebook használatával.

- **Előre beállított környezetek**. A **PySpark**, a **PySpark3**vagy a **Spark** kernelek esetében nem kell explicit módon beállítania a Spark vagy a kaptár környezetét az alkalmazásokkal való munka megkezdése előtt. Ezek alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

  - **SC** – Spark-környezethez
  - **sqlContext** – a kaptár kontextusához

    Ezért nem kell az alábbihoz hasonló utasításokat futtatnia a kontextusok beállításához:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Ehelyett közvetlenül is használhatja az előre definiált kontextusokat az alkalmazásban.

- **Cella-varázslatok**. A PySpark kernel néhány előre definiált "varázslatot" tartalmaz, amelyek a `%%` hívható speciális parancsok (például `%%MAGIC` `<args>`). A Magic parancsnak a kód első szava kell lennie, és több sornyi tartalmat is engedélyeznie kell. A bűvös szó csak a cella első szava lehet. A Magic, még a megjegyzések előtt bármit is megadhat, ami hibát okoz.     A Magics szolgáltatással kapcsolatos további információkért lásd [itt](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A következő táblázat a kerneleken keresztül elérhető különböző varázslatokat sorolja fel.

   | Magic | Példa | Leírás |
   | --- | --- | --- |
   | segítség |`%%help` |Létrehoz egy táblázatot az összes elérhető varázslatról a példával és a leírással |
   | információ |`%%info` |A jelenlegi Livy-végponthoz tartozó munkamenet-információk kimenete |
   | Konfigurálása |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurálja a munkamenetek létrehozásához szükséges paramétereket. A kényszerített jelző (-f) megadása kötelező, ha már létrejött egy munkamenet, amely biztosítja a munkamenet eldobását és újbóli létrehozását. Tekintse meg az érvényes paraméterek listáját a [LIVY post/Sessions kérelem törzsében](https://github.com/cloudera/livy#request-body) . A paramétereket JSON-karakterláncként kell átadni, és a Magic után a következő sorban kell szerepelniük, ahogy az a példában látható oszlopban látható. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Struktúra-lekérdezést hajt végre a sqlContext. Ha a `-o` paraméter át lett adva, a lekérdezés eredménye a (z)%% helyi Python-kontextusban marad a [Panda](https://pandas.pydata.org/) dataframe. |
   | helyi |`%%local`<br>`a=1` |A következő sorokban lévő összes kód helyileg fut. A kódnak érvényes Python2-kódnak is kell lennie, a használt kerneltől függetlenül. Tehát akkor is, ha a **PySpark3** vagy a **Spark** kernelt választotta a jegyzetfüzet létrehozásakor, ha a `%%local` varázslatot egy cellában használja, akkor a cellának csak érvényes Python2-kóddal kell rendelkeznie. |
   | naplók |`%%logs` |Megjeleníti a naplókat az aktuális Livy-munkamenethez. |
   | delete |`%%delete -f -s <session number>` |Törli az aktuális Livy-végpont adott munkamenetét. A rendszermag számára kezdeményezett munkamenet nem törölhető. |
   | tisztítás |`%%cleanup -f` |Törli az aktuális Livy-végpont összes munkamenetét, beleértve a jegyzetfüzetek munkamenetét is. A Force jelző-f megadása kötelező. |

   > [!NOTE]  
   > A PySpark kernel által hozzáadott varázslatok mellett használhatja a [beépített IPython-varázslatokat](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)is, beleértve a `%%sh`is. A `%%sh` Magic használatával parancsfájlokat és blokkokat futtathat a fürt átjárócsomóponthoz.

- **Automatikus vizualizáció**. A Pyspark-kernel automatikusan megjeleníti a struktúra és az SQL-lekérdezések kimenetét. Számos különböző típusú vizualizáció közül választhat, például a tábla, a torta, a vonal, a terület és a sáv.

## <a name="parameters-supported-with-the-sql-magic"></a>A (z)%% SQL Magic által támogatott paraméterek

A `%%sql` Magic különböző paramétereket támogat, amelyek segítségével szabályozhatja a lekérdezések futtatásakor kapott kimenet típusát. A következő táblázat felsorolja a kimenetet.

| Paraméter | Példa | Leírás |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Ezzel a paraméterrel megtarthatja a lekérdezés eredményét a (z)%% helyi Python kontextusban, [pandák](https://pandas.pydata.org/) dataframe. A dataframe változó neve a megadott név. |
| -q |`-q` |Ezzel kikapcsolhatja a cella vizualizációit. Ha nem szeretné megjeleníteni a cella tartalmát, és csak dataframe szeretné rögzíteni, akkor használja a `-q -o <VARIABLE>`. Ha az eredmények rögzítése nélkül szeretné kikapcsolni a vizualizációkat (például egy SQL-lekérdezés futtatásához, például egy `CREATE TABLE` utasításhoz), a `-q`t `-o` argumentum megadása nélkül használhatja. |
| – m |`-m <METHOD>` |Ahol a **metódus** vagy a **Take** vagy a **Sample** (alapértelmezett **érték).** Ha a metódus **elvégzése**megtörténik, a kernel a MAXROWS által meghatározott eredmény-adathalmaz elejétől (a táblázat későbbi részében leírtak szerint) kiválasztja az elemeket. Ha a metódus **minta**, a kernel véletlenszerűen mintákat vesz fel az adatkészletből `-r` paraméternek megfelelően, amely a táblázat következő részében olvasható. |
| -r |`-r <FRACTION>` |Itt a **frakció** egy 0,0 és 1,0 közötti lebegőpontos szám. Ha az SQL-lekérdezéshez tartozó minta metódus `sample`, akkor a kernel véletlenszerűen megkeresi az eredményhalmaz elemeinek megadott részét. Ha például egy SQL-lekérdezést futtat az argumentumokkal `-m sample -r 0.01`, akkor az eredmény sorainak 1%-a véletlenszerűen lett kiválasztva. |
| – n |`-n <MAXROWS>` |A **MAXROWS** egész érték. A kernel korlátozza az **MAXROWS**kimeneti sorok számát. Ha a **MAXROWS** negatív szám, például **-1**, akkor az eredményhalmaz sorainak száma nem korlátozott. |

**Példa**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A fenti utasítás a következő műveleteket végzi el:

- A **hivesampletable**összes rekordjának kijelölése.
- Mivel a-q-t használjuk, kikapcsolja az autovizualizációt.
- Mivel az `-m sample -r 0.1 -n 500` véletlenszerűen mintákat vesz fel a hivesampletable sorainak 10%-ában, és az eredményhalmaz méretét 500 sorra korlátozza.
- Végül, mivel `-o query2`t használtuk, a kimenetet egy **query2**nevű dataframe is menti.

## <a name="considerations-while-using-the-new-kernels"></a>Az új kernelek használatának szempontjai

A használt kernelek, így a futtatott jegyzetfüzetek a fürt erőforrásait használják.  Ezekkel a kernelekkel, mivel a kontextusok előre vannak beállítva, egyszerűen csak a jegyzetfüzetek bezárása nem fogja megölni a környezetet, ezért a fürt erőforrásai továbbra is használatban vannak. Jó gyakorlat a notebook **fájl** menüjének **Bezárás és leállítása** lehetőségének használata, amikor elkészült a jegyzetfüzet használatával, amely megöli a környezetet, majd kilép a jegyzetfüzetből.

## <a name="where-are-the-notebooks-stored"></a>Hol tárolódnak a jegyzetfüzetek?

Ha a fürt az Azure Storage-t használja alapértelmezett Storage-fiókként, a rendszer a Jupyter-jegyzetfüzeteket a **/HdiNotebooks** mappa alatt menti a Storage-fiókba.  A Jupyter belül létrehozott jegyzetfüzetek, szövegfájlok és mappák elérhetők a Storage-fiókból.  Ha például a Jupyter használatával hoz létre egy mappát a **MyFolder** és a **MyFolder/mynotebook. ipynb**nevű jegyzetfüzetből, a jegyzetfüzetet a Storage-fiókon belül `/HdiNotebooks/myfolder/mynotebook.ipynb` érheti el.  A fordított érték szintén igaz, azaz ha közvetlenül a Storage-fiókjába tölt fel egy jegyzetfüzetet a `/HdiNotebooks/mynotebook1.ipynb`, akkor a jegyzetfüzet a Jupyter is látható.  A jegyzetfüzetek a fürt törlése után is a Storage-fiókban maradnak.

> [!NOTE]  
> Az alapértelmezett tárolóval rendelkező Azure Data Lake Storage HDInsight-fürtök nem tárolnak jegyzetfüzeteket a társított tárolóban.

A jegyzetfüzetek a Storage-fiókba való mentésének módja kompatibilis a [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Tehát, ha SSH-t a fürtbe, használhatja a fájlkezelési parancsokat az alábbi kódrészletben látható módon:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Függetlenül attól, hogy a fürt az Azure Storage-t használja-e, vagy Azure Data Lake Storage alapértelmezett Storage-fiókként, a jegyzetfüzeteket a fürt átjárócsomóponthoz is menti `/var/lib/jupyter`.

## <a name="supported-browser"></a>Támogatott böngésző

A Spark HDInsight-fürtökön található Jupyter-jegyzetfüzetek csak a Google Chrome-ban támogatottak.

## <a name="feedback"></a>Visszajelzés

Az új kernelek folyamatosan fejlődnek, és idővel kiforrnak. Ez azt is jelentheti, hogy az API-k megváltozhatnak, mivel ezek a kernelek kiforrtak. Nagyra értékeljük az új kernelek használata során felmerülő visszajelzéseket. Ez a kernelek végleges kiadásának alakításakor hasznos. A megjegyzéseit/visszajelzéseit a cikk alján található **visszajelzések** szakaszban hagyhatja.

## <a name="seealso"></a>Lásd még:

- [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Alkalmazási helyzetek

- [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
- [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
- [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

- [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
- [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

- [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
- [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
- [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
- [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

- [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
- [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
