---
title: A Data Lake tools a Hortonworks védőfal - Azure HDInsight Visual Studio |} Microsoft Docs
description: További tudnivalók az Azure Data Lake tools for Visual Studio használata a helyi virtuális gépen futó Hortonworks védőfal. Ezekkel az eszközökkel hozhat létre, és a a védőfal, és a nézet feladatkiemenetét és a korábbi Hive és a Pig feladatok futtatásához.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: a4c1f5a8100d5d4017e56ef129aa4f4826746868
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886731"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Az Azure Data Lake tools használja a Visual Studio és a Hortonworks védőfal

Azure Data Lake az általános Hadoop-fürtök használata eszközöket tartalmazza. Ez a dokumentum nyújt a Data Lake tools használata a Hortonworks védőfal szükséges lépéseket a helyi virtuális gépen futó.

A a Hortonworks védőfal lehetővé teszi, hogy helyileg dolgozik hadooppal a fejlesztési környezet. Ha olyan megoldást fejlesztett ki, és léptékű telepíteni, majd áthelyezheti egy HDInsight-fürthöz.

## <a name="prerequisites"></a>Előfeltételek

* A Hortonworks védőfal, a virtuális gépen futó a fejlesztési környezetet. Ez a dokumentum lett írva, és a védőfal Oracle VirtualBox-beli tesztelése. A védőfal beállításával kapcsolatos információkért lásd: a [Ismerkedjen meg a Hortonworks védőfal.](hadoop/apache-hadoop-emulator-get-started.md) a dokumentum.

* A Visual Studio 2013, Visual Studio 2015-öt vagy a Visual Studio 2017 (minden kiadás).

* A [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1-es vagy újabb.

* A [Azure Data Lake tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>A védőfal jelszavak beállítása

Győződjön meg arról, hogy fut-e a Hortonworks védőfal. Ezután kövesse a [Ismerkedés a Hortonworks védőfal](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentum. Ezeket a lépéseket a jelszó beállítása az SSH `root` fiókot, és az Ambari `admin` fiók. Ezek a jelszavak használják, a Visual Studio eszközből a védőfal való csatlakozás esetén.

## <a name="connect-the-tools-to-the-sandbox"></a>Az eszközök csatlakozni a védőfal

1. Nyissa meg a Visual Studio, válassza ki **nézet**, majd válassza ki **Server Explorer**.

2. A **Server Explorer**, kattintson a jobb gombbal a **HDInsight** bejegyzést, és válassza **HDInsight emulátoron kapcsolódás**.

    ![Képernyőfelvétel a Server Explorer eszközben a Kapcsolódás a HDInsight emulátoron kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Az a **HDInsight emulátoron kapcsolódás** párbeszédpanelen adja meg a jelszót az Ambari konfigurált.

    ![A kijelölt mezőbe a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

4. Használja a **jelszó** mezőben adja meg a jelszót a konfigurálta a `root` fiók. Az alapértelmezett értéket a többi mezőt hagyja.

    ![A kijelölt mezőbe a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

5. Várjon, amíg befejeződik a szolgáltatások érvényesítéshez. Bizonyos esetekben a érvényesítése sikertelen, és felszólítja a konfiguráció frissítése. Ha az érvényesítés meghiúsul, válassza ki a **frissítés**, és várja meg, a konfiguráció és az ellenőrzés befejezéséhez a szolgáltatás.

    ![A kijelölt frissítés gombra a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > A frissítési folyamat Ambari használatával módosíthatja a Hortonworks védőfal konfigurációját, és mi az elvárás szerint a Data Lake tools for Visual Studio.

6. Érvényesítési befejeződését követően válassza ki a **Befejezés** konfigurálásának befejezéséhez.
    ![A kijelölt a Befejezés gombra a párbeszédpanel képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > Attól függően, hogy a fejlesztési környezetet, és a virtuális gép számára lefoglalt memória sebességétől konfigurálása, és ellenőrizze a szolgáltatások több percet is igénybe vehet.

Ezeket a lépéseket követve után most már rendelkezik egy **helyi HDInsight-fürt** a Server Explorer eszközben bejegyzés alatt a **HDInsight** szakasz.

## <a name="write-a-hive-query"></a>Hive-lekérdezések írása

Hive SQL-szerű lekérdezésnyelvet (HiveQL) biztosít a strukturált adatok használata. Az alábbi lépések segítségével megtudhatja, hogyan igény lekérdezéseinek futtatásához a helyi fürthöz.

1. A **Server Explorer**, kattintson a jobb gombbal a helyi fürthöz, amelyeket a korábban hozzáadott bejegyzést, és válassza **Hive lekérdezés írása**.

    ![Képernyőfelvétel a Server Explorer, a kiemelt Hive lekérdezés írása](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Megnyílik egy új lekérdezési ablak. Itt is gyorsan írása, és küldje el a lekérdezést a helyi fürthöz.

2. Az új lekérdezési ablak adja meg a következő parancsot:

        select count(*) from sample_08;

    Válassza ki a lekérdezés futtatásához **Submit** az ablak tetején. Hagyja meg az egyéb értékek (**kötegelt** és a kiszolgáló neve), az alapértelmezett értékeket.

    ![Képernyőkép a lekérdezési ablakban, a Küldés gomb](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Használhatja a legördülő menü mellett **Submit** kiválasztásához **speciális**. Speciális beállítások lehetővé teszik a további lehetőségeket nyújtanak, ha a feladat elküldéséhez.

    ![Parancsfájl elküldése képernyőkép párbeszédpanel](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. A lekérdezés elküldése után megjelenik a feladat állapotát. A feladat állapotát a feladat információit jeleníti meg, azt Hadoop dolgozza fel. **Feladat állapota** a állapotát mutatja meg a feladatot. Az állapot rendszeresen frissül, vagy az ikon segítségével manuálisan frissítse a állapotát.

    ![Képernyőfelvétel a feladat megtekintése párbeszédpanelen, a kijelölt feladat állapota](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Miután a **feladatállapotot** vált, **befejezett**, egy irányított aciklikus diagramhoz (DAG) jelenik meg. Ezen a diagramon ismerteti a végrehajtás elérési utat, amely a Tez lett határozza meg a Hive-lekérdezés feldolgozása közben. Tez a alapértelmezett-végrehajtó motor a Hive a helyi fürtön.

    > [!NOTE]
    > Tez is az alapértelmezett beállítás használata Linux-alapú HDInsight-fürtök esetén. A Windows-alapú HDInsight alapértelmezett nincs. A használatára, hozzá kell adnia a sor `set hive.execution.engine = tez;` a Hive-lekérdezést elejére.

    Használja a **Job Output** hivatkozás eredményének megtekintéséhez. Ebben az esetben is 823, a sample_08 tábla sorainak száma. A feladat diagnosztikai információ segítségével tekintheti a **Job Log** és **YARN naplók letöltése** hivatkozásokat.

4. Futtathat Hive-feladatok interaktív módosításával a **kötegelt** mezőről **interaktív**. Válassza ki **Execute**.

    ![A kijelölt képernyőkép interaktív és végrehajtási gombok](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Az interaktív lekérdezések az adatfolyamokat a feldolgozás során generált kimeneti naplót a **hiveserver2-n kimeneti** ablak.

    > [!NOTE]
    > Az információ megegyezik, amely elérhető a **Job Log** hivatkozás egy feladat befejezése után.

    ![Képernyőkép a kimeneti naplót](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Hive-projekt létrehozása

A projekt több Hive parancsfájl tartalmazó is létrehozhat. A projekt használhatók lehetnek kapcsolódó parancsfájlokat vagy parancsfájlok tárolása egy verziókezelő rendszert szeretné.

1. A Visual Studio válassza **fájl**, **új**, majd **projekt**.

2. A projektek listájában bontsa ki **sablonok**, bontsa ki a **Azure Data Lake**, majd válassza ki **struktúra (HDInsight)**. Válassza ki a listáról a sablonok **minta Hive**. Adja meg a nevét és helyét, majd válassza ki **OK**.

    ![Képernyőfelvétel az új projekt ablakról, Azure Data Lake, a HIVE, a minta Hive és a OK kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

A **minta Hive** -projekt tartalmazza két parancsfájlok **WebLogAnalysis.hql** és **SensorDataAnalysis.hql**. Elküldheti a ezen parancsfájlok használatával azonos **Submit** gombra az ablak tetején.

## <a name="create-a-pig-project"></a>A Pig-projekt létrehozása

Hive egy SQL-szerű nyelv biztosít a strukturált adatok használata, amíg a Pig adatokon átalakítások elvégzésével működik. Pig biztosít egy nyelv (a Pig latin betűs), amely lehetővé teszi átalakítások adatcsatorna fejlesztéséhez. A Pig használata a helyi fürthöz, kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio, és válassza ki **fájl**, **új**, majd **projekt**. Bontsa ki a projektek listája, **sablonok**, bontsa ki a **Azure Data Lake**, majd válassza ki **Pig (HDInsight)**. Válassza ki a listáról a sablonok **Pig alkalmazás**. Adjon meg egy nevet, a helyre, és válassza ki **OK**.

    ![Képernyőfelvétel az új projekt ablakról, az Azure Data Lake, a Pig, a Pig alkalmazás és az OK gombra a kijelölt](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Adja meg a következő szöveget a tartalmát a **script.pig** projekthez létrehozott fájlt.

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

    Pig más nyelvű Hive-nál, amíg a feladatok futtatásának módját mindkét nyelven, konzisztensek keresztül a **Submit** gombra. Válassza a legördülő lista melletti **Submit** egy speciális Küldés párbeszédpanel megjeleníti a Pig-feladatokkal.

    ![Parancsfájl elküldése képernyőkép párbeszédpanel](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. A feladat állapotát és a kimeneti is látható, ugyanaz, mint a Hive-lekérdezést.

    ![Képernyőkép a Pig projekt](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Feladatok megtekintése

A Data Lake tools is engedélyezi, hogy könnyen tekintse meg a Hadoop futó feladatok. Az alábbi lépések segítségével tekintse meg a helyi fürtön futó feladatok.

1. A **Server Explorer**, kattintson a jobb gombbal a helyi fürthöz, és válassza **feladatok megtekintése**. A fürthöz benyújtott feladatok listája jelenik meg.

    ![Képernyőfelvétel a Server Explorer, a kiemelt feladatok megtekintése](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. A feladatok listáját válassza ki egy, a feladat részleteinek megtekintéséhez.

    ![Képernyőfelvétel a feladat böngészőben valamelyik, a kiemelt feladatok](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    A megjelenő információk eredményének megtekintéséhez és naplózza az adatokat, beleértve a Hive vagy Pig lekérdezés futtatása után megjelenő hasonlít.

3. Módosítja, és küldje el újra a feladatot, itt.

## <a name="view-hive-databases"></a>Hive adatbázisok megtekintése

1. A **Server Explorer**, bontsa ki a **helyi HDInsight-fürt** bejegyzést, majd bontsa ki a **Hive adatbázisokat**. A **alapértelmezett** és **xademo** a helyi fürtön lévő adatbázisok jelennek meg. Egy adatbázis bővíteni jeleníti meg a táblák az adatbázisban.

    ![Képernyőfelvétel a Server Explorer eszközben kibontva adatbázisok](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. A tábla oszlopainak egy táblázat kibontása jeleníti meg. Az adatok gyors megtekintéséhez kattintson a jobb gombbal egy táblát, és válassza ki **View Top 100 sor**.

    ![Képernyőfelvétel a Server Explorer eszközben kibontva tábla és nézet kijelölt Top 100 sorok](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Adatbázis és tábla tulajdonságai

Egy adatbázis vagy táblázat tulajdonságainak megtekintése Kiválasztása **tulajdonságok** megjeleníti a kijelölt elem részleteket a Tulajdonságok ablak. Például olvassa el az alábbi képernyőfelvételen látható módon:

![Képernyőfelvétel a Tulajdonságok ablak](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Tábla létrehozása

Hozzon létre egy táblát, kattintson a jobb gombbal egy adatbázist, és válassza **Create Table**.

![Képernyőfelvétel a Server Explorer eszközben a Create Table kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

A tábla űrlap használatával is létrehozhat. Az alábbi képernyőfelvételen alján megtekintheti a nyers HiveQL a tábla létrehozásához használt.

![Képernyőkép a tábla létrehozásához használt űrlap](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>További lépések

* [Az a Hortonworks védőfal drótkötelek tanulási](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop oktatóanyag – első lépések HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
