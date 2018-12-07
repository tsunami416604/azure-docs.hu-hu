---
title: Data Lake tools with hortonworks – tesztkörnyezet – Azure HDInsight Visual Studio
description: Ismerje meg az Azure Data Lake tools for Visual Studio használata a Hortonworks sandbox fut a helyi virtuális Gépen. Ezekkel az eszközökkel, hozhat létre és futtathat Hive és Pig-feladatok a védőfal, és a feladat kimenetének megtekintése és a korábbi.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: c2fd32ad15366c76c061ba42fa0a59d43a317b43
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012759"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>A Azure Data Lake tools for Visual Studio és a hortonworks – tesztkörnyezet használata

Az Azure Data Lake általános Apache Hadoop-fürtökkel folytatott munkavégzéshez kapcsolódó eszközöket tartalmazza. Ez a dokumentum egy helyi virtuális gépen futó a Data Lake-eszközök használata a Hortonworks Sandbox szükséges lépéseket ismerteti.

A hortonworks – tesztkörnyezet használata lehetővé teszi a fejlesztési környezetet a helyi és a hadoop együttes működését. Miután a megoldást, és a méretezett telepíteni, majd továbbléphet egy HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* A hortonworks – tesztkörnyezet, a fejlesztési környezetet egy virtuális gépen futó. Ez a dokumentum írt volt, és a védőfal futó Oracle VirtualBox teszteltük. A védőfal beállításával kapcsolatos további információkért lásd: a [a Hortonworks sandbox használatának első lépései.](hadoop/apache-hadoop-emulator-get-started.md) a dokumentum.

* A Visual Studio 2013, Visual Studio 2015 vagy Visual Studio 2017 (bármely kiadás esetén).

* A [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1-es vagy újabb.

* A [Azure Data Lake tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>A védőfal jelszavak beállítása

Győződjön meg arról, hogy fut-e a hortonworks – tesztkörnyezet. Ezután kövesse a lépéseket a [Ismerkedjen meg a Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentumot. Ezeket a lépéseket a jelszót az SSH konfigurálása `root` fiókot és az Apache Ambari `admin` fiókot. Ezek a jelszavak használt való csatlakozáskor a védőfal a Visual Studióból.

## <a name="connect-the-tools-to-the-sandbox"></a>Az eszközök csatlakozni a védőfal

1. Nyissa meg a Visual Studióban, válassza ki **nézet**, majd válassza ki **Server Explorer**.

2. A **Server Explorer**, kattintson a jobb gombbal a **HDInsight** bejegyzést, és válassza ki **csatlakozhat a HDInsight Emulator**.

    ![Képernyőkép a Server Explorer eszközben való kapcsolódás a HDInsight Emulator kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Az a **csatlakozhat a HDInsight Emulator** párbeszédpanelen adja meg az Ambari beállított jelszót.

    ![A jelszó szövegmező kiemelve a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

4. Használja a **jelszó** mezőbe írja be a beállított jelszót a `root` fiókot. Az alapértelmezett értéket a többi mezőt hagyja.

    ![A jelszó szövegmező kiemelve a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

5. Várjon, amíg befejeződik a szolgáltatások érvényesítési. Bizonyos esetekben a érvényesítése sikertelen, és kérni fogja, hogy frissítse a konfigurációt. Ha az érvényesítés meghiúsul, válassza ki a **frissítés**, és várjon, amíg a konfiguráció és az ellenőrzés befejezéséhez a szolgáltatás.

    ![Frissítés gomb kiemelésével a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > A frissítési folyamat által a Data Lake tools for Visual Studio várt a hortonworks – tesztkörnyezet konfigurációját módosíthatja az Ambari használ.

6. Érvényesítés befejezése után, válassza ki a **Befejezés** konfigurálásának befejezéséhez.
    ![A Befejezés gomb kiemelésével a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > A fejlesztési környezetet, és a virtuális gép számára lefoglalt memória sebességétől függően konfigurálása és érvényesítése a szolgáltatások több percet is igénybe vehet.

Után az alábbi lépésekkel, most már rendelkezik egy **helyi HDInsight-fürt** a Server Explorerben bejegyzés alatt a **HDInsight** szakasz.

## <a name="write-an-apache-hive-query"></a>Az Apache Hive-lekérdezés írása

Hive használata a strukturált adatok egy SQL-szerű lekérdezési nyelvet (HiveQL) tartalmaz. A következő lépések segítségével megtudhatja, hogyan igény szerinti lekérdezéseket futtassanak a helyi fürtöt.

1. A **Server Explorer**, kattintson a jobb gombbal a helyi fürthöz korábban hozzáadott bejegyzését, és válassza **egy Hive-lekérdezés írása**.

    ![Képernyőkép a Server Explorer eszközben Write a Hive-lekérdezés, kiemelve az](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Megnyílik egy új lekérdezési ablak. Itt is gyorsan írása és elküldése egy lekérdezést a helyi fürthöz.

2. Az új lekérdezési ablakban adja meg a következő parancsot:

        select count(*) from sample_08;

    Válassza ki a lekérdezés futtatásához **küldés** az ablak tetején. A többi értéket hagyja (**Batch** és a kiszolgáló neve) az alapértelmezett értéken.

    ![Képernyőkép a lekérdezési ablakban, a Küldés gomb kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Használhatja a legördülő menü mellett **küldés** kiválasztásához **speciális**. Speciális beállítások lehetővé teszik a további lehetőségeket nyújtanak, ha a feladat elküldéséhez.

    ![Parancsfájl elküldése képernyőkép párbeszédpanel](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Miután elküldi a lekérdezést, a feladat állapota jelenik meg. A feladat állapota a feladatok információit jeleníti meg, hadoop feldolgozás során. **Feladat állapota** biztosít a feladat állapotát. Az állapot rendszeresen frissül, vagy a frissítés ikont használatával frissítse manuálisan az állapotát.

    ![Képernyőfelvétel a feladat nézet párbeszédpanel, kiemelve a feladat állapota](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Miután a **feladat állapota** vált **kész**, egy irányított aciklikus Graph (DAG) jelenik meg. Ez az ábra ismerteti a végrehajtási útvonalát, amely a Tez volt határozza meg a Hive-lekérdezés feldolgozása során. Tez a alapértelmezett-végrehajtó motor a Hive a helyi fürtön.

    > [!NOTE]
    > Az Apache Tez egyben az alapértelmezett Linux-alapú HDInsight-fürtök használata esetén. Akkor sem a Windows-alapú HDInsight az alapértelmezett. A használatára, hozzá kell adnia a sor `set hive.execution.engine = tez;` elején található a Hive-lekérdezést.

    Használja a **Feladatkimenet** hivatkozásra a kimenet megtekintéséhez. Ebben az esetben fontos 823, a sample_08 tábla sorainak számát. A feladat diagnosztikai információ segítségével tekintheti a **Job Log** és **töltse le a YARN-naplók** hivatkozásokat.

4. Futtathat Hive-feladatok interaktív módon módosításával a **Batch** mezőt **interaktív**. Válassza ki **Execute**.

    ![Képernyőkép az interaktív és végrehajtási gombok kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Interaktív lekérdezést streameli a feldolgozás során generált kimeneti naplót a **hiveserver2-n keresztül kimeneti** ablak.

    > [!NOTE]
    > Az információ megegyezik, amely elérhető a **Job Log** hivatkozásra a feladat befejezése után.

    ![Képernyőkép a kimeneti naplót](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Egy Hive-projekt létrehozása

Egy több Hive-parancsfájlt tartalmazó projektet is létrehozhat. A projekt használja, kapcsolódó parancsfájlokat vagy parancsprogramok tárolása egy verziókövetési rendszert szeretne.

1. A Visual Studióban válassza ki a **fájl**, **új**, majd **projekt**.

2. Bontsa ki a listából a projektek **sablonok**, bontsa ki a **az Azure Data Lake**, majd válassza ki **HIVE (HDInsight)**. A sablonok listájából válassza **minta Hive**. Adjon meg egy nevet és egy helyet, és válassza ki **OK**.

    ![Képernyőfelvétel az új projekt ablakról, amelyen a az Azure Data Lake, a HIVE, a Hive-minta és az OK kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

A **minta Hive** -projekt tartalmazza a két parancsfájlok **WebLogAnalysis.hql** és **SensorDataAnalysis.hql**. Ezek a szkriptek használatával azonos küldhet **küldés** gombra az ablak tetején.

## <a name="create-an-apache-pig-project"></a>Hozzon létre egy Apache Pig-projektet

Hive SQL-szerű nyelv biztosít a strukturált adatokkal dolgozik, a Pig átalakításokat végez az adatok alapján működik megfelelően. A Pig, amely lehetővé teszi, hogy egy folyamatot átalakítások fejlesztése nyelv (a Pig latin betűs) biztosít. A Pig használata a helyi fürthöz, kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studiót, és válassza ki **fájl**, **új**, majd **projekt**. Bontsa ki a listából a projektek **sablonok**, bontsa ki a **az Azure Data Lake**, majd válassza ki **Pig (HDInsight)**. A sablonok listájából válassza **Pig alkalmazás**. Adja meg a nevét, helyét, és válassza ki **OK**.

    ![Képernyőfelvétel az új projekt ablakról, amelyen a az Azure Data Lake, a Pig, a Pig-alkalmazás és az OK kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Adja meg a következő szöveget a tartalmát a **script.pig** fájlt, amely a projekt létrejött.

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

    Bár a Pig egy másik nyelvet használja, mint a Hive, a feladatok futtatásának módját a konzisztens között mindkét nyelven, keresztül a **küldés** gombra. Válassza a legördülő elem melletti **küldés** a Pig-speciális küldés párbeszédpanelt jelenít meg.

    ![Parancsfájl elküldése képernyőkép párbeszédpanel](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. A feladat állapota és kimenete is megjelenik, ugyanaz, mint egy Hive-lekérdezést.

    ![Képernyőkép a Pig feladat](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Feladatok megtekintése

Data Lake-eszközök is lehetővé teszik információ a hadoopon futó feladatok megtekintéséhez. Az alábbi lépések segítségével tekintse meg a helyi fürtön futó feladatok.

1. A **Server Explorer**, kattintson a jobb gombbal a helyi fürthöz, és válassza **feladatok megtekintése**. A fürt elküldött feladatok listája jelenik meg.

    ![Képernyőkép a Server Explorer, a kiemelt feladatok megtekintése](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Feladatok listájából válassza ki a feladat részleteinek megtekintéséhez.

    ![Képernyőkép a Feladatböngésző, egy, a feladatok vannak kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    A megjelenő információk a kimenet megtekintéséhez és naplózza az információkat mutató hivatkozásokat tartalmaz Hive és Pig lekérdezés futtatása után megjelenő hasonlít.

3. Módosíthatja, és küldje el újra a feladatot, itt is.

## <a name="view-hive-databases"></a>Hive-adatbázisok megtekintése

1. A **Server Explorer**, bontsa ki a **helyi HDInsight-fürt** bejegyzést, majd **Hive-adatbázisok**. A **alapértelmezett** és **xademo** jelennek meg a helyi fürtön lévő adatbázisok. Egy adatbázis bővítése jeleníti meg a táblák az adatbázisban.

    ![Képernyőkép a Server Explorer eszközben az adatbázisok kibontva](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Egy tábla kibontása jeleníti meg, hogy a táblázat oszlopainak. Az adatok gyors megtekintéséhez kattintson a jobb gombbal egy táblát, és válassza **View Top 100 sor**.

    ![Képernyőkép a Server Explorer eszközben kibontva tábla és nézet első 100 sor van kijelölve](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Adatbázis és tábla tulajdonságai

Megtekintheti egy adatbázis és tábla tulajdonságai. Kiválasztásával **tulajdonságok** megjeleníti a kiválasztott elem részletei a Tulajdonságok ablakban. Például olvassa el az alábbi képernyőképen látható:

![Képernyőfelvétel: a Tulajdonságok ablak](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Tábla létrehozása

Hozzon létre egy táblát, kattintson a jobb gombbal egy adatbázist, és válassza **Create Table**.

![Képernyőkép a Server Explorer eszközben a Create Table kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Ezután létrehozhat a tábla űrlap segítségével. Az alábbi képernyőképen alján megjelenik a nyers HiveQL a tábla létrehozásához használt.

![A tábla létrehozására szolgáló képernyő képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>További lépések

* [Tanulási a köteleknek, a hortonworks – tesztkörnyezet](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Az Apache Hadoop-oktatóanyag – HDP – első lépések](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
