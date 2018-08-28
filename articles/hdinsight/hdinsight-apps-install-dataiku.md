---
title: Közzétett alkalmazás – Dataiku DDS – Azure HDInsight telepítése
description: Telepítheti és használhatja a Dataiku DDS külső Hadoop-alkalmazásokat.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 64a6f393498ca90675712747afc8f9befc4b932f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105169"
---
# <a name="install-published-application---dataiku-dds"></a>Közzétett alkalmazás – Dataiku DDS telepítése

Ez a cikk bemutatja, hogyan telepítheti és futtathatja a [Dataiku DDS](https://www.dataiku.com/) közzé az Azure HDInsight Hadoop-alkalmazásokat. A HDInsight-alkalmazásplatform áttekintése és a egy listát az elérhető független szoftverszállító (ISV) közzétett alkalmazások létrehozásáról: [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-dataiku-dss"></a>A Dataiku DSS kapcsolatban

A Dataiku [Data Science Studio (DDS)](https://www.dataiku.com/dss/features/connectivity/), van egy együttműködést támogató adatelemzési platform, amely lehetővé teszi az adatszakértők és elemzési megoldásokat. Ajánlat DSS, mint a HDInsight-alkalmazás lehetővé teszi adatelemzési használata Big Data-megoldások létrehozását és futtatását azokat a nagyvállalati szintű és a méretezési csoport.

DSS segítségével teljes körű elemzési megoldást, megvalósítani, kezdve az adatbetöltés, előkészítése, és a feldolgozási. DSS megoldást is tartalmazhatnak, képzés és alkalmazó gépi tanulási modelleket, megjelenítési és majd modellezést.

DSS HDInsight Hadoop és Spark-fürtök használatával is telepítheti. Telepítheti DSS meglévő futó fürtökön, vagy ha új fürtök létrehozásához. DSS is támogatja az Azure Blob storage összekötőként adat beolvasására vonatkozóan.

DSS használatával hozhat létre a projektek, majd a MapReduce vagy a Spark-feladatokat hozhat létre. Ezeket a feladatokat, a fürt igény szerint méretezhetők, HDInsight, a MapReduce vagy a Spark normál feladatok végrehajtása.

## <a name="prerequisites"></a>Előfeltételek

Telepíti az alkalmazást egy új HDInsight-fürtöt, vagy egy meglévő fürthöz, a következő konfigurációval kell rendelkeznie:

* A fürt tier(s): Standard, prémium
* Fürttípus(ok): Hadoop, Spark
* A fürt verziót: 3.4-es, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>A közzétett alkalmazás telepítése a Dataiku DSS

Ezzel és más elérhető ISV-alkalmazások telepítésének lépésenkénti útmutatójáért olvassa el a [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Indítsa el a Dataiku DSS

1. A telepítés után már el is indíthatja DSS a fürtből az Azure Portalon lépjen a **beállítások** ablaktáblán, majd kattintson a **alkalmazások** alatt a **általános** kategória. A telepített alkalmazások panelen a telepített alkalmazás fel van sorolva.

    ![Telepített a Dataiku DSS-alkalmazás](./media/hdinsight-apps-install-dataiku/app.png)

2. A HDInsight DSS kiválasztásakor megjelenik egy hivatkozás a weblapot, és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Az alkalmazás első indításakor megnyílik egy űrlapot, ingyenes a Dataiku fiók létrehozása, vagy jelentkezzen be egy meglévő fiókot. Akkor is elindíthatja az ingyenes 2 héten próbaverziójára [Enterprise Edition](https://www.dataiku.com/dss/editions/). Az itt lehetősége van, adjon meg egy licenckulcsot a Enterprise Edition, vagy a Community Edition kiadását használja folytatása.

4. Miután befejezte a kiválasztott lehetőség, megnyílik egy bejelentkezési űrlapot. Adja meg az alapértelmezett hitelesítő adatokat a bejelentkezési űrlap előtt jelenik meg.

Az alábbi lépéseket egy egyszerű bemutató adja meg.

1. [Töltse le a minta rendelések CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. A DSS irányítópultján válassza ki a **+** (új projekt) hivatkozást a bal oldali menüben, hozzon létre egy új projektet.

    ![Új projekt hivatkozás](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Az új projekt képernyőn írja be a **neve**. A **Projektkulcsot** javasolt értéket automatikusan kitölti. Ebben az esetben adja meg az "Orders". Kattintson a **létrehozás**.

    ![Új projekt űrlap](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Válassza ki **+ az első ADATKÉSZLET IMPORTÁLÁSA** az új projekt lapon.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Válassza ki **a fájlok feltöltése** alatt a **fájlok** adatkészlet listája. A fájlfeltöltési párbeszédpaneljére megjelennek. Kattintson a Hozzáadás gombra egy fájlt, jelölje be a `haiku_shirt_sales.csv` letöltött fájl és ellenőrzése.

6. A fájl DSS fel van töltve. Ellenőrizze, hogy ha DSS észlelt a CSV formátum megfelelően az Előnézet gombra kattintva.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-dataiku/preview.png)

7. Az importálás szinte tökéletes. A CSV-fájlt használ fülre az elválasztó. Láthatja az adatok táblázatos formában, funkciók és sorok megfigyelések képviselő nevű oszlopokkal. Az egyetlen hiba, hogy látszólag a fájl tartalmaz egy üres sor a fejléc és az adatok között. Adja meg a hiba javításához `1` a a **átugorja a következő sort** mező.

    ![Mentés](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Adjon meg egy nevet az új adatkészletre. Adja meg **haiku_shirt_sales** a mezőben a képernyő fölött, majd válassza ki a **létrehozás** gombra.

9. Az adatok egy táblázatos nézetben láthatja, ahol megkezdheti azt. Az egyes oszlopok megtekintheti, hogy a Dataiku Science Studio észlelt egy adattípus _kék_ – az az eset, szöveg, szám vagy dátum (neanalyzovanou). A mérőműszer azt jelzi, hogy az oszlop, amelynek az értékeket úgy tűnik, hogy megfelel a (pirossal jelölt), vagy hiányoznak (üres) aránya. Ez a példa az adatkészlet a részleg üres értékeket és a érvénytelen adatokat.

    ![Táblázatos nézet](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Adatkezelés

Valós adatok szinte mindig rendezetlen, és ritkán van, eligazíthatja csomagolva. A parancsfájlokat és a kapcsolódó üzleti logika egy láncot általában adatok törlése szükséges. A Dataiku DSS biztosít egy dedikált **labor** eszközt, hogy ez a feladat felhasználóbarátabb.

1. Kattintson a **labor** jobb felső sarokban.

    ![Labor gomb](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. A Lab-ablak nyílik meg. A tesztkörnyezetben, ahol, iteratív működni feltörni további, az adatkészlet. Ez az oktatóanyag azt ismerteti, hogy a vizuális elemzéshez aspektus. Válassza ki a **új** gomb alatti a vizuális elemzéshez. Adjon meg egy nevet az elemzési kéri. Az alapértelmezett nevet, egyelőre hagyja, majd kattintson a **létrehozás**.

    ![Labor létrehozása](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Válassza ki a **gyors oszlopok stats** gombra az oldal jobb felső sarkában.

    ![Gyors oszlopok stats](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Az adattípusok és idővonal-alapú grafikonok alatt megjelenített értékek statisztikája látja a **oszlopok gyors nézetet** ablaktáblán.

    ![Oszlopok gyors megtekintése](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Most már megvizsgálhatja a mintaadatokkal DSS. Karbantartás és az adatok, és új vizualizációkat létrehozni.

Olvassa el részletes oktatóanyagok [ismerje meg a Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>További lépések

* [A Dataiku DSS dokumentáció](https://doc.dataiku.com/dss/latest/).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása](hdinsight-hadoop-customize-cluster-linux.md): ismerje meg, hogyan telepíthet további alkalmazásokat használ.
* [Üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md): üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetése.
