---
title: Alkalmazás irányítópult LUIS alkalmazásokhoz |} Microsoft Docs
description: Ismerje meg az alkalmazás irányítópult, a feladatkonfigurációkat jelentéskészítési eszköz, amely lehetővé teszi az alkalmazások egyetlen pillantással figyelése.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: c7ef38e2f2edaf795d3d76706afd4aa09b3b6959
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110048"
---
# <a name="application-dashboard"></a>Alkalmazás irányítópult
Alkalmazás irányítópult lehetővé teszi, hogy az alkalmazás egyetlen pillantással figyelheti. A **irányítópult** jelenít meg, amikor egy alkalmazás nyissa meg az alkalmazás neve kattintva **saját alkalmazások** lapon válassza ki azt **irányítópult** az ablak tetején. 

> [!CAUTION]
> Ha szeretne a legfrissebb metrikák LUIS, kell:
> * Használja a LUIS [végpontkulcs](luis-how-to-azure-subscription.md) létrehozása az Azure-ban
> * Végpont kulcs használata LUIS, többek között a LUIS összes végpont kérelem [API](https://aka.ms/luis-endpoint-apis) és botot
> * Minden egyes LUIS app másik végpont kulcs használata. Ne használjon egyetlen végpontkulcs az összes alkalmazáshoz. A végpont kulcs nyomon kulcs szinten, nem az alkalmazás szintjén van.  

A **irányítópult** lap áttekintést nyújt a LUIS alkalmazás, beleértve az aktuális modell állapot, valamint [végpont](luis-glossary.md#endpoint) használat idővel. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Alkalmazás állapota
Az irányítópult megjeleníti az alkalmazás képzési és a közzétételi állapota, beleértve a dátum és idő, amikor az alkalmazás legutóbbi betanítása és közzé.  

![Irányítópult - alkalmazás állapota](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Modell adatok statisztikák
Az irányítópult leképezések, entitások és az alkalmazás létező címkézett utterances teljes számát jeleníti meg. 

![Az alkalmazásadatok statisztikák](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Végpont találatok
Az irányítópult megjelenítése a teljes végpont találatok, amely a LUIS alkalmazást kap, és lehetővé teszi, hogy is megjelenítsen találatok belül, hogy adja meg. Megjelenik a találatok száma érték a összege, azaz használó végpont a találatok egy [végpontkulcs](./luis-concept-keys.md#endpoint-key) végpont találatok használó és egy [szerzői műveletek kulcs](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Végpont találatok](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> A legújabb endpoint találatok száma szerepel az Azure-portálon a LUIS szolgáltatás áttekintése. 
 
### <a name="total-endpoint-hits"></a>Teljes endpoint találatok
Az alkalmazás létrehozása az aktuális dátummal záródó részéből óta az alkalmazáshoz kapott végpont találatok összesített száma

### <a name="endpoint-hits-per-period"></a>Végpont találatok időszakonként
Elmúlt időszakban kapott találatok száma naponta jelenik meg. A kezdő és záró dátum közötti pontok határoz meg az ebben az időszakban alá tartozó nap. Minden pont megjelenítéséhez vigye az egérmutatót a találatok száma naponta, a határidőn belül. 

A diagram megtekintése időszak kiválasztása:
 
1. Kattintson a **további beállításokat** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) az időszakok lista eléréséhez. Kiválaszthatja a kezdve egy hét másolatot egy évig időszakok. 

    ![Végpont találatok időszakonként](./media/luis-how-to-use-dashboard/timerange.png)

2. Válassza ki az adott időszakban a listából, és majd kattintson a Vissza gombra ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) a diagram megjelenítése.

### <a name="key-usage"></a>Kulcshasználat
A végpont kulcsát a felhasznált találatok száma. Végpont kulcsokkal kapcsolatos további információkért lásd: [LUIS kulcsokat](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Leképezési lebontása
A **leképezés lebontása** leképezések címkézett utterances vagy a végpont találatok részletes információkat jelenít meg. Az összegző diagram megjeleníti minden egyes leképezés relatív fontosságát az alkalmazásban. Ha az egérmutatóval rámutat szelet, megjelenik az leképezési nevét és azt jelenti, hogy az címkézett utterances/végpont találatok teljes száma. 

![Leképezési lebontása](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Szabályozásához, hogy a lebontása alapul címkézett utterances vagy a végpont találatok:

1. Kattintson a **további beállításokat** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) a listában, ahogy az alábbi képen eléréséhez:

    ![Leképezési lebontása listája](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Válasszon ki egy értéket a listából, és majd kattintson a Vissza gombra ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) a diagram megjelenítése.

## <a name="entity-breakdown"></a>Entitás lebontása
Az irányítópult entitások alapján címkézett utterances vagy a végpont találatok részletes információkat jelenít meg. Az összegző diagram megjeleníti minden egyes entitás relatív fontosságát az alkalmazásban. Ha az egérmutatóval rámutat szelet, megjelenik az entitás nevét és a címkézett utterances/végpont találatok százalékos. 

![Entitás lebontása](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Szabályozásához, hogy a lebontása alapul címkézett utterances vagy a végpont találatok:

1. Kattintson a **további beállításokat** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) a listában, ahogy az alábbi képen eléréséhez:

    ![Entitás lebontása listájában](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Válasszon ki egy értéket a listából, és majd kattintson a Vissza gombra ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Ennek megfelelően megjelenítése a diagramterület.
