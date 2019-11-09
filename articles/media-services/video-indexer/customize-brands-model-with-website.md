---
title: A Brands Model testreszabása a Video Indexer webhelyen – Azure
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a márkák modelljét a Video Indexer webhelyén.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f4b2f7a8b43c1723beb0dd8da82814f74cd7b135
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838352"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Márkák modell testreszabása a Video Indexer webhellyel

A Video Indexer támogatja a beszédfelismerés és a vizualizáció szövegének felderítését a videó-és hangtartalom indexelése és újraindexelése során. A márka észlelési funkciója a Bing márkák adatbázisa által javasolt termékek, szolgáltatások és vállalatok megemlítését azonosítja. Ha például a Microsoft a videóban vagy a hangtartalomban szerepel, vagy ha a videó vizualizációs szövegben jelenik meg, akkor Video Indexer a tartalmat a tartalomban márkaként észleli. Az egyéni márkák modell segítségével kiválaszthatja, hogy Video Indexer észleli-e a Bing Brands-adatbázisból származó márkákat, kizár bizonyos márkákat a felderítésből (lényegében a márkák fekete listáját hozza létre), és tartalmazza a modell részét képező márkákat. Előfordulhat, hogy nem a Bing Brands (márkák) adatbázisában található (lényegében a márkák fehér listáját hozza létre).

Részletes áttekintést az [Áttekintés](customize-brands-model-overview.md)című témakörben talál.

A jelen témakörben leírtak szerint a Video Indexer weblapon létrehozhat, használhat és szerkeszthet egyéni márkákat tartalmazó modelleket a videóban. Használhatja az API-t is, a [Brands Model API](customize-brands-model-with-api.md)-k használatával történő testreszabása című témakörben leírtak szerint.

## <a name="edit-the-settings-of-the-brands-model"></a>A Brands modell beállításainak szerkesztése  

Lehetősége van beállítani, hogy szeretné-e a márkákat a Bing Brands adatbázisából észlelni. Ehhez módosítania kell a Brands modell beállításait.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. A fiókban található modellek testreszabásához kattintson a lap jobb felső sarkában található **tartalmi modell testreszabása** gombra.
 
   ![Tartalmi modell testreszabása](./media/content-model-customization/content-model-customization.png) 
3. A márkák szerkesztéséhez válassza a **márkák** lapot.

    ![Márkák modell testreszabása](./media/customize-brand-model/customize-brand-model.png)
4. Ellenőrizze a **Bing által javasolt márkák megjelenítése** lehetőséget, ha azt szeretné, hogy a video Indexer a Bing által javasolt márkákat tartalmazzon. Ha nem Video Indexer szeretné, hogy a tartalomban a Bing által javasolt márkák észlelhetők, ne törölje a jelet a jelölőnégyzetből. 

## <a name="include-brands-in-the-model"></a>Márkák belefoglalása a modellbe

A **márkák belefoglalása** szakasz azokat az egyéni márkákat jelöli, amelyeket a video Indexer szeretne felderíteni, még akkor is, ha azokat a Bing nem javasolja.  

### <a name="add-a-brand"></a>Márka hozzáadása

1. Kattintson a "+ márka hozzáadása" elemre.

    ![Márkák modell testreszabása](./media/customize-brand-model/add-brand.png)

    Adja meg a nevet (kötelező), a kategóriát (nem kötelező), a leírást (nem kötelező) és a hivatkozási URL-címet (opcionális).
    A Kategória mező célja, hogy segítsen a márkák címkézésében. Ez a mező a video Indexer API-k használatakor a márka *címkéi* jelenik meg. Például az "Azure" márka címkézhető vagy kategorizálható "Cloud"-ként.

    A hivatkozás URL-címe mező a márka bármely olyan hivatkozási webhelye lehet, mint például a wikipedia oldalára mutató hivatkozás.
2. Kattintson a "márka hozzáadása" lehetőségre, és látni fogja, hogy a márka hozzá lett adva a **include Brands** listához.

### <a name="edit-a-brand"></a>Márka szerkesztése

1. Kattintson a szerkeszteni kívánt márka melletti ceruza ikonra.

    A márka kategóriájának, leírásának vagy hivatkozási URL-címének frissítését végezheti el. A márka neve nem módosítható, mert a márkák nevei egyediek. Ha módosítania kell a márka nevét, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.
2. Kattintson a **frissítés** gombra a Brand új információval való frissítéséhez.

### <a name="delete-a-brand"></a>Márka törlése

1. Kattintson a törölni kívánt márka melletti Kuka ikonra.
2. Kattintson a "Törlés" gombra, és a márka többé nem jelenik meg a *belefoglalási márkák* listájában.

## <a name="exclude-brands-from-the-model"></a>Márkák kizárása a modellből

A **márkák kizárása** szakasz azokat a márkákat jelenti, amelyeket a video Indexer nem észlelhető.

### <a name="add-a-brand"></a>Márka hozzáadása

1. Kattintson a "+ márka hozzáadása" elemre.

    Adjon meg egy nevet (kötelező), kategória (nem kötelező).
2. Kattintson a "márka hozzáadása" elemre, és látni fogja, hogy a márka hozzá lett adva a *kizárási márkák* listához.

### <a name="edit-a-brand"></a>Márka szerkesztése

1. Kattintson a szerkeszteni kívánt márka melletti ceruza ikonra.

    A márka kategóriáját csak frissíteni lehet. A márka neve nem módosítható, mert a márkák nevei egyediek. Ha módosítania kell a márka nevét, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.
2. Kattintson a **frissítés** gombra a Brand új információval való frissítéséhez.

### <a name="delete-a-brand"></a>Márka törlése

1. Kattintson a törölni kívánt márka melletti Kuka ikonra.
2. Kattintson a "Törlés" gombra, és a márka többé nem jelenik meg a *kizárási márkák* listájában.

## <a name="next-steps"></a>További lépések

[A Brands modell testreszabása API-k használatával](customize-brands-model-with-api.md)
