---
title: Telepítés közzétett alkalmazás - Datameer - Azure HDInsight |} Microsoft Docs
description: Telepítheti és használhatja a Datameer külső Hadoop alkalmazás.
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
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---datameer"></a>Közzétett alkalmazás - Datameer telepítése

Ez a cikk ismerteti, hogyan telepíthetnek és futtathatnak a [Datameer](https://www.datameer.com/) on Azure HDInsight Hadoop-alkalmazások közzététele. A HDInsight-alkalmazás platform áttekintését, és a rendelkezésre álló független szoftverszállító (ISV) listáját közzétett alkalmazások: [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-datameer"></a>Datameer kapcsolatos

Datameer Hadoop platform, kibővítése meglévő Azure HDInsight képességek és a quick integration, előkészítési és strukturált és strukturálatlan adatok elemzése a natív alkalmazás, amely. Datameer férhetnek hozzá több mint 70 források és formátumok: strukturált, félig strukturált és strukturálatlan. Közvetlenül feltölteni az adatokat, vagy használja a segítségével szerez adatokat egyedi adatkapcsolatok igény szerint. Datameer által az önkiszolgáló funkciók és ismerős számolótábla felület egyszerűbben Big Data-technológia, és időt megtakarítva növekszik. A táblázat felülete egyszerű mechanizmust, majd lefordítani Hadoop-feladatokat optimalizált deklaratív képletek megadásához. Datameer és az üzleti intelligenciával és Excel képességek segítségével Hadoop a felhőben gyorsan. További információkért lásd: a [Datameer dokumentáció](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Előfeltételek

Ez az alkalmazás egy új HDInsight-fürtöt, vagy egy meglévő fürt telepítéséhez a következő konfigurációval kell rendelkeznie:

* Fürt réteg: Standard
* Fürt típusa: Hadoop
* Fürt verziószáma: 3.4

## <a name="install-the-datameer-published-application"></a>Telepítse a Datameer közzétett alkalmazás

Ez, és más elérhető ISV alkalmazások telepítésével kapcsolatos részletes útmutatás olvasható [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Indítsa el a Datameer

1. A telepítés után is elindíthatja, Datameer Azure-portálon a fürtből a a **beállítások** ablaktáblán, majd kattintson a **alkalmazások** alatt a **általános** kategória . A telepített alkalmazások ablaktábla listázza a telepített alkalmazások.

    ![Telepített Datameer alkalmazás](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Ha Datameer választja, megjelenik egy hivatkozást a weblap és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Az első indítási két módon licenc: vagy a 14 napos ingyenes próbaidőszakra, vagy egy meglévő licenckiszolgáló aktiválása.

    ![Licencbeállítások](./media/hdinsight-apps-install-datameer/license.png)

4. A kiválasztott beállítás befejezése után meg kell jelenik meg a bejelentkezési űrlap. Adja meg az alapértelmezett hitelesítő adatokat a bejelentkezési űrlap előtt jelenik meg. A bejelentkezés után fogadja el a szoftverlicenc-szerződések a folytatáshoz.

    ![Bejelentkezés](./media/hdinsight-apps-install-datameer/login.png)

Az alábbi lépéseket a "Hello, World" bemutató megjelenítése.

1. [Töltse le a fürt megosztott kötetei szolgáltatás minta](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Kattintson a **+** jelentkezzen a Datameer irányítópult felett, és kattintson a **fájlfeltöltés**.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-datameer/upload.png)

3. A feltöltés párbeszédpanelen keresse meg és jelölje ki a **Hello World.csv** letöltött fájl. Győződjön meg arról, hogy a **Fájltípus** CSV értéke / TSV. Kattintson a **Tovább** gombra. Tartsa kattintva **következő** mindaddig, amíg a varázsló végére ér.

    ![Fájlfeltöltés](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. A fájl neve **Hello World** alatt egy új mappát. "Bemutató" módon nevezze át az új mappába. Kattintson a **Mentés** gombra.

    ![Mentés](./media/hdinsight-apps-install-datameer/save.png)

5. Kattintson a **+** jelentkezzen még egyszer, és válassza ki **munkafüzet** az adatok új munkafüzet létrehozásához.

    ![A munkafüzet hozzáadása](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Bontsa ki a **adatok** mappa, **FileUploads**, akkor a **bemutató** a "Hello, World" fájl mentése során létrehozott mappába. Válassza ki **Hello World** alkotják a fájlok listáját, majd kattintson az **adatok hozzáadása**.

    ![Mentés](./media/hdinsight-apps-install-datameer/select-file.png)

7. Az adatokat egy táblázat felületén betöltött látja. Válassza ki az adatok egy részét, válassza ki a **szűrő** gomb az eszköztáron.

    ![Szűrő gomb](./media/hdinsight-apps-install-datameer/filter-button.png)

8. A szűrés párbeszédpanelen válassza ki a **Város** oszlopban **egyenlő** operátor, és írja be **Chicago** a Szűrő mezőbe. Ellenőrizze a **szűrő létrehozása az új lap** jelölőnégyzetet, majd válassza ki **szűrő létrehozása**.

    ![Szűrés](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Gombra kattintva mentse a munkafüzetet **fájl**, majd **mentése**. Adjon meg egy nevet, például a "Hello World munkafüzet".

10. A varázsló beállítások arról, hogyan és mikor készüljön a munkafüzetet. Most az összes beállítás az alapértelmezett értékükön hagyja, akkor ellenőrizze a **Start számítási folyamat közvetlenül utána Mentés**, és válassza ki **mentése**.

    ![A munkafüzet mentéséhez](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer hatékony képi megjelenítés eszközöket biztosít. Az adatok megjelenítéséhez, hozzon létre egy Infographic. Válassza ki a **+** jelentkezzen az irányítópult felett, és válasszon **Infographic**.

    ![Infographic hozzáadása](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Húzza a sávdiagram widget widgeteket közül a bal oldali az 1. lépésben a következő ábrán látható módon. A következő haladjon végig a adatok böngészőben, a jobb oldali adatok mappájában, bontsa a munkafüzetet, majd a munkalap hozzáadni a szűrőt (2. lépés). Húzza a **neve** oszlop felső részén a sávdiagram és az vetett keresztül a **címke** cél-és a munkafüzet névoszlopa állítja be a diagram címke mező (3. lépés).

    ![Szemléltető ábra](./media/hdinsight-apps-install-datameer/infographic.png)

13. A diagramterület Y tengely kora állítja, az egérrel húzzon át a **kora** oszlopot a diagram **adatok** mező.

    ![Szemléltető ábra](./media/hdinsight-apps-install-datameer/infographic-age.png)

Gratulálunk! Az adatok képi megjelenítés létrehozott programozás nélkül. Változatok és további képi megjelenítések most vizsgálatát.

## <a name="next-steps"></a>További lépések

* [Datameer dokumentáció](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Parancsfájlművelet Linux-alapú HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájl művelettel.
* [Üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md): egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetéséhez.
