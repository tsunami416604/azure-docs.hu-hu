---
title: Képek címkézése egy címkézési projektben
title.suffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adatcímkéző eszközöket egy Azure Machine Learning címkézési projektben.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/09/2020
ms.openlocfilehash: 2c21a8770209871be4d871a08e6355e4ca7ed169
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204321"
---
# <a name="tag-images-in-a-labeling-project"></a>Képek címkézése egy címkézési projektben

Miután a projekt rendszergazdája [létrehoz egy címkézési projektet](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) a Azure Machine Learningban, a címkézés eszközzel (nyilvános előzetes verzió) gyorsan elő tudja készíteni a Machine learning projekt adatait. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * A címkézési projektek elérése
> * A címkézési eszközök
> * Az eszközök használata adott címkézési feladatokhoz

## <a name="prerequisites"></a>Előfeltételek

* A szervezet és a projekt [Microsoft-fiók](https://account.microsoft.com/account) vagy Azure Active Directory fiókja
* Közreműködői szintű hozzáférés a címkézési projektet tartalmazó munkaterülethez.

## <a name="sign-in-to-the-workspace"></a>Bejelentkezés a munkaterületre

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki az előfizetést és a munkaterületet, amely tartalmazza a címkézési projektet.  Ezt az információt a projekt rendszergazdájától szerezheti be.

1. A projekt megkereséséhez kattintson a bal oldalon található **adatfeliratok** elemre.  

1. Válassza ki a projekt nevét a listában.

## <a name="understand-the-labeling-task"></a>A címkézési feladat ismertetése

Miután kiválasztotta a projektet, a lap tetején válassza az **adatfelirat**lehetőséget.

Megtekintheti a projektre vonatkozó utasításokat. Leírják, hogy milyen típusú adatokra van szüksége, hogyan teheti meg a döntéseket és egyéb releváns információkat. Miután elolvasta ezeket az információkat, a lap tetején válassza a **feladatok**lehetőséget.  Vagy az oldal alján kattintson a **címkézés indítása**lehetőségre.

## <a name="common-features-of-the-labeling-task"></a>A címkézési feladat gyakori funkciói

Az összes képcímkézési feladatban válasszon ki egy megfelelő címkét vagy címkéket a projekt rendszergazdája által megadott készletből. Az első kilenc címkét a billentyűzetén található számgombok használatával választhatja ki.  

A rendszerkép-besorolási feladatokban egyszerre több képet is megtekintheti. Az elrendezés kiválasztásához használja a képterületen megjelenő ikonokat. 

Az összes megjelenített kép egyidejű kiválasztásához használja az **összes kijelölése lehetőséget**. Az egyes képek kiválasztásához használja a kép jobb felső sarkában található körkörös kijelölés gombot. A címke alkalmazásához ki kell választania legalább egy képet. Ha több lemezképet jelöl ki, a kiválasztott címkék minden kijelölt képre lesznek alkalmazva.

Itt két-két elrendezést választottunk ki, és arra készül, hogy az "emlős" címkét a medve és az Orca képeire alkalmazzuk. A cápa képe már "porcos hal" néven van megjelölve, és az Iguana még nincs megjelölve.

![Több rendszerkép-elrendezés és-kijelölés](./media/how-to-label-images/layouts.png)

> [!Important] 
> Csak akkor váltson át elrendezést, ha a címkézetlen adatlapok friss lapja van. Az elrendezések váltás törli az oldal folyamatban lévő címkézési feltételeit.

Az Azure lehetővé teszi a **Küldés** gombot, amikor címkézte az összes képet az oldalon. A munka mentéséhez válassza a **Submit (Küldés** ) lehetőséget.

Miután elküldte a címkéket az adatokhoz, az Azure frissíti a lapot egy új rendszerképekkel a munkahelyi sorból.

### <a name="assisted-machine-learning"></a>Támogatott gépi tanulás 

A gépi tanulási algoritmusok egy többosztályos vagy több címkés besorolási feladat során indíthatók. Ha ezek az algoritmusok engedélyezve vannak a projektben, a következők jelenhetnek meg:

* Néhány kép megjelölése után előfordulhat, hogy a képernyő tetején a projekt neve mellett **fürtözött feladatok** láthatók.  Ez azt jelenti, hogy a képek csoportosítva vannak, hogy hasonló képeket nyújtsanak ugyanazon a lapon.  Ha igen, váltson át a több Képnézet egyikére, hogy kihasználhassa a csoportosítást.  

* Egy későbbi időpontban a projekt neve mellett előre **címkézett feladatokat** láthat.  A képek ezután egy gépi tanulási besorolási modellből származó javasolt címkével jelennek meg. A Machine learning modellnek nincs 100%-os pontossága. Habár csak olyan rendszerképeket használunk, amelyek esetében a modell magabiztos, előfordulhat, hogy ezek a képek még mindig helytelenül vannak megjelölve.  Ha ezeket a címkéket látja, javítsa ki a helytelen címkéket az oldal elküldése előtt.  

Különösen a címkézési projekt korai szakaszában a gépi tanulási modell csak akkor lehet elég pontos, ha a képek kis részhalmazát szeretné előre megjelölni. Miután megcímkézte a lemezképeket, a címkézési projekt visszatér a manuális címkézéshez, hogy további adatokat gyűjtsön a következő kör betanításához. Az idő múlásával a modell nagyobb biztonságban lesz a képek nagyobb hányadával kapcsolatban, így a projekt későbbi feladataihoz is hozzájárul.

## <a name="tag-images-for-multi-class-classification"></a>Képek címkézése többosztályos besoroláshoz

Ha a projekt "képbesorolás többosztályos" típusú, akkor egyetlen címkét fog rendelni a teljes képhez. Ha bármikor át szeretné tekinteni az utasításokat, lépjen az **utasítások** lapra, és válassza a **részletes utasítások megtekintése**lehetőséget.

Ha úgy látja, hogy hibát vétett, miután hozzárendelt egy címkét egy képhez, azt megteheti. A címke törléséhez válassza ki a képen látható "**X**" címkét. Vagy válassza ki a képet, és válasszon egy másik osztályt. Az újonnan kiválasztott érték a korábban alkalmazott címkét váltja fel.

## <a name="tag-images-for-multi-label-classification"></a>Képek címkézése a többcímkés besoroláshoz

Ha "rendszerkép besorolása többcímkés" típusú projekttel dolgozik, egy *vagy több* címkét fog alkalmazni egy képre. A Project-specifikus irányok megtekintéséhez válassza az **utasítások** lehetőséget, és **tekintse meg a részletes utasításokat**.

Válassza ki a címkét, majd válassza ki a címkét. A címke az összes kijelölt képre vonatkozik, majd a képek ki lesznek választva. További címkék alkalmazásához újra ki kell választania a lemezképeket. A következő animáció a többcímkés címkézést mutatja be:

1. Az **összes kijelölése** az "Ocean" címke alkalmazására szolgál.
1. Egyetlen rendszerkép van kiválasztva és címkézett "Vértes".
1. Három kép van kiválasztva és címkézett "Wide Angle".

![Az animáció többcímkés folyamatot mutat be](./media/how-to-label-images/multilabel.gif)

A hiba elhárításához kattintson az "**X**" elemre egy adott címke törléséhez, vagy válassza ki a képeket, majd válassza ki a címkét, amely törli a címkét az összes kijelölt rendszerképből. Ez a forgatókönyv itt látható. A "Land" elemre kattintva a rendszer törli a címkét a két kiválasztott rendszerképből.

![A képernyőképen több kijelölés látható](./media/how-to-label-images/multiple-deselection.png)

Az Azure csak akkor engedélyezi a **Küldés** gombot, ha legalább egy címkét alkalmaz az egyes képekre. A munka mentéséhez válassza a **Submit (Küldés** ) lehetőséget.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Képek címkézése és az objektumok észleléséhez tartozó határoló mezők megadása

Ha a projekt típusa "Object Identification (határolókeret)", akkor meg kell adnia egy vagy több határolókerett a képen, és minden egyes dobozra alkalmaznia kell egy címkét. A képekhez több határolókeret is tartozhat, amelyek mindegyike egyetlen címkével rendelkezik. A **részletes utasítások megtekintésével** megállapíthatja, hogy a projektben több határolókeret van-e használatban.

1. Jelöljön ki egy címkét a létrehozni kívánt határoló mezőhöz.
1. Jelölje be **a négyzet alakú téglalap** ![alakú eszközt](./media/how-to-label-images/rectangular-box-tool.png) , vagy válassza az "R" lehetőséget.
3. Kattintson és húzza átlósan a célhelyen egy durva határolókeret létrehozásához. A határolókeret módosításához húzza az éleket vagy a sarkokat.

![A képernyőfelvétel alapszintű határolókeret létrehozását mutatja be.](./media/how-to-label-images/bounding-box-sequence.png)

A határolókeret törléséhez kattintson a létrehozás után a határolókeret mellett megjelenő X alakú célra.

Meglévő határolókeret címkéje nem módosítható. Ha címke-hozzárendelési hibát hajt végre, törölnie kell a határoló mezőt, és létre kell hoznia egy újat a megfelelő címkével.

Alapértelmezés szerint szerkesztheti a meglévő határoló mezőket. A **zárolás/zárolás feloldása** eszköz ![zárolási/feloldási régiói eszköz](./media/how-to-label-images/lock-bounding-boxes-tool.png) vagy az "L" a viselkedést is bekapcsolja. Ha a régiók zárolva vannak, akkor csak egy új határolókeret alakzatát vagy helyét módosíthatja.

A meglévő határolókeret módosításához használja a **régiók manipulációs** eszköz ![régiók manipuláció eszközét](./media/how-to-label-images/regions-tool.png) vagy az "M" lehetőséget. Húzza az éleket vagy a sarkokat az alakzat módosításához. A teljes határolókeret húzásához kattintson a belsejére. Ha nem tudja szerkeszteni a régiót, valószínűleg a **zárolási/zárolási régiók** eszközt is kikapcsolta.

A **sablon alapú Box** Tool ![template Box eszköz](./media/how-to-label-images/template-box-tool.png) vagy a "T" használatával több, azonos méretű határolókeret hozható létre. Ha a rendszerkép nem rendelkezik határoló mezőkkel, és aktiválja a sablon alapú mezőket, az eszköz 50-by-50-pixeles mezőket hoz létre. Ha létrehoz egy határoló mezőt, és aktiválja a sablon alapú mezőket, minden új határoló mező a létrehozott utolsó mező mérete lesz. A sablon alapú mezők az elhelyezés után átméretezhetők. A sablon alapú mezők átméretezése csak az adott mezőt méretezi át.

Az aktuális rendszerkép *összes* határoló mezőjének törléséhez válassza a **minden régió törlése** eszköz ![régiók törlése eszközét.](./media/how-to-label-images/delete-regions-tool.png)

Miután létrehozta a képekhez tartozó határoló mezőket, válassza a **Submit (Küldés** ) lehetőséget a munka mentéséhez, vagy a folyamatban lévő munka nem lesz mentve.

## <a name="finish-up"></a>Befejezés

Ha címkézett adatok oldalát küldi el, az Azure új címkézetlen adatok hozzárendelését teszi elérhetővé egy munkahelyi várólistából. Ha nem áll rendelkezésre több címkézetlen információ, egy üzenet jelenik meg, amely a portál kezdőlapjára mutató hivatkozással együtt.

Ha végzett a címkézéssel, válassza ki a nevét a címkézési portál jobb felső sarkában, majd válassza a **kijelentkezés lehetőséget.** Ha nem jelentkezik ki, az Azure-ban az idő kimarad, és az adatait egy másik Labeler rendeli hozzá.

## <a name="next-steps"></a>További lépések

* Ismerje meg a [képbesorolási modellek betanítását az Azure-ban](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

