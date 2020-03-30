---
title: Személymodell testreszabása a Video Indexer webhelyével
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a személymodellt a Video Indexer webhelyével.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499976"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Személymodell testreszabása a Video Indexer webhelyével

A Video Indexer támogatja a hírességek ismertségét a videotartalmakhoz. A híresség elismerés vonás borít körülbelül egy millió szembenéz azon alapszik általában igényelt adatforrás mint IMDB, Wikipedia, és tető Összekapcsolt influencers. A részletes áttekintést a [Személy modell testreszabása a VideoIndexer ben című témakörben találja.](customize-person-model-overview.md)

A Video Indexer webhely segítségével szerkesztheti a videóban észlelt arcokat, a jelen témakörben leírtak szerint. Az API-t is használhatja, ahogy azt a [Személy testreszabása modell API-k használatával](customize-person-model-with-api.md)című részben ismerteti.

## <a name="central-management-of-person-models-in-your-account"></a>Személymodellek központi kezelése a fiókjában

1. A fiókban lévő Személy modellek megtekintéséhez, szerkesztéséhez és törléséhez keresse meg a Video Indexer webhelyét, és jelentkezzen be.

2. Válassza a tartalommodell testreszabási gombját a lap jobb felső sarkában.

    ![Tartalommodell testreszabása](./media/customize-face-model/content-model-customization.png)

3. Válassza a Személyek lapot.

    A fiókjában megjelenik az Alapértelmezett személy modell. Az Alapértelmezett személy modell tartalmazza azokat az arcokat, amelyeket esetleg szerkesztett vagy módosított a videók elemzési adataiban, amelyekhez nem adott meg egyéni személymodellt az indexelés során.

    Ha más személymodelleket is létrehozott, azok is megjelennek ezen az oldalon.

    ![Tartalommodell testreszabása](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Új személymodell létrehozása

1. Válassza a **+ Modell hozzáadása** gombot.

    ![Új személymodell hozzáadása](./media/customize-face-model/add-new-person.png)

2. Írja be a modell nevét, és válassza a név melletti csekk gombot.

    ![Új személymodell hozzáadása](./media/customize-face-model/add-new-person2.png)

    Új Személy modellt hozott létre. Most már hozzáadhat arcokat az új Személy modellhez.

3. Jelölje ki a lista menügombot, és válassza **a + Személy hozzáadása**lehetőséget.

    ![Új személymodell hozzáadása](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Új személy hozzáadása személymodellhez

> [!NOTE]
> A Video Indexer lehetővé teszi, hogy több azonos nevű személyt vegyen fel egy Személy modellbe. Azonban azt javasoljuk, hogy egyedi neveket adjon meg a modell minden egyes személyének a használhatóság és az egyértelműség érdekében.

1. Ha új lapot szeretne hozzáadni egy Személy modellhez, kattintson a lista menügombra annak a Személy modellnek a mellett, amelyhez hozzá szeretné adni az arcot.

1. Válassza a **+ Személy hozzáadása** lehetőséget a menüből.

    ![Új arc hozzáadása a személyhez](./media/customize-face-model/add-new-face.png)

    Egy előugró ablak ban kérni fogja, hogy töltse ki a személy adatait. Írja be a személy nevét, és válassza a csekk gombot.

    ![Új arc hozzáadása a személyhez](./media/customize-face-model/add-new-face2.png)

    Ezután választhat a fájlkezelőből, vagy áthúzhatja az arc arcképeit. A Video Indexer minden szabványos képfájltípust (pl. JPG, PNG stb.) megveszi.

    A Video Indexer észleli ennek a személynek az előfordulásait az indexelt jövőbeli videókban és az aktuálisan már indexelt videókban, azzal a Személy modellel, amelyhez hozzáadta ezt az új arcot. Az aktuális videókban szereplő személy felismerése némi időt vehet igénybe, mivel ez egy kötegelt folyamat.

## <a name="rename-a-person-model"></a>Személymodell átnevezése

A fiókjában lévő bármely személymodellt átnevezheti, beleértve az Alapértelmezett személy modellt is. Még ha átnevezi az alapértelmezett Személy modellt, akkor is alapértelmezett személymodellként fog szolgálni a fiókjában.

1. Jelölje ki az átnevezni kívánt Személy modell melletti lista menügombot.
2. Válassza a menü **Átnevezés parancsát.**

    ![Személymodell átnevezése](./media/customize-face-model/rename-person.png)

3. Válassza ki a modell aktuális nevét, és írja be az új nevet.

    ![Személymodell átnevezése](./media/customize-face-model/rename-person2.png)

4. Jelölje ki az átnevezendő modell ellenőrző gombját.

## <a name="delete-a-person-model"></a>Személymodell törlése

A fiókjában létrehozott bármely Személy modelltörölhető. Az alapértelmezett személymodell azonban nem törölhető.

1. Válassza a menü **Törlés parancsát.**

    ![Személymodell törlése](./media/customize-face-model/delete-person.png)

    Megjelenik egy előugró ablak, amely értesíti, hogy ez a művelet törli a Személy modellt, valamint a benne lévő összes személyt és fájlt. Ez a művelet nem lehet visszavonni.

    ![Személymodell törlése](./media/customize-face-model/delete-person2.png)

1. Ha biztos benne, válassza ismét a Törlés lehetőséget.

> [!NOTE]
> Az ezzel a (most törölt) személymodellel indexelt meglévő videók nem támogatják a videóban megjelenő arcok nevének frissítését. Ezekben a videókban csak akkor szerkesztheti az arcok nevét, ha egy másik Személy modell használatával újraindexeli őket. Ha személymodell megadása nélkül indexel, a rendszer az alapértelmezett modellt fogja használni.

## <a name="manage-existing-people-in-a-person-model"></a>Meglévő személyek kezelése egy személymodellben

A Személy modellek tartalmának megtekintéséhez jelölje ki a Személy modell neve melletti nyilat. A legördülő menü megmutatja az összes ember az adott személy modell. Ha az egyes személyek melletti lista menügombot választja, megjelenik a Beállítások kezelése, átnevezése és törlése.  

![Új arc hozzáadása a személyhez](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Személy átnevezése

1. Ha át szeretne nevezni egy személyt a Személy modellben, jelölje ki a lista menügombot, és válassza a lista menü **Átnevezés** parancsát.
1. Válassza ki a személy aktuális nevét, és írja be az új nevét.
1. Jelölje ki az ellenőrzés gombot, és a személy neve meglesz.

### <a name="delete-a-person"></a>Személy törlése

1. Ha törölni szeretne egy személyt a Személy modellből, jelölje ki a lista menügombot, és válassza a lista menü **Törlés parancsát.**
1. Egy előugró ablak jelzi, hogy ez a művelet törli a személyt, és hogy ez a művelet nem lehet visszavonni.
1. Válassza a **Törlés** ismét lehetőséget, és ezzel eltávolítja a személyt a Személy modellből.

### <a name="manage-a-person"></a>Személy kezelése

Ha a Kezelés lehetőséget **választja,** akkor az összes olyan arc jelenik meg, amelyből ez a személy modell betanítása folyamatban van. Ezek az arcok az adott személy nek az ezt a Személy modellt használó videókban vagy a manuálisan feltöltött képekből származó eseményekből származnak.

A **Képek hozzáadása**lehetőséget választva további arcokat adhat a személyhez.

A Kezelés ablaktáblán átnevezheti a személyt, és törölheti a személyt a Személy modellből.

## <a name="use-a-person-model-to-index-a-video"></a>Videó indexelése személymodell használatával

A Személy modell segítségével indexelheted az új videódat, ha a videó feltöltése során hozzárendeled a Személy modellt.

Ha a Személy modellt új videón szeretné használni, tegye a következőket:

1. Válassza a **Feltöltés** gombot a lap tetején.

    ![Személymodell feltöltése](./media/customize-face-model/upload.png)

1. Dobja el a videofájlt a körbe, vagy keresse meg a fájlt.
1. Válassza a **Speciális beállítások** nyílat.

    ![Személymodell feltöltése](./media/customize-face-model/upload2.png)

1. Jelölje ki a legördülő menüt, és válassza ki a létrehozott Személy modellt.

    ![Személymodell feltöltése](./media/customize-face-model/upload3.png)

1. Válassza a **Feltöltés** lehetőséget az oldal alján, és az új videó indexelése a Személy modell használatával lesz.

Ha a feltöltés során nem ad meg személymodellt, a Video Indexer indexeli a videót a fiókjában lévő Alapértelmezett személy modell használatával indexeli.

## <a name="use-a-person-model-to-reindex-a-video"></a>Videó újraindexelése személymodell használatával

Ha személymodellt szeretne használni a gyűjteményében lévő videók újraindexeléséhez, nyissa meg a fiókvideóit a Video Indexer kezdőlapján, és mutasson az újraindexelni kívánt videó nevére.

A videó szerkesztési, törlési és újraindexelési lehetőségei láthatók.

1. Válaszd ki a videó újraindexelésének lehetőségét.

    ![Videó újraindexelése a Person modell eleinte](./media/customize-face-model/reindex.png)

    Most kiválaszthatja azt a Személy modellt, akivel újraindexelheti a videót.
1. Jelölje ki a legördülő menüt, és válassza ki a használni kívánt Személy modellt.

    ![Videó újraindexelése a Person modell eleinte](./media/customize-face-model/reindex2.png)

1. Válaszd ki az **Újraindex** gombot, és a videód újraindexelésre kerül a Személy modelled használatával.

Az imént újraindexelt videóban észlelt és felismert arcokon végzett új módosításokat a rendszer a videó újraindexeléséhez használt Személy modellbe menti.

## <a name="managing-people-in-your-videos"></a>Emberek kezelése a videóidban

Az arcok szerkesztésével és törlésével kezelheti az észlelt arcokat és az indexelt videókban felismert személyeket.

Az arc törlésével eltávolít egy adott arcot a videó elemzési adatai közül.

Az arc szerkesztése átnevezi az észlelt és esetleg a videóban felismert arcot. Amikor szerkesztesz egy arcot a videódban, a rendszer ezt a nevet személybejegyzésként menti a Személy modellbe, amelyet a feltöltés és az indexelés során rendeltek a videóhoz.

Ha feltöltés közben nem rendel személymodellt a videóhoz, a rendszer a rendszer a fiók alapértelmezett személy modelljébe menti a szerkesztést.

### <a name="edit-a-face"></a>Arc szerkesztése

> [!NOTE]
> Ha egy személymodellnek két vagy több azonos nevű személye van, akkor nem fogod tudni megcímkézni ezt a nevet az adott Személy modellt használó videókon belül. Csak a Video Indexer tartalommodell testreszabási lapjának Kapcsolatok lapján módosíthatja azokat, akik megosztják ezt a nevet. Ezért javasoljuk, hogy a Személy modell minden egyes személyének egyedi neveket adjon meg.

1. Nyissa meg a Video Indexer webhelyét, és jelentkezzen be.
1. Keressen rá egy megtekinteni és szerkesztni kívánt videóra a fiókjában.
1. Ha egy arcot szeretne szerkeszteni a videóban, lépjen az Elemzések lapra, és válassza a ceruza ikont az ablak jobb felső sarkában.

    ![Arc szerkesztése a videóban](./media/customize-face-model/edit-face.png)

1. Jelölje ki az észlelt lapok bármelyikét, és módosítsa a nevüket az "Ismeretlen #X" (vagy az archoz korábban hozzárendelt név) értéken.
1. Az új név beírása után jelölje ki az új név melletti ellenőrző ikont. Ez a művelet menti az új nevet, és felismeri és elnevezi az arc összes előfordulását a többi aktuális videóba és a jövőbeli videókba. Az arc felismerése a többi aktuális videóban időbe telhet, amíg érvénybe lép, mivel ez egy kötegelt folyamat.

Ha egy olyan arcot nevezel meg egy meglévő személy nevével a videó által használt Személy modellben, amelyet a videóból származó észlelt arcképek egyesítnek a modellben már meglévőkkel. Ha új nevű arcot nevez el, a videó által használt Személy modellben új személybejegyzés jön létre.

![Arc szerkesztése a videóban](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Arc törlése

Ha törölni szeretne egy észlelt arcot a videóban, nyissa meg az Elemzési adatok ablaktáblát, és válassza a ceruza ikont az ablaktábla jobb felső sarkában. Az arc neve alatt válassza a **Törlés** lehetőséget. Ez a művelet eltávolítja az észlelt arcot a videóból. A személy arca továbbra is észlelhető lesz a többi videóban, amelyben megjelenik, de az indexelésután is törölheted az arcot ezekből a videókból.

A személy, ha meglett nevezve, továbbra is létezni fog abban a Személy modellben, amelyet az arc törlését tartalmazó videó indexelésére használtak, kivéve, ha kifejezetten törli a személyt a Személy modellből.

![Arc törlése a videóból](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>További lépések

[Személymodell testreszabása API-k használatával](customize-person-model-with-api.md)
