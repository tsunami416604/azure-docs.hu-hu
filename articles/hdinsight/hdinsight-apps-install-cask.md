---
title: "Telepítés közzétett alkalmazás - Datameer - Azure HDInsight |} Microsoft Docs"
description: "Telepítheti és használhatja a Datameer külső Hadoop alkalmazás."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 4b83f2a2228ef0dd7fa56b5a71b267d1e4302620
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Telepítse a közzétett alkalmazás - Cask adatok alkalmazás Platform (CDAP)

Ez a cikk ismerteti, hogyan telepíthetnek és futtathatnak a [CDAP](http://cask.co/products/cdap/) on Azure HDInsight Hadoop-alkalmazások közzététele. A HDInsight-alkalmazás platform áttekintését, és a rendelkezésre álló független szoftverszállító (ISV) listáját közzétett alkalmazások: [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-cdap"></a>CDAP kapcsolatos

A Hadoop-alkalmazások fejlesztésével kihívást jelenthet.  Nincs nagy és az egyre növekvő száma Hadoop technológia bővítményeket, amelyek integrálásához némi időbe telhet. Adatfolyam figyelése és gyűjtése metrikák megkövetelheti egy külön megoldásban összeállításakor.

### <a name="how-does-cdap-help"></a>Hogyan segít a CDAP?

A Cask adatok alkalmazás Platform (CDAP) egy integrációs platformján a Big Data. CDAP lehetővé teszi az alkalmazások létrehozásához és nem az alkalmazás mögötti infrastruktúra a fókuszt.

CDAP fogalmait és absztrakt entitással egészült ki, hogy ismeri a fejlesztők számára használja. Ezek absztrakt entitással egészült ki elrejtése a belső rendszerek összetett szolgáltatásokkal, és ösztönözzék újrahasznosításának megoldások.

Egy CDAP bővítmény nevű [Cask Hydrator](http://cask.co/products/hydrator/) fejlesztésére és adatok folyamatok kezelése felhasználói felülete lehetőséget nyújt. Adatok adatcsatorna áll különböző * beépülő modulok, feladatokat végrehajtó adatgyűjtést, transformation, elemzés és utáni futtatási műveletek, például.

Minden CDAP beépülő modul jól meghatározott felülettel rendelkezik, hogy értékeli a különböző technológiák csak egy beépülő modul lecserélését egy másikat, anélkül, hogy a többi alkalmazáshoz érint.

CDAP *folyamatok* adja meg az alkalmazás az adatok magas szintű képi folyamata. Ez a képi megjelenítés szemlélteti a végpont adatfeldolgozást adatátalakítást és elemzések, keresztül származó adatok, és végül a külső tárolja.

Az alábbi példa adatok adatcsatorna ingests valós időben twitterről adatok, majd kiszűri néhány előre definiált feltételeknek megfelelő Twitter-üzeneteket. Az adatok csővezeték alakítja át a nyers tweetet adatokat, és, hogy az adatok olvashatóbb formátumban, majd a Twitter-üzeneteket az értékek szerint csoportosítja és kiírja az eredményeket a HBase projektek tárolja.

![CDAP folyamat](./media/hdinsight-apps-install-cask/pipeline.png)

A végpont feldolgozási folyamat használatával készített a **Cask Hydrator felhasználói felület**, a beépülő modul felület és a fogd és vidd működésére űrlap kapcsolatok minden lépés használatával. Különítse el, és egymástól függetlenül az egyes beépülő modul működésének módosítását. CDAP, hasonló folyamatok segítségével kell a beépített és órában érvényesítve. A tipikus Hadoop világ hozhat létre, az ilyen megoldások is igénybe vehet néhány nap.

CDAP is nevezett kiterjesztést tartalmaz [Cask követő](http://cask.co/products/tracker/) vizuálisan nyomkövetési adatok, mert az alkalmazás áthaladó. Cask követő hozzáadja *adatszabályozást* a rendszer, hogy az adategységek hivatalosan felügyelt az alkalmazáson keresztül. Nyomon követheti az egyes adatpontokban Leszármaztatás, megfelelő gyűjtéséhez és az adatok auditnapló a folyamat során.

Hogyan áramlik a fenti feldolgozási adatok szemléltetésére itt van:

![CDAP Rendszerleállási események követése](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Előfeltételek

Ez az alkalmazás egy új HDInsight-fürtöt, vagy egy meglévő fürt telepítéséhez a következő konfigurációval kell rendelkeznie:

* Fürt réteg: Standard
* Fürt típusa: HBase
* Fürt verziószáma: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Telepítse a CDAP közzétett alkalmazás

Ez, és más elérhető ISV alkalmazások telepítésével kapcsolatos részletes útmutatás olvasható [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Indítsa el a CDAP

1. A telepítés után indítsa el CDAP Azure-portálon a fürtből a a **beállítások** panelen, jelölje be **alkalmazások** alatt a **általános** kategóriát. A telepített alkalmazások ablaktábla listázza a telepített alkalmazások.

    ![Telepített CDAP alkalmazás](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Ha CDAP választja, megjelenik egy hivatkozást a weblap és HTTP-végpont, valamint az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adataival.

    ![Hitelesítés](./media/hdinsight-apps-install-cask/auth.png)

4. Történő bejelentkezés után tekintse Cask CDAP grafikus felhasználói felület kezdőlapján.

    ![Cask grafikus felhasználói felület kezdőlapján](./media/hdinsight-apps-install-cask/gui.png)

5. A CDAP felület megismeréséhez kattintson a **Cask piaci** menü hivatkozás felett a lapot.

    ![Cask piaci hivatkozás](./media/hdinsight-apps-install-cask/cask-market.png)

6. Válassza ki **hozzáférési napló minta** a listából.

    ![Hozzáférési napló minta](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Kattintson a **terhelés** megerősítéséhez.

    ![Kattintson a terhelés](./media/hdinsight-apps-install-cask/market-load.png)

8. A mellékelt adatok nézet jelenik meg. Válassza ki **következő**.

    ![Hozzáférési napló minta - adatok megtekintése](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Válassza ki **adatfolyam** a cél típusa, adja meg a célkiszolgáló nevét, majd válasszon **Befejezés**.

    ![Hozzáférési napló minta - célkiszolgáló kijelölése](./media/hdinsight-apps-install-cask/market-destination.png)

10. Miután sikeresen betöltötte a datapack, válassza ki **adatfolyam részleteinek megtekintése**.

    ![Datapack sikeresen feltöltve.](./media/hdinsight-apps-install-cask/market-view-details.png)

11. A névtér metaadatok engedélyezéséhez jelölje be **engedélyezése** hozzáférési napló részleteit megjelenítő oldalon a használati lapon.

    ![-Betöltött – hozzáférési napló minta metaadatainak engedélyezése](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Miután a metaadatok engedélyezve van, megjelenik egy grafikonon naplózási üzenet információk megjelenítése.

    ![Hozzáférési napló minta - metaadatok engedélyezve](./media/hdinsight-apps-install-cask/log-metadata.png)

13. A napló adatokba, válassza ki a **böngészés** ikonja felett a lap.

    ![Hozzáférési napló minta - felfedezés](./media/hdinsight-apps-install-cask/log-explore.png)

14. Megjelenik egy minta SQL-lekérdezést. Módosítsa ezt a kívánt, majd válassza ki, nyugodtan **Execute**.

    ![Hozzáférési napló minta - adatkészlet lekérdezés tallózása](./media/hdinsight-apps-install-cask/log-query.png)

15. A lekérdezés befejeződését követően válassza ki a **nézet** műveletek oszlopban a ikonra.

    ![Hozzáférési napló minta - befejeződött nézet lekérdezése](./media/hdinsight-apps-install-cask/log-query-view.png)

16. A lekérdezés eredménye megjelenik.

    ![Hozzáférési napló minta - lekérdezés eredményei](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>További lépések

* [Cask dokumentáció](http://cask.co/resources/documentation/).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Parancsfájlművelet Linux-alapú HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájl művelettel.
* [Üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md): egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetéséhez.
