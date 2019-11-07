---
title: Az Adatfeliratok beolvasása
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és futtathat címkéző projekteket a gépi tanulásra vonatkozó adatcímkézéshez.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 76f995901814c90ff9fd78585c98d56b3478e8b4
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612772"
---
# <a name="get-labels-for-data"></a>Az Adatfeliratok beolvasása

A nagyméretű adatmennyiségek címkézése gyakran fejfájást eredményezett a gépi tanulási projektekben. A Computer látási összetevővel (például képbesorolással vagy objektum-észleléssel) rendelkező projektek esetében általában több ezer rendszerkép és a hozzá tartozó címkék szükségesek. 
 
A Azure Machine Learning Studio központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. A projektek címkézése segít az adatok, címkék és csoporttagok koordinálásában, így hatékonyabban kezelheti a címkézési feladatokat. A jelenleg támogatott feladatok a képbesorolás, a többcímkés vagy a többosztályos, valamint a kötött mezőkkel rendelkező objektumok azonosítása.

Az Azure nyomon követi az előrehaladást, és fenntartja a hiányos címkéző feladatok várólistáját. A címkéző nincs szükségük Azure-fiókra a részvételhez. Miután hitelesítette a Microsoft-fiókját (MSA) vagy a [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), a lehető legtöbb címkét megteheti az idő beállításával. A címkék a billentyűparancsokkal rendelhetők hozzá és módosíthatók. 

Elindíthatja és leállíthatja a projektet, személyeket és csoportokat adhat hozzá és távolíthat el, valamint figyelheti a folyamat előrehaladását. A címkézett adatokat kókusz formátumban vagy Azure ML-adatkészletként is exportálhatja. 

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Projekt létrehozása
> * A projekt adatának és szerkezetének meghatározása
> * A projektben dolgozó csapatok és személyek kezelése
> * A projekt futtatása és figyelése
> * Címkék exportálása 

## <a name="prerequisites"></a>Előfeltételek

* A címkével ellátni kívánt, helyi fájlokban vagy már az Azure Storage-ban tárolt
* Az alkalmazni kívánt címkék készlete
* Címkézési utasítások
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* Egy Azure Machine Learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

A címkézési projektek [Azure Machine learning studióból](https://ml.azure.com/)felügyelhetők. A **címkézési projektek** oldalon kezelheti a projektjeit, csapatait és személyeit. Egy projekthez hozzá van rendelve egy vagy több csapat, és egy csapathoz egy vagy több személy van rendelve. 

Ha az adatai már az Azure Blob Storage-ban vannak tárolva, a címkézési projekt létrehozása előtt elérhetővé kell tenni őket adattárként. További információ: [adattár létrehozása és regisztrálása](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Projekt létrehozásához válassza a **projekt hozzáadása**lehetőséget. Adja meg a megfelelő nevet, és válassza ki a **feladattípus címkézése**elemet. 

![Projekt-létrehozási varázsló címkézése](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Válassza a **Képbesorolás többcímkés** lehetőséget olyan projektek esetében, amelyekben egy **_vagy több_** osztályból származó címkét lehet alkalmazni egy képre. Előfordulhat például, hogy egy kutya fényképét a *kutya* és a *nappali* is címkézi
* Válassza a **Képbesorolás többosztályos** lehetőséget olyan projektek esetében, amelyekben csak **egyetlen osztály** alkalmazható a rendszerképekre
* Válassza az **objektum azonosítása (határolókeret)** lehetőséget olyan projektek esetében, amelyekben a feladat egy osztálynak egy adott képen belüli objektumhoz való hozzárendelésére, valamint az objektum körüli határolókeret megadására szolgál.

Válassza a **Next (tovább** ) lehetőséget, amikor készen áll a beléptetésre.

## <a name="specify-data-to-be-labeled"></a>A címkézni kívánt adattípusok meghatározása

Ha már létrehozott egy adatkészletet, amely az adatokat tartalmazza, kiválaszthatja a **meglévő adatkészlet kiválasztása** legördülő listából. Vagy válassza az **adatkészlet létrehozása** lehetőséget egy meglévő Azure-adattár kiválasztásához, vagy a helyi fájlok feltöltéséhez. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Adatkészlet létrehozása Azure-adattárból

Habár a helyi fájlok közvetlen feltöltésének kiválasztása sok használati esethez megfelelő, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a jelentős adatmennyiségek átvitele sokkal megbízhatóbb és gyorsabb. A fájlok áthelyezésének alapértelmezett módja a Azure Storage Explorer.

Adatkészlet létrehozása az Azure Blob Storage-ban már tárolt adatokból:

1. Válassza az **adatkészlet létrehozása** és **az adattárból** lehetőséget.
1. Az adatkészlet **nevének** megadása
1. Az **adatkészlet típusaként** a "file" elemet kell választania.  
1. Az adattár kiválasztása 
1. Ha az adatai a blob Storage-ban található almappában találhatók, válassza a **Tallózás** lehetőséget az elérési út kiválasztásához. 
    * Emellett a kijelölt elérési út almappáiban található összes fájl belefoglalása után is fűzze `/**` hozzáfűzést.
    * Az aktuális tárolóban és almappákban lévő összes érték belefoglalása a `**/*.*` használatával
1. Adja meg az adatkészlet leírását
1. Válassza a **tovább** lehetőséget 
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy a **Létrehozás** gombra kattintva hozhatja létre az adatkészletet.

### <a name="create-a-dataset-by-uploading-data"></a>Adatkészlet létrehozása adat feltöltésével

Ha közvetlenül szeretné feltölteni az adatait:

1. Válassza **az adatkészlet létrehozása** és **a helyi fájlok** lehetőséget.
1. Az adatkészlet **nevének** megadása
1. Az **adatkészlet típusaként** a "file" elemet kell választania.
1. Ha a **Speciális beállítások**lehetőséget választja, testreszabhatja az adattárat, a tárolót és az elérési utat
1. A **Tallózás** gombra kattintva kiválaszthatja a helyi fájlokat a feltöltéshez
1. Adja meg az adatkészlet leírását
1. Válassza a **tovább** lehetőséget 
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy a **Létrehozás** gombra kattintva hozhatja létre az adatkészletet.

Az adatfeltöltés az Azure ML-munkaterület alapértelmezett blob-tárolójába (`workspaceblobstore`) kerül.

## <a name="specify-label-classes"></a>Címke osztályok meghatározása

A **címkézési osztályok** lapon megadhatja az adatkategorizáláshoz használt osztályok készletét. Gondolja át ezeket az osztályokat, mivel a címkéző pontosságát és sebességét az egyszerűség fogja érinteni, amellyel a lehető legtöbbet választhatják ki. Például a növények vagy állatok teljes nemzetségének és fajainak helyesírása helyett érdemes lehet a Mezőkódok használata vagy a nemzetség rövidítése. 

Adjon meg egy címkét soronként, a **+** gomb használatával új sort adhat hozzá. Ha több mint 3 vagy 4 címkéje van, de kevesebb, mint 10, érdemes lehet az "1:", "2:", stb. előtaggal megerősíteni őket, hogy a címkét a számgombok használatával felgyorsítsa a munka felgyorsításához. 

## <a name="describe-the-labeling-task"></a>A címkézési feladat leírása

A címkézési feladat egyértelmű magyarázata fontos. A **címkézési utasítások** oldal lehetővé teszi, hogy külső webhelyre hivatkozzon, amely útmutatást nyújt a címkéző számára. Tartsa meg az utasítások feladatait és a célközönségnek megfelelőt. 

* Mik a megjelenő címkék, és ezek közül választhatnak? Van olyan hivatkozási szöveg, amelyre hivatkozni kell?
* Mi a teendő, ha nem tűnik megfelelő címkének? 
* Mi a teendő, ha több címke is megfelelőnek tűnik?
* Milyen megbízhatósági küszöbértéket kell alkalmazni a címkére? Szeretné a "legjobb becslést", ha nem biztos benne?
* Mi a teendő a részben lefedett vagy átfedésben lévő objektumokkal?
* Mi a teendő, ha egy érdekes objektumot levágja a rendszerkép széle?
* Mi a teendő, ha úgy érzik, hogy hiba történt a rendszerkép elküldése után? 

A kötött mezőkkel a további fontos kérdések a következők:

* Hogyan van definiálva a feladathoz tartozó határoló mező? Az objektumnak teljesen belsőnek kell lennie, és a lehető legszorosabban kell betakarítani a mennyiséget, vagy bizonyos mennyiségű vámkezelés elfogadható? 
* Milyen szintű ellátást és konzisztenciát vár a Labeler a határolókeret definiálására?

>[!Note]
> Ügyeljen arra, hogy a Labeler az első 9 felirat közül választhat az 1-9-as számú címkével. 

## <a name="initialize-the-labeling-project"></a>A címkézési projekt inicializálása

Az inicializálást követően a címkézési projekt bizonyos aspektusai nem változtathatók meg: nem módosítható a feladat típusa vagy adatkészlete. A címkéket módosíthatja, és módosíthatja a feladat leírásának URL-címét. A projekt létrehozása előtt gondosan tekintse át a beállításokat. Miután elküldte a projektet, vissza fog térni a **címkézési** kezdőlapra, amely a projektet **inicializálásként**jeleníti meg. Ez a lap nem frissül, ezért a manuális frissítés után a rendszer a **létrehozott**projektet jeleníti meg. 

## <a name="manage-teams-and-people"></a>Csapatok és személyek kezelése

A címkézési projekt egy alapértelmezett csapatot kap, és alapértelmezett tagként adja meg. Minden címkéző projekt új alapértelmezett csapatot kap, de a csapatok megoszthatók a projektek között. A projektek több csapattal is rendelkezhetnek. A csapat létrehozásához válassza a **csapat hozzáadása** elemet a **csapatok** lapon. 

A személyek kezelése az **emberek** oldalon történik. Hozzáadhat és eltávolíthat személyeknek szóló e-mail-címüket. Minden Labeler a Microsoft-fiókjával vagy a Azure Active Directory használatával kell hitelesítenie.  

Miután hozzáadta a személyt, hozzárendelheti őket egy vagy több csapathoz. Navigáljon a **csapatok** oldalra, válassza ki azt az adott csapatot, amelyben érdekli, majd használja a **személyek hozzárendelését** vagy a kívánt **személyek eltávolítását** .

Ha valaha is szeretne e-mailt küldeni a csapatnak mindenki számára, válassza ki a csapatot a **csapat részletei** oldalon. Ezen az oldalon az **e-mail csapat** gombja megnyitja az e-mail szerkesztőjét a csapat összes tagja címével.

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése

A projekt inicializálását követően az Azure elindítja a futtatását. Ha a fő **feliratozás** lapon a projektre kattint, a rendszer a **projekt részleteit**veszi figyelembe. Az **irányítópult** lapon megjelenik a címkézési feladat előrehaladása. 

Az **adat** lapon megtekintheti az adatkészletet, és áttekintheti a címkézett adatokat. Ha nem megfelelő címkével látja el az adatfeliratot, **kiválaszthatja** , majd az **elutasítás**gombra kattintva eltávolíthatja a címkéket, és visszahelyezheti az adatfelirat nélküli várólistába. 

A **csapat** lapon hozzárendelheti vagy törölheti a projekthez tartozó csoportokat. 

Ha offline vagy online állapotba kívánja tenni a projektet, válassza a **szüneteltetés**/**Indítás** gombot, amely a projekt futási állapotát váltja ki.

Az adatokat közvetlenül a **Project Details (projekt részletei** ) oldaláról is megcímkézheti a **felirat adatainak**kiválasztásával. Csak akkor címkézheti az adatfeliratot, ha a projekt fut. 

## <a name="export-the-labels"></a>Címkék exportálása

A Machine learning-kísérletezéshez bármikor exportálhatja a feliratot. A képfeliratokat [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure ml-adatkészletként lehet exportálni. Az **Exportálás** gombra a címkézési projekt **Project Details (projekt részletei** ) lapján található.

A kókusz-fájl az Azure ML-munkaterület alapértelmezett blob-tárolójában jön létre az **exportálási és a kókuszos**mappában. Az exportált Azure ML-adatkészletet a Studióban található **adatkészletek** szakaszban érheti el. Az adatkészlet részletei lap a Pythonból származó feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>További lépések

* [Képbesorolás vagy objektum-észlelési](how-to-label-images.md) lemezképek címkézése
* További információ a [Azure Machine learning és a studióról](../compare-azure-ml-to-studio-classic.md)