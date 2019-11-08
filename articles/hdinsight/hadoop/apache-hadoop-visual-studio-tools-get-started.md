---
title: Apache Hadoop & Visual Studio Data Lake-eszközök – Azure HDInsight
description: Megtudhatja, hogyan telepítheti és használhatja a Visual studióhoz készült Data Lake-eszközöket az Azure HDInsight-beli Apache Hadoop-fürtökhöz való kapcsolódáshoz, majd a kaptár-lekérdezések futtatásához.
keywords: hadoop-eszközök,hive-lekérdezés,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824964"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio használata az Azure HDInsight való kapcsolódáshoz és Apache Hive lekérdezések futtatásához

Ismerje meg, hogyan használhatók a Visual Studio Microsoft Azure Data Lake és Stream Analytics eszközei (más néven Data Lake eszközök) az [Azure-HDInsight Apache Hadoop-fürtökhöz](apache-hadoop-introduction.md) való kapcsolódáshoz és a kaptár-lekérdezések elküldéséhez.  

További információ a HDInsight használatáról: Ismerkedés [a HDInsight](apache-hadoop-linux-tutorial-get-started.md)szolgáltatással.  

A Apache Storm-fürthöz való csatlakozással kapcsolatos további információkért tekintse meg [a Apache Storm-topológiák C# fejlesztése a Data Lake Tools for Visual Studio használatával](../storm/apache-storm-develop-csharp-visual-studio-topology.md)című témakört.

A Data Lake Tools for Visual Studio mind az Azure Data Lake Analytics, mind a HDInsight eléréséhez használható. A Data Lake Tools eszközökkel kapcsolatos információkért lásd: [U-SQL-szkriptek fejlesztése Data Lake Tools for Visual Studio használatával](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez és a Visual studióhoz készült Data Lake Tools használatához a következő elemek szükségesek:

* Egy Azure-beli HDInsight-fürt. HDInsight-fürt létrehozásához tekintse [meg az Apache Hadoop Azure HDInsight való használatának](apache-hadoop-linux-tutorial-get-started.md)első lépéseit ismertető témakört. Az interaktív Apache Hive lekérdezések futtatásához [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürtre van szükség.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). A [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) ingyenes. Az itt látható utasítások a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)-re vonatkoznak.

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése  

A Visual Studio verziójának Data Lake eszközeinek telepítéséhez kövesse a megfelelő utasításokat:

- A Visual Studio 2017 vagy a Visual Studio 2019 esetén:

    A Visual Studio telepítése során győződjön meg róla, hogy tartalmazza az **Azure-fejlesztési** számítási feladatot, illetve az **adattárolási és-feldolgozási** munkaterhelést.  

    A meglévő Visual Studio-telepítések esetében lépjen az IDE menüsorba, és válassza az **eszközök** > eszközök **és szolgáltatások beolvasása** lehetőséget a Visual Studio telepítőjének megnyitásához. A **munkaterhelések** lapon válasszon ki legalább az **Azure-fejlesztési** számítási feladatot (a **Web & felhő**alatt) vagy az **adattárolási és-feldolgozási** munkaterhelést ( **más eszközkészletek**alatt).

  ![Számítási feladatok kiválasztása, Visual Studio telepítő](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- A Visual Studio 2015 esetén:

    [Data Lake eszközök letöltése](https://www.microsoft.com/download/details.aspx?id=49504). Válassza ki a Data Lake Tools azon verzióját, amely megfelel a Visual Studio verziójának.

## <a name="update-data-lake-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake Tools frissítése  

Ezt követően győződjön meg arról, hogy a legújabb verzióra frissíti Data Lake eszközöket.

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza a **Folytatás kód nélkül**lehetőséget.

3. A Visual Studio IDE menüsorában válassza a **bővítmények** > a **Bővítmények kezelése**lehetőséget.

4. A **Bővítmények kezelése** párbeszédpanelen bontsa ki a **frissítések** csomópontot.

5. Ha az elérhető frissítések listája **Azure Data Lake és stream analitikai eszközöket**tartalmaz, jelölje ki. Ezután válassza ki a **frissítés** gombot. Miután megjelenik a **letöltés és telepítés** párbeszédpanel, és eltűnik, a Visual Studio hozzáadja a **Azure Data Lake és a stream Analytics-eszközök** bővítményt a frissítési ütemtervhez.

6. Zárjunk be minden Visual Studio-ablakot. Megjelenik a **VSIX-telepítő** párbeszédpanel.

7. Válassza a **licenc** lehetőséget a licencfeltételek beolvasásához, majd a **Bezárás** gombra kattintva térjen vissza a **VSIX-telepítő** párbeszédpanelre.

8. Válassza a **módosítás**lehetőséget. A bővítmény frissítésének telepítése megkezdődik. Egy idő után a párbeszédpanel úgy módosul, hogy megtörtént a módosítások végrehajtása. A telepítés befejezéséhez kattintson a **Bezárás**gombra, majd indítsa újra a Visual studiót.

> [!NOTE]  
> Csak a Data Lake Tools 2.3.0.0-s és újabb verziói támogatják az interaktív lekérdezési fürtökhöz való csatlakozást és az interaktív Hive-lekérdezések futtatását.

## <a name="connect-to-azure-subscriptions"></a>Csatlakozás Azure-előfizetésekhez

A Visual studióhoz készült Data Lake Tools segítségével csatlakozhat a HDInsight-fürtökhöz, elvégezheti az alapszintű felügyeleti műveleteket, és futtathat kaptár-lekérdezéseket.

> [!NOTE]  
> Az általános Hadoop-fürtökhöz való csatlakozással kapcsolatos információkért lásd: [a kaptár-lekérdezések írása és beküldése a Visual Studióval](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

Csatlakozás az Azure-előfizetéshez:

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza a **Folytatás kód nélkül**lehetőséget.

3. Az IDE menüsorban válassza a > **Server Explorer** **megtekintése** lehetőséget.

4. A **Server Explorerben**kattintson a jobb gombbal az **Azure**elemre, válassza a **Kapcsolódás Microsoft Azure előfizetéshez**lehetőséget, és fejezze be a hitelesítési folyamatot. A **Server Explorerben**bontsa ki az **Azure** > **HDInsight** elemet a meglévő HDInsight-fürtök listájának megtekintéséhez.

5. Ha nem rendelkezik fürtökkel, hozzon létre egyet a Azure Portal, Azure PowerShell vagy a HDInsight SDK használatával. További információ: [fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md).

   ![HDInsight, Server Explorer, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Bontson ki egy HDInsight-fürtöt. A fürt a kaptár- **adatbázisok**csomópontjait, egy alapértelmezett Storage-fiókot, minden további társított Storage-fiókot és **Hadoop-szolgáltatási naplót**tartalmaz. Ennél jobban is kibonthatja az elemeket.

Miután csatlakozott az Azure-előfizetéshez, végrehajthatja a következő feladatokat.

### <a name="connect-to-azure-from-visual-studio"></a>Kapcsolódás az Azure-hoz a Visual studióból

Csatlakozás az Azure Portalhoz a Visual Studióból:

1. A **Server Explorerben**bontsa ki az **Azure** > **HDInsight** elemet, és válassza ki a fürtöt.

2. Kattintson a jobb gombbal egy HDInsight-fürtre, és válassza **a fürt kezelése Azure Portalban**lehetőséget.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Kérdések és visszajelzések a Visual studióból

Tegye fel kérdéseit és/vagy küldjön visszajelzést a Visual Studiótól:

1. A Server Explorerben válassza az **Azure** > **HDInsight**lehetőséget.

2. Kattintson a jobb gombbal a **HDInsight** elemre, és válassza az **MSDN-fórum** lehetőséget a kérdések feltevéséhez, vagy **küldjön visszajelzést** a visszajelzésről.

## <a name="link-to-or-edit-a-cluster"></a>Fürt csatolása vagy szerkesztése

> [!NOTE]
> Jelenleg az egyetlen olyan HDInsight-fürt, amelyhez hivatkozás van, egy struktúra típusú.

HDInsight-fürt összekapcsolása:

1. Kattintson a jobb gombbal a **HDInsight**elemre, majd válassza a **HDInsight-fürt csatolása** lehetőséget a **HDInsight-fürt csatolása** párbeszédpanel megjelenítéséhez.

2. Adja meg a **kapcsolati URL-címet** a *https-\://\<-fürt&nbsp;neve: >. azurehdinsight. net*. A **fürt neve** automatikusan betöltődik az URL-cím fürt neve részével, amikor egy másik mezőre lép. Ezután adjon meg egy **felhasználónevet** és egy **jelszót**, majd kattintson a **Tovább gombra**.

    ![Fürt, HDInsight, Visual Studio csatolása](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Válassza a **Finish** (Befejezés) elemet. Ha a fürt csatolása sikeres, a fürt megjelenik a **HDInsight** csomópont alatt.

Egy csatolt fürt frissítéséhez kattintson a jobb gombbal a fürtre, és válassza a **Szerkesztés**lehetőséget. Ezután frissítheti a fürt adatait.

![Csatolt fürt szerkesztése, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Kapcsolt erőforrások vizsgálata
A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve.

![A Visual Studio kapcsolódó erőforrásainak Data Lake eszközei a Server Explorerben](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Kattintson a jobb gombbal a tárolóra, és válassza a **tároló megtekintése** lehetőséget a tároló tartalmának megtekintéséhez. Egy tároló megnyitása után az eszköztár gombjaival **frissítheti** a tartalmat, **feltöltheti a blobokat**, **törölheti a kiválasztott**blobokat, **megnyithatja a blobot**, és letöltheti (**mentheti a Mentés másként**) kiválasztott blobokat.

![Tárolók listája és blob-műveletek, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Interaktív Apache Hive-lekérdezések futtatása
Az [Apache Hive](https://hive.apache.org) egy Hadoop-alapú adattárház-infrastruktúra. A Hive adatösszegzéseket, lekérdezéseket és elemzéseket biztosít. A Data Lake Tools for Visual Studio segítségével Hive-lekérdezéseket futtathat a Visual Studióból. További információ a Kaptárról: [Mi az Apache Hive és a HiveQL az Azure HDInsight?](hdinsight-use-hive.md).

[Az Azure HDInsight-beli interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) a [kaptárt használja a LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) Apache Hive 2,1-es verziójában. Az interaktív lekérdezés összetett, adattárház-stílusú lekérdezéseket biztosít a nagyméretű, tárolt adatkészleteken. A kaptár-lekérdezések interaktív lekérdezésen való futtatása sokkal gyorsabb, mint a hagyományos kaptár batch-feladatok. 

> [!NOTE]  
> Interaktív Hive-lekérdezéseket csak akkor futtathat, ha kapcsolódik egy [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürthöz.

A Data Lake Tools for Visual Studio segítségével a Hive-feladatok tartalmát is megtekintheti. A Data Lake Tools for Visual Studio begyűjti és a felszínre hozza bizonyos Hive-feladatok YARN-naplóit.

A **Server Explorerben**válassza az **Azure** > **HDInsight** elemet, és válassza ki a fürtöt.  Ez a csomópont a **Kiszolgálókezelő** kiindulási pontja a követendő szakaszokhoz.

### <a name="view-hivesampletable"></a>Hivesampletable megtekintése

Az összes HDInsight-fürthöz tartozik egy `hivesampletable`nevű alapértelmezett méhkas-táblázat.  

A fürtből válassza a **kaptár adatbázisok** > **alapértelmezett** > **hivesampletable**lehetőséget.

- A `hivesampletable` séma megtekintése:

    Bontsa ki a **hivesampletable**. Megjelenik a `hivesampletable` oszlopok nevei és adattípusai.

- A `hivesampletable`-adatértékek megtekintése:

    Kattintson a jobb gombbal a **hivesampletable**elemre, majd válassza a **felső 100-sorok megtekintése**lehetőséget. A 100 találatok listája a **kaptár tábla: hivesampletable** ablakban jelenik meg. Ez a művelet egyenértékű a következő kaptár-lekérdezés futtatásával a kaptár ODBC-illesztő használatával:

    `SELECT * FROM hivesampletable LIMIT 100`

    A **sorok számának**módosításával testreszabhatja a sorok számát. a legördülő listából 50, 100, 200 vagy 1000 sort választhat.

### <a name="create-hive-tables"></a>Hive táblák létrehozása
Hive-tábla létrehozásához használhatja a grafikus felhasználói felületet vagy a Hive-lekérdezéseket. A kaptár-lekérdezések használatáról a [kaptár-lekérdezések létrehozása és futtatása](#create-and-run-hive-queries)című témakörben olvashat bővebben.

1. A fürtből válassza a **kaptár adatbázisok** > **alapértelmezett**lehetőséget.

2. Kattintson a jobb gombbal az **alapértelmezett**elemre, majd válassza a **tábla létrehozása**lehetőséget.

3. Konfigurálja a táblát.

4. Kattintson a **create Table (tábla létrehozása** ) gombra a feladatok elküldéséhez, amely létrehozza az új struktúra-táblázatot.

    ![Tábla létrehozása, struktúra, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Struktúra-lekérdezések létrehozása és futtatása
Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

#### <a name="create-an-ad-hoc-query"></a>Alkalmi lekérdezés létrehozása

Ad-hoc lekérdezés létrehozása és futtatása:

1. Kattintson a jobb gombbal arra a fürtre, amelyen futtatni szeretné a lekérdezést, majd válassza a **kaptár-lekérdezés írása**lehetőséget.  

2. Adja meg a kaptár-lekérdezést.

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például beírja a `SELECT * FROM`, az IntelliSense a javasolt táblanév összes nevét listázza. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják.

    ![IntelliSense 1. példa, kaptár ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 2. példa, kaptár ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

    Íme egy példaként használható lekérdezés:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Válassza ki a végrehajtási módot:

    * **Interaktív**  

        Az első legördülő listában válassza az **interaktív**lehetőséget, majd válassza a **végrehajtás**lehetőséget.

        ![Interaktív mód, kaptár ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Az első legördülő listában válassza a **Batch**lehetőséget, majd kattintson a **Küldés** elemre (vagy válassza a **beküldés** melletti legördülő ikont, majd válassza a **speciális**lehetőséget).

        ![Batch mód, kaptár ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Ha a speciális küldés lehetőséget választja, megjelenik a **parancsfájl elküldése** párbeszédpanel. Adja meg a **feladatok nevét**, **argumentumait**, a **további konfigurációkat**és az **állapot könyvtárat** a parancsfájlhoz.

        ![Parancsfájl elküldése párbeszédpanel, kaptár alkalmi lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > A batchs szolgáltatás nem küldhető el interaktív lekérdezési fürtökbe.  Interaktív módot kell használnia.

#### <a name="create-a-hive-application"></a>Hive alkalmazás létrehozása

Hive-megoldás létrehozása és futtatása:

1. A menüsávban válassza a **fájl** > **új** > **projekt**lehetőséget.

2. Az **új projekt létrehozása** ablakban jelölje be a keresőmezőbe, és írja be a **kaptár**kifejezést. Ezután válassza ki a **kaptár alkalmazást** , és kattintson a **Tovább gombra**.

3. Az **új projekt konfigurálása** ablakban adja meg a **projekt nevét**, válassza ki vagy hozza létre a projekt **helyét**, majd válassza a **Létrehozás**lehetőséget.

    ![Új kaptár-alkalmazás, az új projekt ablakának konfigurálása, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **Script.hql** fájlra a szkript megnyitásához.

### <a name="view-job-summary-and-output"></a>Feladatok összegzésének és kimenetének megtekintése

A feladatok összegzése némileg eltér a **Batch** és az **interaktív** mód között.

![Kaptár-feladatok összegző Windows, batch és Interactive mód, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

A **frissítés** ikon használatával frissítse az állapotot, amíg a feladatok állapota **Befejezettre**nem változik.  

* A feladatok részletei a **Batch** -módból lapon válassza ki a lenti hivatkozásokat a **feladatütemezés**, a **feladatok kimenete**vagy a **Projektnapló**megtekintéséhez, vagy a **fonalak naplófájljainak megtekintéséhez**.

* Az **interaktív** módból származó feladatok részleteiért tekintse meg a **kimeneti** és **HiveServer2 kimeneti** ablaktáblákat.

    ![Struktúra interaktív feladatok kimenete, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Feladatok gráfjának megtekintése

Jelenleg a feladatok gráfok csak olyan kaptár-feladatok esetében jelennek meg, amelyek a TEZ-t használják végrehajtó motorként.  További információ a TEZ engedélyezéséről: [Mi az a Apache Hive és a HiveQL az Azure HDInsight?](hdinsight-use-hive.md).  Lásd még: [az Apache TEZ használata a Térkép csökkentése helyett](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

A csúcsponton belüli összes operátor megtekintéséhez kattintson duplán a feladatok gráfjának csúcspontokra. Egy adott operátorra mutatva megtekintheti az operátor részleteit.

Ha a TEZ a végrehajtási motorként van megadva, akkor előfordulhat, hogy a feladatütemezés nem jelenik meg, ha nincs TEZ-alkalmazás elindítva.  Ez a helyzet akkor fordulhat elő, ha a feladatokban nem találhatók DML-utasítások, vagy mert a DML-utasítások TEZ-alkalmazás elindítása nélkül visszatérhetnek. `SELECT * FROM table1` például nem indítja el a TEZ alkalmazást.

![Apache Hive Job Graph, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Feladat-végrehajtás részleteinek megtekintése

A feladat-gráfból kiválaszthatja a **feladat-végrehajtási részletességet** a struktúra-feladatok strukturált és vizualizációs információinak beszerzéséhez. További feladatokat is megtudhat. Teljesítményproblémák esetén a nézet segítségével részletesebb információkat szerezhet a problémáról. Például lekérheti az egyes feladatok működésével és részletes információkkal kapcsolatos információkat az egyes feladatokról (adatolvasási/írási, ütemezett/kezdő/befejező időpont stb.). A megjelenített információk alapján pedig finomhangolhatja a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![Feladat-végrehajtási nézet ablak, Data Lake Visual Studio-eszközök](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive-feladatok megtekintése

Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkimenetét, feladatnaplóit és Yarn naplóit.

Az eszköz legújabb kiadásával láthatja, mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a Yarn-naplókat. A YARN-naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. További információ arról, hogy a HDInsight hogyan gyűjti a fonal-naplókat: [hozzáférés Apache HADOOP fonalak alkalmazási naplóihoz](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Hive-feladatok megtekintése:

1. Kattintson a jobb gombbal egy HDInsight-fürtre, majd válassza a **feladatok megtekintése**lehetőséget.

    ![Feladatok megtekintése, Apache Hive, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Ekkor megjelenik azon Hive-feladatok listája, amelyek a fürtön futottak.  

2. Válasszon ki egy feladatot. A **kaptár-feladatok összegzése** ablakban válassza ki az alábbi hivatkozások egyikét:
    - **Job Query** (Feladat lekérdezése)
    - **Job Output** (Feladat kimenete)
    - **Job Log** (Feladatnapló)  
    - **Fonal naplója**

## <a name="run-apache-pig-scripts"></a>Apache Pig-parancsfájlok futtatása

1. A menüsávban válassza a **fájl** > **új** > **projekt**lehetőséget.

2. A **Start** ablakban jelölje be a keresőmezőbe, és írja be a **Pig**kifejezést. Ezután válassza a **Pig-alkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.

3. Az **új projekt konfigurálása** ablakban adja meg a **projekt nevét**, majd válasszon ki vagy hozzon létre egy **helyet** a projekt számára. Ezután kattintson a **Létrehozás** elemre.

4. Az IDE- **megoldáskezelő** ablaktáblán kattintson duplán a **script. Pig** elemre a parancsfájl megnyitásához.

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák

* Kijavítottunk egy hibát, amelynek következtében a null értékekkel induló eredmények nem jelentek meg. Ha elakad ennél a hibánál, lépjen kapcsolatba a támogatási csapattal.

* A Visual Studio által létrehozott HQL-szkript kódolása a felhasználó helyi régióbeállításaitól függ. A szkript nem fut megfelelően, ha a felhasználó bináris fájlként tölti fel a szkriptet a fürtbe.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studióból a Data Lake Tools for Visual Studio csomaggal. Emellett azt is megtanulta, hogyan futtathat Hive-lekérdezéseket. További információval a következő cikkek szolgálnak:

* [Apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studióval](apache-hadoop-use-hive-visual-studio.md)
* [Mi a Apache Hive és a HiveQL az Azure HDInsight?](hdinsight-use-hive.md)
* [Apache Hadoop fürt létrehozása – sablon](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop feladatok elküldése a HDInsight-ben](submit-apache-hadoop-jobs-programmatically.md)
* [Twitter-adataik elemzése Apache Hive és Apache Hadoop használatával a HDInsight-on](../hdinsight-analyze-twitter-data-linux.md)
