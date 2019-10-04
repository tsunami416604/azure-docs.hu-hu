---
title: A Visual studióhoz készült Apache Hadoop és Data Lake Tools – Azure HDInsight
description: Megtudhatja, hogyan telepítheti és használhatja a Visual studióhoz készült Data Lake-eszközöket az Azure HDInsight-beli Apache Hadoop-fürtökhöz való kapcsolódáshoz, majd a kaptár-lekérdezések futtatásához.
keywords: hadoop-eszközök,hive-lekérdezés,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 44a076ee6979e207ac3992f76d3b89cc188d53b8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076295"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio használata az Azure HDInsight való kapcsolódáshoz és Apache Hive lekérdezések futtatásához

Ismerje meg, hogyan használhatók a [Visual Studio Microsoft Azure Data Lake és stream Analytics eszközei](https://www.microsoft.com/download/details.aspx?id=49504) (más néven Data Lake eszközök) az [Azure-HDInsight](../hdinsight-hadoop-introduction.md) Apache Hadoop-fürtökhöz való kapcsolódáshoz és a kaptár-lekérdezések elküldéséhez.  

További információ a HDInsight használatáról: [A HDInsight bemutatása](../hdinsight-hadoop-introduction.md) és [HDInsight – első lépések](apache-hadoop-linux-tutorial-get-started.md).  

A Apache Storm-fürtökhöz való csatlakozással kapcsolatos további információkért lásd: [a HDInsight-hez készült topológiák fejlesztése C# a Visual studióval való Apache Stormhoz](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

A Data Lake Tools for Visual Studio mind az Azure Data Lake Analytics, mind a HDInsight eléréséhez használható. A Data Lake Tools eszközökkel kapcsolatos információkért lásd: [U-SQL-szkriptek fejlesztése Data Lake Tools for Visual Studio használatával](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez és a Visual studióhoz készült Data Lake Tools használatához a következő elemek szükségesek:

* Egy Azure-beli HDInsight-fürt. HDInsight-fürt létrehozásához tekintse [meg az Apache Hadoop Azure HDInsight való használatának](apache-hadoop-linux-tutorial-get-started.md)első lépéseit ismertető témakört. Az interaktív Apache Hive lekérdezések futtatásához [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürtre van szükség.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 vagy újabb).  A [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) ingyenes.  Lásd még: a [Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)telepítése. A Visual Studio 2019 enyhe felületi változatokkal rendelkezik.

  > [!IMPORTANT]  
  > A Visual Studio 2013 már nem támogatja a Data Lake eszközöket.

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 vagy Visual Studio 2019  
  A telepítés során győződjön meg róla, hogy legalább az **Azure-fejlesztést** , az **adattárolást és-feldolgozást**is magában foglalja.  

  Meglévő telepítések esetén a menüsávban navigáljon > az eszközök**lekérése eszközök és szolgáltatások...** elemre a Visual Studio telepítőjének megnyitásához.  Ezután válassza ki az Azure- **fejlesztés** vagy **az adattárolás és-feldolgozás minimális számítási feladatait**.

  ![A Visual Studio telepítő képernyőképe](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 és 2015  
  [Data Lake eszközök letöltése](https://www.microsoft.com/download/details.aspx?id=49504). Válassza ki a Data Lake Tools azon verzióját, amely megfelel a Visual Studio verziójának.  

> [!NOTE]  
> A Data Lake Tools for Visual Studio jelenleg csak angol nyelven érhető el.

## <a name="update-data-lake-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake Tools frissítése  

1. Nyissa meg a Visual Studiót.

2. A menüsávban navigáljon az **eszközök** > **bővítmények és frissítések...** elemre.

3. A **bővítmények és frissítések** ablakban bontsa ki a **frissítések** elemet a bal oldalon.

4. Ha van elérhető frissítés, a főablakban megjelennek a **Azure Data Lake és a stream analitikai eszközei** .  Válassza a **frissítés**lehetőséget.

> [!NOTE]  
> Csak a Data Lake Tools 2.3.0.0-s és újabb verziói támogatják az interaktív lekérdezési fürtökhöz való csatlakozást és az interaktív Hive-lekérdezések futtatását.

## <a name="connect-to-azure-subscriptions"></a>Csatlakozás Azure-előfizetésekhez
A Data Lake Tools for Visual Studio használatával csatlakozhat a HDInsight-fürtökhöz, alapvető felügyeleti műveleteket végezhet, és Hive-lekérdezéseket futtathat.

> [!NOTE]  
> Az általános Hadoop fürthöz való csatlakozással kapcsolatos információkat a [Hive-lekérdezések írása és elküldése a Visual Studio eszközzel](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) témakörben találja.

Csatlakozás az Azure-előfizetéshez:

1. Nyissa meg a Visual Studiót.

2. A menüsávban navigáljon a**Server Explorer** **megtekintése** > elemre.

3. A Server Explorerben kattintson a jobb gombbal az **Azure**elemre, válassza a **Kapcsolódás Microsoft Azure előfizetéshez**... lehetőséget, és fejezze be a bejelentkezési folyamatot.

4. A Server Explorerben megjelenik a meglévő HDInsight-fürtök listája. Ha nincsenek fürtjei, létrehozhat egyet az Azure Portal, az Azure PowerShell vagy a HDInsight SDK használatával. További információ: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

   A ![Visual Studio-fürtök Data Lake eszközei a Server Explorerben] A (./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Visual Studio-fürtök Data Lake eszközei a Server Explorerben")

5. Bontson ki egy HDInsight-fürtöt. A **kaptár-adatbázisok**, az alapértelmezett Storage-fiók, a társított Storage-fiókok és a **Hadoop szolgáltatás naplója** jelenik meg. Ennél jobban is kibonthatja az elemeket.

Az Azure-előfizetéshez való csatlakozás után a következő feladatokat végezheti el.

Csatlakozás az Azure Portalhoz a Visual Studióból:

1. A Server Explorerben navigáljon az **Azure** > **HDInsight** , és válassza ki a fürtöt.

2. Kattintson a jobb gombbal egy HDInsight-fürtre, majd válassza **a fürt kezelése a Azure Portal [sic]** elemet.

Tegye fel kérdéseit és/vagy küldjön visszajelzést a Visual Studiótól:

1. A Server Explorerben navigáljon az **Azure** > **HDInsight**.

2. Kattintson a jobb gombbal a **HDInsight** elemre, és válassza az **MSDN-fórum** lehetőséget a kérdések feltevéséhez, vagy **küldjön visszajelzést** a visszajelzésről.

## <a name="link-a-cluster"></a>Fürt csatolása
A fürt összekapcsolásához kattintson a jobb gombbal a **HDInsight** elemre, majd válassza **a HDInsight-fürt csatolása**lehetőséget. Adja meg a **hálózati URL-címet**, a **felhasználónevet** és a **jelszót** **, majd kattintson**a **tovább** gombra, a fürtnek a HDInsight csomópont alatt kell szerepelnie.

![Képernyőkép a Visual Studio hivatkozás-fürthöz készült Data Lake eszközökről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Kattintson a jobb gombbal a csatolt fürtre, válassza a **Szerkesztés**lehetőséget, a felhasználó frissítheti a fürt adatait. HDInsight-fürt hozzáadásával most már csak a kaptárt támogatja.

![Képernyőkép a Visual Studio link-fürt frissítésének Data Lake eszközeiről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Kapcsolt erőforrások vizsgálata
A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve. Bármely tárolóra a jobb gombbal kattintva megtekintheti azok tartalmát.

A ![Visual Studio kapcsolódó erőforrásainak Data Lake eszközei a Server Explorerben](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Csatolt erőforrások listázása")

Egy tároló megnyitása után a következő gombokkal tölthet fel, törölhet vagy tölthet le blobokat:

![A Visual Studio blob-műveletek Data Lake eszközei a Server Explorerben](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Blobok feltöltése, törlése és letöltése a Server Explorerben")

## <a name="run-interactive-apache-hive-queries"></a>Interaktív Apache Hive-lekérdezések futtatása
Az [Apache Hive](https://hive.apache.org) egy Hadoop-alapú adattárház-infrastruktúra. A Hive adatösszegzéseket, lekérdezéseket és elemzéseket biztosít. A Data Lake Tools for Visual Studio segítségével Hive-lekérdezéseket futtathat a Visual Studióból. További információ a Kaptárról: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).

Az [interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) az Apache Hive 2.1-en futó [Hive LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkcióit használja. Az interaktív lekérdezés interaktivitást biztosít a nagy tárolt adathalmazokon végzett összetett, adattárház-jellegű lekérdezésekhez. A Hive-lekérdezések futtatása sokkal gyorsabb az interaktív lekérdezésekben, mint a hagyományos Hive-alapú kötegelt feladatokban. 

> [!NOTE]  
> Interaktív Hive-lekérdezéseket csak akkor futtathat, ha kapcsolódik egy [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürthöz.

A Data Lake Tools for Visual Studio segítségével a Hive-feladatok tartalmát is megtekintheti. A Data Lake Tools for Visual Studio begyűjti és a felszínre hozza bizonyos Hive-feladatok YARN-naplóit.

A Server Explorerben navigáljon az **Azure** > **HDInsight** , és válassza ki a fürtöt.  Ez lesz a Server Explorer kiindulópontja a következő szakaszoknak.

### <a name="view-hivesampletable"></a>Hivesampletable megtekintése
Az összes HDInsight-fürthöz egy alapértelmezett minta nevű `hivesampletable`struktúra-tábla tartozik.  

A fürtből navigáljon a **kaptár adatbázisok** > **alapértelmezett** > **hivesampletable**.

* Séma megtekintése `hivesampletable` :  
Bontsa ki a **hivesampletable**.

* `hivesampletable` Az adatmegjelenítéshez:  
Kattintson a jobb gombbal a **hivesampletable**elemre, majd válassza a **felső 100-sorok megtekintése**lehetőséget.  Ez a következő Hive-lekérdezés Hive ODBC-illesztővel végzett futtatásával egyenlő:

   `SELECT * FROM hivesampletable LIMIT 100`

  Testreszabhatja a sorok számát.

  ![Képernyőkép egy HDInsight Hive Visual Studio-sémalekérdezésről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive-lekérdezés eredményei")

### <a name="create-hive-tables"></a>Hive táblák létrehozása
Hive-tábla létrehozásához használhatja a grafikus felhasználói felületet vagy a Hive-lekérdezéseket. További információ a kaptár-lekérdezések használatáról: [Apache Hive lekérdezések futtatása](#run.queries).

1. A fürtből navigáljon a **kaptár-adatbázisok** > **alapértelmezett értékére**.

2. Kattintson a jobb gombbal az **alapértelmezett**elemre, majd válassza a **tábla létrehozása**lehetőséget.

3. Konfigurálja a táblázatot igény szerint.  

4. Kattintson a **Create Table** (Tábla létrehozása) parancsra az új Hive-tábla létrehozására szolgáló feladat elküldéséhez.

    ![Képernyőkép a HDInsight Visual Studio Tools Create Table (Tábla létrehozása) ablakáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Hive-tábla létrehozása")

### <a name="run.queries"></a>Struktúra-lekérdezések létrehozása és futtatása
Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

Alkalmi lekérdezések létrehozása és futtatása:

1. Kattintson a jobb gombbal arra a fürtre, amelyen futtatni szeretné a lekérdezést, majd válassza a **kaptár-lekérdezés írása**lehetőséget.  

2. Adja meg a következő kaptár-lekérdezést:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például a (z) `SELECT * FROM`értéket adja meg, az IntelliSense felsorolja az összes javasolt táblanév nevét. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják.

    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 1. példájáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 2. példájáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

3. Válassza ki a végrehajtási módot:

    * **Interaktív**  

      Jelölje be az **interaktív** jelölőnégyzetet, majd válassza a **végrehajtás**lehetőséget.

      ![A lekérdezés és a végrehajtás képernyőképe](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Győződjön meg arról, hogy a **Batch** be van jelölve, majd válassza a **Küldés**lehetőséget.  Ha a speciális küldés lehetőséget választja, konfigurálja a **feladatok nevét**, **argumentumait**, a **további konfigurációkat**és az **állapot könyvtárat** a parancsfájlhoz.

      ![A Visual Studio lekérdezési és batch-beállításai](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Képernyőkép egy HDInsight Hadoop Hive-lekérdezésről](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Lekérdezések küldése")

      > [!NOTE]  
      > Nem küldhet kötegeket interaktív lekérdezési fürtökbe.  Interaktív módot kell használnia.

Hive-megoldás létrehozása és futtatása:

1. A menüsávban navigáljon a **fájl** > **új** > **projekt...** elemre.

2. A bal oldali ablaktáblán navigáljon a **telepített** > **Azure Data Lake** > **struktúra (HDInsight)** elemre.  

3. A középső panelen válassza a **Hive Application** (Hive-alkalmazás) elemet. Adja meg a tulajdonságokat, majd kattintson az **OK** gombra.

    ![Képernyőkép a HDInsight Visual Studio Toolsban az új Hive-projekt létrehozásáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Hive-alkalmazások létrehozása a Visual Studióból")

4. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **Script.hql** fájlra a szkript megnyitásához.

### <a name="view-job-summary-and-output"></a>Feladatok összegzésének és kimenetének megtekintése

A feladatok összegzése némileg eltér a **Batch** és az **interaktív** mód között.

![Apache Hive feladatok összegzése lap megjelenítése](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Struktúra-feladatok összegzése")

A **frissítés** gomb használatával frissítse az állapotot, amíg a feladatok állapota **Befejezettre**nem változik.  

* A feladatok részleteinek **Batch** -módból való megjelenítéséhez válassza a lenti hivatkozásokat **a feladatok lekérdezése**, a **feladatok kimenete**, a **Projektnapló**vagy a **fonal-napló**megtekintéséhez.

* Az **interaktív** módból származó feladatok részleteiért lásd: lapok **kimeneti** és **HiveServer2 kimenete**.

  ![Visual Studio Apache Hive feladatok részletei](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Kaptár-feladatok részletei")

### <a name="view-job-graph"></a>Feladatok gráfjának megtekintése

Jelenleg a feladatok gráfok csak olyan kaptár-feladatok esetében jelennek meg, amelyek a TEZ-t használják végrehajtó motorként.  További információ a TEZ engedélyezéséről: [Apache Hive használata a HDInsight-ben](hdinsight-use-hive.md).  Lásd még: [az Apache TEZ használata a Térkép csökkentése helyett](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

A feladatgrafikon csúcspontjaira duplán kattintva láthatja a csúcsponton belüli összes operátort. Egy adott operátorra mutatva megtekintheti az operátor részleteit.

A feladatütemezés akkor sem jelenhet meg, ha a TEZ meg van adva végrehajtó motorként, ha nincs TEZ-alkalmazás elindítva.  Ez azért fordulhat elő, mert a feladatokban nem szerepelnek DML-utasítások, vagy a DML-utasítások TEZ-alkalmazás elindítása nélkül is visszatérhetnek. Például `SELECT * FROM table1` nem fogja elindítani a TEZ alkalmazást.

![Visual Studio Apache Hive feladatok gráfja](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Struktúra-feladatok összegzése")

### <a name="task-execution-detail"></a>Feladat-végrehajtás részletei

A feladat-gráfból kiválaszthatja a **feladat-végrehajtási részletességet** a struktúra-feladatok strukturált és vizualizációs információinak beszerzéséhez. További feladatrészleteket is lekérhet. Teljesítményproblémák esetén a nézet segítségével részletesebb információkat szerezhet a problémáról. Például információhoz juthat arról, hogy az egyes feladatok hogyan működnek, és részletes információt kaphat mindegyik feladatról (adatírás/-olvasás, ütemezés/kezdő/záró idő stb.). A megjelenített információk alapján pedig finomhangolhatja a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![Data Lake Visual Studio-eszközök feladat-végrehajtási nézetének ablaka](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Feladat-végrehajtási nézet")


### <a name="view-hive-jobs"></a>Hive-feladatok megtekintése
Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkimenetét, feladatnaplóit és Yarn naplóit.

Az eszköz legújabb kiadásával láthatja, mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a Yarn-naplókat. A YARN-naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. További információ arról, hogy a HDInsight hogyan gyűjti be a YARN-naplókat: [HDInsight alkalmazásnaplók szoftveres elérése](../hdinsight-hadoop-access-yarn-app-logs.md).

Hive-feladatok megtekintése:

1. Kattintson a jobb gombbal egy HDInsight-fürtre, majd válassza a **feladatok megtekintése**lehetőséget. Ekkor megjelenik azon Hive-feladatok listája, amelyek a fürtön futottak.  

2. Válasszon ki egy feladatot. A **Hive Job Summary** (Hive-feladat összegzése) ablakban válassza a következők egyikét:
    - **Job Query** (Feladat lekérdezése)
    - **Job Output** (Feladat kimenete)
    - **Job Log** (Feladatnapló)  
    - **Yarn log** (Yarn-napló)

    ![Képernyőkép a HDInsight Visual Studio Tools Hive-feladatok megtekintését biztosító ablakáról](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Hive-feladatok megtekintése")


## <a name="run-apache-pig-scripts"></a>Apache Pig-parancsfájlok futtatása

1. A menüsávban navigáljon a **fájl** > **új** > **projekt...** elemre.

2. A bal oldali panelen navigáljon a **telepített** > **Azure Data Lake** > **Pig (HDInsight)** elemre.  

3. A középső ablaktáblán válassza a **Pig-alkalmazás**lehetőséget. Adja meg a tulajdonságokat, majd kattintson az **OK** gombra.

4. A **megoldáskezelő**kattintson duplán a **script. Pig** elemre a parancsfájl megnyitásához.

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák
* Kijavítottunk egy hibát, amelynek következtében a null értékekkel induló eredmények nem jelentek meg. Ha elakad ennél a hibánál, lépjen kapcsolatba a támogatási csapattal.
* A Visual Studio által létrehozott HQL-szkript kódolása a felhasználó helyi régióbeállításaitól függ. A szkript nem fut megfelelően, ha a felhasználó bináris fájlként tölti fel a szkriptet a fürtbe.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studióból a Data Lake Tools for Visual Studio csomaggal. Emellett azt is megtanulta, hogyan futtathat Hive-lekérdezéseket. További információval a következő cikkek szolgálnak:

* [Apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studióval](apache-hadoop-use-hive-visual-studio.md)
* [A Hadoop Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Apache Hadoop használatának első lépései a HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop feladatok elküldése a HDInsight-ben](submit-apache-hadoop-jobs-programmatically.md)
* [Twitter-adatApache Hadoopek elemzése a HDInsight-ben](../hdinsight-analyze-twitter-data.md)

