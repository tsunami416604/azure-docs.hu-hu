---
title: Telepítés közzétett alkalmazás - Dataiku DDS - Azure HDInsight |} Microsoft Docs
description: Telepítheti és használhatja a Dataiku DDS külső Hadoop alkalmazás.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: facee9187528fa0c6a74f71ee73636dc23ca35ba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---dataiku-dds"></a>Közzétett alkalmazás - Dataiku DDS telepítése

Ez a cikk ismerteti, hogyan telepíthetnek és futtathatnak a [Dataiku DDS](https://www.dataiku.com/) on Azure HDInsight Hadoop-alkalmazások közzététele. A HDInsight-alkalmazás platform áttekintését, és a rendelkezésre álló független szoftverszállító (ISV) listáját közzétett alkalmazások: [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-dataiku-dss"></a>Kapcsolatos Dataiku DSS

A Dataiku [adatok tudományos Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), egy együttműködési adatok tudományos platform, amely lehetővé teszi az adatszakértőkön át a build és elemzési megoldásokat. Az ajánlat DSS, mint egy HDInsight alkalmazás lehetővé teszi adattudomány Big Data-megoldások létrehozásához, és futtassa azokat vállalati besorolási és a skála.

DSS segítségével adatfeldolgozást, előkészítése, kezdve, és feldolgozása egy teljes analitikai megoldás megvalósításához. DSS megoldás is tartalmazhatnak, képzés és alkalmazása machine learning modellek, adatmegjelenítési és majd végrehajtott.

A HDInsight Hadoop vagy Spark-fürtök használata DSS is telepítheti. Telepítheti DSS meglévő futó fürtökhöz, vagy új fürtök létrehozásakor. DSS is támogatja az Azure Blob storage használatával egy összekötőt, az adatok olvasása.

DSS projektek, amely majd hozhat létre a MapReduce vagy Spark feladatok létrehozására használhatja. Ezek a feladatok végrehajtása feladatokként rendszeres MapReduce vagy a Spark on hdinsight, így a fürt igény szerint méretezheti.

## <a name="prerequisites"></a>Előfeltételek

Ez az alkalmazás egy új HDInsight-fürtöt, vagy egy meglévő fürt telepítéséhez a következő konfigurációval kell rendelkeznie:

* A fürt tier(s): Standard, Premium
* A fürt szerepelnek: Hadoop, Spark
* A fürt verzió(k): 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Telepítse a Dataiku DSS közzétett alkalmazás

Ez, és más elérhető ISV alkalmazások telepítésével kapcsolatos részletes útmutatás olvasható [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Indítsa el a Dataiku DSS

1. A telepítés után is elindíthatja, DSS Azure-portálon a fürtből a a **beállítások** ablaktáblán, majd kattintson a **alkalmazások** alatt a **általános** kategória. A telepített alkalmazások ablaktábla listázza a telepített alkalmazások.

    ![Telepített Dataiku DSS alkalmazás](./media/hdinsight-apps-install-dataiku/app.png)

2. A HDInsight DSS kiválasztásakor megjelenik egy hivatkozást a weblapot, és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Az első indítási lehetősége lesz űrlap szabad Dataiku új fiók létrehozásához, vagy egy meglévő fiókkal bejelentkezni. Lehetősége is van a 2 hét az ingyenes próbaverzió elindítása [Enterprise Edition](https://www.dataiku.com/dss/editions/). Ettől a ponttól kezdve lehetősége van egy licenckulcs megadása Enterprise Edition, vagy a közösségi kiadással folytatása.

4. A kiválasztott beállítás befejezése után lehetősége lesz a bejelentkezési űrlap. Adja meg az alapértelmezett hitelesítő adatokat a bejelentkezési űrlap előtt jelenik meg.

Az alábbi lépések egy egyszerű bemutató nyújtanak.

1. [Töltse le a minta rendeléseket CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. DSS irányítópultot, válassza ki a **+** (új projekt) hivatkozásra kattintva hozzon létre egy új projektet a bal oldali menüből.

    ![Új projekt hivatkozás](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Új projekt formájában, írja be a **neve**. A **projekt kulcs** javasolt értéket automatikusan kitölti. Ebben az esetben adja meg a "Rendelések". Kattintson a **létrehozása**.

    ![Új projekt űrlap](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Válassza ki **+ IMPORTÁLÁSA az első ADATKÉSZLETET** az új projekt lapon.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Válassza ki **a fájlok feltöltése** alatt a **fájlok** dataset listája. A feltöltés párbeszédpanel jelenik meg. Kattintson a Hozzáadás egy fájlt, jelölje be a `haiku_shirt_sales.csv` letöltött fájlt, és ellenőrzése.

6. A fájl DSS van feltöltve. Ellenőrizze, hogy ha DSS észleli a rendszer a CSV formátum megfelelően az Előnézet gombra kattintva.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-dataiku/preview.png)

7. Az importálás szinte tökéletes. A CSV-fájl által használt lapon elválasztó. Látható az adatok táblázatos formátumban, szolgáltatások és a sorok, amelyek megfelelnek a megfigyelések nevű oszlopokkal. Egy hiba: az, hogy látszólag a fájl tartalmaz egy üres sort a fejlécet, és az adatok között. Javítsa ki a hibát, írja be a következőt `1` a a **átugorja a következő sort** mező.

    ![Mentés](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Adjon meg egy nevet az új adatkészlet. Adja meg **haiku_shirt_sales** fölött a képernyő mezőben, majd válassza ki a **létrehozása** gombra.

9. Az adatok táblázatos nézetben láthatja, ahol elindíthatja felfedezését. Az oszlopok, láthatja, hogy Dataiku tudományos Studio észlelt egy adattípus _kék_ – Ez esetben, szöveg, szám vagy dátum (nem elemzett). A mérőműszer azt jelzi, hogy az oszlop, amelynek az értékeket úgy tűnik, hogy a típus (piros) felel meg, vagy hiányoznak (üres) aránya. Példa DataSet adatkészletben a szervezeti egység tartozik, az üres értékeket és érvénytelen adatokat.

    ![A táblázatos nézet](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Adatkezelés

Valós adatok szinte mindig zavaró, és ritkán azt szépen csomagolt. Törli az adatokat általában parancsfájlok és a kapcsolódó üzleti logikát igényel. Dataiku DSS biztosít egy dedikált **Lab** eszközt, amely könnyebben tenni ezt a feladatot.

1. Kattintson a **Lab** jobb felső sarokban.

    ![Labor gomb](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. A tesztlabor-ablak nyílik meg. A laboratóriumi környezet, ahol ismételt dolgozunk a dataset feltölti további azt. Ez az oktatóanyag azt mutatja be, a Visual analysis aspektus. Válassza ki a **új** Visual elemzés gombra. Adjon meg egy nevet az elemzési kéri. Hagyja meg az alapértelmezett nevet, a lépést, majd kattintson az **létrehozása**.

    ![Labor létrehozása](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Válassza ki a **gyors oszlopok statisztikák** gombjára az oldal jobb felső sarkában.

    ![Gyors oszlopok statisztikák](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Megjelenik az adattípusokat és idővonal-alapú diagramjait alatt megjelenített értékek statisztika a **oszlopok áttekintő képet** ablaktáblán.

    ![Oszlopok gyors megtekintése](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Most felfedezheti DSS megadott mintaadatokat használja. Számolja fel és az adatokat, és új képi megjelenítéseket készíthet.

Részletes oktatóprogramjai, olvassa el a [további Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>További lépések

* [Dataiku DSS dokumentáció](https://doc.dataiku.com/dss/latest/).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Parancsfájlművelet Linux-alapú HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájl művelettel.
* [Üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md): egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetéséhez.
