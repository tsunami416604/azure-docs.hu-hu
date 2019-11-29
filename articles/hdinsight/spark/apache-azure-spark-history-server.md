---
title: Kiterjesztett Spark-előzmények kiszolgálója alkalmazások hibakereséséhez – Azure HDInsight
description: Kiterjesztett Spark-előzmények használata a Spark-alkalmazások hibakereséséhez és diagnosztizálásához – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561828"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Apache Spark alkalmazások hibakeresése és diagnosztizálása a kiterjesztett Apache Sparki előzményeket használó kiszolgálón

Ez a cikk útmutatást nyújt arról, hogyan használható a kiterjesztett Apache Sparki előzmények kiszolgálója a befejezett és futó Spark-alkalmazások hibakeresésére és diagnosztizálására. A bővítmény tartalmazza az adatlapok és a Graph lap és a diagnosztika lapot. Az **adatok** lapon a felhasználók megnézhetik a Spark-feladathoz tartozó bemeneti és kimeneti adatokat. A **Graph (gráf** ) lapon a felhasználók megnézhetik az adatfolyamot, és visszajátszhatják a feladatok gráfját. A **diagnosztika** lapon a felhasználó hivatkozhat az **adatok eldöntésére**, az **idő eldöntésére**és a **végrehajtó általi használat elemzésére**.

## <a name="get-access-to-apache-spark-history-server"></a>Apache Spark History-kiszolgáló elérésének beolvasása

A Apache Spark History Server a Spark-alkalmazások befejezéséhez és futtatásához használható webes KEZELŐFELÜLET.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Nyissa meg az Apache Spark History Server webes felhasználói felületét Azure Portal

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).
2. A **fürt irányítópultok**területen válassza a **Spark-előzmények kiszolgáló**elemet. Ha a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait.

    ![a Portal indítása Spark History Server](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-előzmények kiszolgálója")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>A Spark History Server webes felhasználói felületének megnyitása URL-cím alapján

Nyissa meg a Spark History-kiszolgálót úgy, hogy megkeresi a `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`, ahol a CLUSTERNAME a Spark-fürt neve.

A Spark History Server webes FELÜLETe a következőhöz hasonló lehet:

![HDInsight Spark-előzmények kiszolgálója](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Az adatlap a Spark History-kiszolgálón

Válassza a feladatok azonosítója lehetőséget, majd az eszköz menüjében válassza az **adatok** lehetőséget az adatnézet beolvasásához.

+ A lapfülek kiválasztásával tekintse át a **bemenetek**, a **kimenetek**és a **táblák műveleteit** .

    ![A Spark-alkalmazások lapjai](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ A **Másolás gombra kattintva másolja az**összes sort.

    ![A Spark-alkalmazás másolására vonatkozó adatgyűjtés](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Mentse az összes fájlt CSV-fájlként a **CSV**-fájl kiválasztásával.

    ![A Spark-alkalmazás mentésének adatvédelme](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Keresés kulcsszavak beírásával a mező **keresése**mezőbe a keresési eredmény azonnal megjelenik.

    ![A Spark-alkalmazások keresésére vonatkozó adatgyűjtés](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ A táblázat rendezéséhez válassza ki az oszlop fejlécét, és válassza ki a pluszjelet a sorok kibontásához, vagy válassza a mínusz jelet a sorok összecsukásához.

    ![A Spark-alkalmazás táblázatának adatkészlete](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Töltsön le egy fájlt úgy, hogy a gomb **részleges Letöltés** lehetőségre kattint a jobb oldalon, majd a kiválasztott fájl a helyi helyre lesz letöltve, ha a fájl már nem létezik, megnyílik egy új lap a hibaüzenetek megjelenítéséhez.

    ![A Spark-alkalmazás letöltési sorával kapcsolatos adatgyűjtés](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Másolja a teljes elérési utat vagy relatív elérési utat úgy, hogy kiválasztja a **teljes elérési utat**, és a letöltési menüből kibontott **relatív elérési utat másolja** . Az Azure-beli adattárban tárolt fájlok **megnyitásához nyissa meg a Azure Storage Explorer** Azure Storage Explorer, és keresse meg a mappát a bejelentkezéskor.

    ![A Spark-alkalmazás másolási útvonalának elérési útja](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Válassza ki a táblázat alatti számot, ha túl sok sort szeretne megjeleníteni egy oldalon.

    ![A Spark-alkalmazásra vonatkozó adatgyűjtés oldal](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Mutasson az adatok melletti kérdőjelre az elemleírás megjelenítéséhez, vagy válassza a kérdőjelet további információk megadásához.

    ![Adatok a Spark-alkalmazásról – további információ](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Küldjön visszajelzést a problémákkal kapcsolatos visszajelzések **megadásával.**

    ![A Spark Graph újra visszajelzést küld nekünk](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History-kiszolgáló gráf lapja

Válassza a feladatok azonosítója lehetőséget, majd kattintson a **Graph** elemre az eszköz menüjében a feladatütemezés nézet beolvasásához.

+ Tekintse át a feladatok áttekintését a létrehozott feladatütemezés alapján.

+ Alapértelmezés szerint az összes feladat megjelenik, és a **Feladat azonosítója**alapján szűrhető.

    ![Spark-alkalmazás és a feladatütemezés-feladatok azonosítója](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Alapértelmezés szerint a **folyamat** ki van választva, a felhasználó az adatfolyamatot úgy is megtekintheti, hogy a **Megjelenítés**legördülő listájában az írás **/írás** lehetőséget választja.

    ![Spark-alkalmazás és-feladatok diagramjának megjelenítése](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    A Graph csomópont színe a hő jeleníti meg.

    ![Spark-alkalmazás és a Job Graph hő](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ A leállításhoz kattintson a **Lejátszás** gombra, és a Leállítás gomb kiválasztásával bármikor leállíthatja a feladatot. A feladat színesben jelenik meg, hogy a lejátszás közben eltérő állapot jelenjen meg:

    |Szín |Leírás |
    |---|---|
    |Zöld|A feladatot sikeresen befejezte.|
    |Orange|A feladat utolsó eredményét nem befolyásoló feladatok példányai. Ezek a feladatok ismétlődő vagy újrapróbálkozási példányokkal rendelkeztek, amelyek később esetleg sikeresek lehetnek.|
    |Kék|A feladat fut.|
    |Fehér|A feladat futásra vár, vagy a szakasz kimaradt.|
    |Piros|A feladat nem sikerült.|

    ![Spark-alkalmazás és a feladatütemezés színmintája, Futtatás](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    A kihagyott szakasz fehérre van állítva.
    ![Spark-alkalmazás és a feladatütemezés színes mintája, kihagyás](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![A Spark-alkalmazás és a feladatütemezés színes mintája sikertelen](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Az egyes feladatokhoz tartozó lejátszás engedélyezett. Hiányos feladatok esetén a lejátszás nem támogatott.

+ Az egér görgeti a feladatokat a grafikonon, vagy a **nagyításhoz** kattintson a Nagyítás gombra, hogy illeszkedjen a képernyőhöz.

    ![A Spark-alkalmazás és a feladatok gráfjának méretezése](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Mutasson a Graph csomópontra az elemleírás megjelenítéséhez, amikor a feladatok sikertelenek, majd kattintson a szakasz elemre a szakasz megnyitása lapon.

    ![Spark-alkalmazás és a feladatütemezés elemleírása](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ A feladatütemezés lapon a szakaszok elemleírással és kis ikonnal jelennek meg, ha a feladatok megfelelnek az alábbi feltételeknek:
  + Adat-elferdítés: az adatolvasási méret > a fázisban lévő összes feladat átlagos adatolvasási mérete, és az adatolvasási méret > 10 MB.
  + Időeltérés: a végrehajtás ideje > az ebben a szakaszban szereplő összes feladat átlagos végrehajtási idejét, a végrehajtási időt pedig > 2 percen belül.

    ![Spark-alkalmazás és a feladatok gráfjának döntési ikonja](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ A feladatok gráf csomópontja a következő információkat jeleníti meg az egyes szakaszok esetében:
  + ID.
  + Név vagy leírás.
  + A feladat teljes száma.
  + Olvasott adatok: a bemeneti méret és a shuffle olvasási méret összege.
  + Adatírás: a kimeneti méret és a shuffle írási méret összege.
  + Végrehajtási idő: az első kísérlet kezdési időpontja és a legutóbbi kísérlet befejezési időpontja közötti idő.
  + Sorok száma: a bemeneti rekordok, a kimeneti rekordok, a shuffle olvasási rekordok és a shuffle írási rekordok összege.
  + Haladás.

    > [!NOTE]  
    > Alapértelmezés szerint a feladatütemezés csomópontja az egyes szakaszok utolsó próbálkozásának adatait jeleníti meg (kivéve a fázis végrehajtásának idejét), de a lejátszási gráf csomópontjában minden egyes kísérlet információit meg fogja jeleníteni.

    > [!NOTE]  
    > Olvasási és írási adatméret esetén az 1MB = 1000 KB = 1000 * 1000 bájtot használja.

+ Küldjön visszajelzést a problémákkal kapcsolatos visszajelzések **megadásával.**

    ![Spark-alkalmazás és a feladatok gráfjának visszajelzése](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>A Apache Spark History Server diagnosztika lapja

Válassza ki a feladatok AZONOSÍTÓját, majd válassza a **diagnosztika** lehetőséget az eszköz menüben a feladatok diagnosztizálási nézetének lekéréséhez. A diagnosztika lapon szerepel az **adatok eldöntése**, az **időeltérés**és a **végrehajtó általi használat elemzése**.

+ Tekintse át az **adatok eldöntését**, az **időeltérést**és a **végrehajtó használatának elemzését** a lapok kiválasztásával.

    ![SparkUI-diagnosztika adatferdítő lapja](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Az adattorzítás

Válassza az **adattorzítás** fület, a megadott paraméterek alapján megjelennek a megfelelő ferde feladatok.

+ **Paraméterek megadása** – az első szakasz azokat a paramétereket jeleníti meg, amelyek az adatok eldöntésének észlelésére szolgálnak. A beépített szabály a következő: a tevékenységi adatok olvasása nagyobb, mint háromszor az átlagos feladathoz tartozó adat, az olvasás pedig több mint 10 MB. Ha meg szeretné határozni saját szabályát a ferde feladatokhoz, kiválaszthatja a paramétereket, az eldöntött **szakasz**és a **ferde karakter** elválasztása szakaszt.

+ **Ferde fázis** – a második szakasz a szakaszokat jeleníti meg, amelyek a fent megadott feltételeknek megfelelő ferde feladatokat látnak el. Ha egy adott szakaszban több elferdített feladat van, akkor a ferde szakasz tábla csak a leginkább ferde feladatot jeleníti meg (például az elferdítés legnagyobb adatát).

    ![sparkui-diagnosztika adattorzításának lapja](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Döntse** el, hogy mikor van kiválasztva az elferdítő tábla egy sora, az elferdítő diagram több feladat-elosztási részletet jelenít meg az adatok olvasási és végrehajtási ideje alapján. A ferde feladatok piros színnel vannak megjelölve, és a normál feladatok kék színnel vannak megjelölve. A teljesítmény szempontjából a diagram legfeljebb 100 minta feladatot jelenít meg. A feladat részletei a jobb alsó panelen jelennek meg.

    ![sparkui-diagram a 10. fázishoz](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Időbeli döntés

Az **idő megdöntése** lap a feladat végrehajtási ideje alapján elferdített feladatokat jeleníti meg.

+ **Paraméterek megadása** – az első szakasz azokat a paramétereket jeleníti meg, amelyek az időeltérés észlelésére szolgálnak. Az időeltérés észlelésének alapértelmezett feltételei: a feladat-végrehajtási idő nagyobb, mint az átlagos végrehajtási idő háromszorosa, a feladat végrehajtási ideje pedig 30 másodpercnél nagyobb. A paramétereket igény szerint módosíthatja. A **ferde szakasz** és az elferdítés **diagram** a megfelelő szakaszokat és feladatokat jeleníti meg, ugyanúgy, mint a fenti **adattorzítási** lap.

+ Adja meg az **időeltérést**, majd a szűrt **eredmény a szakasz** paramétereinek megadása szakaszban, a **Paraméterek**beállítása szakaszban látható. Válasszon egy elemet a **ferde fázis** szakaszban, majd a megfelelő diagramot a section3-ben, a feladat részletei pedig a jobb alsó panelen jelennek meg.

    ![sparkui-diagnosztika időtartamának eldöntése szakasz](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Végrehajtói használat elemzése

A végrehajtói használati gráf megjeleníti a Spark-feladatok tényleges végrehajtóinak kiosztását és futási állapotát.  

+ Válassza **a végrehajtói használat elemzése**lehetőséget, majd a végrehajtói használattal kapcsolatos négy típusú görbét, beleértve a **lefoglalt végrehajtókat**, a **végrehajtók**, az **üresjárati végrehajtók**és a **maximális végrehajtó példányok**futtatását. A lefoglalt végrehajtók esetében minden "végrehajtó Hozzáadás" vagy "végrehajtó eltávolítva" esemény növeli vagy csökkenti a lefoglalt végrehajtókat. további összehasonlításért tekintse meg az "esemény ütemezése" lapot a "feladatok" lapon.

    ![sparkui-diagnosztikai végrehajtók lap](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ A szín ikonra kattintva kiválaszthatja vagy kiválaszthatja a megfelelő tartalmat az összes piszkozatban.

    ![sparkui-diagnosztika – diagram kiválasztása](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Gyakori kérdések

### <a name="1-revert-to-community-version"></a>1. a közösségi verzió visszaállítása

A közösségi verzióra való visszaálláshoz hajtsa végre a következő lépéseket:

1. Nyissa meg a fürtöt a Ambari-ben.
1. Navigáljon a **Spark2** > **configs** > **Egyéni Spark2 – alapértelmezett értékekre**.
1. Válassza a **tulajdonság hozzáadása...** , majd a **Spark. UI. Enhancement. enabled = false**, mentés lehetőséget.
1. A tulajdonság a **false (hamis) értéket** állítja be.
1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

    ![Az Apache Ambari funkció kikapcsol](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. A bal oldali panelen válassza a **Spark2** lehetőséget, az **Összefoglalás** lapon válassza a **Spark2-előzmények kiszolgáló**elemet.

    ![Apache Ambari Spark2 Összefoglaló Nézet](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Indítsa újra a History Servert a **Spark2-előzmények kiszolgálójának** **újraindítása** lehetőség kiválasztásával.

    ![Apache Ambari Spark2 előzmények újraindítása](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Frissítse a Spark History Server webes FELÜLETét, és a rendszer visszaállít egy közösségi verzióra.

### <a name="2-upload-history-server-event"></a>2. feltöltési előzmények kiszolgálói esemény

Ha az előzmények kiszolgálói hibát futtat, kövesse az alábbi lépéseket az esemény megadásához:

1. Töltse le az eseményt az előzmények kiszolgáló webes felhasználói felületének **Letöltés** lehetőség kiválasztásával.

    ![Spark2-előzmények kiszolgálójának letöltése](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Válassza az adatok/gráf lap **visszajelzés küldése az USA számára** lehetőséget.

    ![A Spark Graph visszajelzést küld nekünk](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Adja meg a hiba címét és leírását, húzza a zip-fájlt a Szerkesztés mezőbe, majd kattintson az **új probléma küldése**elemre.

    ![példa az Apache Spark file problémájára](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. frissítse a jar-fájlt a gyorsjavítási forgatókönyvhöz

Ha gyorsjavítással szeretné frissíteni, használja az alábbi parancsfájlt, amely frissíti a Spark-Enhancement. jar * fájlt.

**upgrade_spark_enhancement. sh**:

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

**Használat**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Példa**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**A bash-fájl használata a Azure Portal**

1. Indítsa el [Azure Portal](https://ms.portal.azure.com), majd válassza ki a fürtöt.
2. Hajtson végre egy [parancsfájl-műveletet](../hdinsight-hadoop-customize-cluster-linux.md) a következő paraméterekkel:

    |Tulajdonság |Value (Díj) |
    |---|---|
    |Parancsfájl típusa|– Egyéni|
    |Név|UpgradeJar|
    |Bash-parancsfájl URI-ja|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Csomópont típusa (i)|Head, Worker|
    |Paraméterek|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Parancsfájl-Azure Portal küldése művelet](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Ismert problémák

+ Jelenleg csak a Spark 2,3 és a 2,4 fürtök esetében működik.

+ A RDD használatával történő bemeneti/kimeneti adatok nem jelennek meg az adatok lapon.

## <a name="next-steps"></a>Következő lépések

+ [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
+ [Apache Spark beállítások konfigurálása](apache-spark-settings.md)

## <a name="contact-us"></a>Kapcsolatfelvétel

Ha bármilyen észrevétele van, vagy ha az eszköz használata során bármilyen problémába ütközik, küldjön egy e-mailt a következő címre: ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
