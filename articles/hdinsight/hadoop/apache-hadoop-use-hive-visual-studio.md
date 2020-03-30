---
title: Az Apache Hive & A Data Lake eszközei a Visual Studio-hoz – Azure HDInsight
description: Ismerje meg, hogyan használhatja a Data Lake eszközöket a Visual Studio számára az Apache Hive-lekérdezések apache Hadoop használatával az Azure HDInsight szolgáltatásban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687804"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studióval

Ismerje meg, hogyan használhatja a Data Lake eszközöket a Visual Studio apache hive lekérdezéséhez. A Data Lake-eszközök lehetővé teszik a Hive-lekérdezések egyszerű létrehozását, elküldését és figyelését az Apache Hadoop számára az Azure HDInsight ban.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Az elem létrehozásáról az [Apache Hadoop-fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával című témakörben](./apache-hadoop-linux-tutorial-get-started.md)olvashat.

* [Visual Studio](https://visualstudio.microsoft.com/vs/). A cikkben ismertetett lépések a Visual Studio 2019-et használják.

* HDInsight-eszközök a Visual Studio vagy az Azure Data Lake-eszközök visual studióhoz. Az eszközök telepítéséről és konfigurálásáról a [Data Lake Tools for Visual Studio című](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)témakörben talál további információt.

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Apache Hive-lekérdezések futtatása a Visual Studio használatával

Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Eseti lekérdezések létrehozása.
* Hozzon létre egy Hive-alkalmazást.

### <a name="create-an-ad-hoc-hive-query"></a>Alkalmi Hive-lekérdezés létrehozása

Az ad hoc lekérdezések **kötegelt** vagy interaktív módban is **végrehajthatók.**

1. Indítsa el **a Visual Studio alkalmazást,** és válassza **a Folytatás kód nélkül**lehetőséget.

2. A **Kiszolgálókezelőben**kattintson a jobb gombbal az **Azure-ra,** válassza a Csatlakozás a Microsoft **Azure-előfizetéshez lehetőséget...** és fejezze be a bejelentkezési folyamatot.

3. Bontsa ki a **HDInsight**csomópontot, kattintson a jobb gombbal arra a fürtre, amelyben futtatni szeretné a lekérdezést, majd válassza **a Hive-lekérdezés írása parancsot.**

4. Adja meg a következő struktúralekérdezést:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Válassza a **Végrehajtás** lehetőséget. A végrehajtási mód alapértelmezés szerint **interaktív.**

    ![Interaktív Hive-lekérdezés végrehajtása, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Ha ugyanazt a lekérdezést **Köteg** módban szeretné futtatni, kapcsolja be a legördülő listát **interaktívról** **kötegre.** A végrehajtási gomb **Execute** -ról **Elküldés**parancsra változik.

    ![Köteghive-lekérdezés küldése, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például a `SELECT * FROM`beírást írja be, az IntelliSense felsorolja az összes javasolt táblanevet. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják. Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

7. A lekérdezés eszköztárán (a lekérdezés lap alatti és a lekérdezés szövege feletti területen) válassza a **Küldés**lehetőséget, vagy válassza a **Küldés** gomb melletti lekéréses nyilat, és válassza a **Lehívási** lista Speciális elemét. Ha az utóbbi lehetőséget választja,

8. Ha a speciális küldési beállítást választotta, konfigurálja a **Feladat nevét,** **az Argumentumokat**, a **További konfigurációk at**és az **Állapotkönyvtárat** a **Parancsfájl elküldése** párbeszédpanelen. Ezután válassza **a Küldés lehetőséget.**

    ![Script küldése párbeszédpanel, HDInsight Hadoop Hive lekérdezés](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Hive alkalmazás létrehozása

Hive-lekérdezés futtatása Hive-alkalmazás létrehozásával, kövesse az alábbi lépéseket:

1. Nyissa meg **a Visual Studio**alkalmazást .

2. A **Start** ablakban válassza **az Új projekt létrehozása**lehetőséget.

3. Az **Új projekt létrehozása** ablak Sablonok **keresése** mezőjébe írja be a *Hive*parancsot. Ezután válassza **a Hive-alkalmazás** és a **Tovább**lehetőséget.

4. Az **Új projekt konfigurálása** ablakban adja meg a **Projekt nevét,** jelölje ki vagy hozza létre az új projekt **helyét,** majd válassza a **Létrehozás lehetőséget.**

5. Nyissa meg a projekttel létrehozott **Script.hql** fájlt, és illessze be a következő HiveQL utasításokba:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Ezek a kijelentések a következő műveleteket követik:

    * `DROP TABLE`: Törli a táblát, ha létezik.

    * `CREATE EXTERNAL TABLE`: Új "külső" táblát hoz létre a Hive-ban. A külső táblák csak a tábladefiníciót tárolják hive.External tables only store the table definition in Hive. (Az adatok az eredeti helyen maradnak.)

        > [!NOTE]  
        > Külső táblákat kell használni, ha azt várja, hogy az alapul szolgáló adatokat egy külső forrásból, például egy MapReduce feladat vagy egy Azure-szolgáltatás frissíti.
        >
        > A külső tábla eldobása **nem** törli az adatokat, csak a tábladefiníciót.

    * `ROW FORMAT`: Közli a Hive-val, hogyan van formázva az adatok. Ebben az esetben az egyes naplók mezőit szóköz választja el egymástól.

    * `STORED AS TEXTFILE LOCATION`: Közli a Hive-val, hogy az adatok a *példa/adatkönyvtárban* tárolódnak, és hogy szövegként tárolódnak.

    * `SELECT`: Az összes olyan sor `t4` számát jelöli `[ERROR]`ki, ahol az oszlop tartalmazza az értéket . Ez az utasítás `3`a értékét adja vissza, mert három sor tartalmazza ezt az értéket.

    * `INPUT__FILE__NAME LIKE '%.log'`: Azt mondja a Hive-nak, hogy csak a .log végződésbe végződő fájlokból adja vissza az adatokat. Ez a záradék az adatokat tartalmazó *sample.log* fájlra korlátozza a keresést.

6. A lekérdezésfájl eszköztárán (amely az ad-hoc lekérdezési eszköztárhoz hasonló megjelenésű) válassza ki azt a HDInsight-fürtöt, amelyet ehhez a lekérdezéshez használni kíván. Ezután módosítsa **az interaktív** **kötegelt** (ha szükséges), és válassza **a Küldés** futtatni az állításokat hive feladatként.

   Megjelenik **a Hive-feladat összegzése,** és megjeleníti a futó feladatadatait. A **Frissítés** hivatkozással frissítheti a feladatadatait, amíg a **feladat állapota** befejezettre nem **változik.**

   ![Befejezett Hive feladat összegzése, Hive alkalmazás, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Válassza **a Feladat kimenete** lehetőséget a feladat kimenetének megtekintéséhez. Ez `[ERROR] 3`a kijelző , amely a lekérdezés által visszaadott érték.

### <a name="additional-example"></a>További példa

A következő példa az `log4jLogs` előző eljárásban létrehozott táblára, a [Hive-alkalmazás létrehozása parancsra](#create-a-hive-application)támaszkodik.

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal a fürtre, és válassza **a Hive-lekérdezés írása parancsot.**

2. Adja meg a következő struktúralekérdezést:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek a kijelentések a következő műveleteket követik:

    * `CREATE TABLE IF NOT EXISTS`: Létrehoz egy táblát, ha még nem létezik. Mivel `EXTERNAL` a kulcsszó nincs használatban, ez az utasítás belső táblát hoz létre. A belső táblák a Hive adattárházban tárolódnak, és a Hive kezeli.

        > [!NOTE]  
        > A `EXTERNAL` tábláktól eltérően a belső táblák eldobása az alapul szolgáló adatokat is törli.

    * `STORED AS ORC`: Az adatokat *optimalizált soroszlop* (ORC) formátumban tárolja. Az ORC egy rendkívül optimalizált és hatékony formátum a Hive-adatok tárolására.

    * `INSERT OVERWRITE ... SELECT`: Kijelöli a `log4jLogs` tartalmazó `[ERROR]`sorokat a táblázatból, `errorLogs` majd beszúrja az adatokat a táblába.

3. Szükség esetén módosítsa **az interaktív** **tanelemet kötegre,** majd válassza a **Küldés**lehetőséget.

4. Annak ellenőrzéséhez, hogy a feladat létrehozta-e a táblát, nyissa meg a **Kiszolgálókezelőt,** és bontsa ki az **Azure** > **HDInsight**alkalmazást. Bontsa ki a HDInsight-fürtöt, majd bontsa ki a **Hive-adatbázisok** > **alapértelmezett csomópontját.** Az **errorLogs** tábla és a **log4jLogs** tábla szerepel a listában.

## <a name="next-steps"></a>További lépések

Amint láthatja, a Visual Studio HDInsight-eszközei vel egyszerűen dolgozhat a HIVe-lekérdezésekkel a HDInsight-on.

* A HIVE-ről a HDInsightban található Hive-ről a [Mi az Apache Hive és a HiveQL az Azure HDInsight-on című témakörben talál általános információt?](hdinsight-use-hive.md)

* A Hadoop hdinsight-alapú használatának egyéb módjairól a [MapReduce használata az Apache Hadoopban a HDInsight-on című témakörben talál](hdinsight-use-mapreduce.md) további információt.

* A Visual Studio HDInsight-eszközeiről a[Data Lake-eszközök használata a Visual Studio használatához az Azure HDInsighthoz való csatlakozáshoz és az Apache Hive-lekérdezések futtatásához](apache-hadoop-visual-studio-tools-get-started.md) című témakörben talál további információt.
