---
title: A Video Indexer webhely segítségével testre szabhatja a márka modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a Video Indexer webhely márkái modell.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285290"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>A Video Indexer webhely márkái modell testreszabása

A video Indexer márka észlelését a beszéd- és vizuális szöveges indexelést és a video- és audiotartalmak újraindexelés során támogatja. A márka duplikálásészlelési szolgáltatását a termékek, szolgáltatások, hírforrásaiból azonosítja, és a vállalatok a Bing márkái adatbázis által javasolt. Például ha egy videó vagy hang tartalom említett, a Microsoft vagy egy videóban visual szöveg megjelenik, Video Indexer észleli ezt, a tartalom egy adott márkához. Egy egyéni márkái modell kiválasztását teszi-e a Video Indexer, észlelni fogja a Bing márkái márkái adatbázis, a kizárási bizonyos legyenek márkái észlelt (lényegében létrehozásakor márkái fekete listája), és márkákat, amelyeket a modell részét kell tartalmaznia Bing márkái adatbázisban (lényegében márkái fehér lista létrehozásával), amely nem feltétlenül.

Részletes ismertetőt talál [áttekintése](customize-brands-model-overview.md).

A Video Indexer webhely segítségével létrehozását, használatát és egyéni márkái modellek észlelhető a videó szerkesztése ebben a témakörben leírtak szerint. Is használhatja az API-t, a leírtak szerint [API-k használatával testre szabhatja márkái modell](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Szerkessze a beállításokat a márka modell  

Lehetősége van észlelhető a Bing márkái adatbázisból való márkái szeretné-e be. Ehhez szerkessze a beállításokat a márka modell kell.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Testre szabhatja egy modellt a fiókjában, kattintson a a **modell testreszabása tartalom** gombra az oldal jobb felső sarkában.
 
   ![Tartalom modell testreszabása](./media/content-model-customization/content-model-customization.png) 
3. Márkái szerkesztéséhez válassza ki a **márkái** fülre.

    ![Márkái modell testreszabása](./media/customize-brand-model/customize-brand-model.png)
4. Ellenőrizze a **megjelenítése a Bing által javasolt márkái** lehetőséget, ha azt szeretné, hogy a Video Indexer, a Bing által javasolt márkái tartalmazza. Hagyja bejelölve a beállítást, ha nem szeretné, hogy a tartalom a Bing által javasolt márkái észleli a Video Indexer. 

## <a name="include-brands-in-the-model"></a>A modell márkái belefoglalása

A **közé tartozik a márka** szakasz egyéni márkákat, amelyeket szeretne észlelése, még akkor is, ha azok nem ajánlott a Bing Video Indexer jelöli.  

### <a name="add-a-brand"></a>Adjon hozzá egy adott márkához

1. Kattintson a "+ Hozzáadás márka".

    ![Márkái modell testreszabása](./media/customize-brand-model/add-brand.png)

    Adjon meg egy (kötelező), a kategória (nem kötelező), a leírást (nem kötelező), és hivatkozást az URL-címe (nem kötelező).
    A kategória mező segítséget nyújtanak a márka címkézése az adott. Ez a mező megjelenik-e a márka, *címkék* a Video Indexer API-k használata esetén. Például a márka "Azure" címkével ellátott is, vagy "Felhő" kategóriába.

    A hivatkozás URL-cím mező lehet bármilyen hivatkozás egy a Wikipédia-oldalának hivatkozása, például a márka webhelyén.
2. "Márka hozzáadása" gombra, és látni fogja, hogy a márka bővült a **közé tartozik a márka** listája.

### <a name="edit-a-brand"></a>Egy adott márkához szerkesztése

1. Kattintson a szerkeszteni kívánt a márka melletti ceruza ikonra.

    A kategória, leírás vagy hivatkozás URL-cím fórummárka frissítheti. Egy adott márkához neve nem módosítható, mivel a márka egyedinek kell lennie. Ha a márkanév módosítani szeretné, törölje a teljes márka (lásd a következő szakaszban), és hozzon létre egy új márka az új nevet.
2. Kattintson a **frissítése** gombbal frissítheti a márka új információkkal.

### <a name="delete-a-brand"></a>Egy adott márkához törlése

1. Kattintson a törölni kívánt márka mellett a Kuka ikonra.
2. A "Törlés" gombra, és a márka többé nem fog megjelenni a *közé tartozik a márka* listája.

## <a name="exclude-brands-from-the-model"></a>Márkái kizárni a modellből

A **márkái kizárása** rész jelöli a márka, amelyeket nem az, hogy észlelje a Video Indexer szeretne.

### <a name="add-a-brand"></a>Adjon hozzá egy adott márkához

1. Kattintson a "+ Hozzáadás márka".

    Adjon egy nevet (kötelező), a kategória (nem kötelező).
2. Kattintson a "Márka hozzáadása", és látni fogja, hogy hozzáadta-e a márka a *márkái kizárása* listája.

### <a name="edit-a-brand"></a>Egy adott Márkához szerkesztése

1. Kattintson a szerkeszteni kívánt a márka melletti ceruza ikonra.

    Csak akkor frissíthető egy adott márkához kategóriáját. Egy adott márkához neve nem módosítható, mivel a márka egyedinek kell lennie. Ha a márkanév módosítani szeretné, törölje a teljes márka (lásd a következő szakaszban), és hozzon létre egy új márka az új nevet.
2. Kattintson a **frissítése** gombbal frissítheti a márka új információkkal.

### <a name="delete-a-brand"></a>Egy adott Márkához törlése

1. Kattintson a törölni kívánt márka mellett a Kuka ikonra.
2. A "Törlés" gombra, és a márka többé nem fog megjelenni a *márkái kizárása* listája.

## <a name="next-steps"></a>További lépések

[API-k használatával márkái modell testreszabása](customize-brands-model-with-api.md)
