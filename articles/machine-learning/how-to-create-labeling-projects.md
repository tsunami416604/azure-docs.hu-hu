---
title: Adatcímkéző projekt létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és futtathat címkéző projekteket a gépi tanulásra vonatkozó adatcímkézéshez.  Az eszközök több mint ml, a feladattal való segítségnyújtásra szolgáló feliratozást, vagy az emberi műveletet tartalmazzák a hurok címkéjén.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: 9974b42f582a3b5f26df0b6e77b42a03f23c47dd
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898708"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Adatcímkéző projekt létrehozása és címkék exportálása 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A gépi tanulási projektek terjedelmes adatcímkézése gyakran fejfájás. A számítógép-látási összetevővel rendelkező projektek, például a képosztályozás vagy az objektumok észlelése általában több ezer rendszerkép használatát igénylik.
 
[Azure Machine learning](https://ml.azure.com/) központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. Felhasználhatja az adatok, címkék és csoporttagok koordinálását a címkézési feladatok hatékony kezelésére. Machine Learning támogatja a képbesorolást, a többcímkés vagy a többosztályos és az objektum-azonosítást a kötött mezőkkel.

Machine Learning nyomon követi a folyamat előrehaladását, és fenntartja a hiányos címkéző feladatok várólistáját. A címkéző nincs szükségük Azure-fiókra a részvételhez. Miután a hitelesítésük megtörtént a Microsoft-fiók vagy [Azure Active Directoryával](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), a lehető legtöbb címkézést teheti lehetővé.

Elindíthatja és leállíthatja a projektet, címkéző és csapatokat adhat hozzá és távolíthat el, valamint figyelheti a címkézési folyamatot. A címkézett adatokat kókusz formátumban vagy Azure Machine Learning adatkészletként is exportálhatja.

> [!Important]
> Jelenleg csak a képbesorolás és az objektum-azonosító címkézési projektek támogatottak. Emellett az adatlemezképeknek elérhetőnek kell lenniük egy Azure Blob-adattárban. (Ha nem rendelkezik meglévő adattárral, képeket tölthet fel a projekt létrehozása során.) 

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Projekt létrehozása
> * A projekt adatának és szerkezetének meghatározása
> * A projektben dolgozó csapatok és személyek kezelése
> * A projekt futtatása és figyelése
> * Címkék exportálása


## <a name="prerequisites"></a>Előfeltételek

* A címkével ellátni kívánt, helyi vagy Azure Blob Storage-beli adatfájlok.
* Az alkalmazni kívánt címkék halmaza.
* A címkézésre vonatkozó utasítások.
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLFree) a virtuális gép létrehozásának megkezdése előtt.
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

A címkézési projektek Azure Machine Learningból vannak felügyelve. A projektek és személyek kezelésére a **címkézési projektek** lapot használhatja. Egy projekthez hozzá van rendelve egy vagy több csapat, és egy csapathoz egy vagy több személy van rendelve.

Ha az adatai már az Azure Blob Storage-ban vannak, akkor a címkézési projekt létrehozása előtt elérhetővé kell tenni az adattárként. Részletekért lásd: adattárolók [létrehozása és regisztrálása](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores).

Projekt létrehozásához válassza a **projekt hozzáadása**lehetőséget. Adjon megfelelő nevet a projektnek, és válassza ki a **feladattípus címkézése**elemet.

![Projekt-létrehozási varázsló címkézése](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Válassza a **Képbesorolás többosztályos** a projektekhez lehetőséget, ha csak *egyetlen osztályt* szeretne alkalmazni osztályokból egy képből.
* Válassza a **Képbesorolás több címkét** a projektekhez lehetőséget, ha egy *vagy több* címkét szeretne alkalmazni az osztályok egy adott képére. Előfordulhat például, hogy egy kutya fényképét a *kutya* és a *nappali*is címkézi.
* Válassza az **objektum azonosítása (határolókeret)** lehetőséget a projektek esetében, ha egy adott képen egy osztályt és egy határoló mezőt szeretne hozzárendelni.

Ha készen áll a folytatásra, válassza a **tovább** lehetőséget.

## <a name="specify-the-data-to-label"></a>A címkéhez használandó adatértékek meghatározása

Ha már létrehozott egy adatkészletet, amely az adatokat tartalmazza, válassza ki azt a **meglévő adatkészlet kiválasztása** legördülő listából. Vagy válassza az **adatkészlet létrehozása** meglévő Azure-adattár használatára vagy a helyi fájlok feltöltéséhez lehetőséget.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Adatkészlet létrehozása Azure-adattárból

Sok esetben elég a helyi fájlok feltöltése. A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) azonban gyorsabb és hatékonyabb megoldást kínál nagy mennyiségű adatok átvitelére. A fájlok áthelyezésének alapértelmezett módja a Storage Explorer.

Adatkészlet létrehozása az Azure Blob Storage-ban már tárolt adatokból:

1. Válassza az **adatkészlet létrehozása** > **az adattárból**lehetőséget.
1. Rendeljen egy **nevet** az adatkészlethez.
1. Válassza a **fájl** lehetőséget **adatkészlet típusaként**.  
1. Válassza ki az adattárt.
1. Ha az adatai a blob Storage-ban található almappában találhatók, válassza a **Tallózás** lehetőséget az elérési út kiválasztásához.
    * Fűzze hozzá a "/* *" utótagot az elérési úthoz, hogy az tartalmazza a kijelölt útvonal almappáiban található összes fájlt.
    * A "* */* . *" hozzáfűzésével adja meg az aktuális tárolóban és annak almappáiban lévő összes adatforrást.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy **létrehozhatja** az adatkészletet.

> [!NOTE]
> A rendszer betölti a kiválasztott adatait a projektbe.  A projekt létrehozása után a projektben nem jelennek meg több információ az adattárhoz.  

### <a name="create-a-dataset-from-uploaded-data"></a>Adatkészlet létrehozása a feltöltött adatokból

Az adatok közvetlen feltöltéséhez:

1. Válassza az **adatkészlet létrehozása** > **helyi fájlokból**lehetőséget.
1. Rendeljen egy **nevet** az adatkészlethez.
1. Válassza a "fájl" lehetőséget az **adatkészlet típusaként**.
1. Nem *kötelező:* Válassza a **Speciális beállítások** lehetőséget az adattár, a tároló és az adatelérési út testreszabásához.
1. Válassza a **Tallózás** lehetőséget a feltölteni kívánt helyi fájlok kiválasztásához.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy **létrehozhatja** az adatkészletet.

A rendszer feltölti az adatait a Machine Learning munkaterület alapértelmezett blob-tárolójába ("workspaceblobstore").

## <a name="specify-label-classes"></a>Címke osztályok meghatározása

A **címkézési osztályok** lapon adja meg az adatkategorizálni kívánt osztályok készletét. Ezt körültekintően hajtsa végre, mert a címke pontosságát és sebességét az osztályok közül választhatják ki. Például a növények vagy állatok teljes nemzetségének és fajainak helyesírása helyett használjon mezőkód vagy rövidítse a nemet.

Soronként egy címkét adjon meg. Új sor hozzáadásához használja a **+** gombot. Ha több mint 3 vagy 4 címke van, de kevesebb, mint 10, érdemes lehet előtagot ("1:", "2:") használni, hogy a jelölők a számgombok használatával gyorsítsák fel a munkájukat.

## <a name="describe-the-labeling-task"></a>A címkézési feladat leírása

Fontos, hogy egyértelműen ismertesse a címkézési feladatot. A **címkézési utasítások** lapon hozzáadhat egy külső webhelyre mutató hivatkozást az utasítások címkézéséhez, vagy megadhatja az oldalon található Szerkesztés mezőben megjelenő utasításokat is. Tartsa meg az utasítások feladatait és a célközönségnek megfelelőt. Vegye figyelembe a következő kérdéseket:

* Mik a megjelenő címkék, és ezek közül választhatnak? Van egy hivatkozási szöveg, amelyre hivatkozni kell?
* Mi a teendő, ha nem tűnik megfelelő címkének?
* Mi a teendő, ha több címke is megfelelőnek tűnik?
* Milyen megbízhatósági küszöbértéket kell alkalmazni a címkére? Szeretné a "legjobb becslést", ha nem biztos benne?
* Mi a teendő a részben lefedett vagy átfedésben lévő objektumokkal?
* Mi a teendő, ha egy érdekes objektumot levágja a rendszerkép széle?
* Mi a teendő, ha a címkét elküldték, ha úgy vélik, hogy hiba történt?

A határoló mezőkhöz a következő fontos kérdések tartoznak:

* Hogyan van definiálva a feladathoz tartozó határoló mező? Teljes egészében az objektum belsejében kell lennie, vagy a külseje legyen? Ha a lehető legszorosabban kell lenyírni, vagy a kimaradás elfogadható?
* Milyen szintű ellátást és következetességet vár a címkéző a határoló mezők definiálásakor?
* Hogyan lehet címkével ellátni a képen részben látható objektumot? 
* Hogyan címkézheti az objektumot, amelyet részben egy másik objektum is lefedi?

>[!NOTE]
> Ügyeljen arra, hogy a címkéző az 1-9-as számú kulcsok használatával kiválaszthatják az első 9 címkét.

## <a name="use-ml-assisted-labeling"></a>ML-támogatással rendelkező címkézés használata

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

A **ml által támogatott címkézési** oldal lehetővé teszi az automatikus gépi tanulási modellek aktiválását a címkézési feladat felgyorsításához. A címkézési projekt elején a rendszer véletlenszerű sorrendben rendezi a képeket a lehetséges torzulások csökkentése érdekében. A betanított modellben azonban megjelennek az adatkészletben található összes torzítás is. Ha például a lemezképek 80%-a egyetlen osztályból áll, akkor a modell betanításához használt adatmennyiség körülbelül 80%-a az adott osztály lesz. Ez a képzés nem tartalmazza az aktív tanulást.

Ez a funkció képbesoroláshoz (többosztályos vagy többcímkés) feladatokhoz érhető el.  

Válassza a *ml támogatott címkézés engedélyezése* lehetőséget, és adjon meg egy GPU-t a támogatott címkézés engedélyezéséhez, amely két fázisból áll:
* Fürtszolgáltatás
* Előcímkézés

A támogatott címkézés indításához szükséges címkézett képek pontos száma nem rögzített szám.  Ez jelentősen változhat az egyik címkéző projektből a másikra. Egyes projektek esetében időnként előfordulhat, hogy a 300-es lemezképek manuális címkézése után megtekintheti az előcímke vagy a fürt feladatait. A ML-támogatással rendelkező címkézés az *átvitel elsajátítása*nevű technikát használja, amely egy előre betanított modellt használ a betanítási folyamat elindításához. Ha az adatkészlet osztályai hasonlóak az előre betanított modellhez, az előzetes feliratok csak néhány száz manuálisan címkézett kép után érhetők el. Ha az adatkészlet jelentősen eltér a modell előzetes betanításához használt adatoktól, sokkal hosszabb időt vehet igénybe.

Mivel a végső címkék továbbra is a Labeler származó adatokra támaszkodnak, ezt a technológiát néha *emberinek is nevezik a hurok* címkézése során.

### <a name="clustering"></a>Fürtszolgáltatás

Bizonyos számú címke elküldése után a Machine learning-modell elkezdi csoportosítani a hasonló képeket.  Ezeket a hasonló képeket a jelölők ugyanazon a képernyőn jelennek meg a manuális címkézés felgyorsításához. A fürtözés különösen akkor hasznos, ha a Labeler 4, 6 vagy 9 rendszerképből álló rácsot tekint meg. 

Miután a gépi tanulási modellt betanítják a manuálisan címkézett adataira, a modell az utolsó teljesen csatlakoztatott rétegre lesz csonkítva. A címke nélküli képeket a rendszer átadja a csonkolt modellnek egy, a "beágyazás" vagy "featurization" néven ismert folyamaton. Ez beágyazza az egyes képeket egy, a modell rétegében definiált, nagy dimenziós területre. A rendszer a terület legközelebbi szomszédaival rendelkező képeket használja a fürtözési feladatokhoz. 

### <a name="prelabeling"></a>Előcímkézés

További képfeliratok elküldése után a rendszer egy besorolási modellt használ a képcímkék előrejelzéséhez.  A Labeler mostantól olyan lapokat lát, amelyek az egyes képeken már szerepelnek előre jelzett címkéket.  A feladat ezután áttekinti ezeket a címkéket, és kijavította a helytelenül címkézett képeket az oldal elküldése előtt.  

Miután a gépi tanulási modellt betanítják a manuálisan címkézett adatokra, a modell kiértékelése manuálisan címkézett képekből történik, hogy meghatározza a pontosságot a különböző megbízhatósági küszöbértékeken. Ez a kiértékelési folyamat egy olyan megbízhatósági küszöbérték meghatározására szolgál, amely felett a modell elég pontos ahhoz, hogy megjelenjenek az előzetes feliratok. Ezt követően a modell kiértékelése a címke nélküli adatértékekkel történik. Az előcímkézéshez használt, a küszöbértéknél jobban megjelenő képek.

> [!NOTE]
> A ML-ben támogatott címkézés **csak** Enterprise Edition-munkaterületeken érhető el.

## <a name="initialize-the-labeling-project"></a>A címkézési projekt inicializálása

A címkézési projekt inicializálását követően a projekt egyes szempontjai nem változtathatók meg. A feladattípus vagy az adatkészlet nem módosítható. A címkék és a feladat leírásának URL-címe *is* módosítható. A projekt létrehozása előtt gondosan tekintse át a beállításokat. A projekt elküldése után visszaadja az **Adatcímkéző** kezdőlapjának, amely a projekt **inicializálását**fogja megjeleníteni. Ez a lap nem frissül automatikusan. Ezért egy szüneteltetés után manuálisan frissítse az oldalt, hogy megtekintse a projekthez **létrehozott**állapotot.

## <a name="manage-teams-and-people"></a>Csapatok és személyek kezelése

Alapértelmezés szerint minden létrehozott címkézési projekt egy új csapatot kap tagként. A csoportok azonban a projektek között is megoszthatók. És a projektek több csapattal is rendelkezhetnek. Csapat létrehozásához válassza a **csapat lap** **Csoport hozzáadása** elemét.

A személyeket a **személyek** oldalon kezelheti. Felhasználók hozzáadása és eltávolítása e-mail-cím alapján. Minden Labeler-nek hitelesítenie kell a Microsoft-fiók vagy Azure Active Directory, ha használja.  

Egy személy hozzáadása után hozzárendelheti a személyt egy vagy több csapathoz: Nyissa meg a **csapatok** lapot, válassza ki a csapatot, majd válassza a **személyek kiosztása** vagy **személyek eltávolítása**lehetőséget.

Ha e-mailt szeretne küldeni a csapatnak, válassza ki a csapatot a **csapat részletei** lap megtekintéséhez. Ezen az oldalon válassza az **e-mail csapat** lehetőséget, hogy megnyisson egy e-mail-piszkozatot a csapat összes tagja számára.

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése

A projekt inicializálását követően az Azure elindítja a futtatását. Válassza ki a projektet a fő **adatfelirat** lapon a **projekt részleteinek**megtekintéséhez. Az **irányítópult** lapon látható a címkézési feladat állapota.

Az **adat** lapon megtekintheti az adatkészletet, és ellenőrizheti a címkézett adatokat. Ha nem megfelelően címkézett információt lát, jelölje ki, majd válassza az **elutasítás**lehetőséget, amely eltávolítja a címkéket, és visszahelyezi az adatfelirat nélküli várólistába.

A **csapat** lapon rendelhet hozzá vagy rendelhet hozzá csoportokat a projekthez.

A projekt szüneteltetéséhez vagy újraindításához kattintson a **pause**/**Start** gombra. Csak akkor címkézheti az adatfeliratot, ha a projekt fut.

Az adatokat közvetlenül a **Project Details (projekt részletei** ) oldaláról is megcímkézheti a **felirat adatainak**kiválasztásával.

## <a name="add-new-label-class-to-a-project"></a>Új címke osztály hozzáadása projekthez

A címkézési folyamat során azt tapasztalhatja, hogy további címkékre van szükség a lemezképek besorolásához.  Előfordulhat például, hogy egy "ismeretlen" vagy "más" címkét szeretne felvenni a zavaró képek jelzésére.

A következő lépések segítségével adhat hozzá egy vagy több címkét egy projekthez:

1. Válassza ki a projektet a fő **adatfelirat** lapon.
1. A lap tetején válassza a **felfüggesztés** lehetőséget a címkéző tevékenységtől való leállításához.
1. Válassza a **részletek** lapot.
1. A bal oldali listában válassza a **címke osztályok**lehetőséget.
1. A lista tetején válassza a **+ Címkék hozzáadása** ![címke hozzáadása](media/how-to-create-labeling-projects/add-label.png)
1. Az űrlapon adja hozzá az új címkét, és válassza ki, hogyan kell továbblépni.  Mivel módosította a képekhez elérhető címkéket, kiválaszthatja, hogyan kezelje a már címkézett adatok:
    * Kezdje újra, és távolítsa el az összes meglévő címkét.  Akkor válassza ezt a lehetőséget, ha az elejétől kezdve a címkék új teljes készletével szeretne feliratot kezdeni. 
    * Kezdjen el az összes meglévő címke megtartásával.  Válassza ezt a lehetőséget, ha az összes adatfelirat nélküli értéket szeretné megjelölni, de a meglévő címkéket a korábban címkézett lemezképek alapértelmezett címkéjéként kívánja megtartani.
    * Folytatás, az összes meglévő címke megtartása. Válassza ezt a lehetőséget, ha az összes olyan adathalmazt meg szeretné őrizni, amely már meg van jelölve, és kezdje el használni az új címkét, amely még nem címkézett.
1. Szükség szerint módosítsa az utasítások lapját az új felirat (ok) hoz.
1. Miután hozzáadta az összes új címkét, a lap tetején kattintson a **Start** gombra a projekt újraindításához.  

## <a name="export-the-labels"></a>Címkék exportálása

A Machine Learning kísérletezéshez bármikor exportálhatja a címkézési adattípust. A képfeliratokat [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure Machine learning adatkészletként lehet exportálni. Használja az **Exportálás** gombot a címkéző projekt **Project Details (projekt részletei** ) lapján.

A kókusz-fájl a Azure Machine Learning munkaterület alapértelmezett blob-tárolójában jön létre az *export/Coco*mappában lévő mappában. Az exportált Azure Machine Learning adatkészletet a Machine Learning **adatkészletek** szakaszában érheti el. Az adatkészlet részletei lap a Pythonból elérhető feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>További lépések

* [Képbesorolás vagy objektum-észlelési](how-to-label-images.md) lemezképek címkézése
* További információ a [Azure Machine learning és a Machine learning Studio (klasszikus)](compare-azure-ml-to-studio-classic.md)
