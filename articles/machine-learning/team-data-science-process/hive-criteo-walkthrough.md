---
title: Az Azure HDInsight Hadoop-fürt használata 1 TB-os adatkészleten – Csapatadat-elemzési folyamat
description: A csapatadat-elemzési folyamat használata egy HDInsight-Hadoop-fürtöt használó teljes körű forgatókönyvhöz egy nagy (1 TB) nyilvánosan elérhető adatkészlet használatával létrehozott és üzembe helyezhető modell tamásként
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1198d3cc7ccc0013e7c894488027d8e162470247
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677602"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>A csapatadat-elemzési folyamat működés közben – Egy Azure HDInsight Hadoop-fürt használata 1 TB-os adatkészleten

Ez a forgatókönyv bemutatja, hogyan használhatja a csapat adatelemzési folyamat egy azure HDInsight Hadoop-fürt teljében egy [Azure HDInsight-hadoop-fürttel](https://azure.microsoft.com/services/hdinsight/) a nyilvánosan elérhető [Criteo-adatkészletek](https://labs.criteo.com/downloads/download-terabyte-click-logs/) egyikének tárolására, feltárására, szolgáltatásmérnökére és mintaadatainak tárolására. Az Azure Machine Learning segítségével bináris besorolási modellt hozhat létre ezeken az adatokon. Azt is bemutatja, hogyan teheti közzé az egyik modellt webszolgáltatásként.

Az iPython-jegyzetfüzetek is használhatók az ebben a forgatókönyvben bemutatott feladatok elvégzéséhez. Azok a felhasználók, akik szeretnék kipróbálni ezt a módszert, a [Hive ODBC-kapcsolat témakörhasználatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) konzultáljon a Criteo forgatókönyvvel.

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Criteo adatkészlet leírása
A Criteo adatok egy kattintás-előrejelzési adatkészlet, amely 370 GB gzip tömörített TSV fájlok (~ 1,3 TB tömörítetlen), amely több mint 4,3 milliárd rekordot. Ez a [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)által rendelkezésre bocsátott 24 napos kattintási adatokból származik. Az adatszakértők kényelme érdekében a kísérletre rendelkezésünkre álló adatokat kibontottuk.

Az adatkészlet minden rekordja 40 oszlopot tartalmaz:

* az első oszlop egy címkeoszlop, amely azt jelzi, hogy a felhasználó egy **hozzáadásra** (1. érték) kattint-e, vagy nem kattint egyre (0 érték)
* a következő 13 oszlop numerikus, és
* az utolsó 26 kategorikus oszlop

Az oszlopok anonimizáltak, és a következő felsorolt nevek et használjuk: "Col1" (a címkeoszlophoz) a "Col40" (az utolsó kategorikus oszlophoz).

Itt van egy részlet az első 20 oszlop két megfigyelés (sor) ebből az adatkészletből:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Az adatkészlet numerikus és kategorikus oszlopaiban is hiányoznak értékek. A hiányzó értékek kezelésének egyszerű módszerét ismerteti. Az adatok további részleteit a rendszer a Hive-táblákba való tárolásukkor vizsgálja meg.

**Definíció:** *Átkattintási arány (CTR):* Ez a mérőszám az adatokban lévő kattintások százalékos aránya. Ebben a Criteo adatkészletben a CTR körülbelül 3,3% vagy 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Példák előrejelzési feladatokra
Ebben a forgatókönyvben két mintaelőrejelzési probléma található:

1. **Bináris besorolás**: Előre jelzi, hogy a felhasználó rákattintott-e egy bővítményre:

   * 0. osztály: Nincs kattintás
   * 1. osztály: Kattintson
2. **Regresszió:** Megjósolja annak valószínűségét, hogy a felhasználói funkciókból hirdetéskattintás tanusol.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>HDInsight-Hadoop-fürt beállítása adatelemzéshez
> [!NOTE]
> Ez a lépés általában egy **felügyeleti** feladat.

Állítsa be az Azure Data Science-környezetet a HDInsight-fürtökkel rendelkező prediktív elemzési megoldások létrehozásához három lépésben:

1. [Tárfiók létrehozása:](../../storage/common/storage-account-create.md)Ez a tárfiók az Azure Blob Storage-ban tárolt adatok tárolására szolgál. A HDInsight-fürtökben használt adatok itt tárolódnak.
2. [Az Azure HDInsight Hadoop-fürtök testreszabása adatelemzéshez:](customize-hadoop-cluster.md)Ez a lépés létrehoz egy Azure HDInsight Hadoop-fürtöt 64 bites Anaconda Python 2.7-es állapotú fürttel az összes csomóponton. A HDInsight-fürt testreszabásakor két fontos lépést kell végrehajtani (ez a témakör ismerteti).

   * Az 1. Ez a tárfiók a fürtön belül feldolgozható adatok elérésére szolgál.
   * A fürt főcsomópontjának távoli elérésének engedélyezése annak létrehozása után. Ne feledje az itt megadott távelérési hitelesítő adatokat (eltér a fürt létrehozásakor megadott hitelesítő adatoktól): hajtsa végre az alábbi eljárásokat.
3. [Hozzon létre egy Azure Machine Learning Studio (klasszikus) munkaterületet:](../studio/create-workspace.md)Ez az Azure Machine Learning-munkaterület gépi tanulási modellek létrehozásához használatos a HDInsight-fürt kezdeti adatfeltárása és mintavételezése után.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Adatok beéselése és felhasználása nyilvános forrásból
A [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) adatkészlet a hivatkozásra kattintva, a használati feltételek elfogadásával és egy név megadásával érhető el. A pillanatkép itt látható:

![Criteo feltételek elfogadása](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kattintson **a Folytatás letöltéshez** gombra az adatkészletről és annak elérhetőségéről.

Az adatok egy [Azure blob-tároló](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) helyen találhatók: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. A "wasb" az Azure Blob Storage helyére vonatkozik.

1. Az Azure blob storage-ban lévő adatok három almappából állnak a kibontott adatokból.

   1. A *nyers almappa/darabszám/* tartalmazza az adatok első\_21\_napját - 00 naptól a 20.
   2. A *nyers/vonat/* almappa egyetlen adatnapból áll, 21.\_
   3. A *nyers/teszt/* almappa két napi adatból\_áll,\_a 22.
2. A nyers gzip adatok is rendelkezésre állnak a fő mappában *nyers /* mint day_NN.gz, ahol NN megy 00-23.

A struktúratártáblák létrehozásakor a forgatókönyv későbbi részében ismertetünk egy másik, az adatok elérésére, feltárására és modellezésére vonatkozó alternatív megközelítést.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Bejelentkezés a fürtfejnodba
A fürt fejnokhálózatba való bejelentkezéshez használja az [Azure Portalon](https://ms.portal.azure.com) a fürt megkereséséhez. Kattintson a bal oldali HDInsight elefánt ikonra, majd kattintson duplán a fürt nevére. Nyissa meg a **Konfiguráció** lapot, kattintson duplán a lap alján lévő CONNECT ikonra, és amikor a rendszer kéri, adja meg a távelérési hitelesítő adatait, és a fürt csomópontjához lépjen.

Itt van, amit egy tipikus első bejelentkezés a fürt headnode néz ki:

![Bejelentkezés a fürtbe](./media/hive-criteo-walkthrough/Yys9Vvm.png)

A bal oldalon van a "Hadoop Command Line", amely a mi igásló az adatok feltárása. Figyelje meg a két hasznos URL-t - "Hadoop Yarn Status" és a "Hadoop Name Node". A fonal állapotÁNAK URL-címe a feladat előrehaladását mutatja, és a névcsomópont URL-címe részletesen ismerteti a fürt konfigurációját.

Most már be van állítva, és készen áll a forgatókönyv első részének megkezdésére: a Hive használatával történő adatfeltárás és az Azure Machine Learningre való felkészülés.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Hive-adatbázis és -táblák létrehozása
Criteo-adatkészletünk höz Hive-táblák létrehozásához nyissa meg a ***Hadoop parancssort*** a főcsomópont asztalán, és írja be a Hive könyvtárat a parancs beírásával

    cd %hive_home%\bin

> [!NOTE]
> Futtassa az összes Hive-parancsokebben a forgatókönyvben a Hive bin/ könyvtár parancs. Ez automatikusan gondoskodik az útvonalproblémákról. A "Hive könyvtársor", a "Hive bin/ directory prompt" és a "Hadoop parancssor" kifejezéseket szinonimaként használhatja.
>
> [!NOTE]
> Bármely Hive-lekérdezés végrehajtásához mindig használhatja a következő parancsokat:
>
>        cd %hive_home%\bin
>        hive

Miután a Hive REPL megjelenik egy "kaptár >" jel, egyszerűen kivágja és illessze be a lekérdezést, hogy hajtsa végre.

A következő kód létrehoz egy adatbázist "criteo", majd létrehoz négy táblát:

* *a* 00-tól\_a 20.\_
* a *table for use as the train dataset* 21.\_
* két *táblázat a* \_22.\_

A tesztadatkészlet felosztása két különböző táblára, mert az egyik nap ünnepnap. A cél annak meghatározása, hogy a modell képes-e észlelni az átkattintási arányközötti különbségeket az üdülésés a nem ünnepnapok között.

A [parancsfájlminta&#95;struktúra&#95;hozzon létre&#95;criteo&#95;adatbázis&#95;és&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) itt jelenik meg a kényelem érdekében:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Ezek a táblák külső, így rámutathat az Azure Blob Storage (wasb) helyekre.

**Bármilyen Hive-lekérdezés kétféleképpen hajtható végre:**

* **A Hive REPL parancssor használata**: Az első a "hive" parancs kiadása, valamint a lekérdezés másolása és beillesztése a Hive REPL parancssorból:

        cd %hive_home%\bin
        hive

     Most a REPL parancssorában a lekérdezés kivágása és beillesztése végrehajtja azt.
* **Lekérdezések mentése fájlba és a parancs végrehajtása**: A második a lekérdezések mentése egy '.hql' fájlba ([minta&#95;struktúra&#95;hozzon létre&#95;criteo&#95;adatbázis&#95;és&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)), majd adja ki a következő parancsot a lekérdezés végrehajtásához:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Adatbázis- és táblalétrehozás megerősítése
Ezután erősítse meg az adatbázis létrehozását a Hive bin/ könyvtárparancs következő paranccsal:

        hive -e "show databases;"

Ez ad:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Ez megerősíti az új adatbázis, a "criteo" létrehozását.

Ha meg szeretné tekinteni, hogy milyen táblákjöttek létre, egyszerűen adja ki a parancsot itt a Hive bin/ könyvtárparancsból:

        hive -e "show tables in criteo;"

Ekkor a következő kimenetet kell látnia:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Adatok feltárása a Hive-ban
Most már készen áll néhány alapvető adatfeltárásra a Hive-ban. Először számolja meg a példák számát a vonat- és tesztadattáblákban.

### <a name="number-of-train-examples"></a>A vonatra példák száma
A minta&#95;[a kaptár&#95;a vonat számlálásának&#95;&#95;a&#95;&#95;a példák.hql táblázat&#95;a példák.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) táblázat tartalmát a következők ben mutatja be:

        SELECT COUNT(*) FROM criteo.criteo_train;

Ez a hozam:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Másik lehetőségként a Hive bin/ könyvtárparancsból is kiadhatunk a következő parancsot:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Vizsgálati példák száma a két vizsgálati adatkészletben
Most számolja meg a két tesztadatkészletben lévő példák számát. A [minta&#95;kaptár&#95;a criteo-&#95;teszt száma&#95;&#95;nap&#95;22&#95;&#95;példák.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql)

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Ez a hozam:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

A szokásos módon a parancsfájlt a Hive bin/ könyvtársorból is meghívhatja a parancs kiadásával:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Végül a\_23.

Az ehhez szükséges parancs hasonló a bemutatotthoz (lásd [a mintát&#95;kaptár&#95;&#95;criteo&#95;tesztet&#95;napon&#95;23&#95;.hql):](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Ez ad:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Címkeeloszlás a vonat adatkészletében
A címke eloszlása a vonat adatkészlet érdekes. Ennek megtekintéséhez mutassa meg a minta tartalmát [&#95;kaptár&#95;a criteo&#95;címke&#95;a&#95;&#95;a table.hql fájl:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Ez adja meg a címke eloszlását:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

A pozitív címkék aránya körülbelül 3,3% (összhangban az eredeti adatkészlettel).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Egyes numerikus változók hisztogram-eloszlása a vonat adatkészletében
A Hive natív "hisztogram\_numerikus" függvényével megtudhatja, hogyan néz ki a numerikus változók eloszlása. Itt vannak a [tartalmát minta&#95;kaptár&#95;criteo&#95;hisztogram&#95;numeric.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Ez a következő:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

Az LATERAL VIEW - explode kombináció a Hive-ban arra szolgál, hogy a szokásos lista helyett SQL-szerű kimenetet hozzon létre. Ebben a táblázatban az első oszlop a raktárhely középpontjának, a második pedig a raktárhely gyakoriságának felel meg.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Egyes numerikus változók hozzávetőleges percentiles a vonat adatkészletében
A numerikus változók nál is érdekesak a hozzávetőleges percentilisek számításai. Hive natív "percentilis\_kb" ezt értünk. A minta&#95;[a hivatás&#95;a criteo&#95;hozzávetőleges&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) tartalma:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Ez a hozam:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A percentilisek eloszlása szorosan kapcsolódik általában bármely numerikus változó hisztogram-eloszlásához.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>A vonat adatkészletének egyes kategorikus oszlopaihoz megadott egyedi értékek száma
Az adatok feltárásának folytatásaként keresse meg néhány kategorikus oszlop esetében az általuk figyelembe vettek egyedi értékek számát. Ehhez mutassa meg [&#95;hive&#95;criteo minta tartalmát&#95;egyedi&#95;értékeket&#95;categoricals.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Ez a hozam:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 19M egyedi értékeket! Az ilyen nagy dimenziós kategorikus változók kódolására nem lehetséges olyan naiv technikák használata, mint az "egy forró kódolás". Különösen egy erőteljes, robusztus technika, a [Learning With Count,](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) amely hatékonyan kezeli ezt a problémát, magyarázható és bemutatkozik.

Végül nézd meg az egyedi értékek számát néhány más kategorikus oszlophoz is. A [minta&#95;a criteo&#95;az egyedi&#95;értékek et&#95;&#95;több&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) a következő tartalma:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Ez a hozam:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Ismét vegye figyelembe, hogy a Col20 kivételével az összes többi oszlopnak sok egyedi értéke van.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>A vonat adatkészletében lévő kategorikus változópárok közös előfordulási száma

A kategorikus változók párjainak számeloszlása is érdekes. Ezt a mintában lévő kód segítségével határozható [meg,&#95;a kaptár&#95;criteo&#95;párosítva&#95;kategorikus&#95;count.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Fordított sorrendben a grófok azok előfordulása, és nézd meg a top 15 ebben az esetben. Ez ad nekünk:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Az Azure Machine Learning adatkészleteinek lebontása
Miután megvizsgálta az adatkészleteket, és bebizonyította, hogyan kell elvégezni az ilyen típusú feltárása bármely változó (beleértve a kombinációkat is), le az adatkészletek, hogy az Azure Machine Learning modellek et lehet építeni. Emlékezzünk vissza, hogy a probléma középpontjában: adott egy sor példa attribútumok (jellemző értékek Col2 - Col40), megjósolni, ha Col1 egy 0 (nincs kattintás) vagy 1 (kattintás).

Ha le szeretné venni a betanítási és tesztelési adatkészletek az eredeti méret 1%-ára, használja a Hive natív RAND() függvényét. A következő parancsfájl, [&#95;minta struktúra&#95;criteo&#95;a&#95;a dataset.hql betanítási&#95;](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) a betanítási adatkészlethez:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Ez a hozam:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

A [parancsfájlminta&#95;kaptár&#95;criteo&#95;downsample&#95;teszt&#95;nap&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) nem azt a vizsgálati adatok, nap\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Ez a hozam:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Végül, a szkript [minta&#95;kaptár&#95;criteo&#95;downsample&#95;teszt&#95;nap&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) nem azt a vizsgálati adatok, nap\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Ez a hozam:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Ezzel készen áll arra, hogy a mintában szereplő vonat- és tesztadatkészleteket használja modellek létrehozásához az Azure Machine Learningben.

Van egy utolsó fontos összetevő, mielőtt az Azure Machine Learning, amely a count tábla vonatkozik. A következő alszakaszban a számlálási táblázat ot részletesen tárgyaljuk.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>Rövid vita a számlálási tábláról
Ahogy láttad, több kategorikus változónak magas a dimenziója. A forgatókönyvben egy hatékony technika, a [Learning With Count](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) nevű kódolni ezeket a változókat hatékony, robusztus módon jelenik meg. További információ erről a technikáról a megadott linken található.

>[!NOTE]
>Ebben a forgatókönyvben a hangsúly a számláló táblák használatával kompakt ábrázolása a nagy dimenziós kategorikus funkciók. Nem ez az egyetlen módja a kategorikus funkciók kódolásának; További információ az egyéb technikák, az érdekelt felhasználók nézd meg [egy-hot-kódolás](https://en.wikipedia.org/wiki/One-hot) és [a szolgáltatás kivonatolás](https://en.wikipedia.org/wiki/Feature_hashing).
>

A számlálási táblák létrehozásához használja a nyers/darabos mappában lévő adatokat. A modellezési szakaszban a felhasználók bemutatják, hogyan hozhat létre ezeket a számláló táblák at kategorikus funkciók a semmiből, vagy pedig egy előre elkészített számláló tábla a feltárások. A következőkben, amikor az "előre elkészített számláló táblák" hivatkoznak, azt jelenti, a megadott számláló táblák használatával. A táblázatok elérésének részletes útmutatása a következő szakaszban található.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Modell készítése az Azure Machine Learning segítségével
Modellépítési folyamatunk az Azure Machine Learningben az alábbi lépéseket követi:

1. [A Hive-táblákból származó adatok beszerezhetése az Azure Machine Learningbe](#step1)
2. [A kísérlet létrehozása: tisztítsa meg az adatokat, és legyen olyan funkció, amely számláló táblákat tartalmaz](#step2)
3. [Építeni, vonat, és a pontszám a modell](#step3)
4. [A modell értékelése](#step4)
5. [A modell közzététele webszolgáltatásként](#step5)

Most már készen áll modellek készítésére az Azure Machine Learning stúdióban. A csoporton ként kiválasztott adatok a fürt hive-táblákként kerülnek mentésre. Az Azure Machine Learning **Import Data** modul segítségével olvassa el ezeket az adatokat. A fürt tárfiókhoz való hozzáféréshez szükséges hitelesítő adatok a következőkben találhatók.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>1. lépés: Adatok importálása és kiválasztása egy gépi tanulási kísérlethez az Azure Machine Learningbe az Azure Machine Learningbe.
Kezdje a **+ÚJ** -> **KÍSÉRLET** -> **üres kísérlet kiválasztásával.** Ezután a bal felső sarokban lévő **Keresőmezőből** keressen rá az "Adatok importálása" kifejezésre. Húzza az **Adatok importálása** modult a kísérlet vászonjára (a képernyő középső részére) az adateléréshez való modul használatához.

Így néz ki az **Adatok importálása** a Hive-táblából történő adatok beszerzése közben:

![Adatok importálása adatok bejut](./media/hive-criteo-walkthrough/i3zRaoj.png)

Az **Adatok importálása** modul esetében az ábrán megadott paraméterek értékei csak példák a megadandó értékekre. Íme néhány általános útmutatás az **Adatok importálása** modul paraméterkészletének kitöltéséhez.

1. Válassza a "Hive lekérdezés" lehetőséget az **adatforráshoz**
2. A **Hive adatbázis lekérdezési** mezőben egy\_egyszerű\_SELECT\_*\_FROM <az adatbázis name.your tábla neve> - elég.
3. **Hcatalog szerver URI**: Ha a fürt "abc",\/akkor ez egyszerűen: https: /abc.azurehdinsight.net
4. **Hadoop felhasználói fiók neve**: A fürt üzembe helyezéskor kiválasztott felhasználónév. (NEM a távelérési felhasználónév!)
5. **Hadoop felhasználói fiók jelszava**: A fürt üzembe helyezéskor kiválasztott felhasználónév jelszava. (NEM a távelérési jelszó!)
6. **Kimeneti adatok helye**: Válassza az "Azure" lehetőséget
7. **Azure Storage-fiók neve:** A fürthöz társított tárfiók
8. **Azure Storage-fiók kulcsa:** A fürthöz társított tárfiók kulcsa.
9. **Azure-tároló neve:** Ha a fürt neve "abc", akkor ez egyszerűen "abc", általában.

Miután az **Adatok importálása** befejezi az adatok beszerzését (a modulon zöld pipa jelenik meg), mentse ezeket az adatokat adatkészletként (az Ön által választott névvel). Hogy néz ki ez:

![Adatok importálása adatok mentése](./media/hive-criteo-walkthrough/oxM73Np.png)

Kattintson a jobb gombbal az **Adatok importálása** modul kimeneti portjára. Ez a **Mentés adatkészletként** és a **Visualize** beállítást mutatja. A **Megjelenítés** beállítás, ha rákattint, 100 sornyi adatot jelenít meg, valamint egy jobb oldali panelt, amely néhány összefoglaló statisztika esetén hasznos. Az adatok mentéséhez egyszerűen válassza **a Mentés adatkészletként** lehetőséget, és kövesse az utasításokat.

Ha ki szeretné jelölni a gépi tanulási kísérletben való használatra szánt mentett adatkészletet, keresse meg az adatkészleteket az alábbi ábrán látható **Keresőmező** használatával. Ezután egyszerűen írja be az adatkészletet részben megadott nevet, és húzza az adatkészletet a főpanelre. Ha a főpanelre dobja, kiválasztja a gépi tanulási modellezéshez.

![Adatkészlet húzása a főpanelre](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Ehhez mind a vonat, mind a tesztadatkészletek esetében. Ne felejtse el használni az erre a célra megadott adatbázisnevet és táblaneveket. Az ábrán használt értékek kizárólag illusztrációs célokat szolgálnak.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>2. lépés: Hozzon létre egy kísérletet az Azure Machine Learningben a kattintások / kattintások nélküli kattintások előrejelzéséhez
Az Azure Machine Learning Studio (klasszikus) kísérletünk a következőképpen néz ki:

![Gépi tanulási kísérlet](./media/hive-criteo-walkthrough/xRpVfrY.png)

Most vizsgálja meg a kísérlet legfontosabb összetevőit. Először húzza a mentett vonat- és tesztadatkészleteket a kísérleti vásznunkra.

#### <a name="clean-missing-data"></a>Hiányzó adatok törlése
A **Tiszta hiányzó adatok** modul azt teszi, amit a neve is sugall: a hiányzó adatokat a felhasználó által megadott módon tisztítja. Nézd meg ezt a modult, hogy ezt lásd:

![Hiányzó adatok tisztítása](./media/hive-criteo-walkthrough/0ycXod6.png)

Itt válassza az összes hiányzó értéket 0-ra cseréli. Vannak más lehetőségek is, ami látható nézi a legördülő a modulban.

#### <a name="feature-engineering-on-the-data"></a>Szolgáltatás mérnöki az adatok
A nagy adatkészletek egyes kategorikus funkcióihoz több millió egyedi érték is lehet. Az ilyen nagy dimenziós kategorikus jellemzők képviseletére szolgáló naiv módszerek, mint például az egy forró kódolás, teljességgel kivitelezhetetlen. Ez a forgatókönyv bemutatja, hogyan használhatja a számláló funkciók at beépített Azure Machine Learning-modulok segítségével kompakt ábrázolása i nagy dimenziós kategorikus változók. A végeredmény egy kisebb modellméret, gyorsabb betanítási idő és teljesítménymérők, amelyek más technikák használatával összehasonlíthatók.

##### <a name="building-counting-transforms"></a>Épületszámlálási átalakítások
A számláló funkciók létrehozásához használja az Azure Machine Learningben elérhető **Build Counting Transform** modult. A modul így néz ki:

![Build Counting Transform](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![modul tulajdonságai Build Counting Transform modul](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Az **Oszlopok számlálása** mezőbe írja be azokat az oszlopokat, amelyeken a számlálókat végre kívánja hajtani. Ezek általában (mint említettük) nagy dimenziós kategorikus oszlopok. Ne feledje, hogy a Criteo adatkészlet 26 kategorikus oszlopot tartalmaz: a Col15-től a Col40-ig. Itt számíthat mindegyikre, és adja meg indexeit (15-től 40-ig vesszővel elválasztva, amint az látható).
>

A modul használata a MapReduce módban (nagy adatkészletek számára megfelelő), hozzáférésre van szüksége egy HDInsight Hadoop-fürthöz (a szolgáltatásfeltáráshoz használt modul is felhasználható erre a célra) és a hitelesítő adataihoz. Az előző ábrák azt mutatják be, hogy hogyan néznek ki a kitöltött értékek (cserélje le az illusztrációhoz megadott értékeket a saját használati esetére vonatkozó értékekre).

![Modul paraméterek](./media/hive-criteo-walkthrough/05IqySf.png)

Az előző ábra bemutatja, hogyan adja meg a bemeneti blob helyét. Ez a hely az épületszámláló táblák számára fenntartott adatokat tárolja.

Amikor ez a modul befejezi a futást, mentse az átalakítást későbbi használatra, kattintson a jobb gombbal a modulra, és válassza a **Mentés átalakításként** beállítást:

!["Mentés átalakításként" beállítás](./media/hive-criteo-walkthrough/IcVgvHR.png)

A fenti kísérleti architektúrában az "ytransform2" adatkészlet pontosan megfelel a mentett számláló transzformációnak. A kísérlet további részében feltételezzük, hogy az olvasó egy **Build Counting Transform** modult használt egyes adatokon a számlálások létrehozásához, és ezután ezekkel a számlálókkal számláló funkciókat hozhat létre a vonaton és a tesztadatkészleteken.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Annak kiválasztása, hogy milyen számlálási funkciókat vegyen fel a vonat ba és a tesztadatkészletekbe
Ha a számláló átalakítás kész, a felhasználó kiválaszthatja, hogy milyen funkciókat kell felvenni a betanítási és a vizsgálati adatkészletek segítségével **a Számláló tábla paraméterei** modul. A teljesség, ez a modul itt látható. De az egyszerűség érdekében valójában ne használjuk a kísérletünkben.

![Számlálótábla paramétereinek módosítása](./media/hive-criteo-walkthrough/PfCHkVg.png)

Ebben az esetben, mint látható, a log-odds kell használni, és a vissza-off oszlop figyelmen kívül hagyja. Olyan paramétereket is beállíthat, mint például a szeméttároló küszöbértéke, hány pszeudo-korábbi példa a simításhoz, és hogy használ-e laplaciai zajt vagy sem. Mindezek fejlett funkciók, és meg kell jegyezni, hogy az alapértelmezett értékek jó kiindulási pont a felhasználók számára, akik újak az ilyen típusú szolgáltatás generáció.

##### <a name="data-transformation-before-generating-the-count-features"></a>Adatátalakítás a számlálási funkciók létrehozása előtt
Most a hangsúly egy fontos pont átalakításáról szóló vonat és vizsgálati adatok előtt ténylegesen generáló gróf funkciókat. Két **R-parancsfájl végrehajtása** modulok at használnak, mielőtt a count transform alkalmazva van az adataink.

![R-parancsfájl-modulok végrehajtása](./media/hive-criteo-walkthrough/aF59wbc.png)

Itt az első R script:

![Első R-parancsfájl](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ez az R szkript átnevezi oszlopainkat a "Col1" névre "Col40"-ra. Ennek az az oka, hogy a számlálóátalakítás ennek a formátumnak a nevét várja.

A második R-parancsfájl kiegyenlíti a pozitív és negatív osztályok (1. és 0. osztály) közötti eloszlást a negatív osztály lefelé mintavételezésével. Az R szkript itt bemutatja, hogyan kell ezt csinálni:

![Második R-szkript](./media/hive-criteo-walkthrough/91wvcwN.png)

Ebben az egyszerű R szkriptben a "pos\_neg\_ratio" a pozitív és a negatív osztályok közötti egyensúly összegének beállítására szolgál. Ez azért fontos, mert javítása osztály egyensúlyhiány általában teljesítményelőnyök osztályozási problémák, ahol az osztály eloszlása ferde (emlékeztetnek arra, hogy ebben az esetben van 3,3% pozitív osztály és 96,7% negatív osztály).

##### <a name="applying-the-count-transformation-on-our-data"></a>A számlálási transzformáció alkalmazása az adatainkra
Végül **használhatja** az Átalakítás alkalmazása modult a számláló transzformációk alkalmazásához a vonaton és a teszt adatkészleteken. Ez a modul a mentett számláló átalakítást egy bemenetként, a betanítási vagy tesztelési adatkészleteket pedig a másik bemenetként veszi fel, és a számlálási funkciókkal rendelkező adatokat ad vissza. Itt látható:

![Átalakítás modul alkalmazása](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Egy részlet, hogy hogyan néznek ki a count funkciók
Ez tanulságos, hogy mi a gróf funkciók néz ki a mi esetünkben. Itt van egy részlet e:

![A Számlálás funkciói](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ez a részlet azt mutatja, hogy az oszlopok számított, akkor kap a grófok és log esélyek mellett minden releváns backoffs.

Most már készen áll egy Azure Machine Learning-modell létrehozásához ezekkel az átalakított adatkészletekkel. A következő szakaszban bemutatjuk, hogyan lehet ezt megtenni.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>3. lépés: Építsd, eddd és szerezd meg a modellt

#### <a name="choice-of-learner"></a>A tanuló választása
Először is, ki kell választanod egy tanulót. Tanulóként használj egy kétosztályos kiemelt döntési fát. A tanuló alapértelmezett lehetőségei a következők:

![Kétosztályos súlyozott döntési fa paraméterei](./media/hive-criteo-walkthrough/bH3ST2z.png)

A kísérlethez válassza ki az alapértelmezett értékeket. Az alapértelmezett értékek értelmesek, és jó módja annak, hogy gyors alapkonfigurációkat kapjon a teljesítményről. A teljesítmény a paraméterek sweepingével javítható, ha úgy dönt, hogy ha rendelkezik alaptervvel.

#### <a name="train-the-model"></a>A modell betanítása
A betanításhoz egyszerűen hívja meg a **Train Model** modul. A két bemenet a két osztály kiemelt döntési fa tanuló és a vonat adatkészlet. Ez itt látható:

![Betanítási modell modul](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>A modell pontozása
Miután rendelkezik egy betanított modell, készen áll a pontszám a teszt adatkészletet, és kiértékelni annak teljesítményét. Ehhez használja a **Score Model** modul látható az alábbi ábrán, valamint egy **kiértékelés modell** modul:

![A Score model (Modell montozása) modul](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>4. lépés: A modell kiértékelése
Végül elemezze a modell teljesítményét. Általában két osztály (bináris) besorolási probléma esetén a jó intézkedés az AUC. A görbe megjelenítéséhez csatlakoztassa a **Score Model** modult egy **Modell kiértékelése** modulhoz. A **Modell kiértékelése** modul **megjelenítésére** kattintva a következőhez hasonló ábra jelenik meg:

![Modul BDT-modelljének kiértékelése](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Bináris (vagy két osztály) besorolási problémák esetén az előrejelzési pontosság jó mértéke a Görbe alatti terület (AUC). A következő szakasz ban az eredmények et használja ezt a modellt a teszt adatkészletünkön. Kattintson a jobb gombbal a **Modell kiértékelése** modul kimeneti portjára, majd **jelenítse meg a megjelenítést.**

![Modell kiértékelése modul megjelenítése](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>5. lépés: A modell közzététele webszolgáltatásként
Az Azure Machine Learning-modell webes szolgáltatásokként való közzétételének lehetősége a minimális felhajtás mellett értékes funkció a széles körben elérhetővé tételhez. Ha ez megtörtént, bárki kezdeményezhet hívásokat a webszolgáltatás babemeneti adatokat, amelyekhez szükségük van előrejelzések, és a webszolgáltatás használja a modellt, hogy adja vissza ezeket az előrejelzéseket.

Először mentse betanított modellünket betanított modellobjektumként úgy, hogy a jobb gombbal a **Betanítási modell** modulra kattint, és használja a **Mentés betanított modellként** beállítást.

Ezután hozzon létre bemeneti és kimeneti portokat webszolgáltatásunkhoz:

* a bemeneti port az adatokat ugyanabban az űrlapon veszi át, mint azok az adatok, amelyekhez
* egy kimeneti port a pontozott címkéket és a kapcsolódó valószínűségeket adja vissza.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>A bemeneti port néhány adatsorának kijelölése
Célszerű egy SQL **Transformation** modul alkalmazása segítségével csak 10 sort kell kiválasztani a bemeneti port adataiként. Válassza ki csak ezeket a sorokat az adatok a mi bemeneti port segítségével az SQL lekérdezés itt látható:

![Bemeneti port adatai](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webszolgáltatás
Most már készen áll egy kis kísérlet futtatására, amely felhasználható a webes szolgáltatás közzétételére.

#### <a name="generate-input-data-for-webservice"></a>Bemeneti adatok létrehozása a webszolgáltatáshoz
Nullázási lépésként, mivel a számláló tábla nagy, hogy néhány sornyi tesztadatok és kimeneti adatok létrehozása belőle a count funkciókat. Ez a kimenet szolgálhat a bemeneti adatok formátuma a webszolgáltatás, ahogy itt látható:

![BDT bemeneti adatok létrehozása](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> A bemeneti adatformátumhoz használja a **Count Featurizer** modul kimenetét. Miután ez a kísérlet befejeződött a futás, mentse a kimenetet a **Count Featurizer** modul adatkészletként. Ez az adatkészlet a webszolgáltatás bemeneti adataihoz használatos.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Pontozási kísérlet a webszolgáltatás közzétételéhez
Először is, az alapvető struktúra egy **score modell** modul, amely elfogadja a betanított modell objektum és néhány sornyi bemeneti adatok, amelyek az előző lépésekben a **Count Featurizer** modul használatával keletkezett. Használja az "Oszlopok kijelölése az adatkészletben" segítségével kivetíti a pontozott címkéket és a pontszám valószínűségeit.

![Oszlopok kiválasztása az adathalmazban](./media/hive-criteo-walkthrough/kRHrIbe.png)

Figyelje meg, hogy az **Adatkészlet oszlopok kiválasztása** modul hogyan használható az adatkészletből kiszűrhető adatok "szűrésére". A tartalom itt látható:

![Szűrés az Oszlopok kijelölése az Adatkészlet modulban](./media/hive-criteo-walkthrough/oVUJC9K.png)

A kék bemeneti és kimeneti portok lejáratához egyszerűen kattintson a **webszolgáltatás előkészítése** a jobb alsó sarokban. A kísérlet futtatása lehetővé teszi számunkra a webszolgáltatás közzétételét is: kattintson a JOBB alsó sarokban található **PUBLISH WEB SERVICE** ikonra, amely itt látható:

![Webszolgáltatás közzététele](./media/hive-criteo-walkthrough/WO0nens.png)

A webszolgáltatás közzététele után átirányítva egy olyan oldalra, amely így néz ki:

![Webszolgáltatás irányítópultja](./media/hive-criteo-walkthrough/YKzxAA5.png)

Figyelje meg a két linket a webservices a bal oldalon:

* A **REQUEST/ Response** Service (vagy RRS) célja az egyetlen előrejelzések, és mi már használt ebben a műhelyben.
* A **BATCH-VÉGREHAJTÁSI** szolgáltatás (BES) kötegelt előrejelzésekhez használatos, és megköveteli, hogy az előrejelzések hez használt bemeneti adatok az Azure Blob Storage-ban legyenek.

A **REQUEST/RESPONSE** linkre kattintva egy olyan oldalra lépünk, amely előre konzervkódot ad nekünk C#, python és R nyelven. Ez a kód kényelmesen használható a webszolgáltatás hívásához. Az ezen az oldalon található API-kulcsot hitelesítéshez kell használni.

Célszerű átmásolni ezt a python kódot egy új cellába az IPython notebookban.

Itt van egy szakasz a python-kód a megfelelő API-kulcs.

![Python-kód](./media/hive-criteo-walkthrough/f8N4L4g.png)

Az alapértelmezett API-kulcs lecserélésre került a webszolgáltatás API-kulcsa. Ha a Futtatás gombra kattint egy IPython-jegyzetfüzetben a **Futtatás** gombra kattint, a következő válasz jelenik meg:

![IPython-válasz](./media/hive-criteo-walkthrough/KSxmia2.png)

A Python-szkript JSON-keretrendszerben feltett két tesztpéldához a "Pontozott címkék, pontozott valószínűség" formában kap vissza válaszokat. Ebben az esetben az alapértelmezett értékeket választotta, hogy az előre konzerv kód biztosítja (0 az összes numerikus oszlopok és a karakterlánc "érték" minden kategorikus oszlopok).

Összefoglalva, a forgatókönyv bemutatja, hogyan kell kezelni a nagyméretű adatkészletek az Azure Machine Learning használatával. Egy terabájtnyi adattal kezdte, készített egy előrejelzési modellt, és webszolgáltatásként telepítette a felhőben.

