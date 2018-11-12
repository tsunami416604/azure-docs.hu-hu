---
title: Közzétett alkalmazás – Datameer – Azure HDInsight telepítése
description: Telepítheti és használhatja a Datameer külső Apache Hadoop-alkalmazásokat.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 97d99aa59c490cf2dcdd4a69f32411a051942d36
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037804"
---
# <a name="install-published-application---datameer"></a>Közzétett alkalmazás – Datameer telepítése

Ez a cikk bemutatja, hogyan telepítheti és futtathatja a [Datameer](https://www.datameer.com/) közzé az Azure HDInsight az Apache Hadoop-alkalmazás. A HDInsight-alkalmazásplatform áttekintése és a egy listát az elérhető független szoftverszállító (ISV) közzétett alkalmazások létrehozásáról: [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-datameer"></a>Tudnivalók a Datameer

Datameer a Hadoop platformon, kiterjesztésének meglévő Azure HDInsight képességeit és a quick integration, előkészítési és strukturált és strukturálatlan adatok elemzése egy natív alkalmazást. Datameer férhet hozzá több mint 70 források és formátumok: strukturált, félig strukturált és strukturálatlan. Töltse fel az adatokat, vagy használhatja azok egyedi adatok hivatkozások segítségével szerez adatokat igény szerinti közvetlenül. A Datameer önkiszolgáló funkciókat és ismerős számolótábla-felülettel Big Data-technológia bonyolultságát is csökkenti, és gyorsítja idő Insight. A számolótábla-felülettel, amely optimalizált, Hadoop-feladatok majd fordítja deklaratív Képletek beírása egy egyszerű mechanizmust biztosít. A Datameer és az üzleti intelligenciára épülő (BI) és az Excel-ismereteit használhatja Hadoop a felhőben gyorsan. További információkért lásd: a [Datameer dokumentáció](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Előfeltételek

Telepíti az alkalmazást egy új HDInsight-fürtöt, vagy egy meglévő fürthöz, a következő konfigurációval kell rendelkeznie:

* Fürt szint: Standard
* Fürt típusa: Hadoop
* Fürt verziója: 3.4

## <a name="install-the-datameer-published-application"></a>A közzétett alkalmazás telepítése a Datameer

Ezzel és más elérhető ISV-alkalmazások telepítésének lépésenkénti útmutatójáért olvassa el a [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Indítsa el a Datameer

1. A telepítés után már el is indíthatja Datameer a fürtből az Azure Portalon lépjen a **beállítások** ablaktáblán, majd kattintson a **alkalmazások** alatt a **általános** kategória . A telepített alkalmazások panelen a telepített alkalmazás fel van sorolva.

    ![A Datameer telepített alkalmazás](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Datameer kiválasztásakor megjelenik egy hivatkozás a weblap és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Az alkalmazás első indításakor, két módon licenc: vagy egy 14 napos ingyenes próbaidőszakra, vagy egy meglévő licenc aktiválása.

    ![Licencbeállítások](./media/hdinsight-apps-install-datameer/license.png)

4. Miután befejezte a kiválasztott lehetőség, jelenik meg egy bejelentkezési űrlapot. Adja meg az alapértelmezett hitelesítő adatokat a bejelentkezési űrlap előtt jelenik meg. A bejelentkezés után fogadja el a szoftverlicenc-szerződés, a folytatáshoz.

    ![Bejelentkezés](./media/hdinsight-apps-install-datameer/login.png)

A következő lépések bemutatják egy "Hello World" bemutató.

1. [Töltse le a mintát CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Kattintson a **+** jelentkezzen a Datameer irányítópultjának tetejénél, és kattintson a **fájlfeltöltés**.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-datameer/upload.png)

3. A feltöltés párbeszédpanelen keresse meg és válassza a **Hello World.csv** a letöltött fájlt. Győződjön meg arról, hogy a **Fájltípus** CSV értéke / TSV. Kattintson a **Tovább** gombra. Tartsa kattintva **tovább** mindaddig, amíg a varázsló végére ér.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. A fájl neve **Hello World** alatt egy új mappát. Nevezze át az új mappába, "Bemutató". Kattintson a **Mentés** gombra.

    ![Mentés](./media/hdinsight-apps-install-datameer/save.png)

5. Kattintson a **+** jelentkezzen még egyszer, és válassza **munkafüzet** hozhat létre egy új munkafüzetet, az adatok.

    ![A munkafüzet hozzáadása](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Bontsa ki a **adatok** mappában **FileUploads**, akkor a **bemutató** a "Hello World" fájl mentésekor létrehozott mappába. Válassza ki **Hello World** űrlap-fájlok listáját, majd kattintson a **adatok hozzáadása**.

    ![Mentés](./media/hdinsight-apps-install-datameer/select-file.png)

7. Az egy számolótábla-felülettel betöltött adatokat láthatja. Válassza ki az adatok egy részét, jelölje be a **szűrő** gomb az eszköztáron.

    ![Szűrő gomb](./media/hdinsight-apps-install-datameer/filter-button.png)

8. A szűrés párbeszédpanelen válassza ki a **Város** oszlopban **egyenlő** operátor, és írja be **Chicago** a szűrő szövegmezőbe. Ellenőrizze a **szűrő létrehozása az új lap** jelölőnégyzetet, majd válassza ki **szűrő létrehozása**.

    ![Szűrő alkalmazása](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Mentse a munkafüzetet kattintva **fájl**, majd **mentése**. Adjon meg egy nevet, például a "Hello World munkafüzet".

10. Megjelennek lehetőségek, hogy hogyan és mikor fusson a munkafüzetet. Egyelőre hagyja meg az összes beállítás az alapértelmezett értékeket, majd ellenőrizze a **kezdő számítási folyamat közvetlenül utána Mentés**, és válassza ki **mentése**.

    ![A munkafüzet mentése](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer hatékony vizualizációs eszközöket biztosít. Az adatok megjelenítéséhez, hozzon létre egy szemléltető ábra. Válassza ki a **+** a irányítópultjának tetejénél aláírásához, majd válassza a **szemléltető ábra**.

    ![Adja hozzá a szemléltető ábra](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Húzza az sávdiagram widget widgetek listája a bal oldalon az 1. lépésben a következő ábrán látható módon. Ezután haladjon végig a jobb oldalon az adatok tallózója adatok mappa, és bontsa ki a munkafüzetet, majd a munkalap hozzáadva a szűrővel (2. lépés). Húzza a **neve** oszlop tetején a sávdiagramot oszlopdiagramra cseréli, majd be vetett keresztül a **címke** cél a munkafüzet neve oszlop beállítani a diagram címke mező (3. lépés).

    ![Szemléltető ábra](./media/hdinsight-apps-install-datameer/infographic.png)

13. Kor állítja be a diagram Y tengely, húzza a **életkor** oszlop át a diagramra **adatok** mező.

    ![Szemléltető ábra](./media/hdinsight-apps-install-datameer/infographic-age.png)

Gratulálunk! Ezzel létrehozott egy vizualizációt, az adatok kód írása nélkül. Most már megvizsgálhatja változata létezik, és további vizualizációkat.

## <a name="next-steps"></a>További lépések

* [Datameer dokumentáció](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása](hdinsight-hadoop-customize-cluster-linux.md): ismerje meg, hogyan telepíthet további alkalmazásokat használ.
* [Üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md): üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetése.
