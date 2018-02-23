---
title: "Az Azure hdinsight Spark-fürtjei Jupyter notebookokhoz kernelek |} Microsoft Docs"
description: "További tudnivalók az Azure hdinsight Spark-fürtjei elérhető Jupyter notebook PySpark PySpark3 és Spark kernelek."
keywords: a spark, jupyter spark jupyter notebook
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 2be4477528c9109151c4737eabc16741cc020ce8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Az Azure hdinsight Spark-fürtjei Jupyter notebookokhoz kernelek 

HDInsight Spark-fürtjei is használhatja a Jupyter notebookkal a Spark on az alkalmazások teszteléséhez kernelt biztosítanak. A rendszermag egy olyan program, fut, és a kód értelmezi. A három kernelek a következők:

- **PySpark** – a Python2 írt alkalmazások esetén
- **PySpark3** – a Python3 írt alkalmazások esetén
- **Spark** - scalában írt alkalmazások esetén

Ebből a cikkből megismerheti, hogyan ezek kernelek és azok használatának előnyeit.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt hdinsightban. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>A Spark on HDInsight Jupyter notebook létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), nyissa meg a fürt.  Lásd: [listája és megjelenítése fürtök](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) vonatkozó utasításokat. A fürt egy új portálpanelen nyílik meg.

2. Az a **Gyorshivatkozások** területén kattintson **irányítópultok fürt** megnyitásához a **irányítópultok fürt** panelen.  Ha nem lát **Gyorshivatkozások**, kattintson a **áttekintése** a panel bal oldali menüjében.

    ![A Spark Jupyter notebook](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Spark a Jupyter notebook") 

3. Kattintson a **Jupyter Notebook**. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.
   
   > [!NOTE]
   > A böngészőben nyissa meg a következő URL-címet is a Jupyter notebook a Spark-fürt lehet elérni. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Kattintson **új**, majd a **Pyspark**, **PySpark3**, vagy **Spark** a notebook létrehozása. Használja a Spark kernel Scala-alkalmazások, a PySpark kernel Python2 alkalmazások és a PySpark3 kernel Python3 alkalmazásokhoz.
   
    ![Jupyter notebookokhoz a Spark mag](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jupyter notebookokhoz a Spark mag") 

4. A notebook a kiválasztott kernel nyílik meg.

## <a name="benefits-of-using-the-kernels"></a>A kernelek használatának előnyei

Az alábbiakban néhány előnyeit az új kernelek használata Jupyter notebook a Spark HDInsight-fürtökön.

- **Az adott néven beállítás környezetek**. A **PySpark**, **PySpark3**, vagy a **Spark** mag, nem kell beállítani a Spark- vagy Hive explicit módon az alkalmazások használatának megkezdése előtt. Ezek a alapértelmezés szerint elérhető. Ezek a környezetek a következők:
   
   * **sc** - Spark-környezet
   * **az sqlContext** - struktúra környezet
   
   Igen akkor nem kell futtatnia utasítások, mint például a következő beállítani:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Ehelyett közvetlenül használhatja a beállításkészletet kontextusban az alkalmazás.

- **A cella magics**. A PySpark kernel tartalmaz néhány előre definiált "magics", amelyeket speciális meghívhatja a parancsok `%%` (például `%%MAGIC` <args>). A magic parancs legyen az első szótól kód cellába, és lehetővé teszik a tartalom több sornyi. A magic word kell lennie az első szótól a cellában. Hozzáadás semmit a Bűvös, még akkor is, a megjegyzéseket, mielőtt hibát okoz.     A magics további információkért lásd: [Itt](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    Az alábbi táblázat a különböző magics a kernelek keresztül érhető el.

   | Magic | Példa | Leírás |
   | --- | --- | --- |
   | súgó |`%%help` |Létrehoz egy táblát az összes rendelkezésre álló magics példa és leírása |
   | információ |`%%info` |A jelenlegi Livy végpont kimenetek munkamenet-információk |
   | konfigurálás |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurálja a paramétereket a munkamenet létrehozásához. A force jelzést (-f) kötelező, ha a munkamenet már létezik, amely ellenőrzi, hogy a munkamenet eldobása és ismételt létrehozása megtörtént. Nézze meg [Livy a FELADÁS egy vagy több /sessions kérelem törzse](https://github.com/cloudera/livy#request-body) érvényes paraméterek listáját. Paraméterek a JSON karakterláncként kell átadnia, és későbbinek kell lennie a következő sorban a Bűvös példa oszlopában látható módon. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Végrehajtja a Hive-lekérdezések a sqlContext ellen. Ha a `-o` paramétert, a lekérdezés eredménye őrzi a %% helyi Python-környezetben, egy [Pandas](http://pandas.pydata.org/) dataframe. |
   | helyi |`%%local`<br>`a=1` |Az egymás utáni sorok a kód végrehajtása helyileg. Kód még a kernel módjától függetlenül érvényes Python2 kódot kell lennie. Igen, akkor is, ha a kiválasztott **PySpark3** vagy **Spark** kernelek a notebook létrehozása, ha közben az `%%local` magic cellába, ezt a cellát csak rendelkeznie kell érvényes Python2 kódot... |
   | naplók |`%%logs` |A naplók kiírja a Livy aktuális munkamenetre. |
   | törlés |`%%delete -f -s <session number>` |Egy adott munkamenet a jelenlegi Livy végpont törlése. A kernel magát a kezdeményezett munkamenet nem törölhető. |
   | tisztítás |`%%cleanup -f` |Törli az aktuális Livy végpont, beleértve a jegyzetfüzet munkamenet a munkamenetek. A kényszerített jelző -f megadása kötelező. |

   > [!NOTE]
   > A PySpark kernel által hozzáadott magics, mellett is használhatja a [beépített IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), többek között a következőket `%%sh`. Használhatja a `%%sh` magic a fürt headnode parancsfájlok és kódblokkot futtatásához.
   >
   >
2. **A képi megjelenítés automatikus**. A **Pyspark** kernel automatikusan visualizes struktúra és az SQL-lekérdezések eredményének. Számos különféle típusú képi megjelenítések, beleértve a tábla, torta, vonal, terület, sáv választhat.

## <a name="parameters-supported-with-the-sql-magic"></a>Támogatott paraméterek a %% sql varázsszám
A `%%sql` magic támogatja, amelyek segítségével szabályozhatja, hogy lekérdezések futtatásakor kap kimeneti típusú más paraméterekkel. Az alábbi táblázat a kimenetet.

| Paraméter | Példa | Leírás |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Használja ezt a paramétert való megőrzésre a lekérdezés eredménye a %% helyi Python-környezetben, mint egy [Pandas](http://pandas.pydata.org/) dataframe. A dataframe változó neve nem a megadott változónév. |
| -k |`-q` |Ezzel a cella képi megjelenítések kikapcsolása. Ha nem kívánja automatikus megjelenítése egy cella tartalmát, és csak szeretné rögzíteni, mint egy dataframe, majd használja `-q -o <VARIABLE>`. Ha szeretné-e az eredmények rögzítése nélkül kapcsolja ki a képi megjelenítések (, például egy SQL-lekérdezést, például fut egy `CREATE TABLE` utasítás), használjon `-q` megadása nélkül egy `-o` argumentum. |
| -m |`-m <METHOD>` |Ha **METÓDUS** vagy **érvénybe** vagy **minta** (alapértelmezett érték a **érvénybe**). Ha a metódus **igénybe**, a kernel választja ki a lista elejéről az adatok eredményhalmaz MAXROWS (később a táblázatban ismertetett) által meghatározott elemek. Ha a metódus **minta**, a kernel véletlenszerűen minták elemei a következők szerint az adatkészlet `-r` paramétert, a következő táblázatban leírt. |
| -r |`-r <FRACTION>` |Itt **mért** 0,0 és 1,0 közötti lebegőpontos szám. Ha a minta az SQL-lekérdezést módja `sample`, akkor a kernel véletlenszerűen minták meg eredménykészlet elemeinek megadott mekkora részét. Például, ha futtatja az SQL-lekérdezést az argumentumokkal `-m sample -r 0.01`, akkor az eredmény sorok 1 % véletlenszerűen lekérdező. |
| -n |`-n <MAXROWS>` |**MAXROWS** egész érték. A kernel kimeneti sorok számának korlátozása **MAXROWS**. Ha **MAXROWS** például van egy negatív szám **-1**, akkor az eredményhalmaz sorok száma nincs korlátozva. |

**Példa**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A következőket hajtja végre a fenti utasítást:

* Kiválasztja az összes rekordot **hivesampletable**.
* Használjuk a - q, mert automatikus-képi megjelenítés kikapcsolása.
* Mivel használjuk `-m sample -r 0.1 -n 500` véletlenszerűen 10 % a hivesampletable sorba minták, és korlátozza az 500 sorok eredményhalmazt méretét.
* Végül mert használtuk `-o query2` be egy nevű dataframe is menti a kimeneti **lekérdezés2**.

## <a name="considerations-while-using-the-new-kernels"></a>Az új kernelek használatakor kapcsolatos szempontok

Használja, amelyik kernel futtató notebookok elhagyása igényel, a fürt erőforrásait.  Ezek mag, a környezet előre van állítva, mert egyszerűen kilép a notebookok nem kill a környezetben, és ezért a fürt erőforrásait, továbbra is használja. Jó gyakorlat a **zárja be és Halt** a notebook kapcsolót **fájl** menü, amikor végzett a notebook, amely használhatatlanná teszi a környezetben, és majd kilép a notebook használatával.     

## <a name="show-me-some-examples"></a>Néhány példa megjelenítése

Jupyter notebook megnyitásakor látni a gyökérszinten elérhető két mappát.

* A **PySpark** mappában van, amely az új mintát notebookok **Python** kernel.
* A **Scala** mappában van, amely az új mintát notebookok **Spark** kernel.

Megnyithatja a **Spark Magic 00 - [OLVASHATÓ első] Kernel szolgáltatások** a notebook a **PySpark** vagy **Spark** mappa további információt a különböző magics érhető el. Használhatja a többi rendelkezésre álló minta notebookok a két mappák áttekintésével megismerheti, hogyan Jupyter notebookok használata a HDInsight Spark-fürtjei különböző alkalmazási helyzetek eléréséhez.

## <a name="where-are-the-notebooks-stored"></a>A notebookok tároló?

Jupyter notebookok a fürthöz tartozó tárfiókba menti a **/HdiNotebooks** mappa.  Notebookok, szöveges fájlt és mappát hoz létre a Jupyter belül érhetők el a tárfiókból.  Például, ha a Jupyter használatával hozzon létre egy mappát **SajátMappa** egy hordozható **myfolder/mynotebook.ipynb**, érheti el, hogy a notebook `/HdiNotebooks/myfolder/mynotebook.ipynb` a tárfiókon belül.  A névkeresési akkor is igaz, ez azt jelenti, hogy ha feltöltött jegyzetfüzet közvetlenül tárolási fiókját a `/HdiNotebooks/mynotebook1.ipynb`, valamint Jupyterről származó látható a notebook.  Notebookok maradni a tárfiókot, a fürtök törlése után is.

Notebookok menti a tárfiók módja kompatibilis a HDFS. Így, ha az SSH-ból a fürt használhatja fájl parancsok látható módon a következő kódrészletet:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


Abban az esetben, ha a fürt a tárfiók elérése problémák vannak, a notebookok is tárolja a headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Támogatott böngésző

A Spark HDInsight-fürtökön Jupyter notebookok csak Google Chrome támogatottak.

## <a name="feedback"></a>Visszajelzés
Az új kernelek szakasz fejlődnek, és adott idő alatt számos lesz. Ez is jelentheti, hogy API-k módosulhatnak, mivel ezek kernelek számos. Köszönjük volna olyan visszajelzést, hogy rendelkezik, ezek új kernelek használata során. Ez akkor hasznos, ezek kernelek végleges kialakításában. Megjegyzések/visszajelzése alapján is hagyhatja a **megjegyzések** szakasz Ez a cikk alján.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
