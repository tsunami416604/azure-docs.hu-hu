---
title: Alkalmazások hibakereséséhez használja az Apache Spark History Server bővített funkcióit – Azure HDInsight
description: Az Apache Spark History Server kiterjesztett szolgáltatásaival debugésése és diagnosztizálása Spark-alkalmazások – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548934"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Az Apache Spark History Server kiterjesztett szolgáltatásainak használata a Spark-alkalmazások hibakereséséhez és diagnosztizálásához

Ez a cikk bemutatja, hogyan használhatja az Apache Spark History Server kiterjesztett funkcióit a befejezett vagy futó Spark-alkalmazások hibakereséséhez és diagnosztizálásához. A bővítmény tartalmaz egy **Adat** lapot, egy **Grafikon** lapot és egy **Diagnosztika** lapot. Az **Adatok** lapon ellenőrizheti a Spark-feladat bemeneti és kimeneti adatait. A **Grafikon** lapon ellenőrizheti az adatfolyamot, és visszajátszhatja a feladatgrafikont. A **Diagnosztika** lapon az **Adatdöntés**, **az Idődöntés**és a **Végrehajtó használat elemzése** szolgáltatásra hivatkozhat.

## <a name="get-access-to-the-spark-history-server"></a>Hozzáférés a Spark-előzmények kiszolgálójához

A Spark Előzmények kiszolgáló a webes felhasználói felület a spark-alkalmazások befejezéséhez és futtatásához. Megnyithatja az Azure Portalon vagy egy URL-címről.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>A Spark History Server webes felhasználói felületének megnyitása az Azure Portalról

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a Spark-fürt. További információt a Fürtök listája és megjelenítése című témakörben [talál.](../hdinsight-administer-use-portal-linux.md#showClusters)
2. A **Fürtirányítópultok**területen válassza a **Spark-előzmények kiszolgálója**lehetőséget. Amikor a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait.

    ![Indítsa el a Spark-előzmények kiszolgálóját az Azure Portalról.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-előzmények kiszolgálója")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>A Spark History Server webes felhasználói felületének megnyitása URL-cím szerint

Nyissa meg a Spark-előzmények `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`kiszolgálót a tallózásával, ahol a **CLUSTERNAME** a Spark-fürt neve.

A Spark History Server webes felhasználói felülete hasonló lehet ehhez a képhez:

![A Spark Előzmények kiszolgáló ja.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>A Spark-előzmények kiszolgáló Adatok lapjának használata

Jelölje ki a feladatazonosítót, majd az adatnézet megtekintéséhez válassza az eszköz **menüjének Adatok** parancsát.

+ Tekintse át **a bemeneteket,** **a kimeneteket**és a **táblaműveleteket** az egyes lapok kiválasztásával.

    ![A Spark-alkalmazás adatai lap adatlapjai.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Az összes sor másolása a **Másolás** gomb kiválasztásával.

    ![Adatok másolása a Spark alkalmazáslapján.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Mentse az összes adatot . CSV fájlt a **csv** gomb kiválasztásával.

    ![Adatok mentése . CSV-fájlt a Spark-alkalmazáshoz adatok lapról.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Keressen az adatok között kulcsszavak beírásával a **Keresés** mezőbe. A keresési eredmények azonnal megjelennek.

    ![Keresés az adatok a Spark-alkalmazás adatai lapon.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ A táblázat rendezéséhez jelölje ki az oszlopfejlécet. Válassza ki a pluszjelet a sor kibontásához, hogy további részleteket jelenítsen meg. A sor összecsukásához válassza a mínuszjelet.

    ![Az adatok tábla a Data for Spark Application oldalon.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Egyetlen fájl letöltése a jobb oldali **Részleges letöltés** gomb bal oldalán. A kijelölt fájl helyileg töltődik le. Ha a fájl már nem létezik, ez egy új lapot nyit meg a hibaüzenetek megjelenítéséhez.

    ![Az adatok letöltési sora a Spark-alkalmazáshoz adatok oldalon.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Teljes elérési út vagy relatív elérési út másolása a **Teljes elérési út másolása** vagy a **Relatív elérési út másolása** beállítással, amely a letöltési menüből kibővül. Az Azure Data Lake Storage-fájlok esetén válassza **a Megnyitás az Azure Storage Explorerben** lehetőséget az Azure Storage Explorer elindításához, és a bejelentkezés után keresse meg a mappát.

    ![Teljes elérési út másolása és relatív elérési út másolása beállítások a Spark-alkalmazás adatai lapon.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Ha túl sok sor van egy oldalon, jelölje ki a táblázat alján lévő oldalszámokat a navigáláshoz.

    ![Oldalszámok a Spark-alkalmazás adatai lapon.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ További információkért mutasson az elemleírás megjelenítéséhez mutasson a **Spark-alkalmazás adatai** melletti kérdőjelre, vagy jelölje ki a kérdőjelet.

    ![További információkért a Spark-alkalmazás adatai lapon.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Ha visszajelzést szeretne küldeni a problémákról, válassza **a Visszajelzés küldése**lehetőséget.

    ![Visszajelzés küldése a Spark-alkalmazás adatai lapról.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>A Diagram lap használata a Spark-előzmények kiszolgálón

+ Jelölje ki a feladatazonosítót, majd válassza a **Diagram** lehetőséget az eszköz menüjében a feladatgrafikon megtekintéséhez. Alapértelmezés szerint a grafikon az összes feladatot jeleníti meg. Az eredmények szűrése a **Feladatazonosító** legördülő menüvel.

    ![A Projektazonosító legördülő menüa Spark alkalmazás & Job Graph oldalon.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **A folyamat** alapértelmezés szerint be van jelölve. Ellenőrizze az adatfolyamot a **Megjelenítés** legördülő menü **Olvasás** vagy **Írás parancsára** kattintva.

    ![Ellenőrizze az adatfolyamot a Spark alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Az egyes feladatok háttérszíne egy hőtérképnek felel meg.

   ![A hőtérkép a Spark alkalmazás & Job Graph oldalon.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |Leírás |
    |---|---|
    |Zöld|A feladat sikeresen befejeződött.|
    |Narancssárga|A feladat nem sikerült, de ez nincs hatással a feladat végeredményére. Ezek a feladatok ismétlődő vagy újrapróbálkozási példányokkal rendelkeznek, amelyek később sikeresek lehetnek.|
    |Kék|A feladat fut.|
    |Fehér|A feladat futásra vár, vagy a szakasz kimarad.|
    |Vörös|A feladat nem sikerült.|

     ![Feladat futtatása a Spark alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     A kihagyott szakaszok fehér színben jelennek meg.
    ![Kihagyott feladat a Spark alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Sikertelen feladat a Spark alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > A lejátszás a befejezett feladatokhoz érhető el. A **feladat** lejátszásához válassza a Lejátszás gombot. A stop gomb kiválasztásával bármikor leállíthatja a feladatot. A feladat lejátszásakor minden feladat szín szerint jeleníti meg az állapotát. A lejátszás nem támogatott a befejezetlen feladatok esetén.

+ Görgessen a feladatgrafikon nagyításához vagy kicsinyítéséhez, vagy válassza a **Nagyítás lehetőséget, hogy** illeszkedjen a képernyőhöz.

    ![Válassza a Nagyítás lehetőséget, ha el szeretné férnie a Spark alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Ha a feladatok sikertelenek, vigye az egérmutatót a diagramcsomópontra az elemleírás megtekintéséhez, majd jelölje ki a fázist egy új lapon való megnyitásához.

    ![Tekintse meg az elemleírást a Spark Alkalmazás & feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ A Spark Application & Job Graph lapon a szakaszok elemleírásokat és kis ikonokat jelenítenek meg, ha a feladatok megfelelnek ezeknek a feltételeknek:
  + Adatdöntés: Az adatok olvasási mérete > a fázison belüli összes feladat átlagos olvasási mérete * 2 *és* az adatok olvasási mérete > 10 MB.
  + Időeltérés: A végrehajtási idő > a fázison belüli összes feladat átlagos végrehajtási ideje * 2 *és* a végrehajtási idő 2 >.

    ![A ferde feladat ikonja a Spark Alkalmazás & Feladatgrafikon lapon.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ A feladatgrafikon-csomópont a következő információkat jeleníti meg az egyes fázisokról:
  + ID (Azonosító)
  + Név vagy leírás
  + Feladat teljes száma
  + Olvasott adatok: a bemeneti méret és a véletlen sorrendű olvasási méret összege
  + Adatírás: a kimeneti méret és a véletlen sorrendű írási méret összege
  + Végrehajtási idő: az első kísérlet kezdési időpontja és az utolsó kísérlet befejezési időpontja közötti idő
  + Sorszám: bemeneti rekordok, kimeneti rekordok, véletlen sorrendű olvasási rekordok és írási rekordok véletlen sorrendű írási rekordjainak összege
  + Előrehaladás

    > [!NOTE]  
    > Alapértelmezés szerint a feladatgrafikon-csomópont megjeleníti az egyes fázisok utolsó kísérletéből származó információkat (kivéve a fázis végrehajtási idejét). De lejátszás közben a feladat graph csomópont minden kísérlet ről információt jelenít meg.

    > [!NOTE]  
    > Az adatok olvasási és írási méreteihez 1 MB = 1000 KB = 1000 * 1000 bájtot használunk.

+ Küldjön visszajelzést a problémákról a **Visszajelzés küldése**lehetőséget választva.

    ![A Visszajelzés i a Spark alkalmazás & Job Graph oldalon.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>A Diagnosztika lap használata a Spark-előzmények kiszolgálón

Válassza ki a feladatazonosítót, majd válassza a **Diagnosztika parancsot** az eszköz menüjében a feladatdiagnosztikai nézet megtekintéséhez. A **Diagnosztika** lapon megtalálható **az Adatdöntés**, **az Idődöntés**és **a Végrehajtó használati elemzése.**

+ Tekintse át **az adatdöntési**, **idődöntési**és **végrehajtóhasználati elemzés című** adatokat a lapok kiválasztásával.

    ![A Diagnosztika lap Adatdöntés lapja.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Adatdöntés

Válassza az **Adatdöntés** lapot. A megfelelő ferde feladatok a megadott paraméterek alapján jelennek meg.

#### <a name="specify-parameters"></a>Paraméterek megadása

A **Paraméterek megadása** szakasz megjeleníti azokat a paramétereket, amelyek az adatdöntés észlelésére szolgálnak. Az alapértelmezett szabály a következő: Az olvasott tevékenységadatok nagyobbak, mint az olvasott átlagos tevékenységadatok háromszorosa, és az olvasott tevékenységadatok száma meghaladja a 10 MB-ot. Ha saját szabályt szeretne meghatározni a ferde feladatokhoz, kiválaszthatja a paramétereket. A **Ferde szakasz** és a Ferde diagram **szakaszok** ennek megfelelően frissülnek.

#### <a name="skewed-stage"></a>Ferde szakasz

A **Ferde szakasz** szakasz azokat a szakaszokat jeleníti meg, amelyek a megadott feltételeknek megfelelő tevékenységeket torzították. Ha egy fázisban egynél több ferde feladat van, a **Ferde szakasz** szakasz csak a legferdebb tevékenységet jeleníti meg (azaz az adatdöntés legnagyobb adatait).

![Az Adatdöntés lap nagyobb nézete a Diagnosztika lapon.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Diagram döntés

Amikor kijelöl egy sort a **Döntési fázis** táblában, a **Döntésdiagram** az adatok olvasási és végrehajtási ideje alapján további tevékenységeloszlási részleteket jelenít meg. A ferde feladatok piros, a normál feladatok pedig kék színnel vannak megjelölve. Teljesítményszempontból a diagram legfeljebb 100 mintafeladatot jelenít meg. A tevékenység részletei a jobb alsó panelen jelennek meg.

![A Döntésdiagram a 10.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Idő döntés

Az **Időeltérés** lap a feladat végrehajtási ideje alapján jeleníti meg a ferde feladatokat.

#### <a name="specify-parameters"></a>Paraméterek megadása

A **Paraméterek megadása** szakasz megjeleníti az időeltérés észlelésére használt paramétereket. Az alapértelmezett szabály a következő: A feladat végrehajtási ideje az átlagos végrehajtási idő háromszorosa, a feladat végrehajtási ideje pedig 30 másodpercnél nagyobb. A paramétereket az igényeid nek megfelelően módosíthatja. A **Ferde szakasz** és a **Döntésdiagram** a megfelelő szakaszokat és tevékenységeket jeleníti meg, ugyanúgy, mint az **Adatdöntés** lapon.

Ha az Időeltérés lehetőséget **választja,** a szűrt eredmény a **Ferde szakasz** szakaszban jelenik meg a **Paraméterek megadása** szakaszban megadott paramétereknek megfelelően. Ha a **Ferde szakasz** szakaszban kijelöl egy elemet, a megfelelő diagram a harmadik szakaszban jelenik meg, és a tevékenység részletei a jobb alsó panelen jelennek meg.

![A Diagnosztika lap Idődöntés lapján.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Végrehajtó használatelemzési grafikonjai

A **Végrehajtó használati grafikonja** megjeleníti a feladat tényleges végrehajtói foglalási és futási állapotát.  

Ha a Végrehajtó használati elemzése lehetőséget **választja,** a végrehajtó használatának négy különböző görbéje kerül kidolgozásra: **Lefoglalt végrehajtók**, **Végrehajtók futtatása**, **tétlen végrehajtók**és **Maximális végrehajtó példányok**. Minden **végrehajtó hozzáadott** vagy Végrehajtó **eltávolított** esemény növeli vagy csökkenti a kiosztott végrehajtók. További összehasonlításokat az **Esemény idővonalán** a **Feladatok** lapon ellenőrizheti.

![A Végrehajtó használat elemzése lap a Diagnosztika lapon.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Jelölje ki a színikont a megfelelő tartalom kijelöléséhez, vagy törölje belőle a jelet az összes piszkozatban.

 ![Jelölje ki a diagramot a Végrehajtó használat elemzése lapon.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>GYIK

### <a name="how-do-i-revert-to-the-community-version"></a>Hogyan térhetek vissza a közösségi verzióhoz?

A közösségi verzióhoz való visszatéréshez tegye a következő lépéseket.

1. Nyissa meg a fürtöt az Ambari ban.
1. Keresse meg a **Spark2** > **Configs**.
1. Válassza **az Egyéni szikra2-alapértelmezések**lehetőséget.
1. Válassza **a Tulajdonság hozzáadása ... lehetőséget.**
1. Adja hozzá **a spark.ui.enhancement.enabled=false**értéket, majd mentse.
1. A tulajdonság most **hamisra** állítódás.
1. A konfiguráció mentéséhez válassza a **Mentés** gombot.

    ![Kapcsolja ki a funkciót az Apache Ambari.Turn off a feature in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Válassza a **Spark2** elemet a bal oldali panelen. Ezután az **Összegzés** lapon válassza a **Spark2 Előzménykiszolgáló lehetőséget.**

    ![Az összefoglaló nézet az Apache Ambari-ban.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. A Spark Előzménykiszolgáló újraindításához válassza a **Spark2 Előzmények kiszolgálótól**jobbra található **Indítás** gombot, majd válassza a legördülő menü **Újraindítás** parancsát.

    ![Indítsa újra a Spark History Server alkalmazást az Apache Ambari ban.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Frissítse a Spark History Server webes felhasználói felületét. Vissza fog térni a közösségi verzióhoz.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hogyan tölthetek fel egy Spark History Server-eseményt, hogy problémaként jelentsem?

Ha hibát észlel a Spark Előzmények kiszolgálójában, tegye a következő lépéseket az esemény jelentéséhez.

1. Töltse le az eseményt a **Letöltés** a Spark History Server webes felhasználói felületén lehetőség kiválasztásával.

    ![Töltse le az eseményt a Spark-előzmények kiszolgáló felhasználói felületén.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Válassza **a Visszajelzés küldése** a Spark **alkalmazás& Job Graph** oldalon lehetőséget.

    ![Visszajelzés küldése a Spark alkalmazás & feladatgrafikon oldaláról](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Adja meg a címet és a hiba leírását. Ezután húzza a .zip fájlt a szerkesztési mezőre, és válassza **az Új probléma küldése**lehetőséget.

    ![Töltsön fel és küldjön be egy új problémát.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hogyan frissíthetem a .jar fájlt gyorsjavító esetén?

Ha gyorsjavítással szeretne frissíteni, használja a következő parancsfájlt, amely frissíti a . `spark-enhancement.jar*`

**upgrade_spark_enhancement.sh:**

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Használat

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Példa

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>A bash-fájl használata az Azure Portalról

1. Indítsa el az [Azure Portalt](https://ms.portal.azure.com), majd válassza ki a fürtöt.
2. [Parancsfájl-művelet](../hdinsight-hadoop-customize-cluster-linux.md) befejezése a következő paraméterekkel.

    |Tulajdonság |Érték |
    |---|---|
    |Parancsfájl típusa|- Egyéni|
    |Név|UpgradeJar között|
    |Bash parancsfájl URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Csomóponttípus(ok)|Fej, Munkás|
    |Paraméterek|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Az Azure Portal parancsfájl-műveletküldése](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Ismert problémák

+ Jelenleg a Spark History Server csak a Spark 2.3 és 2.4 esetén működik.

+ Az RDD-t használó bemeneti és kimeneti adatok nem jelennek meg az **Adatok** lapon.

## <a name="next-steps"></a>További lépések

+ [Apache Spark-fürt erőforrásainak kezelése a HDInsighton](apache-spark-resource-manager.md)
+ [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)

## <a name="feedback"></a>Visszajelzés

Ha bármilyen visszajelzést, vagy ráakad bármilyen probléma,[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)ha használja ezt az eszközt, küldjön egy e-mailt ( ).
