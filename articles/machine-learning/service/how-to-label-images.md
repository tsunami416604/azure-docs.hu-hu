---
title: A Azure Machine Learning adatcímkéző eszközének használata
title.suffix: Azure Machine Learning
description: Ez a cikk bemutatja, hogyan használhatja az adatcímkéző eszközöket egy Azure Machine Learning címkézési projektben.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: af12361ed11d0a16e5a5d0cfe5989bb3918ce154
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586396"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Képek címkézése egy címkézési projektben

Miután a projekt rendszergazdája létrehozta a címkézési projektet a Azure Machine Learning Studióban, a címkézés eszköz használatával gyorsan előkészítheti a Machine learning-projekt adatait. 

> [!div class="checklist"]
> * A címkézési projektek elérése
> * Eszközök címkézése
> * Az eszközök használata adott címkézési feladatokhoz

## <a name="prerequisites"></a>Előfeltételek

* A futó adatcímkéző projekt címkézési portáljának URL-címe
* [Microsoft-fiók](https://account.microsoft.com/account) vagy
* A szervezet és a projekt Azure Active Directory fiókja

> [!Note]
> A projekt rendszergazdája a **projekt részletei** oldalon található **részletek** lapon találja meg a címkézési portál URL-címét. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Bejelentkezés a projekt címkézési portálján

Lépjen a projekt rendszergazdája által megadott címkéző portál URL-címére. 

Jelentkezzen be az e-mail-fiókkal, amelyet a rendszergazda felhasznált a csapathoz való hozzáadáshoz. A legtöbb felhasználó esetében a bejelentkezés a Microsoft-fiók történik. Ha a címkézési projekt Azure Active Directory használ, a bejelentkezés módja. 

## <a name="understanding-the-labeling-task"></a>A címkézési feladat ismertetése

Miután bejelentkezett, a projekt áttekintés lapjára kerül. 

Első **lépésként tekintse meg a részletes utasításokat**. Ezek az utasítások a projektre jellemzőek, és leírják, hogy milyen típusú adatokra van szükség, hogyan kell döntéseket hoznia és egyéb releváns információkat. Ha elkészült, térjen vissza a projekt lapra, és válassza a **címkézés indítása**lehetőséget.

## <a name="common-features-of-the-labeling-task"></a>A címkézési feladat gyakori funkciói

Az összes rendszerkép-címkézési feladat magában foglalja a projekt rendszergazdája által meghatározott megfelelő címke vagy címkék kiválasztását. Az első kilenc címke közül választhat a billentyűzetén található számgombok használatával.  

A képbesorolási feladatok lehetővé teszik több rendszerkép egyidejű megjelenítését. A különböző elrendezések a képrész felett található ikonokkal választhatók ki. Az összes megjelenített képet egyszerre kiválaszthatja az **összes kijelölése** gomb megnyomásával. Válassza ki az egyes képeket a kép területének jobb felső sarkában található körkörös kijelölés gomb használatával. A címke alkalmazásához ki kell választania legalább egy képet. Ha több rendszerkép is ki van választva, a címke kiválasztásakor a rendszer az adott címkét a kiválasztott fényképek mindegyikére alkalmazza.

Itt egy 2x2-es elrendezést választottunk, amely arra készül, hogy az "emlős" címkét a medve és az Orca képeire alkalmazza. A Shark nevű rendszerkép már "porcos hal" néven van megjelölve, és az Iguana még nincs megjelölve.

![Több rendszerkép-elrendezés és-kijelölés](media/how-to-label-images/layouts.png)

> [!Important] 
> Csak olyan elrendezést váltson át, amely a címkézetlen adatlapok friss oldalát jelöli. Az elrendezések váltás törli az oldal folyamatban lévő címkézési feltételeit. 

Az Azure lehetővé teszi a **Küldés** gombot, amikor címkézte az összes képet az oldalon. Kattintson a **Submit (Küldés** ) gombra a munka mentéséhez. 

Miután elküldte a címkéket az adatokhoz, az Azure frissíti a lapot egy új rendszerképpel a munkahelyi várólistából.  

## <a name="tagging-images-for-multi-class-classification"></a>Képek címkézése többosztályos besoroláshoz

Ha a projekt "képbesorolás többosztályos" típusú, akkor egyetlen címkét fog rendelni a teljes képhez. Bármikor válassza az **utasítások** lapot, és keresse meg a **részletes utasításokat** a Project-specifikus útmutatás megtekintéséhez. 

Ahogy korábban már említettük, a képek megjelenítéséhez több elrendezés közül választhat. Ha a rendszerkép kiválasztása és a címke kiosztása után elvégezte a hiba elhárítását, akkor azt is megteheti. Ha a kép alatt látható címkén a `X` céljára kattint, törölheti a címkét. Ha kiválasztja a rendszerképet, és másik osztályt választ, a címke az újonnan kiválasztott értékre vált.

## <a name="tagging-images-for-multi-label-classification"></a>Képek címkézése a többcímkés besoroláshoz

Ha "rendszerkép besorolása többcímkés" típusú projekttel dolgozik, egy _vagy több_ címkét fog alkalmazni egy képre. Bármikor válassza az **utasítások** lapot, és keresse meg a **részletes utasításokat** a Project-specifikus útmutatás megtekintéséhez. 

Jelölje ki a címkét, majd kattintson a címkére. Ha kiválasztja a címkét, az összes kijelölt képre alkalmazza, és kijelöli őket. További címkék alkalmazásához újra ki kell választania a lemezképeket. Ez az animáció egy többcímkés címkézési oldalt mutat be:

* **Válassza az összes lehetőséget** az "Ocean" címke alkalmazásához
* Egyetlen rendszerkép van kiválasztva és címkézett "Vértes"
* Három kép van kiválasztva és címkézett "Wide Angle"

![A többcímkés folyamatot bemutató animáció](media/how-to-label-images/multilabel.gif)

A hiba elhárításához kattintson a `X` az egyes címkék törléséhez, vagy válassza ki a képeket, és válassza ki a címkét, amely törli a címkét az összes kijelölt rendszerképből. Ez a forgatókönyv itt látható, ahol a "Land" gombra kattintva a rendszer törli a címkét a két kiválasztott rendszerképből.

![Több kiválasztást ábrázoló képernyőfelvétel](media/how-to-label-images/multiple-deselection.png)

Az Azure csak akkor engedélyezi a **Küldés** gombot, ha legalább egy címkét alkalmaz az egyes képekre. Kattintson a **Küldés** gombra a munka mentéséhez.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Az objektumok észleléséhez szükséges képek és határoló mezők címkézése

Ha a projekt "objektum-azonosító (határoló mezők)" típusú, adjon meg egy vagy több határolókerett a képen, és alkalmazzon egy címkét a mezőbe. Minden képnek több határolókeret is lehet, amelyek mindegyike egyetlen címkével rendelkezik. A **részletes utasítások megtekintésével** megállapíthatja, hogy a projekthez tartozó több határolókeret hozzáadása megfelelő-e.

1. Válasszon címkét a létrehozni kívánt határoló mezőhöz
1. Jelölje be a **téglalap alakú Box** eszközt ![téglalap alakú box eszköz](media/how-to-label-images/rectangular-box-tool.png) vagy nyomja le az "R" billentyűt. 
1. Kattintson és húzza átlósan a célhelyen egy durva határoló mező létrehozásához
    * Állítsa be a határolókeret szegélyét úgy, hogy a mező éleit vagy sarkaira kattint.

![Képernyőfelvétel az alapszintű határolókeret létrehozásáról](media/how-to-label-images/bounding-box-sequence.png)

Ha törölni szeretné a határolókeret mezőjét, kattintson a létrehozás után a határolókeret mellett megjelenő X alakú célra.

Egy meglévő határolókeret címkéjét nem lehet újból hozzárendelni. Ha a címke-hozzárendeléssel hibát vétett, törölnie kell a határolókeret mezőt, és létre kell hoznia egy újat a megfelelő címkével.

Alapértelmezés szerint a meglévő határoló mezők szerkeszthetők. A **régiók zárolása/feloldása** eszköz ![zárolás/zárolás feloldása eszköz](media/how-to-label-images/lock-bounding-boxes-tool.png) vagy az "L" a viselkedést váltja ki. Ha a régiók zárolva vannak, előfordulhat, hogy csak egy új határolókeret alakját vagy helyét szeretné megváltoztatni.

A meglévő határolókeret módosításához használja a **régiók manipulációs** eszköz ![Regions manipulációs eszköz](media/how-to-label-images/regions-tool.png) vagy az "M" lehetőséget. Az alakzat módosításához kattintson a szegélyek vagy a sarkok elemre. Ha a belsejére kattint, a teljes határolókeret húzása is megadható. Ha nem tudja szerkeszteni a régiót, valószínűleg a **zárolási/zárolási régiók** eszközt is kikapcsolta. 

A **sablon alapú Box** Tool ![a Template box eszköz](media/how-to-label-images/template-box-tool.png) vagy a "t" használatával több, azonos méretű határolókeret hozható létre. Ha a képnek nincsenek határoló mezői, és aktiválja a sablon alapú mezőket, az eszköz 50x50 képpont-mezőket fog létrehozni. Ha létrehozta a határolókeret mezőt, és aktiválja a sablon alapú mezőket, az új határoló mezők az utolsóként megadott méretet fogják megtenni. A sablon alapú mezők az elhelyezés után átméretezhetők. A sablon alapú mezők átméretezése csak az adott mezőt méretezi át. 

Ha törölni szeretné az aktuális rendszerkép _összes_ határoló mezőjét, kiválaszthatja az **összes régió törlése** eszközt ![a régiók törlése eszközt](media/how-to-label-images/delete-regions-tool.png). 

Miután létrehozta a képhez tartozó határoló mezőket, nyomja meg a **Submit (Küldés** ) gombot a munka mentéséhez. A folyamatban lévő munka nem lesz mentve, kivéve, ha megnyomja a **Küldés** gombot. 

## <a name="finishing-up"></a>Befejezés 

Ha címkézett adatok oldalát küldi el, az Azure új címkézetlen adatmennyiséget rendel hozzá a munkavárólistákból. Ha nincs több címkézetlen információ, megjelenik egy üzenet, amely a portál kezdőlapján található hivatkozással jelenik meg. 

Ha tudja, hogy nem fog több címkézést végezni, válassza ki a nevét a címkézési portál jobb felső sarkában, és válassza a **kijelentkezés**lehetőséget. Ha nem jelentkezik ki, az Azure-ban az idő kimarad, és az adatait egy másik Labeler rendeli hozzá. 

## <a name="next-steps"></a>További lépések

* Ismerje meg a [képbesorolási modellek betanítását az Azure-ban](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* [Az objektumok észlelésének áttekintése az Azure-ban és a gyorsabb R-CNN-módszer használatával](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)