---
title: Nyelvi modellek testreszabása a Video Indexer webhely használatával – Azure
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a nyelvi modellt a Video Indexer webhellyel.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513915"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Nyelvi modell testreszabása a Video Indexer webhellyel

A Video Indexer lehetővé teszi, hogy egyéni nyelvi modelleket hozzon létre a beszédfelismerés testre szabásához az adaptációs szöveg feltöltésével, azaz olyan tartományból származó szöveggel, amelynek a szókincsét szeretné a motorhoz igazítani. A modell betanítása után a rendszer az adaptációs szövegben szereplő új szavakat fogja felismerni. 

Az egyéni nyelvi modellekkel kapcsolatos részletes áttekintést és ajánlott eljárásokat lásd: [nyelvi modell testreszabása video Indexer](customize-language-model-overview.md)használatával.

A következő témakörben leírtak szerint használhatja a Video Indexer webhelyét egyéni nyelvi modellek létrehozásához és szerkesztéséhez a fiókjában. Az API-t is használhatja az API-k [használata a nyelvi modell testreszabása](customize-language-model-with-api.md)című témakörben leírtak szerint.

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. A fiókban található modellek testreszabásához kattintson a lap jobb felső sarkában található **tartalmi modell testreszabása** gombra.

   ![Tartalmi modell testreszabása](./media/content-model-customization/content-model-customization.png)

3. Válassza a **nyelv** fület.

    Ekkor megjelenik a támogatott nyelvek listája. 

    ![Nyelvi modell testreszabása](./media/customize-language-model/customize-language-model.png)

4. A kívánt nyelven válassza a **modell hozzáadása**menüpontot.
5. Írja be a nyelvi modell nevét, és nyomja le az ENTER billentyűt.

    Ezzel létrehozza a modellt, és lehetővé teszi a szöveges fájlok feltöltését a modellbe.
6. Szövegfájl hozzáadásához kattintson a **fájl hozzáadása**gombra. Ekkor megnyílik a fájlkezelő.

7. Navigáljon a fájlhoz, és válassza ki a szövegfájlt. Több szövegfájlt is hozzáadhat egy nyelvi modellhez.

    Szövegfájl hozzáadásához kattintson a nyelvi modell jobb oldalán található **...** gombra, majd a **fájl hozzáadása**lehetőségre.
8. Ha elkészült a szövegfájlok feltöltésével, kattintson a zöld **vonat** lehetőségre.

    ![Tanítási nyelvi modell](./media/customize-language-model/train-model.png)

A betanítási folyamat eltarthat néhány percig. A képzés elvégzése után a modell **melletti képzést** láthatja. Megtekintheti, letöltheti és törölheti a fájlt a modellből.

![Betanított nyelvi modell](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Nyelvi modell használata új videón

A nyelvi modell új videón való használatához tegye a következők egyikét:

* Kattintson a lap tetején található **feltöltés** gombra. 

    ![Feltöltés](./media/customize-language-model/upload.png)
* Hang-vagy videofájl eldobása a kör vagy a fájl tallózásával

    ![Feltöltés](./media/customize-language-model/upload2.png)

Ezzel a beállítással kiválaszthatja a **videó forrásának nyelvét**. Kattintson a legördülő listára, és válassza ki a listából létrehozott nyelvi modellt. Meg kell mondania a nyelvi modell nyelvét, valamint azt a nevet, amelyet zárójelben adott meg.

Kattintson a lap alján található **feltöltés** lehetőségre, és az új videó a nyelvi modell alapján lesz indexelve.

### <a name="using-a-language-model-to-reindex"></a>Nyelvi modell használata az újraindexeléshez

Ha a nyelvi modellt szeretné használni a gyűjteményben lévő videók újraindexeléséhez, lépjen az [video Indexer](https://www.videoindexer.ai/) kezdőlapján található **fiókra** , és vigye az egérmutatót az átindexelni kívánt videó nevére.

Megtekintheti a videó szerkesztésének lehetőségeit, a videó törlését és a videó újraindexelését. A videó újraindexeléséhez kattintson a lehetőségre.

![Újraindexelés](./media/customize-language-model/reindex1.png)

Ezzel a beállítással kiválaszthatja a **videó forrásának nyelvét** , és átindexelheti a videóját. Kattintson a legördülő listára, és válassza ki a listából létrehozott nyelvi modellt. Meg kell mondania a nyelvi modell nyelvét, valamint azt a nevet, amelyet zárójelben adott meg.

![Újraindexelés](./media/customize-language-model/reindex.png)

Kattintson az **újraindexelés** gombra, és a videó az Ön nyelvi modellje alapján újraindexelve lesz.

## <a name="edit-a-language-model"></a>Nyelvi modell szerkesztése

A nyelvi modell szerkesztéséhez módosítsa a nevét, adjon hozzá fájlokat, és törölje a fájlokat.

Ha a nyelvi modellből fájlokat ad hozzá vagy töröl, a zöld **vonat** lehetőségre kattintva ismét be kell tanítania a modellt.

### <a name="rename-the-language-model"></a>A nyelvi modell átnevezése

A nyelvi modell nevének módosításához kattintson a **...** gombra a nyelvi modell jobb oldalán, és válassza az **Átnevezés**lehetőséget. 

Írja be az új nevet, és nyomja le az ENTER billentyűt.

### <a name="add-files"></a>Fájlok hozzáadása

Szövegfájl hozzáadásához kattintson a **fájl hozzáadása**gombra. Ekkor megnyílik a fájlkezelő.

Navigáljon a fájlhoz, és válassza ki a szövegfájlt. Több szövegfájlt is hozzáadhat egy nyelvi modellhez.

Szövegfájl hozzáadásához kattintson a nyelvi modell jobb oldalán található **...** gombra, majd a **fájl hozzáadása**lehetőségre.

### <a name="delete-files"></a>Fájlok törlése

Ha törölni szeretne egy fájlt a nyelvi modellből, kattintson a szövegfájl jobb oldalán található **...** gombra, majd válassza a **Törlés**lehetőséget. Ekkor megjelenik egy új ablak, amely arról tájékoztat, hogy a törlés nem vonható vissza. Kattintson a **Törlés** lehetőségre az új ablakban.

Ez a művelet teljesen eltávolítja a fájlt a nyelvi modellből.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

Ha törölni szeretne egy nyelvi modellt a fiókjából, kattintson a nyelvi modell jobb oldalán található **...** gombra, és válassza a **Törlés**lehetőséget.

Ekkor megjelenik egy új ablak, amely arról tájékoztat, hogy a törlés nem vonható vissza. Kattintson a **Törlés** lehetőségre az új ablakban.

Ez a művelet teljesen eltávolítja a nyelvi modellt a fiókjából. A törölt nyelvi modellt használó összes videó esetében Ugyanez az index marad, amíg újra nem indexeli a videót. Ha újra indexeli a videót, új nyelvi modellt rendelhet a videóhoz. Ellenkező esetben a Video Indexer az alapértelmezett modellt fogja használni a videó újbóli indexeléséhez. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Nyelvi modellek testreszabása átiratok kijavítani

A Video Indexer támogatja a nyelvi modellek automatikus testreszabását a felhasználók által a videók átírásakor végzett tényleges javítások alapján.

1. Ha meg szeretne javítani egy átiratot, nyissa meg azt a videót, amelyet szerkeszteni szeretne a fiókjának videói alapján. Válassza az **Idősor** fület.

    ![Nyelvi modell testreszabása](./media/customize-language-model/timeline.png)
1. Kattintson a ceruza ikonra az átirat átiratának szerkesztéséhez. 

    ![Nyelvi modell testreszabása](./media/customize-language-model/edits.png)

    Video Indexer rögzíti a videó átírásával korrigált összes sort, és automatikusan hozzáadja azokat egy "from átirat szerkesztések" nevű szövegfájlhoz. Ezek a módosítások a videó indexeléséhez használt nyelvi modell újbóli betanítására szolgálnak. 
    
    Ha a videó indexelése során nem adott meg nyelvi modellt, akkor a videóhoz tartozó összes módosítást egy alapértelmezett nyelvi modell fogja tárolni, amelyet a videó észlelt nyelvén, a fiók módosításainak nevezzük. 
    
    Ha ugyanarra a sorra több szerkesztés lett létrehozva, akkor a nyelvi modell frissítésére csak a javított vonal utolsó verziója lesz felhasználva.  
    
    > [!NOTE]
    > A testreszabáshoz csak szöveges helyesbítések használatosak. Ez azt jelenti, hogy nem szerepelnek a tényleges szavakat (például írásjeleket vagy szóközöket) nem tartalmazó javítások. 
    
1. A tartalmi modell testreszabása oldal Language (nyelv) lapján láthatók a megjelenő átiratok.

    ![Nyelvi modell testreszabása](./media/customize-language-model/customize.png)

   Ha meg szeretné nézni az egyes nyelvi modellekhez tartozó "feladói szerkesztések" fájlt, kattintson rá a megnyitásához. 

    ![Az átirat szerkesztései](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Következő lépések

[Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
