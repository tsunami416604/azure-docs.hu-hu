---
title: Kiterjesztett Spark-előzmények kiszolgálója Spark-alkalmazások hibakereséséhez – Azure HDInsight
description: Kiterjesztett Spark-előzmények használata a Spark-alkalmazások hibakereséséhez és diagnosztizálásához – Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 9ba03f6b1e658e08c3d07d7ccb5e2a99e96fe69c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736469"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Apache Spark alkalmazások hibakeresése és diagnosztizálása a kiterjesztett Apache Sparki előzményeket használó kiszolgálón

Ez a cikk útmutatást nyújt arról, hogyan használható a kiterjesztett Apache Sparki előzmények kiszolgálója a befejezett és futó Spark-alkalmazások hibakeresésére és diagnosztizálására. A bővítmény tartalmazza az adatlapok és a Graph lap és a diagnosztika lapot. Az **adatok** lapon a felhasználók megnézhetik a Spark-feladathoz tartozó bemeneti és kimeneti adatokat. A **Graph (gráf** ) lapon a felhasználók megnézhetik az adatfolyamot, és visszajátszhatják a feladatok gráfját. A **diagnosztika** lapon a felhasználó hivatkozhat az **adatok eldöntésére**, az **idő eldöntésére** és a **végrehajtó használati elemzésre**.

## <a name="get-access-to-apache-spark-history-server"></a>Apache Spark History-kiszolgáló elérésének beolvasása

A Apache Spark History Server a Spark-alkalmazások befejezéséhez és futtatásához használható webes KEZELŐFELÜLET. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Nyissa meg az Apache Spark History Server webes felhasználói felületét Azure Portal

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).
2. A **Gyorshivatkozások**területen kattintson a **fürt irányítópultja**, majd a **Spark-előzmények kiszolgáló**elemre. Ha a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait. 

    ![Spark-előzmények kiszolgálója](./media/apache-azure-spark-history-server/launch-history-server.png "Spark-előzmények kiszolgálója")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>A Spark History Server webes felhasználói felületének megnyitása URL-cím alapján
Nyissa meg a Spark History-kiszolgálót úgy, hogy megkeresi a következő URL-címet, a helyére `<ClusterName>` pedig a Spark-fürt nevét használja.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

A Spark History-kiszolgáló webes felhasználói felülete a következőképpen néz ki:

![HDInsight Spark-előzmények kiszolgálója](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Az adatlap a Spark History-kiszolgálón
Válassza ki a feladatok AZONOSÍTÓját, majd az eszköz menüjében kattintson az **adatok** elemre az adatnézet beolvasásához.

+ A lapokat külön kiválasztva jelölje be a **bemenetek**, a **kimenetek**és a **táblák műveleteit** .

    ![Adatlapok](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ A Másolás gombra kattintva másolja az összes **sort.**

    ![Adatok másolása](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Mentse az összes fájlt CSV-fájlként a **CSV**-fájl gombra kattintva.

    ![Adatmentés](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Keresés kulcsszavak beírásával a mező **keresése**mezőbe a keresési eredmény azonnal megjelenik.

    ![Adatkeresés](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Kattintson az oszlop fejlécére a táblázat rendezéséhez, kattintson a pluszjelre egy sor kibontásához a további részletek megjelenítéséhez, vagy kattintson a mínusz jelre egy sor összecsukásához.

    ![Adattábla](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Töltsön le egy fájlt úgy, hogy a gomb **részleges Letöltés** lehetőségre kattint a jobb oldalon, majd a kiválasztott fájl a helyi helyre lesz letöltve, ha a fájl nem létezik többé, megnyílik egy új lap a hibaüzenetek megjelenítéséhez.

    ![Adatletöltési sor](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Másolja a teljes elérési utat vagy relatív elérési utat úgy, hogy kiválasztja a **teljes elérési utat**, és a letöltési menüből kibontott **relatív elérési utat másolja** . Az Azure-beli adattárat tároló fájlok esetében a **megnyitás Azure Storage Explorer** elindítja Azure Storage Explorer, majd a bejelentkezéshez keresse meg a mappát.

    ![Adatmásolási útvonal](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ A táblázat alatti számra kattintva navigáljon a lapokhoz, amikor túl sok sor jelenjen meg egy oldalon. 

    ![Adatoldal](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Mutasson az adatok melletti kérdőjelre az elemleírás megjelenítéséhez, vagy kattintson a kérdőjelre további információk megszerzéséhez.

    ![Adatok további adatai](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Küldjön visszajelzést a problémákkal kapcsolatos visszajelzések **megadásával.**

    ![gráf-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History-kiszolgáló gráf lapja
Válassza a feladatok azonosítója lehetőséget, majd kattintson a **Graph** elemre az eszköz menüjében a feladatütemezés nézet beolvasásához.

+ Tekintse át a feladatot a generált feladatütemezés alapján. 

+ Alapértelmezés szerint az összes feladat megjelenik, és a **Feladat azonosítója**alapján szűrhető.

    ![gráf-feladatokhoz tartozó azonosító](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Alapértelmezés szerint a **folyamat** ki van választva, a felhasználó az adatfolyamatot úgy is megtekintheti, hogy a **Megjelenítés**legördülő listájában az írás **/írás** lehetőséget választja.

    ![gráf megjelenítése](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    A Graph csomópont színe a hő jeleníti meg.

    ![gráf hő](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ A feladatot a **Lejátszás** gombra kattintva, majd a Leállítás gombra kattintva bármikor leállíthatja. A feladat színesben jelenik meg, hogy a lejátszás közben eltérő állapot jelenjen meg:

  + A következő zöld: A feladatot sikeresen befejezte.
  + A narancssárga az újrapróbálkozáshoz: A sikertelen feladatok példányai, amelyek nem befolyásolják a feladat végeredményét. Ezek a feladatok ismétlődő vagy újrapróbálkozási példányokkal rendelkeztek, amelyek később esetleg sikeresek lehetnek.
  + Kék a futtatáshoz: A feladat fut.
  + A várt vagy kihagyott fehér: A feladat futásra vár, vagy a szakasz kimaradt.
  + Nem sikerült a vörös: A feladat nem sikerült.

    ![gráf színmintája, futás](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    A kihagyott szakasz fehérre van állítva.
    ![gráf színének mintája, kihagyás](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![gráf színének mintája, sikertelen](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Az egyes feladatokhoz tartozó lejátszás engedélyezett. Hiányos feladatok esetén a lejátszás nem támogatott.


+ Az egér görgeti a feladatokat a grafikonon, vagy a **nagyításhoz** kattintson a Nagyítás gombra, hogy illeszkedjen a képernyőhöz.
 
    ![diagram nagyítása a Mérethez igazítás](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Mutasson a Graph csomópontra az elemleírás megjelenítéséhez, amikor a feladatok sikertelenek, majd kattintson a szakasz elemre a szakasz megnyitása lapon.

    ![gráf elemleírása](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ A feladatütemezés lapon a szakaszok elemleírással és kis ikonnal jelennek meg, ha a feladatok megfelelnek az alábbi feltételeknek:
  + Adat-elferdítés: az adatolvasási méret > a fázisban lévő összes feladat átlagos adatolvasási mérete, és az adatolvasási méret > 10 MB.
  + Időeltérés: a végrehajtás ideje > az ebben a szakaszban szereplő összes feladat átlagos végrehajtási idejét, a végrehajtási időt pedig > 2 percen belül.

    ![gráf ferde ikonja](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

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

    ![gráf-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>A Apache Spark History Server diagnosztika lapja
Válassza ki a feladatok AZONOSÍTÓját, majd kattintson a **diagnosztika** elemre az eszköz menüjében a feladatok diagnosztizálási nézetének lekéréséhez. A diagnosztika lapon szerepel az **adatok eldöntése**, az **időeltérés**és a **végrehajtó általi használat elemzése**.
    
+ A lapfülek kiválasztásával keresse meg az **adatok eldöntését**, az **időeltérést**és a **végrehajtó használatának elemzését** .

    ![Diagnosztikai lapok](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Az adattorzítás
Kattintson az **adatok eldöntése** lapra, a megadott paraméterek alapján megjelennek a megfelelő ferde feladatok. 

+ **Paraméterek megadása** – az első szakasz azokat a paramétereket jeleníti meg, amelyek az adatok eldöntésének észlelésére szolgálnak. A beépített szabály a következőket eredményezi: A feladathoz tartozó adatok olvasása nagyobb, mint az átlagos feladathoz tartozó adatok olvasási ideje, és a feladat adatolvasása meghaladja a 10 MB-ot. Ha meg szeretné határozni saját szabályát a ferde feladatokhoz, kiválaszthatja a paramétereket, az eldöntött **szakasz**és a **ferde karakter** elválasztása szakaszt.

+ **Ferde fázis** – a második szakasz azokat a szakaszokat jeleníti meg, amelyek a fent megadott feltételeknek megfelelő ferde feladatokat látnak el. Ha egy adott szakaszban több elferdített feladat van, akkor a ferde szakasz tábla csak a leginkább ferde feladatot jeleníti meg (például az elferdítés legnagyobb adatát).

    ![Az adatdöntési section2](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Döntse** el, hogy mikor van kiválasztva az elferdítő tábla egy sora, az elferdítő diagram több feladat-elosztási részletet jelenít meg az adatok olvasási és végrehajtási ideje alapján. A ferde feladatok piros színnel vannak megjelölve, és a normál feladatok kék színnel vannak megjelölve. A teljesítmény szempontjából a diagram legfeljebb 100 minta feladatot jelenít meg. A feladat részletei a jobb alsó panelen jelennek meg.

    ![Az adatdöntési section3](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Időbeli döntés
Az **idő megdöntése** lap a feladat végrehajtási ideje alapján elferdített feladatokat jeleníti meg. 

+ **Paraméterek megadása** – az első szakasz az időeltérés észleléséhez használt paramétereket jeleníti meg. Az időeltérés észlelésének alapértelmezett feltételei: a feladat-végrehajtási idő nagyobb, mint az átlagos végrehajtási idő 3-szor, a feladat végrehajtási ideje pedig 30 másodpercnél nagyobb. A paramétereket igény szerint módosíthatja. A **ferde szakasz** és az elferdítés **diagram** a megfelelő szakaszokat és feladatokat jeleníti meg, ugyanúgy, mint a fenti **adattorzítási** lap.

+ Kattintson az **időeltérés**elemre, majd a szűrt eredmény **megjelenik a szakasz** paramétereinek meghatározása szakaszban, a paraméterek **megadása**részben leírtak szerint. Kattintson egy elemre az **elferdített fázis** szakaszban, majd a megfelelő diagramot a section3-ben, a feladat részletei pedig a jobb alsó panelen jelennek meg.

    ![Section2 idő](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Végrehajtói használat elemzése
A végrehajtói használati gráf megjeleníti a Spark-feladatok tényleges végrehajtóinak kiosztását és futási állapotát.  

+ Kattintson **a végrehajtói használat elemzése**lehetőségre, majd a végrehajtói használattal kapcsolatos négy típusú görbére, beleértve a **lefoglalt végrehajtókat**, a **végrehajtók**, az**üresjárati végrehajtók**és a **maximális végrehajtó példányok**futtatását. A lefoglalt végrehajtók esetében minden "végrehajtó Hozzáadás" vagy "végrehajtó eltávolítva" esemény növeli vagy csökkenti a lefoglalt végrehajtókat. további összehasonlításért tekintse meg az "esemény ütemezése" lapot a "feladatok" lapon.

    ![Végrehajtók lap](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ A szín ikonra kattintva kiválaszthatja vagy kiválaszthatja a megfelelő tartalmat az összes piszkozatban.

    ![Diagram kiválasztása](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>GYIK

### <a name="1-revert-to-community-version"></a>1. A közösségi verzió visszaállítása

A közösségi verzióra való visszaálláshoz hajtsa végre a következő lépéseket:

1. Nyissa meg a fürtöt a Ambari-ben. Kattintson a **Spark2** elemre a bal oldali panelen.
2. Kattintson a **konfigurációk** fülre.
3. Bontsa ki a csoport **Egyéni spark2 – alapértelmezett beállítások**elemet.
4. Kattintson a **tulajdonság hozzáadása**gombra, adja hozzá a **Spark. UI. Enhancement. enabled = false**, mentés lehetőséget.
5. A tulajdonság a **false (hamis) értéket** állítja be.
6. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

    ![funkció kikapcsolva](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Kattintson a **Spark2** elemre a bal oldali panelen, az **Összefoglalás** lapon, kattintson a **Spark2-előzmények kiszolgáló**elemre.

    ![Kiszolgáló1 újraindítása](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Indítsa újra az előzmények kiszolgálóját a **Spark2-előzmények kiszolgálójának** **újraindítása** elemre kattintva.

    ![Kiszolgáló2 újraindítása](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Frissítse a Spark History Server webes FELÜLETét, és a rendszer visszaállít egy közösségi verzióra.

### <a name="2-upload-history-server-event"></a>2. Feltöltési előzmények kiszolgálói esemény

Ha az előzmények kiszolgálói hibát futtat, kövesse az alábbi lépéseket az esemény megadásához:
1. Az esemény letöltéséhez kattintson az előzmények kiszolgáló webes felhasználói felületén a **Letöltés** elemre.

    ![esemény letöltése](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Kattintson az adatok/gráf lapról **elérhető visszajelzés küldése** lehetőségre.

    ![gráf-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Adja meg a hiba címét és leírását, húzza a zip-fájlt a Szerkesztés mezőbe, majd kattintson az **új probléma küldése**elemre.

    ![fájl probléma](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Jar-fájl frissítése gyorsjavítási forgatókönyvhöz

Ha gyorsjavítással szeretné frissíteni, használja az alábbi parancsfájlt, amely frissíti a Spark-Enhancement. jar * fájlt.

**upgrade_spark_enhancement.sh**:

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
2. Kattintson a **parancsfájlok műveletek**, majd az **új küldése**elemre. Fejezze **be a parancsfájl elküldése művelet** űrlapját, majd kattintson a **Létrehozás** gombra.
    
    + **Parancsfájl típusa**: válassza az **Egyéni**lehetőséget.
    + **Név**: adjon meg egy parancsfájl nevét.
    + **Bash-parancsfájl URI-ja**: töltse fel a bash-fájlt a privát fürtbe, majd ide másolja az URL-címet. Másik lehetőségként használja a megadott URI-t.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + A **fej** és a **feldolgozó**keresése.
   + **Paraméterek**: állítsa be a paramétereket a bash-használat után.

     ![napló-vagy frissítési gyorsjavítás feltöltése](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Ismert problémák

1.  Jelenleg csak a Spark 2,3 és a 2,4 fürtök esetében működik.

2.  A RDD-t használó bemeneti/kimeneti adatok nem jelennek meg az adatok lapon.

## <a name="next-steps"></a>További lépések

* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Apache Spark beállítások konfigurálása](apache-spark-settings.md)


## <a name="contact-us"></a>Kapcsolat

Ha visszajelzést szeretne küldeni, vagy ha az eszköz használata során bármilyen más problémába ütközik, küldjön egy e-mailt a[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)következő címre: ().
