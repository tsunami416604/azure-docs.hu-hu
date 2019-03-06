---
title: Csatlakozás az Apache Hadoop használatával a Data Lake Tools for Visual Studio – Azure HDInsight
description: Megtudhatja, hogyan kell telepíteni, és az Apache Hadoop fürtök csatlakoztatásához az Azure HDInsight a Data Lake Tools for Visual Studio használatával és majd a Hive-lekérdezések futtatásához.
keywords: hadoop-eszközök,hive-lekérdezés,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 7d93e5777e3456098e1282fd70da55e85e1f80d0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438245"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>A Data Lake Tools for Visual Studio használatával csatlakozhat az Azure HDInsight és az Apache Hive-lekérdezések futtatása

Ismerje meg, hogyan használható [a Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (más néven is néven a Data Lake Tools) szeretne csatlakozni az Apache Hadoop-fürtök a [Azure HDInsight](../hdinsight-hadoop-introduction.md) és Hive-lekérdezések elküldéséhez.  

További információ a HDInsight használatáról: [A HDInsight bemutatása](../hdinsight-hadoop-introduction.md) és [HDInsight – első lépések](apache-hadoop-linux-tutorial-get-started.md).  

Csatlakozás az Apache Storm fürthöz kapcsolatos további információkért lásd: [Develop C# Visual Studio használatával a HDInsight Apache Storm-topológiák](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

A Data Lake Tools for Visual Studio mind az Azure Data Lake Analytics, mind a HDInsight eléréséhez használható. A Data Lake Tools eszközökkel kapcsolatos információkért lásd: [U-SQL-szkriptek fejlesztése Data Lake Tools for Visual Studio használatával](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez és a Data Lake Tools for Visual Studio használatához a következőkre van szüksége:

* Egy Azure-beli HDInsight-fürt. Egy HDInsight-fürt létrehozása: [Azure HDInsight az Apache Hadoop használatának első lépései](apache-hadoop-linux-tutorial-get-started.md). Interaktív Apache Hive-lekérdezések futtatásához, szükség van egy [HDInsight interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) fürt.  

* [A Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013-as vagy újabb).  A [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) ingyenes.  Lásd még a [Visual Studio 2017 telepítése](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

  > [!IMPORTANT]  
  > A Data Lake Tools for Visual Studio 2013 már nem támogatott. 

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017  
  A telepítés során, ellenőrizze, akkor tartalmaznia kell legalább számítási feladatok **Azure-fejlesztési** vagy **adattárolási és feldolgozási**.  

  A meglévő telepítések esetén a menüsoron lépjen **eszközök** > **első eszközök és szolgáltatások...**  Visual Studio telepítőjének megnyitása.  Válassza ki legalább számítási feladatok **Azure-fejlesztési** vagy **adattárolási és feldolgozási**.

  ![Képernyőfelvétel a Visual Studio telepítőjének](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 and 2015  
  [Töltse le a Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Válassza ki a Data Lake Tools azon verzióját, amely megfelel a Visual Studio verziójának.  

> [!NOTE]  
> A Data Lake Tools for Visual Studio jelenleg csak angol nyelven érhető el.

## <a name="update-data-lake-tools-for-visual-studio"></a>Frissítés a Data Lake Tools for Visual Studio  

1. Nyissa meg a Visual Studiót.

2. Lépjen a menüsoron **eszközök** > **bővítmények és frissítések...** .

3. Az a **bővítmények és frissítések** ablakában bontsa ki a **frissítések** a bal oldalon.

4. Frissítés érhető el, ha **az Azure Data Lake és Stream Analytics Tools** jelenik meg a fő ablakot.  Válassza ki **frissítés**.

> [!NOTE]  
> Csak a Data Lake Tools 2.3.0.0-s és újabb verziói támogatják az interaktív lekérdezési fürtökhöz való csatlakozást és az interaktív Hive-lekérdezések futtatását.

## <a name="connect-to-azure-subscriptions"></a>Csatlakozás Azure-előfizetésekhez
A Data Lake Tools for Visual Studio használatával csatlakozhat a HDInsight-fürtökhöz, alapvető felügyeleti műveleteket végezhet, és Hive-lekérdezéseket futtathat.

> [!NOTE]  
> Az általános Hadoop fürthöz való csatlakozással kapcsolatos információkat a [Hive-lekérdezések írása és elküldése a Visual Studio eszközzel](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) témakörben találja.

Csatlakozás az Azure-előfizetéshez:

1. Nyissa meg a Visual Studiót.

2. Lépjen a menüsoron **nézet** > **Server Explorer**.

3. A Server Explorer eszközben kattintson a jobb gombbal **Azure**válassza **kapcsolódás a Microsoft Azure-előfizetéshez...** , és a bejelentkezési folyamat befejezéséhez.

4. Server Explorer eszközben meglévő HDInsight-fürtök listája jelenik meg. Ha nincsenek fürtjei, létrehozhat egyet az Azure Portal, az Azure PowerShell vagy a HDInsight SDK használatával. További információ: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Fürtök listája a Data Lake Tools for Visual Studio Server Explorerében – képernyőkép](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Fürtök listája a Data Lake Tools for Visual Studio Server Explorerében")

5. Bontson ki egy HDInsight-fürtöt. Megjelenik a **Hive Databases** (Hive-adatbázisok), egy alapértelmezett tárfiók, a társított tárfiókok és a **Hadoop-szolgáltatásnapló**. Ennél jobban is kibonthatja az elemeket.

Miután csatlakozott az Azure-előfizetéshez, akkor a következő feladatok végrehajtására.

Csatlakozás az Azure Portalhoz a Visual Studióból:

1. Lépjen a Server Explorer eszközből **Azure** > **HDInsight** , és válassza ki a fürtöt.

2. Kattintson a jobb gombbal egy HDInsight-fürtöt, és válassza ki **fürt kezelése az Azure Portalon**.

Tegyen fel kérdéseket, és/vagy visszajelzés a Visual Studióból:

1. Lépjen a Server Explorer eszközből **Azure** > **HDInsight**.

2. Kattintson a jobb gombbal **HDInsight** válassza **MSDN-fórum** Kérdés feltevéséhez, vagy **visszajelzés** visszajelzés küldéséhez.

## <a name="explore-linked-resources"></a>Kapcsolt erőforrások vizsgálata
A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve. Bármely tárolóra a jobb gombbal kattintva megtekintheti azok tartalmát.

![Kapcsolt erőforrások listázása a Data Lake Tools for Visual Studio Server Explorerével – képernyőkép](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Kapcsolt erőforrások listázása")

Egy tároló megnyitása után a következő gombokkal tölthet fel, törölhet vagy tölthet le blobokat:

![Blobműveletek a Data Lake Tools for Visual Studio Server Explorerében – képernyőkép](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Blobok feltöltése, törlése és letöltése a Server Explorerben")

## <a name="run-interactive-apache-hive-queries"></a>Az Apache Hive interaktív lekérdezések futtatása
Az [Apache Hive](https://hive.apache.org) egy Hadoop-alapú adattárház-infrastruktúra. A Hive adatösszegzéseket, lekérdezéseket és elemzéseket biztosít. A Data Lake Tools for Visual Studio segítségével Hive-lekérdezéseket futtathat a Visual Studióból. Hive-ról további információkért lásd: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).

Az [interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) az Apache Hive 2.1-en futó [Hive LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkcióit használja. Az interaktív lekérdezés interaktivitást biztosít a nagy tárolt adathalmazokon végzett összetett, adattárház-jellegű lekérdezésekhez. A Hive-lekérdezések futtatása sokkal gyorsabb az interaktív lekérdezésekben, mint a hagyományos Hive-alapú kötegelt feladatokban. További információkért lásd: kötegelt feladatok futtatása az Apache Hive.

> [!NOTE]  
> Interaktív Hive-lekérdezéseket csak akkor futtathat, ha kapcsolódik egy [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürthöz.

A Data Lake Tools for Visual Studio segítségével a Hive-feladatok tartalmát is megtekintheti. A Data Lake Tools for Visual Studio begyűjti és a felszínre hozza bizonyos Hive-feladatok YARN-naplóit.

Lépjen a Server Explorer eszközből **Azure** > **HDInsight** , és válassza ki a fürtöt.  Ez lesz a kiindulási pont, a Kiszolgálókezelőben, kövesse a szakaszokat.

### <a name="view-hivesampletable"></a>Nézet hivesampletable
Az összes HDInsight-fürtök rendelkeznek egy nevű alapértelmezett minta Hive táblát `hivesampletable`.  

Lépjen a fürtről **Hive-adatbázisok** > **alapértelmezett** > **hivesampletable**.

* Megtekintéséhez `hivesampletable` séma:  
Bontsa ki a **hivesampletable**.

* Megtekintéséhez `hivesampletable` adatokat:  
Kattintson a jobb gombbal **hivesampletable**, és válassza ki **View Top 100 sor**.  Ez a következő Hive-lekérdezés Hive ODBC-illesztővel végzett futtatásával egyenlő:

   `SELECT * FROM hivesampletable LIMIT 100`

  Testreszabhatja a sorok számát.

  ![Képernyőkép egy HDInsight Hive Visual Studio-sémalekérdezésről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive-lekérdezés eredményei")

### <a name="create-hive-tables"></a>Hive táblák létrehozása
Hive-tábla létrehozásához használhatja a grafikus felhasználói felületet vagy a Hive-lekérdezéseket. Hive-lekérdezések használatával kapcsolatos információkért lásd: [futtatása Apache Hive-lekérdezések](#run.queries).

1. Lépjen a fürtről **Hive-adatbázisok** > **alapértelmezett**.

2. Kattintson a jobb gombbal **alapértelmezett**, és válassza ki **Create Table**.

3. Konfigurálja a táblát a kívánt módon.  

4. Kattintson a **Create Table** (Tábla létrehozása) parancsra az új Hive-tábla létrehozására szolgáló feladat elküldéséhez.

    ![Képernyőkép a HDInsight Visual Studio Tools Create Table (Tábla létrehozása) ablakáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Hive-tábla létrehozása")

### <a name="run.queries"></a>Hozzon létre, és Hive-lekérdezések futtatása
Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

Hozzon létre, és alkalmi lekérdezések futtatása:

1. Kattintson a jobb gombbal a fürt, ahol szeretné futtatni a lekérdezést, és válassza ki **egy Hive-lekérdezés írása**.  

2. Adja meg a Hive-lekérdezéseket.  

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Például, ha beírja `SELECT * FROM`, IntelliSense listázza az összes javasolt táblanevet. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják.

    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 1. példájáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")

    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 2. példájáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

3. Válassza ki a végrehajtási mód:

    * **Interaktív**  

      Győződjön meg, hogy **interaktív** van kiválasztva, majd **Execute**.

      ![Képernyőkép a lekérdezést, és hajtsa végre](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Győződjön meg, hogy **Batch** van kiválasztva, majd **küldés**.  Ha a speciális küldés lehetőséggel konfigurálja **feladat neve**, **argumentumok**, **további konfigurációs beállítások**, és **állapot Directory**a szkript esetében.

      ![Képernyőkép a lekérdezés és a batch](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Képernyőkép egy HDInsight Hadoop Hive-lekérdezésről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Lekérdezések küldése")

      > [!NOTE]  
      > Nem interaktív lekérdezési fürtökhöz való kötegek küldhet.  Interaktív módot kell használniuk.

Hive-megoldás létrehozása és futtatása:

1. Lépjen a menüsoron **fájl** > **új** > **projekt...** .

2. A bal oldali panelen lépjen **telepített** > **az Azure Data Lake** > **HIVE (HDInsight)**.  

3. A középső panelen válassza a **Hive Application** (Hive-alkalmazás) elemet. Adja meg a tulajdonságokat, majd kattintson az **OK** gombra.

    ![Képernyőkép a HDInsight Visual Studio Toolsban az új Hive-projekt létrehozásáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Hive-alkalmazások létrehozása a Visual Studióból")

4. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **Script.hql** fájlra a szkript megnyitásához.

### <a name="view-job-summary-and-output"></a>Feladat összegzésének megtekintése és kimenet

A feladat összegzésében némiképp eltérő **Batch** és **interaktív** mód.

![Feladat összegzése](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive-feladat összegzése")

Használja a **frissítése** gombra kattintva frissítheti az állapotot, amíg a feladat állapota **befejezett**.  

* A feladat részleteit a **Batch** módot, válassza ki, tekintse meg az alul lévő hivatkozásokra **Job Query**, **Feladatkimenet**, **Job log**, vagy **a yarn-naplók**.

* A feladat részleteit a **interaktív** mód, lásd: lapok **kimeneti** és **hiveserver2-n keresztül kimeneti**.

  ![feladat részletei](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive-feladat részletei")

### <a name="view-job-graph"></a>A feladatgrafikon megtekintése

Feladatok grafikonjai jelenleg csak használata a Tez végrehajtómotor Hive-feladatok láthatók.  További információ a Tez engedélyezéséről: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).  Lásd még a [Mapreduce helyett használja az Apache Tez](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

A feladatgrafikon csúcspontjaira duplán kattintva láthatja a csúcsponton belüli összes operátort. Egy adott operátorra mutatva megtekintheti az operátor részleteit.

A feladatgrafikon nem jelenhet meg akkor is, ha a Tez végrehajtómotor van megadva, ha nincs Tez alkalmazás elindításakor.  Ez akkor fordulhat elő, mert a feladat nem tartalmaz DML-utasítást, vagy a DML-utasítást a Tez alkalmazás megnyitása nélkül adhat vissza. Ha például `SELECT * FROM table1` nem indulnak el a Tez-alkalmazás.

![A feladatgrafikon](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Hive-feladat összegzése")


### <a name="task-execution-detail"></a>Feladat-végrehajtás részletei

A feladatgrafikon kiválaszthatja **feladat-végrehajtás részletei** beolvasni a strukturált és vizualizált információkhoz a Hive-feladatokról. További feladatrészleteket is lekérhet. Teljesítményproblémák esetén a nézet segítségével részletesebb információkat szerezhet a problémáról. Például információhoz juthat arról, hogy az egyes feladatok hogyan működnek, és részletes információt kaphat mindegyik feladatról (adatírás/-olvasás, ütemezés/kezdő/záró idő stb.). A megjelenített információk alapján pedig finomhangolhatja a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![Képernyőkép a Data Lake Visual Studio Tools feladat-végrehajtási nézetének ablakáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Feladat-végrehajtási nézet")


### <a name="view-hive-jobs"></a>Hive-feladatok megtekintése
Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkiemenetét, feladatnaplóit és Yarn naplóit.

Az eszköz legújabb kiadásával láthatja, mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a Yarn-naplókat. A YARN-naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. További információ arról, hogy a HDInsight hogyan gyűjti be a YARN-naplókat: [HDInsight alkalmazásnaplók elérése programozási környezetből](../hdinsight-hadoop-access-yarn-app-logs.md).

Hive-feladatok megtekintése:

1. Kattintson a jobb gombbal egy HDInsight-fürtöt, és válassza ki **feladatok megtekintése**. Ekkor megjelenik azon Hive-feladatok listája, amelyek a fürtön futottak.  

2. Válasszon ki egy feladatot. A **Hive Job Summary** (Hive-feladat összegzése) ablakban válassza a következők egyikét:
    - **Job Query** (Feladat lekérdezése)
    - **Job Output** (Feladat kimenete)
    - **Job Log** (Feladatnapló)  
    - **Yarn log** (Yarn-napló)

    ![Képernyőkép a HDInsight Visual Studio Tools Hive-feladatok megtekintését biztosító ablakáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Hive-feladatok megtekintése")


## <a name="run-apache-pig-scripts"></a>Az Apache Pig-parancsfájlok futtatása

1. Lépjen a menüsoron **fájl** > **új** > **projekt...** .

2. A bal oldali panelen lépjen **telepített** > **az Azure Data Lake** > **Pig (HDInsight)**.  

3. A középső ablaktáblán válassza ki a **Pig alkalmazás**. Adja meg a tulajdonságokat, majd kattintson az **OK** gombra.

4. A **Megoldáskezelőben**, kattintson duplán a **Script.pig** a szkript megnyitásához.

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák
* Kijavítottunk egy hibát, amelynek következtében a null értékekkel induló eredmények nem jelentek meg. Ha elakad ennél a hibánál, lépjen kapcsolatba a támogatási csapattal.
* A Visual Studio által létrehozott HQL-szkript kódolása a felhasználó helyi régióbeállításaitól függ. A szkript nem fut megfelelően, ha a felhasználó bináris fájlként tölti fel a szkriptet a fürtbe.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studióból a Data Lake Tools for Visual Studio csomaggal. Emellett azt is megtanulta, hogyan futtathat Hive-lekérdezéseket. További információval a következő cikkek szolgálnak:

* [A Data Lake tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](apache-hadoop-use-hive-visual-studio.md)
* [A Hadoop Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A HDInsight Apache Hadoop használatának első lépései](apache-hadoop-linux-tutorial-get-started.md)
* [A HDInsight Apache Hadoop-feladatok elküldése](submit-apache-hadoop-jobs-programmatically.md)
* [A HDInsight Apache Hadoop-keretrendszerrel Twitter-adatok elemzése](../hdinsight-analyze-twitter-data.md)

