---
title: A csoportos adatelemzési folyamat működés közben – használatával egy Azure HDInsight Hadoop-fürt az 1 TB-os adatkészlet |} A Microsoft Docs
description: A csoportos adatelemzési folyamat használatával egy végpontok közötti forgatókönyv alkalmazó készíthet és helyezhet üzembe egy nagy (1 TB) nyilvánosan elérhető adatkészletet használó modell egy HDInsight Hadoop-fürt
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 4c368c3f06347b1164731d056a7341bdabb759b4
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "34837344"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>A csoportos adatelemzési folyamat működés közben – használatával egy Azure HDInsight Hadoop-fürt az 1 TB-os adatkészlet

Ez az útmutató bemutatja, hogyan lehet a csoportos adatelemzési folyamat használatához egy teljes körű egy forgatókönyvben egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/) szeretné tárolni, ismerje meg, a visszafejtés funkciót, és le a mintaadatokat a nyilvánosan elérhető közül[ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) adatkészletek. Az Azure Machine Learning használ a bináris osztályozási modell létrehozása az adatokon. Azt is bemutatja, hogyan tehet közzé egyet ezek a modellek webszolgáltatásként.

Akkor is megjelenik ez az útmutató a feladatok elvégzését az IPython notebook használatával. Próbálja ki ezt a módszert szeretné felhasználók konzultáljon az [Hive ODBC-kapcsolattal Criteo forgatókönyv](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) témakör.

## <a name="dataset"></a>Criteo adatkészlet leírása
Az adatok körülbelül 370 GB-os gzip formátumban tömörített TSV-fájlok (tömörítetlen ~1.3TB), egy kattintással előrejelzési adatkészlet Criteo 4.3 milliárdnál több rekordok pedig sorkulcsként. 24 napnyi származik által rendelkezésre bocsátott adatok kattintson [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Az adatszakértők kényelmi célokat szolgál a számunkra, hogy kísérletezzen a rendelkezésre álló adatok kicsomagolt lett.

Ez az adatkészlet minden rekord 40 oszlopokat tartalmazza:

* az első oszlop egy olyan címke oszlop, amely azt jelzi, hogy a felhasználó rákattint egy **hozzáadása** (érték-1), vagy kattintson az egyik (értéke 0)
* Ezután 13 oszlopok a következők numerikus, és
* utolsó 26 kategorikus oszlopok

Az oszlopok vannak anonimek, és használja a felsorolt nevek sorozata:, (a felirat az oszlopban) "Col1" "Col40" (a kategorikus utolsó oszlop).            

A következő cikkből szerint az első 20 oszlopok az adatkészletből származó két megfigyelések (sor):

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Nincsenek hiányzó értékek a numerikus és a kategorikus oszlopok ehhez az adatkészlethez. A hiányzó értékek kezelése egyszerű módszer leírása. További részletek az adatok teljesítménykezelési Hive táblákba tárolja őket.

**Definíciója:** *Átkattintós sebessége (Parancsra):* Ez az, hogy az adatok kattintással aránya. Ez Criteo az adatkészlet a Parancsra a körülbelül 3.3-as % vagy 0.033.

## <a name="mltasks"></a>Példák az előrejelzés
Ez a forgatókönyv két minta előrejelzési problémák foglalkozik:

1. **Bináris osztályozás**: előrejelzi, hogy a felhasználó rákattint egy hozzáadása:
   
   * Osztály 0: Nincs kattintson
   * 1 osztály: kattintson a
2. **Regresszió**: előre megbecsüli egy ad kattintson a felhasználói funkciók valószínűségét.

## <a name="setup"></a>Adatelemzés mentése beállítása egy HDInsight szabható Hadoop bemutatása-fürt
**Megjegyzés:** Ez általában a egy **rendszergazdai** feladat.

A prediktív elemzési megoldások fejlesztése a HDInsight-fürtök három lépésben az Azure Data Science környezet beállítása:

1. [Hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md): ezt a tárfiókot az Azure Blob Storage-adatok tárolására szolgál. Itt a HDInsight-fürtök használt adatokat tárolja.
2. [Az Azure HDInsight Hadoop-fürtök testreszabása adatelemzéshez](customize-hadoop-cluster.md): Ebben a lépésben létrehoz egy Azure HDInsight Hadoop-fürt összes csomópontjára telepítse a 64 bites Anaconda Python 2.7. Nincsenek (ebben a témakörben ismertetett) két fontos lépést a HDInsight-fürt testreszabása hajthatja végre.
   
   * A storage-fiók létrehozásakor a HDInsight-fürt az 1. lépésben létrehozott kell kapcsolni. Ehhez a tárfiókhoz való hozzáféréshez szükséges adatokat a fürtön belül feldolgozható szolgál.
   * A fürt fő csomópontjának létrehozása után engedélyezze távelérési. Az itt megadott (eltérő a fürthöz a létrehozáskor megadott) távelérési hitelesítő adatok megjegyzése: szüksége lesz rájuk a következő műveletek végrehajtásához.
3. [Az Azure ML-munkaterület létrehozásához](../studio/create-workspace.md): Ez az Azure Machine Learning munkaterület szolgál a machine learning-modellek létrehozása után egy kezdeti adatfeltárás és a HDInsight-fürtön mintavételi le.

## <a name="getdata"></a>Első és a egy nyilvános forráskódú felhasználását
A [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) adatkészlet sorrendvezérlése előtt a hivatkozásra kattint, elfogadja a használati feltételeket és a egy nevet. Erre pillanatkép itt jelenik meg:

![Criteo feltételek elfogadása](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kattintson a **tovább a letöltési** további információ az adatkészlet és a rendelkezésre állási.

Az adatok találhatók nyilvános [az Azure blob storage-bA](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) helye: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. A "wasb" Azure Blob Storage-helyre hivatkozik. 

1. A nyilvános blobtárolóban lévő adatokat a kicsomagolt adatokat három almappák áll.
   
   1. A kívánt almappába *nyers és száma/* tartalmazza az első 21 napnyi adatot – nap\_00 Day\_20
   2. A kívánt almappába *nyers train / /* az adatokat, egy nap áll nap\_21
   3. A kívánt almappába *nyers és tesztelés/* két napnyi adat, áll nap\_22-es és a nap\_23
2. Azok számára, akiknek el szeretné indítani a nyers gzip adatokkal, ezeket is elérhetők a fő mappában *nyers /* day_NN.gz, ahol Neurális hálózat kerül 00-tól 23-as.

Egy lehetséges alternatív módszer az eléréséhez, ismerje meg, és ezeket az adatokat, amely nem igényel minden olyan helyi letöltések kifejtett az útmutató későbbi részeiben létrehozott Hive-táblákat modellje.

## <a name="login"></a>Jelentkezzen be az átjárócsomóponthoz
Jelentkezzen be a fürt átjárócsomópontjával, használja a [az Azure portal](https://ms.portal.azure.com) keresse meg a fürt. A HDInsight elefántviselkedési ikonra a bal oldalon, majd kattintson duplán az a fürt nevére. Keresse meg a **konfigurációs** fülre, kattintson duplán a a lap alján található csatlakozási ikonra, és adja meg a távelérési hitelesítő adatait, amikor a rendszer kéri. Ekkor megjelenik a fürt átjárócsomópontjával.

Íme egy jellegzetes az első bejelentkezéskor a fürt átjárócsomópontjával néz ki:

![Jelentkezzen be a fürthöz](./media/hive-criteo-walkthrough/Yys9Vvm.png)

A bal oldalon a "Hadoop parancssor", amely a workhorse az adatok feltárásához. Figyelje meg, hogy a két hasznos URL-címek – "Hadoop Yarn állapot" és "Hadoop neve csomópont". A yarn állapot URL-CÍMÉT jeleníti meg a feladat állapotát, és a csomópont URL-CÍMÉT részleteket nyújt a fürt konfigurációját.

Most vannak beállítva, és készen áll arra, első részében a bemutató megkezdéséhez: adatok előkészítése az Azure Machine Learning és a Hive használatával az adatok feltárása.

## <a name="hive-db-tables"></a> Hive-adatbázis és tábla létrehozása
Hive táblák létrehozása a Criteo importáltuk, nyissa meg a ***Hadoop parancssor*** az asztalon, a fő csomópontot, és adja meg a Hive-könyvtár parancs beírásával

    cd %hive_home%\bin

> [!NOTE]
> Ez a forgatókönyv minden Hive parancsait futtatja a Hive bin / directory parancssort. Ez gondoskodik a elérési utat problémákat automatikusan. Használhatja a feltételeket "Hive directory prompt", "Hive bin / directory prompt", és a "Hadoop parancssor" felcserélhető.
> 
> [!NOTE]
> Bármely Hive-lekérdezés végrehajtásához egy mindig használhatja a következő parancsokat:
> 
> 

        cd %hive_home%\bin
        hive

A Hive REPL jelenik meg, miután egy "hive >"aláírásához, egyszerűen kivágja és beilleszti a lekérdezés végrehajtásához azt.

Az alábbi kód létrehoz egy adatbázist "criteo", és majd generál egy 4 táblák:

* egy *tábla létrehozásához számát* nap napi épülő\_00 Day\_20,
* egy *train adatkészletként használatra tábla* nap épülő\_21., és
* két *táblák használata a teszt adatkészletek* nap épülő\_22-es és a nap\_23 jelölik.

Mivel a nap egyik szabadnap-tesztelési adathalmazon felosztása két különböző tábla. A célja meghatározni, ha a modell képes észlelni az átkattintásos aránya egy szünnap és a nem szünnap közötti különbségeket.

A parancsfájl [minta&#95;hive&#95;létrehozása&#95;criteo&#95;adatbázis&#95;és&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) kényelmi itt jelenik meg:

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

Ezek a táblák olyan külső, így egyszerűen mutasson az Azure Blob Storage (wasb) helyükre.

**BÁRMELY Hive-lekérdezés végrehajtásához két módja van:**

1. **A Hive REPL parancssori használatával**: az első, hogy egy "hive" parancsot, és másolja ki és illessze be a lekérdezést, a Hive REPL parancssori. Ehhez tegye:
   
        cd %hive_home%\bin
        hive
   
     Most, a parancssori REPL, Kivágás és beillesztés a lekérdezés végrehajtja.
2. **Lekérdezés mentése fájlba, és a parancs végrehajtása**: A második, hogy a lekérdezések .hql fájlba mentése ([minta&#95;hive&#95;létrehozása&#95;criteo&#95;adatbázis&#95;és&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) és hogyan adhat ki a lekérdezés végrehajtásához a következő parancsot:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Erősítse meg az adatbázis és tábla létrehozása
Ezután erősítse meg a következő parancsot a Hive van az adatbázis létrehozása / directory parancssorba:

        hive -e "show databases;"

Ez biztosítja:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Ez megerősíti, hogy az új adatbázis, "criteo" létrehozását.

Milyen táblákat is létre megtekintéséhez egyszerűen a parancsot Itt a Hive bin ki / directory parancssorba:

        hive -e "show tables in criteo;"

Ezután megjelenik a következő kimenet:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> A Hive adatfeltárás
Most már készen áll a Hive néhány egyszerű adatfeltárás tennie. A szerelvény szereplő példák számával kezdődik, és adattáblák teszteléséhez.

### <a name="number-of-train-examples"></a>Train példák száma
A tartalmát [minta&#95;hive&#95;száma&#95;betanításához&#95;tábla&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) itt látható:

        SELECT COUNT(*) FROM criteo.criteo_train;

Ez eredményez:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Azt is megteheti, egy is adhatnak ki a következő parancsot a Hive bin / directory parancssorba:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Tesztelési példák a két teszt adatkészletekben száma
Most már számát a tesztelés két adatkészletet szereplő példák. A tartalmát [minta&#95;hive&#95;száma&#95;criteo&#95;tesztelése&#95;nap&#95;22-es&#95;tábla&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) itt:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Ez eredményez:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

A szokásos módon, előfordulhat, hogy is hívja a parancsfájl a Hive bin / mappa kérdezzen rá a parancs a kiadása:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Végül, vizsgálja meg a tesztelési példák a tesztelési adathalmazon alapuló napi száma\_23.

Ehhez a parancs csak meg hasonlít, (tekintse meg [minta&#95;hive&#95;száma&#95;criteo&#95;tesztelése&#95;nap&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Ez biztosítja:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Címke terjesztése train adatkészlet
A címke terjesztése train adatkészlet érdekében áll. Ennek megtekintéséhez tartalmának megjelenítése [minta&#95;hive&#95;criteo&#95;címke&#95;terjesztési&#95;betanításához&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

A címke terjesztése ez eredményez:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Vegye figyelembe, hogy a címkék pozitív százalékos körülbelül 3.3-as % (konzisztensek az eredeti adathalmazból).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Néhány numerikus változók train adatkészlet hisztogram disztribúciók
Használhatja a Hive a natív "hisztogram\_numerikus" függvényt, hogy a terjesztési numerikus változó néz ki. Az alábbiakban a tartalmát [minta&#95;hive&#95;criteo&#95;hisztogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Ez a eredményez a következőket:

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

NÉZET – OLDALIRÁNYÚ explode Hive szolgál a szokásos lista helyett egy SQL-hez hasonló kimenet létrehozására kombinációja. Vegye figyelembe, hogy az ebben a táblában az első oszlop felel meg a doboz center, a másik pedig a bin gyakoriságát.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Hozzávetőleges percentilisei néhány numerikus változók train adatkészlet
A lényeges a numerikus változók a hozzávetőleges. percentilisei az belül a számításnak. Hive a natív "PERCENTILIS\_hozzávetőleges" ezt a szolgáltatást, mert végzi. A tartalmát [minta&#95;hive&#95;criteo&#95;hozzávetőleges&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) vannak:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Ez eredményez:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A terjesztési. percentilisei az szorosan kapcsolódik a hisztogram terjesztési bármely numerikus változó általában.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Keresse meg az egyedi értékek számának néhány kategorikus oszlopok train adatkészlet
Folytatás az adatfeltárás, keresése, néhány kategorikus oszlopok, egy egyedi értékek száma. Ehhez tartalmának megjelenítése [minta&#95;hive&#95;criteo&#95;egyedi&#95;értékek&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Ez eredményez:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Ne feledje, hogy Col15 19M egyedi értékeket! "Egy gyakori kódolási" naiv eljárások kódolása ilyen nagy dimenziós kategorikus változók használata nem megvalósítható. Különösen hatékony, robusztus technika nevű [tanulás az Counts](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) probléma hatékonyan amelyek ismertetése, és találja meg.

Végül tekintse meg néhány más kategorikus oszlopok, valamint az egyedi értékek száma. A tartalmát [minta&#95;hive&#95;criteo&#95;egyedi&#95;értékek&#95;több&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) vannak:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Ez eredményez:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Újra vegye figyelembe, hogy Col20, kivéve a többi oszlop számos egyedi értékeket.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Közös előfordulás párok train adatkészlet kategorikus változók száma

A kategorikus változók párok közös előfordulási száma. az is fontos. Ez lehet meghatározni a kód [minta&#95;hive&#95;criteo&#95;párosított&#95;kategorikus&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Fordított a számlálás rendezés az eseményt, és tekintse meg az első 15 ebben az esetben. Ez lehetővé teszi nekünk:

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

## <a name="downsample"></a> Az adatkészletek az Azure Machine Learning minta le
Az adatkészletek megvizsgálta és mutatja be, hogyan teheti (beleértve a kombinációk), változók feltárása az ilyen típusú minta le az adatkészletek, hogy az Azure Machine Learning modellek építhetők fel. Visszaírási, amely a lépéseknek az ismertetése, a problémát: Adja meg például attribútumok (Col2 - Col40 értékeit a szolgáltatás), előre jelezni, ha Col1, a 0 (nincs ide) vagy 1 (ide).

A minta a tanítási és tesztelési adatkészleteket, az eredeti méret 1 %, a Hive a natív RAND() függvény használható. A következő parancsfájl [minta&#95;hive&#95;criteo&#95;felbontásának&#95;betanításához&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) azért teszi ezt a vonat adatkészlet:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Ez eredményez:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

A parancsfájl [minta&#95;hive&#95;criteo&#95;felbontásának&#95;tesztelése&#95;nap&#95;22-es&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) elvégzi a tesztadatok, nap\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Ez eredményez:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Végül, a parancsfájl [minta&#95;hive&#95;criteo&#95;felbontásának&#95;tesztelése&#95;nap&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) elvégzi a tesztadatok, nap\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Ez eredményez:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Ez készen áll a lefelé a mintavételezett train használhatja, és tesztelje az adatkészleteket az Azure Machine Learning modellek létrehozásához.

Azure Machine Learning, amely kiterjed a count táblázat áthelyezése előtt van egy utolsó fontos összetevő. A következő alárendelt szakaszban a count táblázatban ismertetett néhány részletet.

## <a name="count"></a> Tömören összefoglalja a count táblán
Láthatta, számos kategorikus változót kell egy nagyon nagy dimenziói. A forgatókönyv egy hatékony módszer nevű [tanulás az Counts](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) kódolása ezekre a változókra olyan hatékony, robusztus módon jelennek meg. További információt az ezzel a technikával a megadott hivatkozás van.

[!NOTE]
>Ez az útmutató célja nagy dimenziós kategorikus funkciók kompakt ábrázolását előállításához száma táblák használatával. Ez nem az egyetlen módszer kategorikus szolgáltatások kódolása További információ az egyéb technikák érdekelt felhasználók megtekinthet [egy-ritkáról gyakori elérésű-encoding](http://en.wikipedia.org/wiki/One-hot) és [szolgáltatáskivonatolás](http://en.wikipedia.org/wiki/Feature_hashing).
>

A count adatok-összeállítási táblázatok száma, használja az adatok a mappa nyers és száma. A modellezés szakaszban felhasználó megjelenik, hogyan hozhat létre, előzmények nélküli kategorikus funkciók száma táblázatokra vagy másik lehetőségként egy előre elkészített száma tábla saját explorations használandó. A következőkben amikor a "beépített száma táblák" hivatkozunk, alatt azt értjük, amelyek adtak meg a count táblák használata. Ezek a táblázatok el részletes utasításokat a következő szakaszban találhatók.

## <a name="aml"></a> Modell létrehozása az Azure Machine Learning
A modell az Azure Machine Learning folyamat létrehozásához kövesse az alábbi lépéseket:

1. [Az adatok beolvasása a Hive-táblák az Azure Machine Learningbe](#step1)
2. [A kísérlet létrehozása: tisztítsa meg az adatokat és a szabadkézi táblák száma](#step2)
3. [Hozhat létre, betanítását és pontozását a modellt.](#step3)
4. [A modell értékelése](#step4)
5. [A modell közzététele webszolgáltatásként-](#step5)

Most már készen áll az Azure Machine Learning studio a modelleket. A mintavételezett adatok mentve lesznek a fürtben Hive-táblákat, lefelé. Az Azure Machine Learning segítségével **adatok importálása** modult, olvassa el ezeket az adatokat. Az ehhez a fürthöz, a tárfiók eléréséhez szükséges hitelesítő adatokat az alábbiak szerepelnek.

### <a name="step1"></a> 1. lépés: Adatok beolvasása a Hive-táblák az Azure Machine Learning, az adatok importálása modullal, és jelölje ki a machine learning-kísérletből
Első lépésként válassza ki a **+ új** -> **kísérlet** -> **üres kísérlet**. Ezután a **keresési** bal felső sarokban keressen a "Adatok importálása" mezőjében. Húzza át a **adatok importálása** modul a kísérletben be (a középső rész a képernyő) vászonalapú a modult szeretné használni az adatok eléréséhez.

Ez az, hogy mi a **adatok importálása** tűnik a Hive-tábla adatainak lekérése közben:

![Adatok importálása az adatok beolvasása](./media/hive-criteo-walkthrough/i3zRaoj.png)

Az a **adatok importálása** modul, a paraméterek, amelyek szerepelnek a képet az értékek a következők meg kell adnia értékeket a rendezés csak például szolgál. Íme néhány általános útmutatást való töltse ki a paraméter beállítása a **adatok importálása** modul.

1. Válassza a "Hive-lekérdezés" **adatforrás**
2. Az a **adatbázis-lekérdezés Hive** mezőbe egy egyszerű SELECT * FROM < a\_adatbázis\_name.your\_tábla\_neve >-elegendő.
3. **Hcatalog kiszolgáló URI**: Ha a fürt "abc", akkor ez egyszerűen a: https://abc.azurehdinsight.net
4. **Hadoop-felhasználói fiók nevét**: A felhasználó nevét, az üzembe helyezés a fürt időpontjában választott. (Nem a távelérési felhasználónév!)
5. **Hadoop-felhasználói fiók jelszava**: a felhasználó nevét, az üzembe helyezés a fürt időpontjában kiválasztott jelszavát. (Nem a távelérési jelszót!)
6. **Kimeneti adatok helyének**: válassza az "Azure"
7. **Az Azure storage-fiók neve**: a fürthöz társított storage-fiók
8. **Az Azure storage-fiókkulcs**: a fürthöz társított storage-fiók a kulcsot.
9. **Az Azure container name**: Ha a fürt neve "abc", akkor ez az egyszerű "abc", általában a.

Miután a **adatok importálása** befejeződik (, lásd a modul a zöld osztásjelek) adatok, mentse ezeket az adatokat (a választott nevű) adatkészletként. Ez néz ki:

![Adatok importálása az adatok mentése](./media/hive-criteo-walkthrough/oxM73Np.png)

Kattintson a jobb gombbal a kimeneti portjára, a **adatok importálása** modul. Ez a tár egy **mentés adatkészletként** lehetőséget és a egy **Visualize** lehetőséget. A **Visualize** lehetőség, ha lehetőséget választotta, jelenít meg a 100 sor az adatokról, és a egy jobb oldali panel, amely akkor hasznos, ha bizonyos összefoglaló statisztikáit. Adatok mentése, egyszerűen válassza **mentés adatkészletként** , és kövesse az utasításokat.

Ki a mentett adatkészlet használható a machine learning-kísérlet, keresse meg az adatkészleteket használja a **keresési** be az alábbi ábrán látható. Egyszerűen írja be a nevét, az adatkészlet részlegesen való eléréséhez és az adatkészletet a fő panelen húzzon rendelte. Húzza azt a fő panelen kiválasztja a machine learning modellezési használatos.

![A fő panelen való Drage adatkészlet](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Ehhez a tanítási és a teszt adatkészletek. Azt se feledje, az adatbázis neve és a táblaneveket, hogy a megadott erre a célra. Az ábrán használt értékek kizárólag az ábra purposes.* *
> 
> 

### <a name="step2"></a> 2. lépés: Egyszerű kísérlet létrehozása az Azure Machine Learningben való kattintással előrejelzése és egyetlen kattintással
Az Azure Machine Learning-kísérlet így néz ki:

![Machine Learning-kísérlet](./media/hive-criteo-walkthrough/xRpVfrY.png)

Most vizsgálja meg a legfontosabb összetevők, ezzel a kísérlettel. Húzza a mentett train, és a kísérleti vászonra be adatkészletek először tesztelje.

#### <a name="clean-missing-data"></a>Hiányzó adatok törlése
A **Clean Missing Data** modul does, mi a neve is sugallja:, törli a hiányzó adatokat, hogy a felhasználó által megadott módon. Ez a modul ennek megtekintéséhez meg:

![Hiányzó adatok tisztítása](./media/hive-criteo-walkthrough/0ycXod6.png)

Itt választotta, hogy az összes hiányzó értékeket cserélje le a 0. Nincsenek, valamint egyéb lehetőségeket, amelyek megnézzük a legördülő menük, a modul láthatók.

#### <a name="feature-engineering-on-the-data"></a>Jellemzőkiemelés az adatokon
Több millió, a nagyméretű adathalmazok egyes kategorikus funkciók egyedi értékeket is lehet. Natív módszerrel, például egy gyakori kódolása nagy dimenziós kategorikus funkciók jelölő használata nem teljesen megoldhatatlanná tenné. Ez az útmutató bemutatja, hogyan kompakt objektumképviseleteket ezek nagy dimenziós kategorikus változók létrehozásához használt beépített Azure Machine Learning-modulok száma funkcióinak használatát. A végeredmény a modell kisebb méretet, a gyorsabb képzés és a teljesítmény-mérőszámok, amelyek teljesen összehasonlítható más módszerek használatával.

##### <a name="building-counting-transforms"></a>Leltár készítése átalakítások
Száma funkciókat hozhat létre, használja a **hozhat létre számbavételi átalakítása** modul, amely az Azure Machine Learningben elérhető. A modul a következőhöz hasonló:

![Leltár átalakítása modul készítése](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![modul készítése számbavételi átalakítása](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> Az a **oszlopok száma** adja meg az érintett végre kívánt oszlopokat. Ezek rendszerint (említett) nagy dimenziós kategorikus oszlopok. Ne feledje, hogy a Criteo adatkészlet rendelkezik 26 kategorikus oszlopok: az Col40 Col15 a. Itt, ezek mindegyike tanúsítással, és adja meg az indexek (a 15 vesszővel elválasztott, ahogy 40-re).
> 

A modul használatához a MapReduce módban (nagy méretű adatkészletek esetében megfelelő), hozzá kell férnie egy HDInsight Hadoop-fürtöt (a szolgáltatás feltárási használt korábbit újból használhatna erre a célra) és a hitelesítő adatok. Az előző ábra bemutatják, milyen kitöltött értékek néz ki (cserélje le az értékeket illusztrációs célokat azokkal, a saját használati esetekhez kapcsolódó).

![A modul paraméterei](./media/hive-criteo-walkthrough/05IqySf.png)

Az előző ábra bemutatja, hogyan adja meg a bemeneti blob helyét. Ezen a helyen a táblák száma kialakításához a fenntartott adatokat tartalmaz.

Ez a modul a lejáratot követően újrainduljon, ha az átalakítás a később mentse kattintson a jobb gombbal a modult, majd válassza a **Mentés másként átalakító** lehetőséget:

!["Mentés másként átalakító" lehetőség](./media/hive-criteo-walkthrough/IcVgvHR.png)

A fenti kísérlet architektúrában az adatkészlet "ytransform2" felel meg pontosan egy mentett száma átalakító. Ezzel a kísérlettel fennmaradó, feltételezzük, hogy az olvasó használja egy **hozhat létre számbavételi átalakítása** létrehozni a számát, és majd a count létrehozni ezeket számát is néhány adatot a modul a tanítási és tesztelési adatkészletek a funkciói.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Milyen száma a tanítási és tesztelési adatkészletek részét képező funkciók kiválasztása
Egyszer száma átalakítás kész, a felhasználó kiválaszthatja a milyen funkciók közé tartozik a saját train és tesztelése adatkészletek használatával a **száma tábla paraméterek módosítása** modul. A teljesség kedvéért ez a modul itt jelenik meg. De az egyszerűség érdekében ne ténylegesen használja a kísérletben.

![Módosítsa a paraméterek száma tábla](./media/hive-criteo-walkthrough/PfCHkVg.png)

Ebben az esetben látható, mert a napló-zajok használandó és a késleltetési oszlop figyelmen kívül hagyja. Paraméterek például a szemétgyűjtési bin küszöbértéket, hány pszeudo előzetes példák hozzáadása simítás, és hogy Laplacian zaj vagy nem is beállíthatja. Ezek mindegyike speciális funkciók és Megjegyzendő, hogy az alapértelmezett értékek-e a felhasználók számára, akik erre a típusra, a szolgáltatás létrehozásakor új jó kiindulási pont.

##### <a name="data-transformation-before-generating-the-count-features"></a>Adatátalakítás a count funkciók létrehozása előtt
Most való összeállításán van egy fontos átalakítását a tanítási és a Tesztadatok száma funkciók ténylegesen létrehozása előtt. Vegye figyelembe, hogy nincsenek két **R-szkript végrehajtása** az adatokra alkalmazott száma megtekintheti az átalakítás előtt használt modulok.

![Hajtsa végre az R-szkript-modulok](./media/hive-criteo-walkthrough/aF59wbc.png)

Az első R-szkript a következő:

![Első R-szkript](./media/hive-criteo-walkthrough/3hkIoMx.png)

Az R-szkriptet az oszlopok nevei "Col1", "Col40" átnevezi. Ennek az oka az száma átalakítás vár ebben a formátumban nevei.

A második R-szkript elosztja a pozitív és negatív osztályok között a terjesztési (1 és 0 osztály jelölik) lefelé-mintavétel a negatív osztály alapján. Itt az R-szkript bemutatja, hogyan ehhez:

![Második R-szkript](./media/hive-criteo-walkthrough/91wvcwN.png)

Ez egyszerű R-szkript a "pos\_Neg.\_arány" adja meg az a pozitív és negatív osztályok közötti egyensúly szolgál. Ez azért fontos, mivel visszajelzéseivel osztály egyenetlenségének általában jobb teljesítményt nyújt a ahol osztály elosztása az osztályozási problémák torzítja (ebben az esetben, hogy 3.3-as % pozitív és negatív osztály 96.7 % visszaírási) tennie.

##### <a name="applying-the-count-transformation-on-our-data"></a>Az adatok az száma átalakítás alkalmazása
Végül, használhatja a **átalakítás alkalmazása** modul a alkalmazni a count átalakítások a tanítási és adatkészletek tesztelése. Ez a modul egy bemenetként a mentett száma átalakítás és a más bemeneti train- vagy tesztkörnyezetből adatkészletek vesz igénybe, és száma funkciókkal adatokat ad vissza. Ez itt látható:

![A alkalmazni átalakítási modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Milyen funkciók kinéznie száma kivonat
Fontos fontos információkat tartalmazó száma szolgáltatások kinézni ebben az esetben a megtekintéséhez. Itt látható egy olyan, erre:

![Szolgáltatások száma](./media/hive-criteo-walkthrough/FO1nNfw.png)

A cikkből szerint jeleníti meg, hogy az oszlopok számít, a számok lekérése és zajok mellett minden releváns backoffs naplózása.

Most már készen áll-e átalakított adatkészleteket használó Azure Machine Learning-modellek létrehozását. A következő szakasz bemutatja, hogyan ezt megteheti.

### <a name="step3"></a> 3. lépés: Build, betanítását és pontozását a modell

#### <a name="choice-of-learner"></a>Learner kiválasztása
Először meg kell választania egy learner. Kétosztályos gyorsított döntési fa használja a tanuló. Az alábbiakban a learner alapértelmezett beállításainak:

![Két osztályú súlyozott döntési fa paraméterek](./media/hive-criteo-walkthrough/bH3ST2z.png)

A kísérlethez válassza ki az alapértelmezett értékeket. Vegye figyelembe, hogy általában jelentéssel bíró-e az alapértelmezett beállításokat és a egy jó módja gyors alaptervek a teljesítményre. Abszolút paraméterek, ha úgy dönt, hogy az alapterv után javíthatja a teljesítményt.

#### <a name="train-the-model"></a>A modell betanítása
A képzés, egyszerűen meghívása egy **tanítási modell** modul. A két bemenet azt a következők: a két osztályú súlyozott döntési fa learner és az train adatkészletet. Ez itt látható:

![Tanítási modell modulhoz](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>A modell pontozása
Miután a betanított modell, készen áll a tesztelési adathalmazon pontozásához és a teljesítmény értékeléséhez. Ezt úgy teheti meg a **Score Model** az alábbi ábrán látható, a modul egy **Evaluate Model** modul:

![A Score model (Modell montozása) modul](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> 4. lépés: A modell értékelése
Végül elemezni kell a modell teljesítményét. Általában két osztály (bináris) a besorolás kapcsolatos problémák esetén a megfelelő mérték is a AUC. Ez megjelenítése, bekötése a **Score Model** modult egy **Evaluate Model** a modul. Kattintson a **Visualize** a a **Evaluate Model** modul következő egy hasonló képet eredményez:

![A modul táblázatos adatbázisba kerülő modell értékelése](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Bináris (vagy a két osztály) a besorolás problémákat, az előrejelzési pontosság jó mérték az a terület alatt görbe (AUC). A következő szakasz bemutatja az eredményeket a tesztelési adatkészletnél a modell használatával. A probléma, kattintson a jobb gombbal a kimeneti portjára, a **Evaluate Model** modult, majd **Visualize**.

![Modell kiértékelése modul megjelenítése](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> 5. lépés: A modell közzététele webszolgáltatásként
Lehetővé teszi az Azure Machine Learning-modellek közzététele webszolgáltatásként fuss legalább egy olyan értékes szolgáltatás végzett széles körben elérhető. Miután ez megtörtént, bárki teheti, hogy a szükséges adatokat, és a web service a modell segítségével ezeket az előrejelzéseket adja vissza a bemeneti adatokkal a webszolgáltatás-hívások.

Ehhez először mentse a betanított modell egy Trained Model-objektumot. Ez történik, kattintson a jobb gombbal a **tanítási modell** modul, és használja a **Mentés másként betanított modell** lehetőséget.

Ezután hozzon létre bemeneti és kimeneti portok a webszolgáltatás:

* egy bemeneti portját beolvassa az adatokat az adat, amelyet az előrejelzéseket kell ugyanabban a formában
* kimeneti portját a pontozott címkék és a kapcsolódó valószínűségek adja vissza.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Válassza pár sornyi adatot a bemeneti porthoz
Célszerű használni egy **SQL-átalakítás alkalmazása** modul állnak az adatok bemeneti portját csak 10 sorok kiválasztásához. Ezeknek az itt látható az SQL-lekérdezést használja bemeneti porthoz Adatsor kiválasztása:

![Bemeneti portját adatok](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webszolgáltatás
Most már készen áll egy kis kísérletet, amely segítségével a webszolgáltatás közzététele futtatásához.

#### <a name="generate-input-data-for-webservice"></a>A webszolgáltatás bemeneti adatok létrehozása
Zeroth lépésben a count táblánk túl nagy, mivel igénybe vehet néhány sornyi teszt adatai, valamint származó kimeneti adatokat száma funkciókat hozhat létre. Ez a szolgálhat a webszolgáltatás bemeneti adatok formátumát. Ez itt látható:

![Táblázatos adatbázisba kerülő bemeneti adatok létrehozása](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> A bemeneti adatok formátumát, használja a KIMENETÉT a **száma Featurizer** modul. Után ez a kísérlet futtatása befejeződik, mentse a kimenete a **száma Featurizer** adatkészletként modul. Ez az adatkészlet a bemeneti adatokat a webszolgáltatás az szolgál.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Kísérlet pontozási webszolgáltatás közzététele
Először is látható, erre. Az alapvető struktúra egy **Score Model** modul, amely elfogadja a betanított modell objektum és a bemeneti adatok az előző lépésben létrehozott néhány sornyi a **száma Featurizer** modul. Projekt Scored címkéket és a pontszám valószínűségek "Kiválasztása oszlopok az adatkészlet" használatával.

![Oszlopok kiválasztása az adathalmaz](./media/hive-criteo-walkthrough/kRHrIbe.png)

Figyelje meg a **Select Columns in Dataset** modul használható "kiszűrése" adatok egy adatkészletből. A tartalom itt látható:

![Az a Select Columns in Dataset modul szűrése](./media/hive-criteo-walkthrough/oVUJC9K.png)

A kék bemeneti és kimeneti portokkal beolvasásához, egyszerűen kattintson **webszolgáltatás előkészítése** jobb alsó. Ez a kísérlet futtatása lehetővé teszi számunkra, hogy a webszolgáltatás közzététele: kattintson a **WEBSZOLGÁLTATÁS közzététele** ikonra, majd az alul jobbra, látható itt:

![Webszolgáltatás közzététele](./media/hive-criteo-walkthrough/WO0nens.png)

A webszolgáltatási közzététele után első átirányítja egy oldal, amely így néz ki:

![Webszolgáltatás irányítópultján](./media/hive-criteo-walkthrough/YKzxAA5.png)

Figyelje meg, hogy a két mutató hivatkozásokat a bal oldali problémák megoldásához segítséget:

* A **KÉRÉS/válasz** szolgáltatás (vagy RRS) egyetlen előrejelzéseket számára ideális, és mi kihasználták a workshop a rendszer.
* A **KÖTEGELT végrehajtási** Service (BES) batch ismeretekkel szolgál, és megköveteli, hogy a bemeneti adatok találhatók az Azure Blob Storage-előrejelzéseket használja.

A hivatkozásra kattintva **KÉRÉS/válasz** vesz igénybe, hogy egy oldalt, amely olyan biztosít, amely előre konzerv kódot C#, python, és az r segítségével Ezt a kódot, hogy a webszolgáltatás hívásainak kényelmesen használható. Vegye figyelembe, hogy ezen az oldalon az API-kulcsot kell használni a hitelesítéshez.

Célszerű a python-kód másolja át az IPython notebook az új cellára.

Íme egy szeletét, a python-kód a megfelelő API-kulccsal.

![Python-kód](./media/hive-criteo-walkthrough/f8N4L4g.png)

Vegye figyelembe, hogy az alapértelmezett API-kulcsot a problémák megoldásához segítséget az API-kulcs lett cserélve. Kattintson a **futtatása** ezen a cellán a az IPython notebook eredményez a következő választ:

![IPython-válasz](./media/hive-criteo-walkthrough/KSxmia2.png)

A két tesztelése (a python-szkript JSON-keretrendszer) kapcsolatban feltett példák, kapja vissza az űrlap válaszokat "Scored valószínűségek Scored címke". Ebben az esetben az alapértelmezett értékeket kell megválasztani, hogy a előre jelzésének kódot tartalmaz (0 összes numerikus oszlop és az összes kategorikus oszlopok esetében a "value" karakterlánc).

Ezzel befejezte az útmutató, amely az Azure Machine Learning használatával nagy méretű adatkészlet kezelésének módját. A terabájt adat használatába, egy előrejelzési modell felépítése és üzembe webszolgáltatásként, amely a felhőben.

