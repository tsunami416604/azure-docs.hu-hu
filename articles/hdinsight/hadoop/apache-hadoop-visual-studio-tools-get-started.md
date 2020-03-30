---
title: Az Apache Hadoop & Visual Studio Data Lake tools – Azure HDInsight
description: Megtudhatja, hogy miként telepítheti és használhatja a Data Lake Tools for Visual Studio alkalmazást az Apache Hadoop-fürtökhöz való csatlakozáshoz az Azure HDInsightban, majd futtathatja a Hive-lekérdezéseket.
keywords: hadoop-eszközök,hive-lekérdezés,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272785"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>A Data Lake Tools for Visual Studio segítségével csatlakozhat az Azure HDInsighthoz, és apache Hive-lekérdezéseket futtatható

Megtudhatja, hogy miként csatlakozhat a Microsoft Azure Data Lake és a Stream Analytics Tools for Visual Studio (más néven Data Lake-eszközök) segítségével [az Azure HDInsight Ban lévő Apache Hadoop-fürtökhöz,](apache-hadoop-introduction.md) és hogyan küldhet hive-lekérdezéseket.  

A HDInsight használatáról további információt az [Első lépések a HDInsight használatáért című témakörben talál.](apache-hadoop-linux-tutorial-get-started.md)  

Az Apache Storm-fürthöz való csatlakozásról további információt [az Apache Storm C# topológiáinak fejlesztése a Visual Studio Data Lake eszközeivel című](../storm/apache-storm-develop-csharp-visual-studio-topology.md)témakörben talál.

A Data Lake Tools for Visual Studio mind az Azure Data Lake Analytics, mind a HDInsight eléréséhez használható. A Data Lake Tools eszközökkel kapcsolatos információkért lásd: [U-SQL-szkriptek fejlesztése Data Lake Tools for Visual Studio használatával](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez és a Data Lake Tools for Visual Studio használatához a következő elemekre van szükség:

* Egy Azure-beli HDInsight-fürt. HDInsight-fürt létrehozásáról az [Első lépések az Apache Hadoop használatával az Azure HDInsightban című témakörben található.](apache-hadoop-linux-tutorial-get-started.md) Az interaktív Apache Hive-lekérdezések futtatásához [hdinsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürtre van szükség.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). A [Visual Studio Community kiadás](https://visualstudio.microsoft.com/vs/community/) ingyenes. Az itt látható utasítások a [Visual Studio 2019-hez tartoznak.](https://visualstudio.microsoft.com/downloads/)

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése  

Kövesse a Megfelelő utasításokat a Data Lake Tools visual studio verziójához való telepítéséhez:

- Visual Studio 2017 vagy Visual Studio 2019 esetén:

    A Visual Studio telepítése során győződjön meg arról, hogy tartalmazza az **Azure fejlesztési** számítási feladatok vagy az adatok tárolási **és feldolgozási** számítási feladatok.  

    Meglévő Visual Studio-telepítésesetén nyissa meg az IDE menüsort, és válassza az **Eszközök** > **betöltőeszközök és szolgáltatások** lehetőséget a Visual Studio Installer megnyitásához. A **Számítási feladatok** lapon válassza ki legalább az **Azure fejlesztési** számítási feladatait (a Web & **Cloud**alatt) vagy az **Adattárolási és feldolgozási** számítási feladatokat **(az Egyéb eszközkészletek**területen).

  ![Számítási feladatok kiválasztása, Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Visual Studio 2015 esetén:

    [Letöltés Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Válassza ki a Data Lake Tools azon verzióját, amely megfelel a Visual Studio verziójának.

## <a name="update-data-lake-tools-for-visual-studio"></a>A Visual Studio Data Lake eszközeinek frissítése  

Ezután győződjön meg arról, hogy frissíti a Data Lake Tools-t a legújabb verzióra.

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza a **Folytatás kód nélkül**lehetőséget.

3. A Visual Studio IDE menüsorában válassza a **Bővítmények** > **bővítmények kezelése**lehetőséget.

4. A Bővítmények kezelése párbeszédpanelen **bontsa** ki a **Frissítések csomópontot.**

5. Ha az elérhető frissítések listája tartalmazza az **Azure Data Lake és a Stream Analytic Tools,** válassza ki azt. Ezután válassza ki a **Frissítés** gombot. Miután a **Letöltés és telepítés** párbeszédpanel megjelenik és eltűnik, a Visual Studio hozzáadja az Azure Data Lake és a Stream **Analitikus eszközök** bővítményt a frissítési ütemezéshez.

6. Zárja be az összes Visual Studio-ablakot. Megjelenik a **VSIX telepítő** párbeszédpanel.

7. Válassza a **Licenc** lehetőséget a licencfeltételek olvasásához, majd a **Bezárás** lehetőséget a **VSIX telepítő** párbeszédpanelre való visszatéréshez.

8. Válassza **a Módosítás**lehetőséget. Megkezdődik a bővítményfrissítés telepítése. Egy idő után a párbeszédpanel megváltozik, hogy megmutassa, hogy végzett a módosításokkal. Válassza **a Bezárás**gombot, majd a telepítés befejezéséhez indítsa újra a Visual Studio alkalmazást.

> [!NOTE]  
> Csak a Data Lake Tools 2.3.0.0-s és újabb verziói támogatják az interaktív lekérdezési fürtökhöz való csatlakozást és az interaktív Hive-lekérdezések futtatását.

## <a name="connect-to-azure-subscriptions"></a>Csatlakozás Azure-előfizetésekhez

A Data Lake Tools for Visual Studio segítségével csatlakozhat a HDInsight-fürtökhöz, elvégezhet néhány alapvető felügyeleti műveletet, és futtathat Hive-lekérdezéseket.

> [!NOTE]  
> Az általános Hadoop-fürthöz való csatlakozásról a [Hive-lekérdezések írása és beküldése a Visual Studio használatával című](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)témakörben talál további információt.

### <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

Csatlakozás az Azure-előfizetéshez:

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza a **Folytatás kód nélkül**lehetőséget.

3. Az IDE menüsorban válassza a**Kiszolgálókezelő** **megtekintése** > lehetőséget.

4. A **Kiszolgálókezelőben**kattintson a jobb gombbal az **Azure**elemre, válassza **a Csatlakozás a Microsoft Azure-előfizetéshez**lehetőséget, és fejezze be a hitelesítési folyamatot. A **Kiszolgálókezelőből**bontsa ki az **Azure** > **HDInsight** alkalmazást a meglévő HDInsight-fürtök listájának megtekintéséhez.

5. Ha nem rendelkezik fürtökkel, hozzon létre egyet az Azure Portalon, az Azure PowerShell vagy a HDInsight SDK használatával. További információt a [Fürtök beállítása a HDInsightban című témakörben talál.](../hdinsight-hadoop-provision-linux-clusters.md)

   ![HDInsight-fürtlista, Kiszolgálókezelő, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Bontson ki egy HDInsight-fürtöt. A fürt tartalmazza a **Hive-adatbázisok csomópontjait,** egy alapértelmezett tárfiókot, minden további csatolt tárfiókot és **a Hadoop-szolgáltatásnaplót.** Ennél jobban is kibonthatja az elemeket.

Miután csatlakozott az Azure-előfizetéshez, végrehajthatja a következő feladatokat.

### <a name="connect-to-azure-from-visual-studio"></a>Csatlakozás az Azure-hoz a Visual Studio-ból

Csatlakozás az Azure Portalhoz a Visual Studióból:

1. A **Server Explorer**ben bontsa ki az **Azure** > **HDInsight** alkalmazást, és válassza ki a fürtöt.

2. Kattintson a jobb gombbal egy HDInsight-fürtre, és válassza **a Fürt kezelése parancsot az Azure Portalon.**

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Kérdések és visszajelzések a Visual Studio-tól

Kérdések felkérése és/vagy visszajelzés küldése a Visual Studio-tól:

1. A Server Explorer ben válassza az **Azure** > **HDInsight**lehetőséget.

2. Kattintson a jobb gombbal a **HDInsight** elemre, és válassza az **MSDN-fórum** lehetőséget a kérdések felkéréséhez, vagy **a Visszajelzés küldése** lehetőséget.

## <a name="link-to-or-edit-a-cluster"></a>Fürtre mutató hivatkozás vagy szerkesztés

> [!NOTE]
> Jelenleg az egyetlen típusú HDInsight-fürt, amelyhez csatolható, az egy Hive-típus.

HDInsight-fürt csatolása:

1. Kattintson a jobb gombbal a **HDInsight**elemre, majd válassza a **HDInsight-fürt csatolása** parancsot a **HDInsight-fürt csatolása** párbeszédpanel megjelenítéséhez.

2. Adjon meg egy **kapcsolat url-címét** a *\://\<https-fürt&nbsp;név>.azurehdinsight.net*formában. A **fürtneve** automatikusan kitölti az URL-cím fürtnév részét, amikor másik mezőre lép. Ezután adja meg **a felhasználónevet** és **a jelszót**, és válassza a **Tovább**gombot.

    ![Fürt, HDInsight, Visual Studio összekapcsolása](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Válassza a **Finish** (Befejezés) elemet. Ha a fürtcsatolás sikeres, a fürt a **HDInsight-csomópont** alatt jelenik meg.

Csatolt fürt frissítéséhez kattintson a jobb gombbal a fürtre, és válassza a **Szerkesztés parancsot.** Ezután frissítheti a fürtadatait.

![Csatolt fürt, HDInsight, Visual Studio szerkesztése](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Kapcsolt erőforrások vizsgálata
A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve.

![Data Lake-eszközök a Visual Studio kapcsolódó erőforrásaihoz a Server Explorerben](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Kattintson a jobb gombbal egy tárolóra, és válassza a **Tároló megtekintése parancsot** a tároló tartalmának megtekintéséhez. A tároló megnyitása után az eszköztárgombokkal **frissítheti** a tartalomlistát, a **Blob feltöltése,** **a kijelölt blobok törlése**, a Blob **megnyitása**és a kijelölt blobok letöltése **(Mentés másként)** segítségével.

![Tárolólista és blobműveletek, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Interaktív Apache Hive-lekérdezések futtatása
Az [Apache Hive](https://hive.apache.org) egy Hadoop-alapú adattárház-infrastruktúra. A Hive adatösszegzéseket, lekérdezéseket és elemzéseket biztosít. A Data Lake Tools for Visual Studio segítségével Hive-lekérdezéseket futtathat a Visual Studióból. A Hive-ről további információt a [Mi az Apache Hive és a HiveQL az Azure HDInsightban című témakörben talál.](hdinsight-use-hive.md)

[Az Azure HDInsight interaktív lekérdezése](../interactive-query/apache-interactive-query-get-started.md) az Apache Hive 2.1-es [rendszerében lévő LLAP-on](https://cwiki.apache.org/confluence/display/Hive/LLAP) használ Hive-t. Az Interaktív lekérdezés interaktív interaktivitást hoz létre a nagyméretű, tárolt adatkészletek összetett, adattárház-stílusú lekérdezéseihez. Hive-lekérdezések interaktív lekérdezésen futtatása sokkal gyorsabb, mint a hagyományos Hive kötegelt feladatok. 

> [!NOTE]  
> Interaktív Hive-lekérdezéseket csak akkor futtathat, ha kapcsolódik egy [HDInsight interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürthöz.

A Data Lake Tools for Visual Studio segítségével a Hive-feladatok tartalmát is megtekintheti. A Data Lake Tools for Visual Studio begyűjti és a felszínre hozza bizonyos Hive-feladatok YARN-naplóit.

A **Server Explorer ben**válassza az **Azure** > **HDInsight** lehetőséget, és válassza ki a fürtöt.  Ez a csomópont a **kiszolgálókezelőben** a követendő szakaszok kiindulópontja.

### <a name="view-hivesampletable"></a>A hivesampletable megtekintése

Minden HDInsight-fürt rendelkezik egy alapértelmezett `hivesampletable`minta Hive-táblával, amelynek neve .  

A fürtből válassza a **Hive-adatbázisok** > **alapértelmezett** > **struktúramintatáblát**lehetőséget.

- A `hivesampletable` séma megtekintése:

    Bontsa ki **a hivesampletable csomópontot.** Az `hivesampletable` oszlopok neve és adattípusa látható.

- Az `hivesampletable` adatok megtekintése:

    Kattintson a jobb gombbal **a hivesampletable**elemre, és válassza **a Nézet a Felső 100 sor lehetőséget**. A 100 találatot tartalmazó lista megjelenik a **Hive-táblázatban: hivesampletable** ablakban. Ez a művelet egyenértékű a következő Hive-lekérdezés futtatásával a Hive ODBC illesztőprogram használatával:

    `SELECT * FROM hivesampletable LIMIT 100`

    A sorok számát a **Sorok számának**módosításával testreszabhatja; a legördülő listából 50, 100, 200 vagy 1000 sort választhat.

### <a name="create-hive-tables"></a>Hive táblák létrehozása
Hive-tábla létrehozásához használhatja a grafikus felhasználói felületet vagy a Hive-lekérdezéseket. A Hive-lekérdezések használatáról a [Hive-lekérdezések létrehozása és futtatása](#create-and-run-hive-queries)című témakörben talál.

1. A fürtből válassza a **Hive-adatbázisok** > **alapértelmezett beállítását.**

2. Kattintson a jobb gombbal az **alapértelmezett**elemre, és válassza **a Tábla létrehozása parancsot.**

3. Konfigurálja a táblát.

4. A **feladat** elküldéséhez válassza a Tábla létrehozása gombot, amely létrehozza az új Hive-táblát.

    ![Táblaablak létrehozása, Hive, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Hive-lekérdezések létrehozása és futtatása
Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

#### <a name="create-an-ad-hoc-query"></a>Alkalmi lekérdezés létrehozása

Alkalmi lekérdezés létrehozása és futtatása:

1. Kattintson a jobb gombbal arra a fürtre, amelyben futtatni szeretné a lekérdezést, és válassza **a Hive-lekérdezés írása parancsot.**  

2. Adjon meg egy Hive-lekérdezést.

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például a `SELECT * FROM`beírást írja be, az IntelliSense felsorolja az összes javasolt táblanevet. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják.

    ![IntelliSense példa 1, Hive ad hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense példa 2, Hive ad hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

    Az alábbiakban egy mintalekérdezést olvashat, amelyet használhat:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Válassza ki a végrehajtási módot:

    * **Interaktív**  

        Az első legördülő listában válassza az **Interaktív**lehetőséget, majd a **Végrehajtás**lehetőséget.

        ![Interaktív mód, Hive ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Az első legördülő listában válassza a **Köteg**lehetőséget , majd a **Küldés** lehetőséget (vagy válassza a **Küldés** és a **Speciális**lehetőséget választó legördülő ikont).

        ![Kötegelt mód, Hive ad-hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Ha a speciális küldési lehetőséget választja, megjelenik a **Parancsfájl küldése** párbeszédpanel. Adja meg a **parancsfájl feladatnevét,** **argumentumait,** **további konfigurációkés** **állapotkönyvtárát.**

        ![Script küldése párbeszédpanel, Hive ad hoc lekérdezés, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > A kötegek nem küldhető el interaktív lekérdezési fürtöknek.  Interaktív módot kell használnia.

#### <a name="create-a-hive-application"></a>Hive alkalmazás létrehozása

Hive-megoldás létrehozása és futtatása:

1. A menüsorban válassza az**Új** > **projekt fájlja** **File** > lehetőséget.

2. Az **Új projekt létrehozása** ablakban jelölje ki a keresőmezőt, és írja be a **Hive**parancsot . Ezután válassza **a Hive-alkalmazás** és a **Tovább**lehetőséget.

3. Az **Új projekt konfigurálása** ablakban adja meg a **Projekt nevét,** jelölje ki vagy hozza létre a projekt **helyét,** majd kattintson a **Létrehozás gombra.**

    ![Új Hive alkalmazás, Az új projektablak konfigurálása, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **Script.hql** fájlra a szkript megnyitásához.

### <a name="view-job-summary-and-output"></a>Feladat összegzésének és kimenetének megtekintése

A feladat összegzése kis mértékben változik **a Köteg** és **az Interaktív** mód között.

![Hive feladat összefoglaló ablakai, kötegelt és interaktív mód, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

A **Frissítés** ikonnal frissítheti az állapotot, amíg a feladat állapota **Befejezettre nem változik.**  

* A **feladat részleteinek kötegelt** módban történő megtekintéséhez jelölje ki az alsó hivatkozásokat a **Feladatlekérdezés**, **A Feladat kimenet**vagy a **Feladatnapló**, illetve a **Fonalnaplók megtekintéséhez.**

* Az **interaktív** módból származó feladat részleteiről a **Kimenet** és a **HiveServer2 kimeneti** ablaktáblák című témakörben talál.

    ![Hive interaktív kimenet, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Feladatgrafikon megtekintése

Jelenleg a feladatgrafikonok csak a Hive-feladatok, amelyek tez a végrehajtási motor használata.  A Tez engedélyezéséről a [Mi az Apache Hive és a HiveQL az Azure HDInsightban című témakörben.](hdinsight-use-hive.md)  Lásd még: [Az Apache Tez használata a Térképcsökkentés helyett.](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)  

A csúcsponton belüli összes operátor megtekintéséhez kattintson duplán a feladatgrafikon csúcspontjaira. Egy adott operátorra mutatva megtekintheti az operátor részleteit.

Még akkor is, ha Tez van megadva a végrehajtási motor, a feladat grafikon nem jelenik meg, ha nem Tez alkalmazás indul.  Ez a helyzet azért fordulhat elő, mert a feladat nem tartalmaz DML-utasításokat, vagy mert a DML-utasítások visszatérhetnek a Tez-alkalmazás elindítása nélkül. Például `SELECT * FROM table1` nem indítja el a Tez alkalmazást.

![Apache Hive feladatgrafikon, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Feladat-végrehajtási részletek megtekintése

A feladatgrafikonon kiválaszthatja a **Feladat-végrehajtási részletek lehetőséget** a Hive-feladatok strukturált és láthatóvá tanusítandó információinak beszerezéséhez. Ön is kap több munkát részleteket. Teljesítményproblémák esetén a nézet segítségével részletesebb információkat szerezhet a problémáról. Például lekérheti az egyes feladatok működésére vonatkozó információkat, valamint az egyes feladatokra vonatkozó részletes információkat (adatok olvasása/írása, ütemezés/kezdés/befejezés időpontja stb.). A megjelenített információk alapján pedig finomhangolhatja a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![Feladatvégrehajtás nézet ablak, Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive-feladatok megtekintése

Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkimenetét, feladatnaplóit és Yarn naplóit.

Az eszköz legújabb kiadásával láthatja, mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a Yarn-naplókat. A YARN-naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. Arról, hogy a HDInsight hogyan gyűjti a fonalnaplókat, az [Access Apache Hadoop YARN alkalmazásnaplóiban](../hdinsight-hadoop-access-yarn-app-logs-linux.md)talál további információt.

Hive-feladatok megtekintése:

1. Kattintson a jobb gombbal egy HDInsight-fürtre, és válassza **a Feladatok megtekintése parancsot.**

    ![Feladatok megtekintése, Apache Hive, HDInsight-fürt, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Ekkor megjelenik azon Hive-feladatok listája, amelyek a fürtön futottak.  

2. Válasszon ki egy feladatot. A **Hive feladat összegzése** ablakban válasszon az alábbi hivatkozások közül:
    - **Job Query** (Feladat lekérdezése)
    - **Feladat kimenete**
    - **Job Log** (Feladatnapló)  
    - **Fonal napló**

## <a name="run-apache-pig-scripts"></a>Apache Pig parancsfájlok futtatása

1. A menüsorban válassza az**Új** > **projekt fájlja** **File** > lehetőséget.

2. A **Start** ablakban jelölje ki a keresőmezőt, és írja be a **Pig**parancsot. Ezután válassza **a Pig Alkalmazás lehetőséget,** és válassza a **Tovább**gombot.

3. Az **Új projekt konfigurálása** ablakban adja meg a **Projekt nevét**, és válassza ki vagy hozza létre a **projekt helyét.** Ezután válassza **a Létrehozás lehetőséget.**

4. Az **IDE-megoldáskezelő** ablaktáblán kattintson duplán a **Script.pig** ikonra a parancsfájl megnyitásához.

## <a name="feedback-and-known-issues"></a>Visszajelzés és ismert problémák

* Kijavítottunk egy hibát, amelynek következtében a null értékekkel induló eredmények nem jelentek meg. Ha elakad ennél a hibánál, lépjen kapcsolatba a támogatási csapattal.

* A Visual Studio által létrehozott HQL-szkript kódolása a felhasználó helyi régióbeállításaitól függ. A szkript nem fut megfelelően, ha a felhasználó bináris fájlként tölti fel a szkriptet a fürtbe.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studióból a Data Lake Tools for Visual Studio csomaggal. Emellett azt is megtanulta, hogyan futtathat Hive-lekérdezéseket. További információval a következő cikkek szolgálnak:

* [Apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studióval](apache-hadoop-use-hive-visual-studio.md)
* [Mi az Apache Hive és a HiveQL az Azure HDInsightban?](hdinsight-use-hive.md)
* [Apache Hadoop-fürt létrehozása – Sablon](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-feladatok beküldése a HDInsightban](submit-apache-hadoop-jobs-programmatically.md)
* [A Twitter-adatok elemzése Apache Hive és Apache Hadoop használatával a HDInsighton](../hdinsight-analyze-twitter-data-linux.md)
