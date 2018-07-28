---
title: Használja a hibakereséshez és diagnosztizálhatja a Spark-alkalmazások – Azure HDInsight Spark-Előzménykiszolgáló kiterjesztett |} A Microsoft Docs
description: Használja a hibakereséshez és diagnosztizálhatja a Spark-alkalmazások – Azure HDInsight Spark-Előzménykiszolgáló terjeszteni.
services: hdinsight
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 07/12/2018
ms.author: jejiang
ms.openlocfilehash: 61e857b01f75cb30e3d213703bd8ee8014d902d6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309832"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Kiterjesztett Spark-Előzménykiszolgáló használatával hibakeresése és diagnosztizálása a Spark-alkalmazások

Ez a cikk ismerteti, hogyan használja bővített Spark-Előzménykiszolgáló hibakeresése és diagnosztizálása a befejezett és futó Spark-alkalmazások. A bővítmény jelenleg magában foglalja az adatok és graph-lapon. Az adatok lapot a felhasználók ellenőrizheti a bemeneti és kimeneti adatokat a Spark-feladat. A felhasználók graph lapon ellenőrizze az adatok az adatfolyam és a feladatgrafikon visszajátszani.

## <a name="open-the-spark-history-server"></a>Nyissa meg a Spark-Előzménykiszolgáló

Spark-Előzménykiszolgáló a webes felhasználói felület a befejezett és futó Spark-alkalmazások. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>A Spark előzmények kiszolgáló webes felhasználói felület megnyitása az Azure Portalon

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a Spark-fürtön. További információkért lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. A **Gyorshivatkozások**, kattintson a **fürt irányítópultja**, és kattintson a **Spark-Előzménykiszolgáló**. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatokat a Spark-fürtön. 

    ![Spark-Előzménykiszolgáló](./media/apache-azure-spark-history-server/launch-history-server.png "Spark-Előzménykiszolgáló")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>A Spark előzmények kiszolgáló webes felhasználói felületének megnyitásához URL-cím szerint
Nyissa meg a Spark-Előzménykiszolgáló keresse a következő URL-címet, cserélje le <ClusterName> az ügyfél a Spark-fürt neve.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

A Spark-Előzménykiszolgáló webes felhasználói felület hasonlóan néz ki:

![HDInsight Spark-Előzménykiszolgáló](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Nyissa meg az adatok lapot a Spark-Előzménykiszolgáló
Válassza ki a feladat azonosítója, majd kattintson a **adatok** beolvasni az adatnézet az Eszközök menü.

+ Ellenőrizze a **bemenetek**, **kimenetek**, és **Table műveletek** lapok külön-külön kiválasztásával.

    ![Adatlap](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Másolja ki az összes sor gombra kattintva **másolási**.

    ![Adatok másolása](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Az összes adat mentése CSV-fájlként gombra kattintva **csv**.

    ![Adatok mentése](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ A keresési mezőben adjon meg kulcsszavakat **keresési**, a keresési eredmény jelenik meg azonnal.

    ![Adatok keresése](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Kattintson a táblázat rendezéséhez, kattintson a plusz jelre kattintva bontsa ki a további részletek egy sor vagy a mínuszjelet sor összecsukásához kattintson az oszlop fejlécére.

    ![Adattábla](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Gombra kattintva egyetlen fájl letöltése **részleges letöltés** jobb, amely helyezni, majd a rendszer letölti a kiválasztott fájl helyi, ha a fájl nem létezik, megnyílik egy új lap megjelenítéséhez a hibaüzenetek.

    ![Adatok letöltése sor](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Másolja ki a teljes elérési útja vagy relatív elérési út kiválasztásával a **másolás teljes elérési útja**, **másolási relatív elérési út** , amely kibővíti a letöltés menüben. Az azure data lake storage fájlok **nyissa meg az Azure Storage Explorerben** indítsa el az Azure Storage Explorert, és keresse meg, amikor jelentkezzen be a mappához.

    ![Adatok másolása elérési útja](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Kattintson a szám keresse meg a táblázat alatti lapok mikor túl sok sorok oldal megjelenítéséhez. 

    ![Az adatok lap](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Vigye a mutatót a kérdőjel mellett az elemleírásban megjeleníteni az adatokat, vagy kattintson a kérdőjel további információért.

    ![Adatok további információ](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Küldjön visszajelzést problémákkal rendelkező kattintva **várjuk visszajelzéseit**.

    ![Graph-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Nyissa meg a gráf lapot a Spark-Előzménykiszolgáló
Válassza ki a feladat azonosítója, majd kattintson a **Graph** beolvasni a feladat Grafikonnézet az Eszközök menü.

+ A feladat áttekintése a generált feladatgrafikon ellenőrzéséhez. 

+ Alapértelmezés szerint az összes feladat megjelenik, és sikerült filtered by **Feladatazonosító**.

    ![Graph-feladat azonosítója](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Alapértelmezés szerint **folyamatban** van kijelölve, felhasználói sikerült ellenőrizni az adatfolyamot kiválasztásával **olvasás/Written** legördülő listájában **megjelenített**.

    ![diagram megjelenítése](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    A színt, amely bemutatja az intenzitástérkép graph csomópont megjelenítéshez.

    ![Graph-intenzitástérkép](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Játssza le a feladat kattintva a **lejátszás** gombra, és a Leállítás gombra kattintva bármikor leállítása. A feladat megjelenítése a színt, különböző állapot megjelenítése során lejátszási:

    + Zöld, a sikeres: A feladat sikeresen befejeződött.
    + Narancssárga, a rendszer megpróbálja újból végrehajtani: nem sikerült, de nincsenek hatással a feladat végső eredményt feladatok példányait. Ezek a feladatok rendelkezett ismétlődő, vagy próbálkozzon újra, amelyek később valószínűleg sikeres lesz.
    + Piros, sikertelen: A feladat sikertelen volt.
    + Kék futtatásához: A feladat fut-e.
    + Fehér, a rendszer kihagyja vagy Várakozás: vár a feladat futtatásához, vagy a fázis rendelkezik kihagyása.

    ![Graph színminta fut](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![sikertelen Graph színminta](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > Lejátszás feladatonként engedélyezett. Amikor egy feladat nem rendelkezik bármelyik szakaszában, vagy még nem teljes, a lejátszás nem támogatott.


+ Nagyítás/kimenő a feladatábra csúcsaira, vagy kattintson a jobbra görget egér **igazítás nagyítás** legyen a képernyőhöz igazítás.
 
    ![Graph nagyítás igazítás](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Vigye a mutatót az elemleírás, ha a meghiúsult feladatok megtekintéséhez graph-csomópont, majd kattintson a fázis fázis lap megnyitásához.

    ![Graph-elemleírás](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ A feladat graph csomópont megjeleníti minden egyes szakasza a következő információkat:
    + AZONOSÍTÓ.
    + Név vagy leírás.
    + Tevékenységek teljes száma.
    + Olvasott adatok: a bemeneti méret és a shuffle olvasási méret.
    + Írása: a kimeneti mérete és a shuffle írási mérete.
    + Végrehajtási idő: az első kísérlet kezdete és befejezési idő a legutóbbi kísérlet között eltelt idő.
    + Sorok száma: bemeneti rekord összege kimeneti bejegyzések, olvasási rekordok shuffle és shuffle írási rögzíti.
    + Folyamatban van.

    > [!NOTE]
    > Alapértelmezés szerint a feladat graph csomópont és az egyes szintek (kivéve a végrehajtási idő fázis) utolsó kísérlet adatait jeleníti meg, de a grafikon a lejátszás során csomópont információk jelennek meg minden egyes kísérlethez.

    > [!NOTE]
    > Az olvasási és írási 1 használjuk adatméret MB = 1000 KB = 1000 * 1000 bájt.

+ Küldjön visszajelzést problémákkal rendelkező kattintva **várjuk visszajelzéseit**.

    ![Graph-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>GYIK

### <a name="1-revert-to-community-version"></a>1. Közösségi verzió visszaállítása

Közösségi verzió visszaállításához kövesse az alábbi lépéseket:

1. Nyissa meg a fürt Ambari az. Kattintson a **Spark2** a bal oldali panelen.
2. Kattintson a **Configs** fülre.
3. Bontsa ki a csoport **egyéni spark2-alapértelmezett**.
4. Kattintson a **tulajdonság hozzáadása**, adjon hozzá **spark.ui.enhancement.enabled=false**, mentse.
5. A tulajdonság határozza meg **hamis** most.
6. Kattintson a **mentése** a konfiguráció mentéséhez.

    ![a szolgáltatás kikapcsolása](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Kattintson a **Spark2** a bal oldali panel alatt **összefoglalás** lapra, majd **Spark2 Előzménykiszolgáló**.

    ![Indítsa újra a kiszolgáló1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Indítsa újra a korábbi kiszolgáló kattintva **indítsa újra a** , **Spark2 Előzménykiszolgáló**.

    ![Indítsa újra a kiszolgáló2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Frissítse a Spark előzmények kiszolgáló webes felhasználói felületen, azt vissza fog állítani community verziójához.

### <a name="2-upload-history-server-event"></a>2. Töltse fel a korábbi kiszolgáló esemény

Ha előzmények kiszolgálóhiba, kövesse a lépéseket adja meg az esemény:
1. Esemény letöltéséhez kattintson **letöltése** előzménykiszolgáló a webes felhasználói Felületét.

    ![esemény letöltése](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Kattintson a **várjuk visszajelzéseit** data/graph lapról.

    ![Graph-visszajelzés](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Adja meg a cím és a hiba leírása, húzza át a zip-fájlt a Szerkesztés mezőbe, majd kattintson a **új probléma küldése**.

    ![fájl probléma](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Frissítse a jar-fájlra a gyorsjavítás a forgatókönyvhöz

Ha szeretné frissíteni, hogy a gyorsjavítás, használja a parancsfájlt, ami frissíteni fogja a spark-enhancement.jar*.

**upgrade_spark_enhancement.SH**:

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

**Használati**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Példa**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**A bash-fájl az Azure Portalról**

1. Indítsa el a [az Azure Portal](https://ms.portal.azure.com), és válassza ki a fürtöt.
2. Kattintson a **Szkriptműveletek**, majd **új küldés**. Végezze el a **Szkriptművelet** alkotnak, majd kattintson a **létrehozás** gombra.
    
    + **Parancsprogram típusa**: válasszon **egyéni**.
    + **Név**: Adjon meg egy szkriptnevet.
    + **Bash parancsfájl URI azonosítója**: a bash-fájl feltöltése a privát fürtbe, akkor itt másolja az URL-címet. Másik megoldásként használhatja a megadott URI azonosító.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Ellenőrizze a **fő** és **feldolgozó**.
    + **Paraméterek**: állítsa be a paraméterek kövesse a bash használatát.

    ![napló feltöltése vagy a gyorsjavítás frissítése](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Ismert probléma

1.  Jelenleg csak működik Spark 2.3-fürt.

2.  Bemeneti és kimeneti adatokat RDD használatával nem jelenik meg az adatok lapot.

## <a name="next-steps"></a>További lépések

* [A HDInsight Spark-fürt erőforrásainak kezelése](apache-spark-resource-manager.md)
* [A Spark beállításainak konfigurálása](apache-spark-settings.md)


## <a name="contact-us"></a>Kapcsolat

Ha bármilyen visszajelzése van, vagy bármely egyéb problémákat tapasztal, amikor ezzel az eszközzel, küldjön egy e-mailt ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).