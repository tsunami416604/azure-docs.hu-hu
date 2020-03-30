---
title: A Jupyter-jegyzetfüzetek kernelei az Azure HDInsight Spark-fürtjein
description: Ismerje meg a PySpark, PySpark3 és Spark kernelek Jupyter notebook elérhető Spark-fürtök az Azure HDInsight.
keywords: jupyter notebook a szikra, jupyter szikra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064470"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>A Jupyter-jegyzetfüzetek kernelei az Azure HDInsightban lévő Apache Spark-fürtökön

A HDInsight Spark-fürtök olyan kerneleket biztosítanak, amelyeket az [Apache Spark](./apache-spark-overview.md) Jupyter-jegyzetfüzetével használhat az alkalmazások teszteléséhez. A kernel egy olyan program, amely fut, és értelmezi a kódot. A három kernel a következő:

- **PySpark** - python2-ben írt alkalmazásokhoz.
- **PySpark3** - Python3-ban írt alkalmazásokhoz.
- **Spark** - a Scala-ban írt alkalmazásokhoz.

Ebben a cikkben megtudhatja, hogyan használhatja ezeket a kerneleket és azok használatának előnyeit.

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Jupyter-jegyzetfüzet létrehozása a Spark HDInsightban

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a Spark-fürt.  Az utasításokat a [Fürtök listája és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) című témakörben találja. Megnyílik **az Áttekintés** nézet.

2. Az **Áttekintés** nézet **Fürtirányítópultok** mezőjében válassza a **Jupyter-jegyzetfüzet lehetőséget.** Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![Jupyter notebook az Apache Sparkon](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook a Sparkon")
  
   > [!NOTE]  
   > A Jupyter-jegyzetfüzetet a Spark-fürtön is elérheti a következő URL-cím megnyitásával a böngészőben. Cserélje le a **CLUSTERNAME-t** a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Válassza **az Új**lehetőséget, majd a **Pyspark**, **a PySpark3**vagy a **Spark** lehetőséget válassza a jegyzetfüzet létrehozásához. Használja a Spark kernel Scala alkalmazások, PySpark kernel Python2 alkalmazások hoz és PySpark3 kernel Python3 alkalmazásokhoz.

    ![Kernelek a Jupyter-jegyzetfüzethez a Sparkon](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernelek a Jupyter-jegyzetfüzethez a Sparkon")

4. Megnyílik egy jegyzetfüzet a kijelölt kernellel.

## <a name="benefits-of-using-the-kernels"></a>A kernelek használatának előnyei

Íme néhány előnye az új kernelek használatával Jupyter notebook Spark HDInsight-fürtök.

- **Előre beállított környezetek**. **A PySpark,** **PySpark3**vagy a **Spark** kernelek, nem kell explicit módon beállítani a Spark vagy a Hive környezetek kifejezetten az alkalmazásokkal való munka megkezdése előtt. Ezek alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

  - **sc** - Spark-környezethez
  - **sqlContext** - Hive környezethez

    Így a környezetek beállításához **nem** kell a következőhöz hasonló állításokat futtatnia:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Ehelyett közvetlenül használhatja az előre beállított környezeteket az alkalmazásban.

- **Sejt varázslatok**. A PySpark kernel néhány előre definiált "varázslatot" biztosít, amelyek `%%` speciális parancsok, amelyekkel meghívhat (például `%%MAGIC` `<args>`). A mágikus parancsnak a kódcella első szavának kell lennie, és több sornyi tartalmat kell lehetővé tennie. A varázsszó legyen az első szó a cellában. Hozzáadása semmit, mielőtt a mágia, még megjegyzéseket, hibát okoz.     További információ a varázslatok, lásd [itt](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    Az alábbi táblázat felsorolja a kerneleken keresztül elérhető különböző varázslatokat.

   | Magic | Példa | Leírás |
   | --- | --- | --- |
   | segítség |`%%help` |Létrehoz egy táblázatot az összes rendelkezésre álló varázslatról, példával és leírással |
   | Info |`%%info` |Az aktuális Livy-végpont munkamenet-információinak kitermelése |
   | Konfigurálja |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |A munkamenet létrehozásának paramétereit adja meg. Az erőjelző (-f) kötelező, ha már létrejött egy munkamenet, ami biztosítja a munkamenet eldobását és újbóli létrehozását. Nézd meg [Livy's POST / sessions Request Body](https://github.com/cloudera/livy#request-body) egy listát az érvényes paramétereket. A paramétereket JSON-karakterláncként kell átadni, és a mágia után a következő sorban kell lenniük, amint az a példa oszlopban látható. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Hive-lekérdezést hajt végre az sqlContext-en. Ha `-o` a paraméter átlett adva, a lekérdezés eredménye [pandas](https://pandas.pydata.org/) adatkeretként a %%local Python környezetben is megmarad. |
   | Helyi |`%%local`<br>`a=1` |A következő sorokban lévő összes kód helyileg kerül végrehajtásra. A kódnak érvényes Python2-kódnak kell lennie, függetlenül attól, hogy a használt kernelt használja.Code must be valid Python2 code even as to the kernel you're using. Így még akkor is, ha a **Pirspark3** vagy **spark** kernelek a jegyzetfüzet létrehozása közben, ha a mágia használata egy cellában, hogy a `%%local` cella csak érvényes Python2-kód. |
   | naplók |`%%logs` |Az aktuális Livy-munkamenet naplóinak kimenete. |
   | delete |`%%delete -f -s <session number>` |Az aktuális Livy-végpont egy adott munkamenetének törlése. Nem törölheti a rendszermag számára kezdeményezett munkamenetet. |
   | tisztítás |`%%cleanup -f` |Törli az aktuális Livy-végpont összes munkamenetét, beleértve a jegyzetfüzet munkamenetét is. Az erőjelző -f kötelező. |

   > [!NOTE]  
   > A PySpark kernel által hozzáadott varázslatok mellett használhatja a [beépített IPython varázslatokat](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)is, beleértve a `%%sh`. A `%%sh` mágiával parancsfájlok és kódblokk futtatásához a fürt csomóponton.

- **Automatikus megjelenítés**. A Pyspark kernel automatikusan vizualizálja a Hive és az SQL-lekérdezések kimenetét. Többféle képi megjelenítés közül választhat, például táblázat, kör, vonal, terület, sáv.

## <a name="parameters-supported-with-the-sql-magic"></a>A(z) %%sql magic paraméterei támogatottak

A `%%sql` mágia támogatja a különböző paramétereket, amelyek segítségével szabályozhatja, hogy milyen típusú kimenet, amely a lekérdezések futtatásakor kap. Az alábbi táblázat a kimenetet sorolja fel.

| Paraméter | Példa | Leírás |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Ezzel a paraméterrel a lekérdezés eredményét a %%local Python környezetben pandas adatkeretként [őrizheti](https://pandas.pydata.org/) meg. A dataframe változó neve a megadott változónév. |
| -q |`-q` |Ezzel kikapcsolhatja a cella vizualizációit. Ha nem szeretné automatikusan vizualizálni egy cella tartalmát, és csak adatkeretként szeretné rögzíteni, használja a használatát. `-q -o <VARIABLE>` Ha az eredmények rögzítése nélkül szeretné kikapcsolni a vizualizációkat (például SQL-lekérdezés, például `CREATE TABLE` utasítás futtatásához), `-q` `-o` argumentum megadása nélkül használja a vizualizációkat. |
| -m |`-m <METHOD>` |Ahol a **MÓDSZER** vagy **venni,** vagy **minta** (alapértelmezett **venni**). Ha a metódus **a**rendszermag, a kernel a MAXROWS által megadott eredményadatkészlet tetejéről választja ki az elemeket (a táblázat későbbi részében ismertetett). Ha a módszer **minta,** a kernel véletlenszerűen minták `-r` elemei az adatkészlet paraméter szerint, a következő ebben a táblázatban leírt. |
| -r |`-r <FRACTION>` |Itt a **TÖRT** egy 0,0 és 1,0 közötti lebegőpontos szám. Ha az SQL-lekérdezés mintametódusa a `sample`, akkor a kernel véletlenszerűen mintát vesz az eredményhalmaz elemeinek megadott hányadából. Ha például egy SQL-lekérdezést az `-m sample -r 0.01`argumentumokkal futtat, akkor az eredménysorok 1%-a véletlenszerűen mintavételezésre kerül. |
| -n |`-n <MAXROWS>` |**A MAXROWS** egész szám. A kernel a kimeneti sorok számát **MAXROWS**értékre korlátozza. Ha a **MAXROWS** negatív szám , például **-1,** akkor az eredményhalmaz sorainak száma nincs korlátozva. |

**Példa:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A fenti nyilatkozat a következőket teszi:

- Kijelöli az összes rekordot a **hivesampletable**listából.
- Mivel a -q-t használjuk, kikapcsolja az autovisualizációt.
- Mivel véletlenszerűen használjuk `-m sample -r 0.1 -n 500` a kaptármintatábla sorainak 10%-át, és az eredmény méretét 500 sorra korlátozza.
- Végül, mert `-o query2` azt is elmenti a kimenetet egy dataframe nevű **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Az új kernelek használata során figyelembe vett szempontok

Bármelyik kernelt is használja, a notebookok futtatása a fürt erőforrásait használja fel.  Ezekkel a kernelekkel, mivel a környezetek előre be vannak állítva, a jegyzetfüzetek ből való kilépés nem öli meg a környezetet, ezért a fürt erőforrásai továbbra is használatban vannak. A fontos gyakorlat, hogy a jegyzetfüzet **Fájl** menüjének **Bezárás és leállítás** parancsát használja, amikor befejezte a jegyzetfüzet használatát, ami megöli a környezetet, majd kilép a jegyzetfüzetből.

## <a name="where-are-the-notebooks-stored"></a>Hol tárolják a jegyzetfüzeteket?

Ha a fürt az Azure Storage-t használja alapértelmezett tárfiókként, a Jupyter-jegyzetfüzetek a **/HDiNotebooks** mappában tárolódnak.  A Jupyterből létrehozott jegyzetfüzetek, szövegfájlok és mappák a tárfiókból érhetők el.  Ha például a Jupyter segítségével hoz létre egy mappát **a myfolder** mappával és egy `/HdiNotebooks/myfolder/mynotebook.ipynb` notebook **myfolder/mynotebook.ipynb**mappával, akkor a tárfiókon belül érheti el a jegyzetfüzetet.  Ennek fordítottja is igaz, azaz ha egy jegyzetfüzetet `/HdiNotebooks/mynotebook1.ipynb`közvetlenül a tárfiókba tölt fel, a jegyzetfüzet a Jupyter-ből is látható.  A jegyzetfüzetek a fürt törlése után is a tárfiókban maradnak.

> [!NOTE]  
> HDInsight-fürtök az Azure Data Lake Storage alapértelmezett tárolóként nem tárolja a jegyzetfüzeteket a társított tárolóban.

A notebookok tárolóhely-mentésének módja kompatibilis az [Apache Hadoop HDFS rendszerrel.](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) Tehát, ha SSH a fürtbe használhatja a fájlkezelési parancsokat, ahogy az a következő kódrészletben látható:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Függetlenül attól, hogy a fürt az Azure Storage vagy az Azure Data Lake Storage alapértelmezett `/var/lib/jupyter`tárfiókként használja, a notebookok is menti a fürt headnode a.

## <a name="supported-browser"></a>Támogatott böngésző

A Spark HDInsight-fürtökjének Jupyter-jegyzetfüzetei csak a Google Chrome-ban támogatottak.

## <a name="feedback"></a>Visszajelzés

Az új kernelek fejlődnek, és idővel kiforrnak. Ez azt is jelentheti, hogy az API-k változhatnak, ahogy ezek a kernelek kiforrott. Nagyra értékelnénk minden visszajelzést, hogy van használata közben ezeket az új kernelek. Ez hasznos a kernelek végleges kiadásának alakításában. A megjegyzéseket/visszajelzéseket a cikk alján található Visszajelzés szakaszban **hagyhatja.**

## <a name="see-also"></a>Lásd még

- [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

- [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
- [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
- [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

- [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
- [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

- [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
- [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
- [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
- [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

- [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
- [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
