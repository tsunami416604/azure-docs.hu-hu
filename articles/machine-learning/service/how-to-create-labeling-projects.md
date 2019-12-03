---
title: Az Adatfeliratok beolvasása
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és futtathat címkéző projekteket a gépi tanulásra vonatkozó adatcímkézéshez.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: e66a9f8a775a46c906601ea08be52ca9dfbe0171
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689294"
---
# <a name="get-labels-for-data"></a>Az Adatfeliratok beolvasása

A gépi tanulási projektek terjedelmes adatcímkézése gyakran fejfájás. A számítógép-látási összetevővel rendelkező projektek, például a képosztályozás vagy az objektumok észlelése általában több ezer rendszerkép használatát igénylik.
 
[Azure Machine learning](https://ml.azure.com/) központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. Felhasználhatja az adatok, címkék és csoporttagok koordinálását a címkézési feladatok hatékony kezelésére. Machine Learning támogatja a képbesorolást, a többcímkés vagy a többosztályos, valamint az objektum-azonosítást a kötött mezőkkel együtt.

Machine Learning nyomon követi a folyamat előrehaladását, és fenntartja a hiányos címkéző feladatok várólistáját. A címkéző nincs szükségük Azure-fiókra a részvételhez. Miután a hitelesítésük megtörtént a Microsoft-fiók vagy [Azure Active Directoryával](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), a lehető legtöbb címkézést teheti lehetővé.

Machine Learning a projekt elindítását és leállítását, a személyek és a csoportok hozzáadását és eltávolítását, valamint a figyelés folyamatát. A címkézett adatokat kókusz formátumban vagy Azure Machine Learning adatkészletként is exportálhatja.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Projekt létrehozása
> * A projekt adatának és szerkezetének meghatározása
> * A projektben dolgozó csapatok és személyek kezelése
> * A projekt futtatása és figyelése
> * Címkék exportálása

## <a name="prerequisites"></a>Előfeltételek

* A címkével ellátni kívánt, helyi vagy Azure-beli tárolóban tárolt adatfájlok.
* Az alkalmazni kívánt címkék halmaza.
* A címkézésre vonatkozó utasítások.
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLFree) a virtuális gép létrehozásának megkezdése előtt.
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

A címkézési projektek Azure Machine Learningból vannak felügyelve. A projektek és személyek kezelésére a **címkézési projektek** lapot használhatja. Egy projekthez hozzá van rendelve egy vagy több csapat, és egy csapathoz egy vagy több személy van rendelve.

Ha az adatai már az Azure Blob Storage-ban vannak, akkor a címkézési projekt létrehozása előtt elérhetővé kell tenni az adattárként. Részletekért lásd: adattárolók [létrehozása és regisztrálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores).

Projekt létrehozásához válassza a **projekt hozzáadása**lehetőséget. Adjon megfelelő nevet a projektnek, és válassza ki a **feladattípus címkézése**elemet.

![Projekt-létrehozási varázsló címkézése](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Válassza a **Képbesorolás több címkét** a projektekhez lehetőséget, ha egy *vagy több* címkét szeretne alkalmazni az osztályok egy adott képére. Előfordulhat például, hogy egy kutya fényképét a *kutya* és a *nappali*is címkézi.
* Válassza a **Képbesorolás többosztályos** a projektekhez lehetőséget, ha csak *egyetlen osztályt* szeretne alkalmazni osztályokból egy képből.
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

Fontos, hogy egyértelműen ismertesse a címkézési feladatot. A **címkézési utasítások** lapon hozzáadhat egy külső webhelyre mutató hivatkozást az utasítások címkézéséhez. Tartsa meg az utasítások feladatait és a célközönségnek megfelelőt. Vegye figyelembe a következő kérdéseket:

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

>[!NOTE]
> Ügyeljen arra, hogy a címkéző az 1-9-as számú kulcsok használatával kiválaszthatják az első 9 címkét.

## <a name="initialize-the-labeling-project"></a>A címkézési projekt inicializálása

A címkézési projekt inicializálását követően a projekt egyes szempontjai nem változtathatók meg. A feladattípus vagy az adatkészlet nem módosítható. A címkék és a feladat leírásának URL-címe *is* módosítható. A projekt létrehozása előtt gondosan tekintse át a beállításokat. A projekt elküldése után visszatért a **címkézési** kezdőlapra, amely a projekt **inicializálását**fogja megjeleníteni. Ez a lap nem frissül automatikusan. Ezért egy szüneteltetés után manuálisan frissítse az oldalt, hogy megtekintse a projekthez **létrehozott**állapotot.

## <a name="manage-teams-and-people"></a>Csapatok és személyek kezelése

Alapértelmezés szerint minden létrehozott címkézési projekt egy új csapatot kap tagként. A csoportok azonban a projektek között is megoszthatók. És a projektek több csapattal is rendelkezhetnek. Csapat létrehozásához válassza a **csapat lap** **Csoport hozzáadása** elemét.

A személyeket a **személyek** oldalon kezelheti. Felhasználók hozzáadása és eltávolítása e-mail-cím alapján. Minden Labeler-nek hitelesítenie kell a Microsoft-fiók vagy Azure Active Directory, ha használja.  

Egy személy hozzáadása után hozzárendelheti a személyt egy vagy több csapathoz: Nyissa meg a **csapatok** lapot, válassza ki a csapatot, majd válassza a **személyek kiosztása** vagy **személyek eltávolítása**lehetőséget.

Ha e-mailt szeretne küldeni a csapatnak, válassza ki a csapatot a **csapat részletei** lap megtekintéséhez. Ezen az oldalon válassza az **e-mail csapat** lehetőséget, hogy megnyisson egy e-mail-piszkozatot a csapat összes tagja számára.

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése

A projekt inicializálását követően az Azure elindítja a futtatását. A **projekt részleteinek**megtekintéséhez válassza ki a projektet a fő **feliratozás** lapon. Az **irányítópult** lapon látható a címkézési feladat állapota.

Az **adat** lapon megtekintheti az adatkészletet, és ellenőrizheti a címkézett adatokat. Ha nem megfelelően címkézett információt lát, jelölje ki, majd válassza az **elutasítás**lehetőséget, amely eltávolítja a címkéket, és visszahelyezi az adatfelirat nélküli várólistába.

A **csapat** lapon rendelhet hozzá vagy rendelhet hozzá csoportokat a projekthez.

A projekt szüneteltetéséhez vagy újraindításához kattintson a **pause**/**Start** gombra. Csak akkor címkézheti az adatfeliratot, ha a projekt fut.

Az adatokat közvetlenül a **Project Details (projekt részletei** ) oldaláról is megcímkézheti a **felirat adatainak**kiválasztásával.

## <a name="export-the-labels"></a>Címkék exportálása

A Machine Learning kísérletezéshez bármikor exportálhatja a címkézési adattípust. A képfeliratokat [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure Machine learning adatkészletként lehet exportálni. Használja az **Exportálás** gombot a címkéző projekt **Project Details (projekt részletei** ) lapján.

A kókusz-fájl a Azure Machine Learning munkaterület alapértelmezett blob-tárolójában jön létre az *export/Coco*mappában lévő mappában. Az exportált Azure Machine Learning adatkészletet a Machine Learning **adatkészletek** szakaszában érheti el. Az adatkészlet részletei lap a Pythonból elérhető feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Következő lépések

* [Képbesorolás vagy objektum-észlelési](how-to-label-images.md) lemezképek címkézése
* További információ a [Azure Machine learning és a Machine learning Studio (klasszikus)](../compare-azure-ml-to-studio-classic.md)
