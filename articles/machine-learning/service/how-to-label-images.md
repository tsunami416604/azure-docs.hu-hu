---
title: Képek címkézése egy címkézési projektben
title.suffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adatcímkéző eszközöket egy Azure Machine Learning címkézési projektben.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b36eed1918be2c769bcf74c0d8dee09a5bd62756
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978134"
---
# <a name="tag-images-in-a-labeling-project"></a>Képek címkézése egy címkézési projektben

Miután a projekt rendszergazdája létrehoz egy címkézési projektet a Azure Machine Learningban, a címkézés eszközzel gyorsan elő lehet készíteni egy Machine Learning projekt adatait. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * A címkézési projektek elérése
> * A címkézési eszközök
> * Az eszközök használata adott címkézési feladatokhoz

## <a name="prerequisites"></a>Előfeltételek

* A futó adatcímkéző projekt címkézési portáljának URL-címe
* A szervezet és a projekt [Microsoft-fiók](https://account.microsoft.com/account) vagy Azure Active Directory fiókja

> [!NOTE]
> A projekt rendszergazdája a **projekt részletei** lap **részletek** lapján találja a címkézési portál URL-címét.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Bejelentkezés a projekt címkézési portálján

Lépjen a projekt rendszergazdája által biztosított feliratú portál URL-címére. Jelentkezzen be az e-mail-fiókkal, amelyet a rendszergazda felhasznált a csapatba való felvételhez. A legtöbb felhasználó számára a Microsoft-fiók lesz. Ha a címkézési projekt Azure Active Directory használ, a bejelentkezés módja.

## <a name="understand-the-labeling-task"></a>A címkézési feladat ismertetése

A bejelentkezést követően megjelenik a projekt Áttekintés lapja.

A **részletes utasítások megtekintéséhez**nyissa meg a következőt:. Ezek az utasítások a projektre jellemzőek. Leírják, hogy milyen típusú adatokra van szüksége, hogyan teheti meg a döntéseket és egyéb releváns információkat. Miután elolvasta ezeket az információkat, térjen vissza a projekt lapra, és válassza a **címkézés indítása**lehetőséget.

## <a name="common-features-of-the-labeling-task"></a>A címkézési feladat gyakori funkciói

Az összes képcímkézési feladatban válasszon ki egy megfelelő címkét vagy címkéket a projekt rendszergazdája által megadott készletből. Az első kilenc címkét a billentyűzetén található számgombok használatával választhatja ki.  

A rendszerkép-besorolási feladatokban egyszerre több képet is megtekintheti. Az elrendezés kiválasztásához használja a képterületen megjelenő ikonokat. Az összes megjelenített kép egyidejű kiválasztásához használja az **összes kijelölése lehetőséget**. Az egyes képek kiválasztásához használja a kép jobb felső sarkában található körkörös kijelölés gombot. A címke alkalmazásához ki kell választania legalább egy képet. Ha több lemezképet jelöl ki, a kiválasztott címkék minden kijelölt képre lesznek alkalmazva.

Itt két-két elrendezést választottunk ki, és arra készül, hogy az "emlős" címkét a medve és az Orca képeire alkalmazzuk. A cápa képe már "porcos hal" néven van megjelölve, és az Iguana még nincs megjelölve.

![Több rendszerkép-elrendezés és-kijelölés](media/how-to-label-images/layouts.png)

> [!Important] 
> Csak akkor váltson át elrendezést, ha a címkézetlen adatlapok friss lapja van. Az elrendezések váltás törli az oldal folyamatban lévő címkézési feltételeit.

Az Azure lehetővé teszi a **Küldés** gombot, amikor címkézte az összes képet az oldalon. A munka mentéséhez válassza a **Submit (Küldés** ) lehetőséget.

Miután elküldte a címkéket az adatokhoz, az Azure frissíti a lapot egy új rendszerképekkel a munkahelyi sorból.

## <a name="tag-images-for-multi-class-classification"></a>Képek címkézése többosztályos besoroláshoz

Ha a projekt "képbesorolás többosztályos" típusú, akkor egyetlen címkét fog rendelni a teljes képhez. Ha bármikor át szeretné tekinteni az utasításokat, lépjen az **utasítások** lapra, és válassza a **részletes utasítások megtekintése**lehetőséget.

Ha úgy látja, hogy hibát vétett, miután hozzárendelt egy címkét egy képhez, azt megteheti. A címke törléséhez válassza ki a képen látható "**X**" címkét. Vagy válassza ki a képet, és válasszon egy másik osztályt. Az újonnan kiválasztott érték a korábban alkalmazott címkét váltja fel.

## <a name="tag-images-for-multi-label-classification"></a>Képek címkézése a többcímkés besoroláshoz

Ha "rendszerkép besorolása többcímkés" típusú projekttel dolgozik, egy *vagy több* címkét fog alkalmazni egy képre. A Project-specifikus irányok megtekintéséhez válassza az **utasítások** lehetőséget, és **tekintse meg a részletes utasításokat**.

Válassza ki a címkét, majd válassza ki a címkét. A címke az összes kijelölt képre vonatkozik, majd a képek ki lesznek választva. További címkék alkalmazásához újra ki kell választania a lemezképeket. A következő animáció a többcímkés címkézést mutatja be:

1. Az **összes kijelölése** az "Ocean" címke alkalmazására szolgál.
1. Egyetlen rendszerkép van kiválasztva és címkézett "Vértes".
1. Három kép van kiválasztva és címkézett "Wide Angle".

![Az animáció többcímkés folyamatot mutat be](media/how-to-label-images/multilabel.gif)

A hiba elhárításához kattintson az "**X**" elemre egy adott címke törléséhez, vagy válassza ki a képeket, majd válassza ki a címkét, amely törli a címkét az összes kijelölt rendszerképből. Ez a forgatókönyv itt látható. A "Land" elemre kattintva a rendszer törli a címkét a két kiválasztott rendszerképből.

![A képernyőképen több kijelölés látható](media/how-to-label-images/multiple-deselection.png)

Az Azure csak akkor engedélyezi a **Küldés** gombot, ha legalább egy címkét alkalmaz az egyes képekre. A munka mentéséhez válassza a **Submit (Küldés** ) lehetőséget.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Képek címkézése és az objektumok észleléséhez tartozó határoló mezők megadása

Ha a projekt típusa "Object Identification (határolókeret)", akkor meg kell adnia egy vagy több határolókerett a képen, és minden egyes dobozra alkalmaznia kell egy címkét. A képekhez több határolókeret is tartozhat, amelyek mindegyike egyetlen címkével rendelkezik. A **részletes utasítások megtekintésével** megállapíthatja, hogy a projektben több határolókeret van-e használatban.

1. Jelöljön ki egy címkét a létrehozni kívánt határoló mezőhöz.
1. Válassza a **téglalap alakú Box** eszközt ![téglalap alakú box eszköz](media/how-to-label-images/rectangular-box-tool.png) vagy válassza az "R" lehetőséget.
3. Kattintson és húzza átlósan a célhelyen egy durva határolókeret létrehozásához. A határolókeret módosításához húzza az éleket vagy a sarkokat.

![A képernyőfelvétel alapszintű határolókeret létrehozását mutatja be.](media/how-to-label-images/bounding-box-sequence.png)

A határolókeret törléséhez kattintson a létrehozás után a határolókeret mellett megjelenő X alakú célra.

Meglévő határolókeret címkéje nem módosítható. Ha címke-hozzárendelési hibát hajt végre, törölnie kell a határoló mezőt, és létre kell hoznia egy újat a megfelelő címkével.

Alapértelmezés szerint szerkesztheti a meglévő határoló mezőket. A **régiók zárolása/feloldása** eszköz ![zárolás/zárolás feloldása eszköz](media/how-to-label-images/lock-bounding-boxes-tool.png) vagy az "L" a viselkedést váltja ki. Ha a régiók zárolva vannak, akkor csak egy új határolókeret alakzatát vagy helyét módosíthatja.

A meglévő határolókeret módosításához használja a **régiók manipulációs** eszköz ![Regions manipulációs eszköz](media/how-to-label-images/regions-tool.png) vagy az "M" lehetőséget. Húzza az éleket vagy a sarkokat az alakzat módosításához. A teljes határolókeret húzásához kattintson a belsejére. Ha nem tudja szerkeszteni a régiót, valószínűleg a **zárolási/zárolási régiók** eszközt is kikapcsolta.

A **sablon alapú Box** Tool ![a Template box eszköz](media/how-to-label-images/template-box-tool.png) vagy a "t" használatával több, azonos méretű határolókeret hozható létre. Ha a rendszerkép nem rendelkezik határoló mezőkkel, és aktiválja a sablon alapú mezőket, az eszköz 50-by-50-pixeles mezőket hoz létre. Ha létrehoz egy határoló mezőt, és aktiválja a sablon alapú mezőket, minden új határoló mező a létrehozott utolsó mező mérete lesz. A sablon alapú mezők az elhelyezés után átméretezhetők. A sablon alapú mezők átméretezése csak az adott mezőt méretezi át.

Ha az aktuális képen az *összes* határolókeret törlését szeretné törölni, válassza a **minden régió törlése** eszközt ![a régiók törlése eszközt](media/how-to-label-images/delete-regions-tool.png).

Miután létrehozta a képekhez tartozó határoló mezőket, válassza a **Submit (Küldés** ) lehetőséget a munka mentéséhez, vagy a folyamatban lévő munka nem lesz mentve.

## <a name="finish-up"></a>Befejezés

Ha címkézett adatok oldalát küldi el, az Azure új címkézetlen adatok hozzárendelését teszi elérhetővé egy munkahelyi várólistából. Ha nem áll rendelkezésre több címkézetlen információ, egy üzenet jelenik meg, amely a portál kezdőlapjára mutató hivatkozással együtt.

Ha végzett a címkézéssel, válassza ki a nevét a címkézési portál jobb felső sarkában, majd válassza a **kijelentkezés lehetőséget.** Ha nem jelentkezik ki, az Azure-ban az idő kimarad, és az adatait egy másik Labeler rendeli hozzá.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg a [képbesorolási modellek betanítását az Azure-ban](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* További információ [az objektumok észleléséről az Azure-ban és a gyorsabb R-CNN-módszer használatával](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
