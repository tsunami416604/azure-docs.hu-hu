---
title: A Video Indexer webhely segítségével testre szabhatja egy személy modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a Video Indexer webhelyet egy személy modellt.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892840"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>A Video Indexer webhely személy modell testreszabása

Video Indexer – hírességek felismerése videótartalmak támogatja. A hírességek felismerése funkció körülbelül 1 millió arcok IMDB Wikipedia és felső LinkedIn véleményvezérek például gyakran lekérdezett adatforrás alapján ismerteti. Részletes ismertetőt talál [egy személy modellje a Video Indexer](customize-person-model-overview.md).

A Video Indexer webhely használatával, amely az észlelt a videó szerkesztése ebben a témakörben leírtak szerint. Is használhatja az API-t, a leírtak szerint [API-kat használó személy modell testreszabása](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>A fiókjában személy modellek kezelése

1. Megtekintése, szerkesztése, és törölje a személy modellek a fiókjában, a Video Indexer webhelyre navigálhat, és jelentkezzen be.
2. Kattintson a tartalommodell Testreszabás gombra az oldal jobb felső sarkában.

    ![Tartalommodell testreszabása](./media/customize-face-model/content-model-customization.png)
3. Válassza ki a személyek fülre.

    Látni fogja az alapértelmezett személy modell-fiókjában található. Az alapértelmezett személy modell bármely arcok, előfordulhat, hogy szerkesztésekor vagy módosításakor a videók, amelyhez nem adott meg egy egyéni személy modell indexelés során az elemzéseket a tárolja. 

    Ha más személy modellek hozta létre, azok ezen a lapon megjelenik. 

    ![Tartalommodell testreszabása](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Hozzon létre egy új személynek modell

1. Kattintson a **+ Hozzáadás modell** gombra.

    ![Adjon hozzá egy új személy](./media/customize-face-model/add-new-person.png)
2. Adja meg a modell neve, és kattintson a pipa gombra a neve mellett.

    ![Adjon hozzá egy új személy](./media/customize-face-model/add-new-person2.png)

    Létrehozott egy új személynek modellt. Arcok adhat hozzá az új személy modell.
3. Kattintson a lista menügombra, és válassza a **+ Hozzáadás személy**.

    ![Adjon hozzá egy új személy](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Új személy egy személy modell hozzáadása

> [!NOTE]
> Video Indexer segítségével ugyanazzal a névvel több felhasználó hozzáadása egy személy modellben. Ajánlott azonban, hogy engedélyezi a unque neveket, mindenki, aki a modellben a használhatóság és az átláthatóság érdekében.

1. Egy személy modell ad hozzá egy új lapra, kattintson a lista menü gomb mellett a, a face a hozzáadni kívánt személy modellt.
1. Kattintson a **+ Hozzáadás személy** a menüből.

    ![Adjon hozzá egy új arc személlyel](./media/customize-face-model/add-new-face.png)
 
    Egy előugró ablak, amely felszólítja a személy részletek megadását kérik. Írja be a személy nevét, és kattintson a pipa gombra.

    ![Adjon hozzá egy új arc személlyel](./media/customize-face-model/add-new-face2.png)
 
Akkor is majd telepítőre a Fájlkezelőben válassza ki vagy húzza a face face képei. Video Indexer tart az összes szabványos lemezkép fájltípusok (például: JPG, PNG és más).

Video Indexer – ezt a személyt előfordulását észleléséhez a későbbi videók, hogy Ön index és a jelenlegi videók, korábban már indexelt, a személy modell, amelyhez hozzáadta ezt az új face használatával képesnek kell lennie. Annak a személynek a jelenlegi videók a felismerés érvénybe léptetéséhez, időbe telhet, mivel ez egy batch-folyamat.


## <a name="rename-a-person-model"></a>Nevezze át a személy modell

A fiókjában, beleértve az alapértelmezett személy modell bármely személy modellt is át lehet nevezni. Akkor is, ha az alapértelmezett személy modell átnevezéséhez, továbbra is erre a célra az alapértelmezett személy modellt a fiókjában.

1. Kattintson a lista menü gombra az átnevezni kívánt személy modell mellett.
2. Kattintson a **átnevezése** a menüből.

    ![Nevezze át az adott személy](./media/customize-face-model/rename-person.png)
3. A modell a jelenlegi nevére, és írja be az új nevet.

    ![Nevezze át az adott személy](./media/customize-face-model/rename-person2.png)
4. Kattintson a pipa gombra a modell át kell nevezni.

## <a name="delete-a-person-model"></a>Egy személy modell törlése

Bármely személy modell, amely a fiókban létrehozott törölheti. Az alapértelmezett személy modell azonban nem törölhető.

1. Kattintson a **törlése** a menüből.

    ![Egy személy törlése](./media/customize-face-model/delete-person.png)
    
    Egy előugró ablak jelenik meg, és a Önt, hogy a művelet törli a személy modell és az összes a személyek és a benne található fájlokat. A művelet nem vonható vissza. 

    ![Egy személy törlése](./media/customize-face-model/delete-person2.png)
1. Ha biztos abban, kattintson ismét a törlése.

> [!NOTE]
> A meglévő videókat személy (most már törölt) modell használatával indexelt arra, hogy az, hogy frissítse az arcokat a videókban megjelenő nevei nem támogatja. Szerkesztheti az arcokat a videókban nevei, csak azt követően, újraindexelése őket egy másik személy modell használatával fogja. Ha egy személy modell megadása nélkül, újraindexelés, az alapértelmezett modell használható. 

## <a name="manage-existing-people-in-a-person-model"></a>Egy személy modellben meglévő felhasználók kezelése

Tekintse meg a tartalmát, a személy modellek bármelyikét, kattintson a személy modell neve melletti nyílra.
A legördülő megjeleníti annak a személynek az összes adott személy modellt. Ha a mellett annak a személynek a lista menügombra kattint, láthatja, kezelése, átnevezése és törlése a beállításokat.  

![Adjon hozzá egy új arc személlyel](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Nevezze át az adott személy

1. Nevezze át a személy modell az a személy, kattintson a lista menügombra, és válassza a **átnevezése** a lista menüből.
1. Kattintson a személy, és írja be az új nevét, az aktuális nevét.
1. Kattintson a pipa gombra, majd személy neve lesz.

### <a name="delete-a-person"></a>Egy személy törlése

1. A személy modellből egy személy törléséhez kattintson a lista menügombra, válassza a **törlése** a lista menüből.
1. Egy előugró ablak jelzi, hogy a művelet törli a személy és, hogy ez a művelet nem vonható vissza.
1. Kattintson ismét a Delete és a személy távolítja el az a személy modell.

### <a name="manage-a-person"></a>Egy személy kezelése

Kattintva **kezelés**, láthatja az arcok, amely az a személy modell betanítása. Ezek az arcokat a videókban, a személy modellt használó személy előfordulását vagy a manuálisan feltöltött képek származnak. 

Lemezképek hozzáadása gombra kattintva adhat hozzá további arcokat személynek.

Használhatja a kezelés panelen nevezze át a személy, és törli a személynek a személy modellből.

## <a name="use-a-person-model-to-index-a-video"></a>Személy modellt alkalmaznak a videó indexelése

Egy személy modell segítségével teheti a személy modell a videó feltöltésekor a új videó indexeléséről.

A személy modellt használ egy új videót, tegye a következőket:

1. Kattintson a **feltöltése** gombra a lap tetején.

    ![Feltöltés](./media/customize-face-model/upload.png)
1. A körre a dobja el a videofájl, vagy keresse meg a fájlt.
1. A Speciális beállítások nyílra.

    ![Feltöltés](./media/customize-face-model/upload2.png)
1. Kattintson a legördülő menüből, és válassza ki a létrehozott személy modellt.

    ![Feltöltés](./media/customize-face-model/upload3.png)
1. A feltöltés lehetőséget az oldal alján kattintson, és az új videót a személy modell indexelését.

Ha nem ad meg egy személy modellt a feltöltés során, a Video Indexer indexeli a videót a fiókjában a személy alapértelmezett modell használatával.

## <a name="use-a-person-model-to-reindex-a-video"></a>Egy személy modell segítségével videó újraindexelése 

Egy személy modell használatával egy videót a gyűjteményt a újraindexelés, lépjen a fiók videók a Video Indexer – kezdőlap és vigye az egérmutatót a videó újraindexelése kívánt.

Beállítások szerkesztése, törlése és a videó újraindexelése láthatja. 

1. Kattintson a lehetőségre újraindexelni a videót.

    ![Reindex](./media/customize-face-model/reindex.png)

    Most kiválaszthatja a személy modell újraindexelni a videót.
1. Kattintson a legördülő menüből, és válassza ki a használni kívánt személy modellt. 

    ![Reindex](./media/customize-face-model/reindex2.png)

1. Kattintson a **újraindexelése** gomb, valamint a videó lesz újra kell indexelni a személy modell használatával.

Új szerkesztéseket, hogy az észlelt, és felismeri a videóban, amely csak újraindexelése az arcok a személy modell, amellyel a videó újraindexelése menti.

## <a name="managing-people-in-your-videos"></a>A videók felhasználóinak kezelése

Az arcok észlelt és sikerült felismerni a videókban index szerkesztését, és törölje az arcok személyek kezelheti.

Egy adott arc törlése a egy oldallal, eltávolítja az elemzéseket a videó.

Egy észlelt és a videó esetleg felismert arc egy ARC szerkesztése, átnevezése. A videó egy ARC szerkeszti, a személy modell, amely hozzá lett rendelve a videó feltöltése és az indexelés során személy bejegyzésének ugyanez a neve is menti.

Nem rendel egy személy modellt a videó feltöltésekor, ha a rendszer menti a módosításokat a fiók alapértelmezett személy modellben.

### <a name="edit-a-face"></a>A face szerkesztése


> [!NOTE]
> Ha egy személy modell két vagy több különböző személy ugyanazzal a névvel rendelkezik, nem lesz felcímkézheti a ugyanez a neve, amely a személy modellek a videókban. Csak lesz módosításokat hajthat végre, amelyek ezt a nevet a személyek lapja tartalommodell testreszabása a Video Indexer a személyeknek. Ebből kifolyólag javasoljuk, hogy egyedi nevet adni minden egyes személy számára a személy modellben.

1. A Video Indexer webhelyre navigálhat, és jelentkezzen be.
1. Keresse meg a kívánt videót megtekintése és szerkesztése a fiókjában.
1. A face a videó szerkesztéséhez nyissa meg az Insights lapot, és kattintson a ceruza ikonra az ablak jobb felső sarkában.

    ![A face a videó szerkesztése](./media/customize-face-model/edit-face.png)
1. Az egyes az észlelt arcok, és módosítsa a nevük "Ismeretlen #x2713" (vagy a neve, amely korábban már hozzá lett rendelve a face). 
1. Után írja be az új nevet, kattintson a pipa ikonra az új neve mellett. Ez menti az új név és felismeri és összes előfordulását a többi aktuális videók és a jövőben feltöltött videók a face-neveket. Felismerés, a face található egyéb aktuális érvénybe, időbe telhet, mivel ez egy batch-folyamat.

Ha egy ARC egy meglévő, a személy modellben a videó használó személy nevét, ez a videó, mi a modell már létezik az adott személy észlelt face képeit egyesíteni fogják a. Ha teljesen új néven nevezze el a egy oldallal, ez egy új személynek bejegyzést hoz létre a személy modell, amely a videó használja. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>A face törlése

A videó egy felismert arc törléséhez nyissa meg a Insights ablaktáblán, és kattintson a ceruza ikonra a jobb felső sarokban található meg a. Kattintson a Törlés lehetőségre, a face neve alatt. Ezzel eltávolítja a videót a face észlelt. A személy face továbbra is észlelhető, amely megjelenik a más videókat, de a face ezeket videókkal után törölheti indexelve van. A személy neveztek, ha is továbbra is szerepel a személy modell, amellyel a videót, amelyből törölte az arcfelismerés, kivéve, ha kifejezetten a személy törli a személy modellből index.

![A videó egy ARC törlése](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>További lépések

[API-k használatával személy modell testreszabása](customize-person-model-with-api.md)
