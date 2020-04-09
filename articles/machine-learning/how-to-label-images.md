---
title: Képek címkézése feliratozási projektben
title.suffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adatok címkézési eszközeit egy Azure Machine Learning címkézési projektben.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 371b99c794feb4a64eb3e9af389020e25d14eedb
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879428"
---
# <a name="tag-images-in-a-labeling-project"></a>Képek címkézése feliratozási projektben

Miután a projekt rendszergazdája [létrehozott egy címkézési projektet](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) az Azure Machine Learningben, a címkézési eszköz (nyilvános előzetes verzió) segítségével gyorsan előkészítheti az adatokat egy Machine Learning-projekthez. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * A címkézési projektek elérése
> * A címkéző eszközök
> * Az eszközök használata adott címkézési feladatokhoz

## <a name="prerequisites"></a>Előfeltételek

* A futó adatcímkézési projekt címkézési portálURL-címe
* [Microsoft-fiók](https://account.microsoft.com/account) vagy Azure Active Directory-fiók a szervezethez és a projekthez

> [!NOTE]
> A projektadminisztrátor a címkeportál URL-címét a **Projekt részletei** lap **Részletek** lapján találja.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Bejelentkezés a projekt címkézési portáljára

Nyissa meg a címkeportál által biztosított URL-címet. Jelentkezzen be azzal az e-mail fiókkal, amelyet a rendszergazda használt, hogy felvegye Önt a csapatba. A legtöbb felhasználó számára ez lesz a Microsoft-fiókja. Ha a címkézési projekt az Azure Active Directoryt használja, így fog bejelentkezni.

## <a name="understand-the-labeling-task"></a>A címkézési feladat megismerése

A bejelentkezés után megjelenik a projekt áttekintő lapja.

Nyissa meg a Részletes utasítások megtekintése című **nézetet.** Ezek az utasítások a projektre vonatkoznak. Ismertetik az ön előtt álló adatok típusát, a döntések meghozatalának módját és egyéb releváns információkat. Az információ elolvasása után térjen vissza a projektlapra, és válassza **a Címkézés indítása**lehetőséget.

## <a name="common-features-of-the-labeling-task"></a>A címkézési feladat közös jellemzői

Minden képcímkézési tevékenységben a projekt rendszergazdája által megadott készletből választhat megfelelő címkét vagy címkéket. Az első kilenc címkét a billentyűzet számgombjai segítségével választhatja ki.  

A képbesorolási feladatokban egyszerre több képet is megtekinthet. Az elrendezés kiválasztásához használja a képterület feletti ikonokat. 

Az összes megjelenített kép egyidejű kijelöléséhez használja az **Összes kijelölése**lehetőséget. Az egyes képek kijelöléséhez használja a kép jobb felső sarkában található kör alakú kijelölés gombot. A címke alkalmazásához legalább egy képet ki kell jelölnie. Ha több képet jelöl ki, a kiválasztott címke az összes kijelölt képre vonatkozik.

Itt már kiválasztott egy 2-by-2 elrendezés, és hamarosan alkalmazni a tag "Emlős" a képeket a medve és orka. A cápa képét már "Porchal" címkével látták el, és az iguánát még nem jelölték meg.

![Több képelrendezés és kijelölés](./media/how-to-label-images/layouts.png)

> [!Important] 
> Csak akkor váltson elrendezéseket, ha friss, címkézetlen adatokat tartalmazó oldallal rendelkezik. Az elrendezésváltás törli az oldal folyamatban lévő címkézési munkáját.

Az Azure engedélyezi a **Küldés** gombot, ha az oldalon lévő összes képet címkézte. A munka mentéséhez válassza a **Küldés** lehetőséget.

Miután elküldte az aktuális adatok címkéit, az Azure frissíti az oldalt egy új képkészlettel a munkavárólistából.

### <a name="assisted-machine-learning"></a>Támogatott gépi tanulás 

A gépi tanulási algoritmusok többosztályos vagy többelrendezésű besorolási feladat során aktiválhatók. Ha ezek az algoritmusok engedélyezve vannak a projektben, a következők jelenhetnek meg:

* Miután bizonyos mennyiségű kép meg lett címkézve, a projekt neve mellett a képernyő tetején **csoportosított feladatok** jelenhetnek meg.  Ez azt jelenti, hogy a képek csoportosítva jelennek meg hasonló képeket ugyanazon az oldalon.  Ha igen, váltson a több képnézet egyikére a csoportosítás előnyeinek kihasználásához.  

* Egy későbbi időpontban a Projekt neve mellett a **Tevékenységek felirat** látható.  A képek ezután megjelennek egy javasolt címkével, amely egy gépi tanulási besorolási modellből származik. Egyetlen gépi tanulási modell sem rendelkezik 100%-os pontossággal. Bár csak olyan képeket használunk, amelyekben a modell magabiztos, ezek a képek továbbra is helytelenül lehetnek előre címkézve.  Amikor ezeket a címkéket látja, javítsa ki a hibás címkéket az oldal elküldése előtt.  

Különösen a címkézési projekt korai szakaszában a gépi tanulási modell csak akkor lehet elég pontos a képek egy kis részhalmazának előzetes címkézéséhez. Miután ezek a képek címkével vannak ellátva, a címkézési projekt visszatér a manuális címkézéshez, hogy több adatot gyűjtsön a modellbetanítás következő köréhez. Idővel a modell magabiztosabbá válik a képek nagyobb arányával kapcsolatban, ami a projekt későbbi részében több előcímkézett tevékenységet eredményez.

## <a name="tag-images-for-multi-class-classification"></a>Képek címkézése többosztályos besoroláshoz

Ha a projekt "Image Classification Multi-Class" típusú, akkor egyetlen címkét rendel a teljes képhez. Az utasítások bármikori áttekintéséhez lépjen az **Utasítások** lapra, és válassza **a Részletes utasítások megtekintése**lehetőséget.

Ha rájössz, hogy hibát követtél el, miután címkét rendeltél egy képhez, kijavíthatja azt. A címke törléséhez válassza az **"X**" lehetőséget a kép alatt megjelenő címkén. Vagy jelölje ki a képet, és válasszon másik osztályt. Az újonnan kiválasztott érték felülírja a korábban alkalmazott címkét.

## <a name="tag-images-for-multi-label-classification"></a>Képek címkézése többcímkés besoroláshoz

Ha "Image Classification Multi-Label" típusú projekten dolgozik, egy *vagy több* címkét kell alkalmazni a képre. A projektspecifikus útvonaltervek megtekintéséhez válassza az **Utasítások** lehetőséget, és válassza a Részletes utasítások megtekintése című **témakört.**

Jelölje ki a címkét címkézni kívánt képet, majd jelölje ki a címkét. A címke az összes kijelölt képre vonatkozik, majd a program kijelöli a képeket. Ha további címkéket szeretne alkalmazni, újra ki kell jelölnie a képeket. A következő animáció többcímkés címkézést mutat:

1. **Válassza ki** az összes et az "Ocean" címke alkalmazásához.
1. Egyetlen kép van kijelölve, és "Closeup" címkével van ellátva.
1. Három kép van kiválasztva, és címkézett "Széles látószögű."

![Az animáció többcímkés folyamatot mutat](./media/how-to-label-images/multilabel.gif)

Hiba kijavításához kattintson az **"X"** gombra az egyes címke törléséhez vagy a képek kijelöléséhez, majd jelölje ki a címkét, amely törli a címkét az összes kiválasztott képről. Ez a forgatókönyv itt látható. A "Land" gombra kattintva a címke törlődik a két kiválasztott képből.

![A képernyőkép több kiválasztásokat mutat](./media/how-to-label-images/multiple-deselection.png)

Az Azure csak akkor engedélyezi a **Küldés** gombot, ha minden egyes lemezképre legalább egy címkét alkalmazott. A munka mentéséhez válassza a **Küldés** lehetőséget.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Képek címkézése és határolókeret megadása az objektumészleléshez

Ha a projekt "Objektumazonosítás (határolókeret)" típusú, akkor a képen egy vagy több határolókeretet kell megadnia, és minden egyes mezőre egy-egy címkét kell alkalmaznia. A képektöbb határolókerettel is rendelkezhetnek, amelyek mindegyike egyetlen címkével rendelkezik. A **Részletes nézet utasításokkal** megállapíthatja, hogy több határolókeretet használ-e a projektben.

1. Jelöljön ki egy címkét a létrehozni kívánt határolókerethez.
1. Jelölje ki a **Téglalap alakú doboz** eszköz ![](./media/how-to-label-images/rectangular-box-tool.png) Téglalap alakú doboz eszközt, vagy válassza az "R" lehetőséget.
3. Kattintson és húzza átlósan a cél, hogy hozzon létre egy durva határolókeret. A határolókeret módosításához húzza a szegélyeket vagy sarkokat.

![A képernyőkép az alapvető határolókeret-létrehozást mutatja.](./media/how-to-label-images/bounding-box-sequence.png)

Ha határolókeretet szeretne törölni, kattintson a létrehozás után a határolókeret mellett megjelenő X-alakú célra.

Meglévő határolókeret címkéje nem módosítható. Ha címkehozzárendelési hibát követ el, törölnie kell a határolókeretet, és létre kell hoznia egy újat a megfelelő címkével.

Alapértelmezés szerint szerkesztheti a meglévő határolókereteket. A **Régiók zárolása/feloldása** eszköz ![A régiók zárolása/feloldása eszköz](./media/how-to-label-images/lock-bounding-boxes-tool.png) vagy az "L" kapcsolók között van ez a viselkedés. Ha a területek zárolva vannak, csak egy új határolókeret alakját vagy helyét módosíthatja.

A **Régiók** manipuláció ![eszköz](./media/how-to-label-images/regions-tool.png) Régiók manipuláció eszközzel vagy az "M" eszközzel módosíthatja a meglévő határolókeretet. Az alakzat módosításához húzza a széleket vagy a sarkokat. Kattintson a belső, hogy képes legyen húzni a teljes határolókeret. Ha nem tud szerkesztésre egy régiót, valószínűleg bevan kapcsolva a **Régiók zárolása/feloldása** eszköz.

A **Sablon alapú** dobozsablon-doboz ![](./media/how-to-label-images/template-box-tool.png) eszközzel vagy a "T" eszközzel több azonos méretű határolókeretet hozhat létre. Ha a képnem rendelkezik határolókerettel, és aktiválja a sablonalapú mezőket, az eszköz 50 x 50 képpontos mezőket fog létrehozni. Ha létrehoz egy határolókeretet, majd aktiválja a sablonalapú mezőket, minden új határolókeret akkora lesz, mint az utoljára létrehozott mező. A sablonalapú mezők elhelyezés után átméretezhetők. A sablonalapú doboz átméretezése csak az adott mezőt méretezi át.

Az aktuális kép *összes* határolódobozának törléséhez jelölje ![be az](./media/how-to-label-images/delete-regions-tool.png)Összes **terület törlése** eszköz Régiók törlése eszközt .

Miután létrehozta egy kép határolókereteit, válassza a **Küldés** lehetőséget a munka mentéséhez, különben a folyamatban lévő munka nem lesz mentve.

## <a name="finish-up"></a>Befejezés

A címkézett adatok at tartalmazó lap beküldésekor az Azure új, címkézetlen adatokat rendel önhöz egy munkavárólistából. Ha nincs több címkézetlen adat, akkor egy üzenetet kap, amely ezt a portál kezdőlapjára mutató hivatkozással együtt kapja meg.

Ha végzett a címkézéssel, jelölje ki a nevét a címkeportál jobb felső sarkában, majd válassza a **kijelentkezés**lehetőséget. Ha nem jelentkezik ki, végül az Azure "időkitérést" kap, és hozzárendeli az adatokat egy másik címkézőhöz.

## <a name="next-steps"></a>További lépések

* Ismerje meg a [lemezképbesorolási modellek betanítását az Azure-ban](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

