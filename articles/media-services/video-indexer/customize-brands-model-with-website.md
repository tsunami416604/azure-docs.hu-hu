---
title: Márkák modell testreszabása a Video Indexer webhelyével
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a Brands modellt a Video Indexer webhelyével.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128043"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Márkák modell testreszabása a Video Indexer webhelyével

A Video Indexer támogatja a beszéd- és vizuális szövegből származó márkaészlelést a video- és hangtartalmak indexelése és újraindexelése során. A márkaészlelési funkció azonosítja a Bing márkaadatbázisa által javasolt termékek, szolgáltatások és vállalatok említését. Ha például a Microsoftot megemlítik a video- vagy hangtartalomban, vagy ha vizuális szövegben jelenik meg egy videóban, a Video Indexer márkaként észleli azt a tartalomban.

Az egyéni Márkák modell lehetővé teszi, hogy:

- akkor válassza a lehetőséget, ha azt szeretné, hogy a Video Indexer észlelje a márkákat a Bing márkák adatbázisából.
- jelölje be, ha azt szeretné, hogy a Video Indexer bizonyos márkákat kizárjon az észlelésből (lényegében a márkák megtagadási listájának létrehozása).
- akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a Video Indexer olyan márkákat vegyen fel, amelyek nem szerepelnek a Modell ben a Bing márkaadatbázisában (lényegében a márkák elfogadható listájának létrehozása).

A részletes áttekintést ebben az [áttekintésben](customize-brands-model-overview.md)találja.

A Video Indexer webhely segítségével létrehozhat, használhat és szerkeszthet egy videóban észlelt egyéni Márkák modelleket, a hogy az ebben a témakörben is szerepel. Az API-t is használhatja, amárkák [testreszabása modell API-k használatával](customize-brands-model-with-api.md)című modellben leírtak szerint.

## <a name="edit-brands-model-settings"></a>Márkák modellbeállításainak szerkesztése

Beállíthatja, hogy a Bing márkák adatbázisából származó márkákat észlelje-e vagy sem. A beállítás beállításához a Márkák modell beállításait kell szerkesztenie. Kövesse az alábbi lépéseket:

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. A fiókban lévő modell testreszabásához kattintson a lap jobb felső sarkában található **Tartalommodell testreszabása** gombra.

   ![Tartalommodell testreszabása a Video Indexelőben](./media/content-model-customization/content-model-customization.png)

3. A márkák szerkesztéséhez válassza a **Márkák** lapot.

    ![Márkák modell testreszabása a Video Indexerben](./media/customize-brand-model/customize-brand-model.png)

4. Ha azt szeretné, hogy a Video Indexer észlelje a Bing által javasolt márkákat, jelölje be a **Bing által javasolt márkák** megjelenítése jelölőnégyzetet, ha nem jelöli be.

## <a name="include-brands-in-the-model"></a>Márkák felvétele a modellbe

A **Márkák belefoglalása** szakasz olyan egyéni márkákat jelöl, amelyeket a Video Indexer észlelni szeretne, még akkor is, ha a Bing nem javasolta őket.  

### <a name="add-a-brand-to-include-list"></a>Márka hozzáadása a lista felvételéhez

1. Válassza **a + Márka hozzáadása**lehetőséget.

    ![Márkák modell testreszabása a Video Indexerben](./media/customize-brand-model/add-brand.png)

    Adjon meg egy nevet (kötelező), kategória (nem kötelező), leírás (nem kötelező) és hivatkozási URL-t (nem kötelező).
    A kategóriamező célja, hogy segítsen megjelölni a márkákat. Ez a mező a márka *címkéiként* jelenik meg a Video Indexer API-k használatakor. Például az "Azure" márka címkézhető vagy "Felhő" kategóriába sorolható.

    A referencia URL-mező lehet a márka bármely referenciaweboldala (például a Wikipédia oldalára mutató hivatkozás).

2. Válassza **a Márka hozzáadása lehetőséget,** és látni fogja, hogy a márka fel került a Márkák **felvétele** listára.

### <a name="edit-a-brand-on-the-include-list"></a>Márka szerkesztése a include listán

1. Jelölje ki a szerkesztni kívánt márka melletti ceruza ikont.

    Frissítheti egy márka kategóriáját, leírását vagy hivatkozási URL-címét. Nem változtathatja meg egy márka nevét, mert a márkák neve egyedi. Ha meg kell változtatnia a márkanevet, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.

2. A **Frissítés gombra** kattintva frissítheti a márkát az új információkkal.

### <a name="delete-a-brand-on-the-include-list"></a>Márka törlése a belefoglalási listáról

1. Válassza a törölni kívánt márka melletti kuka ikont.
2. Válassza a **Törlés** lehetőséget, és a márka a továbbiakban nem jelenik meg a *Márkák kalkuláltak* listában.

## <a name="exclude-brands-from-the-model"></a>Márkák kizárása a modellből

A **Márkák kizárása** szakasz azokat a márkákat jelöli, amelyeket nem szeretne, hogy a Video Indexer észleljen.

### <a name="add-a-brand-to-exclude-list"></a>Márka hozzáadása a lista kizárásához

1. Válassza a **+ Márka hozzáadása lehetőséget.**

    Adjon meg egy nevet (kötelező), kategóriát (nem kötelező).

2. Válaszd a **Márka hozzáadása lehetőséget,** és látni fogod, hogy a márka felkerült a *Márkák kizárása* listára.

### <a name="edit-a-brand-on-the-exclude-list"></a>Márka szerkesztése a kizárási listán

1. Jelölje ki a szerkesztni kívánt márka melletti ceruza ikont.

    Csak egy márka kategóriáját frissítheti. Nem változtathatja meg egy márka nevét, mert a márkák neve egyedi. Ha meg kell változtatnia a márkanevet, törölje a teljes márkát (lásd a következő szakaszt), és hozzon létre egy új márkát az új névvel.

2. A **Frissítés gombra** kattintva frissítheti a márkát az új információkkal.

### <a name="delete-a-brand-on-the-exclude-list"></a>Márka törlése a kizárási listáról

1. Válassza a törölni kívánt márka melletti kuka ikont.
2. Válassza a **Törlés** lehetőséget, és a márka a továbbiakban nem jelenik meg a *Márkák kizárása* listában.

## <a name="next-steps"></a>További lépések

[Márkák modell testreszabása API-k használatával](customize-brands-model-with-api.md)
