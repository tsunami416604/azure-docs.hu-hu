---
title: A Data Lake eszközei a Hortonworks visual studio-hoz & - Azure HDInsight
description: Ismerje meg, hogyan használhatja az Azure Data Lake eszközöket a Visual Studio a Hortonworks sandbox fut egy helyi virtuális gép. Ezekkel az eszközökkel létrehozhatja és futtathatja a Hive- és a Pig-feladatokat a sandboxon, és megtekintheti a feladat kimenetét és előzményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500156"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Az Azure Data Lake eszközök használata a Visual Studio számára a Hortonworks sandboxsegítségével

Az Azure Data Lake az általános Apache Hadoop-fürtökkel való munkához szükséges eszközöket is tartalmaz. Ez a dokumentum a Data Lake-eszközök használatához szükséges lépéseket a Hortonworks sandbox fut egy helyi virtuális gép.

A Hortonworks sandbox használatával helyileg dolgozhat a Hadoop-fal a fejlesztői környezetben. Miután kifejlesztett egy megoldást, és szeretné üzembe helyezni a nagy méretekben, majd áthelyezheti egy HDInsight-fürtre.

## <a name="prerequisites"></a>Előfeltételek

* A Hortonworks sandbox, fut egy virtuális gép a fejlesztői környezetben. Ezt a dokumentumot az Oracle VirtualBox-ban futó homokozóval írták és tesztelték. A sandbox beállításáról a [Hortonworks sandbox – Első lépések című témakörben talál.](hadoop/apache-hadoop-emulator-get-started.md) Dokumentum.

* Visual Studio.

* Az [Azure SDK a .NET](https://azure.microsoft.com/downloads/) 2.7.1 vagy újabb.

* Az [Azure Data Lake eszközei a Visual Studio számára.](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>Jelszavak konfigurálása a sandboxhoz

Győződjön meg arról, hogy a Hortonworks sandbox fut. Ezután kövesse a [Hortonworks sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentum első lépései című dokumentum lépéseit. Ezek a lépések konfigurálják `root` az SSH-fiók és `admin` az Apache Ambari-fiók jelszavát. Ezek a jelszavak akkor használatosak, amikor a Visual Studio rendszeréből csatlakozik a homokozóhoz.

## <a name="connect-the-tools-to-the-sandbox"></a>Az eszközök csatlakoztatása a homokozóhoz

1. Nyissa meg a Visual Studio alkalmazást, válassza **a Nézet**lehetőséget, majd a **Kiszolgálókezelő**lehetőséget.

2. A **Kiszolgálókezelőben**kattintson a jobb gombbal a **HDInsight-bejegyzésre,** majd válassza a Csatlakozás **HDInsight-emulátorhoz parancsot.**

    ![Kiszolgálóexplorer, kiemelve a Csatlakozás HDInsight-emulátorhoz](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. A **Csatlakozás HDInsight-emulátorhoz** párbeszédpanelen adja meg az Ambari hoz beállított jelszót.

    ![Képernyőkép: az ambari jelszó szövegmezője ki van emelve](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

4. A **Jelszó** mezősegítségével adja meg a `root` fiókhoz megadott jelszót. Hagyja a többi mezőt az alapértelmezett értéken.

    ![Képernyőkép a gyökérjelszó szövegmezőjével](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    A folytatáshoz kattintson a **Tovább** gombra.

5. Várja meg, amíg befejeződik a szolgáltatások ellenőrzése. Bizonyos esetekben az érvényesítés sikertelen lehet, és a konfiguráció frissítését kéri. Ha az ellenőrzés sikertelen, válassza **a Frissítés**lehetőséget, és várja meg a szolgáltatás befejezéséhez szükséges konfigurációt és ellenőrzést.

    ![Képernyőkép a párbeszédpanelről, amelyen a Frissítés gomb ki van emelve](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > A frissítési folyamat ambari segítségével módosítja a Hortonworks sandbox konfigurációját a Visual Studio Data Lake eszközei által elvárt értékre.

6. Az ellenőrzés befejezése után válassza a **Befejezés** gombot a konfiguráció befejezéséhez.
    ![Képernyőkép a párbeszédpanelről, kiemelve a Befejezés gombbal](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > A fejlesztői környezet sebességétől és a virtuális gép számára lefoglalt memória mennyiségétől függően a szolgáltatások konfigurálása és ellenőrzése több percet is igénybe vehet.

Az alábbi lépések bekövetése után most már rendelkezik egy **HDInsight helyi fürtbejegyzéssel** a Kiszolgálókezelőben, a **HDInsight** szakaszban.

## <a name="write-an-apache-hive-query"></a>Apache Hive-lekérdezés írása

Hive egy SQL-szerű lekérdezési nyelvet (HiveQL) biztosít a strukturált adatok kal dolgozik. Az alábbi lépésekkel megtudhatja, hogyan futtathat igény szerinti lekérdezéseket a helyi fürtön.

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal a korábban hozzáadott helyi fürt bejegyzésére, majd válassza **a Hive-lekérdezés írása parancsot.**

    ![Képernyőkép a Kiszolgálókezelőről, kiemelve a Hive-lekérdezés írása](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Megnyílik egy új lekérdezési ablak. Itt gyorsan írhat és küldhet el lekérdezést a helyi fürtnek.

2. Az új lekérdezésablakban adja meg a következő parancsot:

        select count(*) from sample_08;

    A lekérdezés futtatásához válassza a **Küldés** lehetőséget az ablak tetején. Hagyja a többi értéket **(Köteg** és kiszolgáló név) az alapértelmezett értékeken.

    ![Képernyőkép a lekérdezésablakról, kiemelve a Küldés gomb](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    A **Küldés** elem melletti legördülő menüben a Speciális lehetőséget is **használhatja.** A speciális beállítások lehetővé teszik, hogy további lehetőségeket adjon meg a feladat elküldésekor.

    ![Képernyőkép: Parancsfájl küldése párbeszédpanel-struktúra](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. A lekérdezés elküldése után megjelenik a feladat állapota. A feladat állapota információkat jelenít meg a hadoop által feldolgozott feladatról. **A feladat állapota** adja meg a feladat állapotát. Az állapot rendszeresen frissül, vagy a frissítésikonnal manuálisan frissítheti az állapotot.

    ![Képernyőkép a Feladatnézet párbeszédpanelről, kiemelve a Feladat állapotával](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Miután a **feladat állapota** **befejezettre**változik, egy irányított aciklikus grafikon (DAG) jelenik meg. Ez az ábra a Tez által a Hive-lekérdezés feldolgozásakor meghatározott végrehajtási útvonalat ismerteti. A Tez a Hive alapértelmezett végrehajtási motorja a helyi fürtön.

    > [!NOTE]  
    > Az Apache Tez is az alapértelmezett, ha Linux-alapú HDInsight-fürtöket használ. A Windows-alapú HDInsight nem alapértelmezett. Az ott való használathoz hozzá `set hive.execution.engine = tez;` kell adnia a sort a Hive-lekérdezés elejéhez.

    A **kimenet** megtekintéséhez használja a Projekt kimenet hivatkozását. Ebben az esetben ez 823, a sorok száma a sample_08 táblázatban. A feladat diagnosztikai információit a **Feladatnapló** és a **YARN napló letöltése** hivatkozásokkal tekintheti meg.

4. A Hive-feladatokinteraktív futtatásához módosítsa a **Batch mezőt** **interaktívra.** Ezután válassza **a Végrehajtás lehetőséget.**

    ![Kiemelve az Interaktív és A Végrehajtás gombok képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Egy interaktív lekérdezés streameli a feldolgozás során létrehozott kimeneti naplót a **HiveServer2 kimeneti** ablakba.

    > [!NOTE]  
    > Az információ megegyezik azzal, amely a **feladat naplója** hivatkozáson keresztül érhető el a feladat befejezése után.

    ![A HiveServer2 kimenet képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Hive-projekt létrehozása

Több Hive-parancsfájlt tartalmazó projektet is létrehozhat. Akkor használjon projektet, ha kapcsolódó parancsfájlokkal rendelkezik, vagy verzióvezérlő rendszerben szeretné tárolni a parancsfájlokat.

1. A Visual Studio programban válassza a **Fájl**, **Új**, majd **a Project**lehetőséget.

2. A projektek listájában bontsa ki a **Sablonok csomópontot, bontsa**ki az **Azure Data Lake**csomópontot, majd válassza a **HIVE (HDInsight) lehetőséget.** A sablonok listájában válassza a **Hive minta**lehetőséget. Írja be a nevet és a helyet, majd kattintson **az OK gombra.**

    ![Új projektablak az Azure Data Lake, a Hive-minta és az OK alkalmazással](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

A **Hive minta** projekt két parancsfájlt tartalmaz: **webloganalysis.hql** és **SensorDataAnalysis.hql**. Ezeket a parancsfájlokat az ablak tetején található **Küldés** gombbal küldheti el.

## <a name="create-an-apache-pig-project"></a>Apache Pig projekt létrehozása

Míg a Hive sql-szerű nyelvet biztosít a strukturált adatokkal való munkához, a Pig az adatokon végzett átalakítások végrehajtásával működik. A Pig olyan nyelvet (Pig Latin) biztosít, amely lehetővé teszi az átalakítások folyamatának kifejlesztését. Ha a Pig-t a helyi fürttel szeretné használni, kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio alkalmazást, és válassza a **Fájl**, **Az Új**, majd a **Project**lehetőséget. A projektek listájában bontsa ki a **Sablonok csomópontot, bontsa**ki az **Azure Data Lake**csomópontot, majd válassza a Pig **(HDInsight) lehetőséget.** A sablonok listájában válassza a **Pig Alkalmazás**lehetőséget. Írjon be egy nevet, egy helyet, majd kattintson **az OK gombra.**

    ![Képernyőkép az Új projekt ablakról, amelyen az Azure Data Lake, a Pig, a Pig Application és az OK ki van emelve](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Írja be a következő szöveget a projekttel létrehozott **script.pig** fájl tartalmaként.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Bár a Pig a Hive-tól eltérő nyelvet használ, a feladatok futtatásának futtatása a **Küldés** gombon keresztül konzisztens a két nyelv között. A **Küldés** melletti legördülő lista egy speciális küldési párbeszédpanelt jelenít meg a Pig számára.

    ![Képernyőkép: A Parancsfájl küldése párbeszédpanel malaca](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. A feladat állapota és kimenete is megjelenik, ugyanaz, mint egy Hive-lekérdezés.

    ![Képernyőkép egy befejezett Pig-feladatról](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Feladatok megtekintése

A Data Lake eszközök lehetővé teszik a Hadoop-on futtatott feladatokra vonatkozó információk egyszerű megtekintését is. Az alábbi lépésekkel megtekintheti a helyi fürtön futtatott feladatokat.

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal a helyi fürtre, majd válassza **a Feladatok megtekintése parancsot.** Megjelenik a fürtnek elküldött feladatok listája.

    ![Képernyőkép a Kiszolgálókezelőről, kiemelt Nézetfeladatokkal](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. A feladatok listájából válasszon egyet a feladat részleteinek megtekintéséhez.

    ![Képernyőkép a Feladattalló-böngészőről, az egyik kiemelt feladattal](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    A megjelenített információk hasonlóak ahhoz, amit egy Hive- vagy Pig-lekérdezés futtatása után láthat, beleértve a kimeneti és naplóadatok megtekintésére mutató hivatkozásokat is.

3. A feladatot itt is módosíthatja és újra elküldheti.

## <a name="view-hive-databases"></a>Hive-adatbázisok megtekintése

1. A **Kiszolgálókezelőben**bontsa ki a **HDInsight helyi fürtbejegyzést,** majd a **Hive-adatbázisok csomópontot.** Megjelennek a helyi fürt **alapértelmezett** és **xademo-adatbázisai.** Az adatbázis kibontása az adatbázistábláit jeleníti meg.

    ![Képernyőkép a Kiszolgálókezelőről, kibontott adatbázisokkal](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. A táblázat kibontása a tábla oszlopait jeleníti meg. Az adatok gyors megtekintéséhez kattintson a jobb gombbal egy táblázatra, és válassza **a Nézet a 100 sor között parancsot.**

    ![Kiszolgálókezelő, kibontott táblázattal és a Felső 100 sor kijelölésével](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Adatbázis- és táblatulajdonságok

Az adatbázis vagy tábla tulajdonságai megtekinthetők. A **Tulajdonságok** lehetőséget választva a kijelölt elem részleteit jeleníti meg a Tulajdonságok ablakban. Lásd például az alábbi képernyőképen látható információkat:

![Képernyőkép a Tulajdonságok ablakról](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Tábla létrehozása

Tábla létrehozásához kattintson a jobb gombbal egy adatbázisra, majd válassza **a Tábla létrehozása parancsot.**

![Képernyőkép a Kiszolgálókezelőről, kiemelve a Táblázat létrehozása lehetőséggel](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Ezután űrlap használatával hozhatja létre a táblát. Az alábbi képernyőkép alján láthatja a táblázat létrehozásához használt nyers HiveQL-t.

![Képernyőkép a táblázat létrehozásához használt űrlapról](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>További lépések

* [Tanulás a kötelek a Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop oktatóanyag – A HDP – Első lépések](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
