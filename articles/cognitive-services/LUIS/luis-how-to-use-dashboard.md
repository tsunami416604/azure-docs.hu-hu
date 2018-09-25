---
title: Alkalmazás irányítópult, a LUIS-alkalmazások |} A Microsoft Docs
description: Ismerje meg az irányítópult, egy teszi jelentéskészítési eszköz, amely lehetővé teszi, hogy egyetlen pillantással alkalmazások figyelése.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: diberry
ms.openlocfilehash: cab07a994f2bdfb7ef8491adbb482c1902e4d4cb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031985"
---
# <a name="application-dashboard"></a>Alkalmazás irányítópult
Alkalmazás irányítópult lehetővé teszi az alkalmazás egyetlen pillantással figyelésére. A **irányítópult** jelenít meg, amikor megnyit egy alkalmazást az alkalmazás neve kattintva **saját alkalmazások** lapon válassza ki **irányítópult** az ablak tetején. 

> [!CAUTION]
> Ha azt szeretné, a legfrissebb metrikák a LUIS, kell tennie:
> * A LUIS használatát [végponti kulcs](luis-how-to-azure-subscription.md) létrehozása az Azure-ban
> * Végpont kulcs használata LUIS, LUIS többek között az összes endpoint kérelmek [API](https://aka.ms/luis-endpoint-apis) és robotot
> * A LUIS alkalmazás másik végponti kulcs használata. Ne használjon egyetlen végpontkulcsának összes alkalmazáshoz. A végpont kulcs rendszer nyomon követi a legfontosabb szintjén, az alkalmazás szintjén nem.  

A **irányítópult** oldal áttekintést nyújt a LUIS-alkalmazás, beleértve az aktuális modell állapot, valamint [végpont](luis-glossary.md#endpoint) használat az idő függvényében. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Alkalmazás állapota
Az irányítópult megjeleníti az alkalmazás-képzési és közzétételi állapota, beleértve a dátum és idő, amikor az alkalmazás történt legutóbbi betanított és a közzétett.  

![Irányítópult - alkalmazás állapota](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Modell adatok statisztika
Az irányítópult szándék fog vonatkozni, az entitások és az alkalmazás meglévő címkézett utterances teljes számát jeleníti meg. 

![Az alkalmazásadatok statisztika](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Végpont találatok
Az irányítópult megjeleníti az összes endpoint találatok, amely a LUIS alkalmazás kap, és lehetővé teszi, hogy a megjelenítési eléri egy időtartamon belül, amikor adja meg. Megjelenik a találatok teljes száma, az összeg, használó végpont a találatok egy [végponti kulcs](./luis-concept-keys.md#endpoint-key) és végpontot használó eléri egy [szerzői műveletek kulcs](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Végpont találatok](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> A legfrissebb végpont találatok száma az intelligens HANGFELISMERÉSI szolgáltatás áttekintése az Azure Portalon található. 
 
### <a name="total-endpoint-hits"></a>Teljes endpoint találatok
Alkalmazás létrehozása az aktuális dátummal záródó részéből. óta az alkalmazáshoz kapott végpont-találatok teljes száma.

### <a name="endpoint-hits-per-period"></a>Végpont találatok időszakonként
Egy elmúlt időszakban kapott találatok száma naponta jelenik meg. A pontokat a kezdő és záró dátuma között az ebben az időszakban alá tartozó nap képviseli. Minden pont megtekintéséhez vigye az egérmutatót a találatok száma naponta időszakon belül. 

A diagram megtekintése időszak kiválasztása:
 
1. Kattintson a **további beállítás** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) időszakok lista eléréséhez. Időszakok és a egy hét másolatot egy évig is választhat. 

    ![Végpont találatok időszakonként](./media/luis-how-to-use-dashboard/timerange.png)

2. Egy időszakot válasszon a listából, és kattintson a Vissza nyíl ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) a diagram megjelenítése.

### <a name="key-usage"></a>Kulcshasználat
Az alkalmazás végponti kulcs üzenetdíjat találatok száma. Végpont kulcsokkal kapcsolatos további információkért lásd: [kulcsok a LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Leképezési lebontása
A **szándékot lebontása** jelenít meg információkat a leképezések címkézett utterances vagy a végpont a találatok alapján. Az összegző diagram megjeleníti minden egyes szándékot relatív fontosságát az alkalmazásban. Ha az egérmutatót egy szeletet, láthatja a leképezés neve és a százalékos aránya, amely jelöl, címkézett kimondott szöveg/végpont találatok teljes száma. 

![Leképezési lebontása](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Szabályozhatja, hogy táblázat összefoglalja alapul címkézett utterances vagy a végpont a találatok:

1. Kattintson a **további beállítás** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) eléréséhez a listában, ahogyan az alábbi képen is látható:

    ![Leképezési lebontása listája](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Válasszon ki egy értéket a listából, és kattintson a Vissza nyíl ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) a diagram megjelenítése.

## <a name="entity-breakdown"></a>Entitás lebontása
Az irányítópult egy bontása entitások címkézett utterances vagy a végpont a találatok alapján jeleníti meg. Az összegző diagram megjeleníti a minden entitás relatív fontosságát az alkalmazásban. Ha az egérmutatót egy szeletet, megjelenik az entitás nevét és a címkézett kimondott szöveg/végpont találatok százalékos. 

![Entitás lebontása](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Szabályozhatja, hogy táblázat összefoglalja alapul címkézett utterances vagy a végpont a találatok:

1. Kattintson a **további beállítás** ![további beállítások gomb](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) eléréséhez a listában, ahogyan az alábbi képen is látható:

    ![Entitáslista lebontása](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Válasszon ki egy értéket a listából, és kattintson a Vissza nyíl ![Vissza nyíl](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) a diagram ennek megfelelően megjelenítéséhez.
