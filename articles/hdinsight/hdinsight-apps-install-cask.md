---
title: Közzétett alkalmazás – Datameer – Azure HDInsight telepítése
description: Telepítheti és használhatja a Datameer külső Hadoop-alkalmazásokat.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: a8961da0a62815ce60a6ee694d57f3172b81718b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592176"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Közzétett alkalmazás – Cask Data Application Platform (CDAP) telepítése

Ez a cikk bemutatja, hogyan telepítheti és futtathatja a [CDAP](http://cask.co/products/cdap/) közzé az Azure HDInsight Hadoop-alkalmazásokat. A HDInsight-alkalmazásplatform áttekintése és a egy listát az elérhető független szoftverszállító (ISV) közzétett alkalmazások létrehozásáról: [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-cdap"></a>CDAP kapcsolatban

A Hadoop-alkalmazások fejlesztéséhez használható kihívást jelenthet.  Van egy nagy és egyre szélesebbé váló Hadoop technológia bővítmények, ami eltarthat egy ideig való integrálásához. Az adatfolyam figyelése és gyűjtése metrikák megkövetelheti külön megoldás létrehozása.

### <a name="how-does-cdap-help"></a>Hogyan segít a CDAP?

A Cask Data Application Platform (CDAP) az Big Data-integrációs platform. CDAP lehetővé teszi az alkalmazások létrehozására, nem pedig az alapul szolgáló infrastruktúra a fókuszt.

CDAP használ alapvető fogalmait és absztrakciók, amely a fejlesztők számára is felismerhetők. Ezek absztrakciók kiküszöböli a belső rendszerek elrejtéséhez, és ösztönözze az újrahasznosíthatóság megoldások.

Egy CDAP bővítmény nevű [Cask Hydrator](http://cask.co/products/hydrator/) hozhat létre és kezelhet adatfolyamatokat állíthat össze egy felhasználói felületet biztosít. Egy adatfolyamat áll különböző * feladatokat elvégző beépülő modulok, például adatgyűjtés, átalakítási, elemzési és a Futtatás utáni műveletek.

Minden egyes CDAP beépülő modul jól definiált felülettel rendelkezik, úgy, hogy különböző technológiák kiértékelése annyit egy beépülő modul cserélje le egy másikat, az alkalmazás többi touch nélkül.

CDAP *folyamatok* adjon meg egy magas szintű képi folyamatot az adatok az alkalmazásban. Ezt a vizualizációt az adatfeldolgozást az adatátalakítások és elemzések, az adatok a teljes körű folyamatot mutatja, és végül egy külső adatok tárolására.

Egy adatfolyamat a következő példa feltölti a valós idejű twitter-adatok, majd szűri ki az egyes tweetek előre meghatározott feltételek alapján. Az adatfolyamatok nyers tweet adatátalakítást, és projektek, hogy az adatok importálása egy olvashatóbb formátumban, majd a tweeteket értékek szerint csoportosítja és írja az eredményeket egy HBase tárolja.

![CDAP folyamat](./media/hdinsight-apps-install-cask/pipeline.png)

A végpontok közötti folyamatok használatával lett összeállítva a **Cask Hydrator felhasználói felület**, a beépülő modul felület és fogd és vidd szolgáltatással űrlap kapcsolatok közötti minden egyes szakaszhoz. Azonosíthatók, és egymástól függetlenül minden beépülő modul működésének módosítását. CDAP használata esetén hasonló a folyamatok lehetnek beépített és (óra) ellenőrzi. A tipikus Hadoop világban hozhat létre, az ilyen megoldások is igénybe vehet néhány nap.

CDAP is biztosít egy bővítmény nevű [Cask Tracker](http://cask.co/products/tracker/) vizuálisan nyomkövetési adatok, mert az alkalmazáson keresztül zajlik. Hozzáadja a cask Tracker *adatszabályozást* a rendszer, hogy az adategységek hivatalosan történik az alkalmazásban. Nyomon követheti az egyes adatpontok leszármaztatási, releváns metrikákat gyűjthet, és az adat-auditnapló a folyamat során.

Íme egy hogyan adat áramlik a fenti folyamat ábrája:

![CDAP tracker](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Előfeltételek

Telepíti az alkalmazást egy új HDInsight-fürtöt, vagy egy meglévő fürthöz, a következő konfigurációval kell rendelkeznie:

* Fürt szint: Standard
* Fürt típusa: HBase
* Fürt verziója: 3.4-es, 3.5

## <a name="install-the-cdap-published-application"></a>A közzétett alkalmazás telepítése a CDAP

Ezzel és más elérhető ISV-alkalmazások telepítésének lépésenkénti útmutatójáért olvassa el a [külső gyártótól származó Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Indítsa el a CDAP

1. A telepítés után indítsa el a CDAP a fürtből az Azure Portalon lépjen a **beállítások** ablaktáblán, majd válassza **alkalmazások** alatt a **általános** kategória. A telepített alkalmazások panelen a telepített alkalmazás fel van sorolva.

    ![Telepített CDAP alkalmazás](./media/hdinsight-apps-install-cask/cdap-app.png)

2. CDAP kiválasztásakor megjelenik egy hivatkozás a weblap és HTTP-végpontot, és emellett az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adataival.

    ![Hitelesítés](./media/hdinsight-apps-install-cask/auth.png)

4. Bejelentkezés után tekintse meg a Cask CDAP grafikus felhasználói felület kezdőlapján.

    ![A cask grafikus felhasználói felület kezdőlapján](./media/hdinsight-apps-install-cask/gui.png)

5. Fedezze fel a CDAP felületen, kattintson a **Cask piaci** menü hivatkozásra a lap fölött.

    ![Cask piaci hivatkozás](./media/hdinsight-apps-install-cask/cask-market.png)

6. Válassza ki **hozzáférési napló minta** a listából.

    ![Hozzáférési napló minta](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Kattintson a **terhelés** megerősítéséhez.

    ![A Betöltés gombra](./media/hdinsight-apps-install-cask/market-load.png)

8. A csomagban foglalt mintaadatok nézete jelenik meg. Válassza ki **tovább**.

    ![Hozzáférési napló minta - adatok megtekintése](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Válassza ki **Stream** a cél típusa, adja meg a célkiszolgáló nevét, majd válassza ki **Befejezés**.

    ![Hozzáférési napló minta - célkiszolgáló kijelölése](./media/hdinsight-apps-install-cask/market-destination.png)

10. Miután sikeresen betöltötte a datapack, válassza ki a **Stream részleteinek megtekintése**.

    ![Datapack sikeresen feltöltve.](./media/hdinsight-apps-install-cask/market-view-details.png)

11. A névtérhez tartozó metaadatok engedélyezéséhez jelölje be **engedélyezése** hozzáférési napló részleteit megjelenítő oldalon a használati lap.

    ![-Betöltött – hozzáférési napló minta metaadatainak engedélyezése](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Metaadatok engedélyezése után megjelenik egy diagram megjelenítése a naplózási üzenetek adatait.

    ![Hozzáférési napló minta - metaadatok engedélyezve](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Ismerje meg a naplózási adatokat, válassza a **Intéző** ikont a lap fölött.

    ![Napló minta – felfedezése](./media/hdinsight-apps-install-cask/log-explore.png)

14. Megjelenik egy minta SQL-lekérdezést. Nyugodtan módosítsa a kívánt, majd válassza ki azt **Execute**.

    ![Hozzáférési napló minta - adatkészlet lekérdezéssel felfedezése](./media/hdinsight-apps-install-cask/log-query.png)

15. A lekérdezés futtatása után válassza ki a **nézet** ikonra a műveletek oszlopban láthatók.

    ![Hozzáférési napló minta - lekérdezés befejeződött megtekintése](./media/hdinsight-apps-install-cask/log-query-view.png)

16. A lekérdezés eredményét láthatja.

    ![Hozzáférési napló minta - lekérdezés eredményei](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>További lépések

* [Cask dokumentáció](http://cask.co/resources/documentation/).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása](hdinsight-hadoop-customize-cluster-linux.md): ismerje meg, hogyan telepíthet további alkalmazásokat használ.
* [Üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md): üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetése.
