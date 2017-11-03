---
title: "Egy függvény hívása a PowerApps |} Microsoft Docs"
description: "Hozzon létre egy egyéni összekötőt, akkor ezt az összekötőt használó függvény."
services: functions
keywords: "a felhőalapú alkalmazások, szolgáltatások, PowerApps, üzleti folyamatok, a felhő üzleti alkalmazás"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 1e262fde37b68bcfcee3c974deb91bd07965de19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-powerapps"></a>Függvény hívása a PowerAppsből
A [PowerApps](https://powerapps.microsoft.com) platform készült üzleti szakértők nélkül hagyományos alkalmazáskód alkalmazásokat lehet készíteni. Professzionális fejlesztők számára az Azure Functions segítségével PowerApps, képességekkel bővíthetik a technikai részleteket a PowerApps app rendszerépítők védelme során.

Egy alkalmazást ebben a témakörben a szél turbinák karbantartási forgatókönyv alapján hoz létre. Ez a témakör bemutatja, hogyan, amelyet a megadott függvény [létrehozása a következő függvényt egy OpenAPI definíciója](functions-openapi-definition.md). A függvény meghatározza, hogy a szél turbina egy helyreállítási költséghatékony.

![A powerapps segítségével végzett alkalmazás](media/functions-powerapps-scenario/finished-app.png)

A Microsoft Flow ugyanezt a funkciót előhívásának információkért lásd: [meghívni a függvényt a Microsoft Flow](functions-flow-scenario.md).

Ebben a témakörben elsajátíthatja, hogyan:

> [!div class="checklist"]
> * Készítse elő a mintaadatok az Excel programban.
> * Exportálja egy API-definíció.
> * Kapcsolat hozzáadása az API-t.
> * Hozzon létre egy alkalmazást, és vegye fel adatforrásokat.
> * Az alkalmazás adatainak megtekintéséhez vezérlők hozzáadása.
> * A függvény ki és jelenítheti meg az adatok vezérlők hozzáadása.
> * Futtassa az alkalmazást, annak megállapításához, hogy a javítási költséghatékony.

## <a name="prerequisites"></a>Előfeltételek

+ Az aktív [PowerApps fiók](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md) a azonos bejelentkezési hitelesítő adatokat az Azure-fiókként. 
+ Excel-, mert az alkalmazás adatforrásként Excel fog használhatja.
+ Az oktatóanyag befejezése [létrehozása a következő függvényt egy OpenAPI definíciója](functions-openapi-definition.md).


## <a name="prepare-sample-data-in-excel"></a>Mintaadatok létrehozása az Excel programban
Ön kiindulásképpen mintaadatok, amelyekkel az alkalmazás előkészítése. Másolja az alábbi táblázat az Excelbe. 

| Cím      | Szélesség  | Longtitude  | LastServiceDate | MaxOutput | ServiceRequired | EstimatedEffort | InspectionNotes                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| Turbinás 1  | 47.438401 | -121.383767 | 2/23/2017       | 2850      | Igen             | 6               | Ez a jelenség a második ebben a hónapban.       |
| Turbinás 4  | 47.433385 | -121.383767 | 5/8/2017        | 5400      | Igen             | 6               |                                            |
| Turbinás 33 | 47.428229 | -121.404641 | 6/20/2017       | 2800      |                 |                 |                                            |
| Turbinás 34 | 47.463637 | -121.358824 | 2/19/2017       | 2800      | Igen             | 7               |                                            |
| Turbinás 46 | 47.471993 | -121.298949 | 3/2/2017        | 1200      |                 |                 |                                            |
| Turbinás 47 | 47.484059 | -121.311171 | 8/2/2016        | 3350      |                 |                 |                                            |
| Turbinás 55 | 47.438403 | -121.383767 | 10/2/2016       | 2400      | Igen             | 40               | Ez egy várható bizonyos részei van. |

1. Az Excel programban, jelölje ki az adatokat, majd a a **Home** lapra, majd **táblázatként formátum**.

    ![Formázza a tábla](media/functions-powerapps-scenario/format-table.png)

1. Bármely stílusának kiválasztása, és kattintson a **OK**.

1. A kijelölt, a tábla a **tervezési** lapra, adja meg `Turbines` a **táblanév**.

    ![Tábla neve](media/functions-powerapps-scenario/table-name.png)

1. Az Excel-munkafüzet mentéséhez.

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Kapcsolat hozzáadása az API-t
Az egyéni API-t (más néven egyéni összekötő) a powerapps segítségével érhető el, de kapcsolatot kell létesíteni az API-hoz, mielőtt használhatná egy alkalmazás.

1. A [web.powerapps.com](https://web.powerapps.com), kattintson a **kapcsolatok**.

    ![PowerApps kapcsolatok](media/functions-powerapps-scenario/powerapps-connections.png)

1. Kattintson **új kapcsolat**, görgessen le a **turbina javítása** összekötő, és kattintson rá.

    ![Új kapcsolat](media/functions-powerapps-scenario/new-connection.png)

1. Adja meg az API-kulcsot, majd kattintson a **létrehozása**.

    ![Kapcsolat létrehozása](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Ha az alkalmazás megosztja másokkal, minden egyes személy, aki működik, vagy használja az alkalmazást is meg kell adnia az API-kulcsot a API-val való kapcsolódáshoz. Ez a viselkedés a későbbiekben változhat, és ez a témakör, amely megfelelően frissítjük.

## <a name="create-an-app-and-add-data-sources"></a>Hozzon létre egy alkalmazást, és adja hozzá az adatforrások
Most már készen áll az alkalmazás a powerapps segítségével, és adja hozzá az alkalmazáshoz tartozó adatforrások az Excel-adatok és az egyéni API-t.

1. A [web.powerapps.com](https://web.powerapps.com), kattintson a bal oldali ablaktáblában **új alkalmazás**.

1. A **üres alkalmazás**, kattintson a **a telefonos elrendezés**.

    ![Tábla-alkalmazás létrehozása](media/functions-powerapps-scenario/create-phone-app.png)

    Az alkalmazás webes PowerApps Studio megnyitása. A következő kép bemutatja a PowerApps Studio különböző részeit. Ez a rendszerkép van a kész alkalmazás; a középső ablaktáblán először üres képernyő jelenik meg.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) bal oldali navigációs sáv**, minden egyes képernyőn az összes vezérlő hierarchikus nézete látható

    **(2) a középső ablaktáblán**, amely mutatja a képernyő, amelyen dolgozunk

    **(3) a jobb oldali**, amelyen beállíthatja az beállítások például elrendezés és adatforrások

    **(4) tulajdonság** legördülő listából válassza ki, ahol ki kell választania a képletek tulajdonságok

    **(5) a képletsávba**, ahol adja hozzá az alkalmazás viselkedését meghatározó képletek (ahogy Excel)
    
    **(6) a menüszalag**, amelyen vezérlők hozzáadása és látványelemeket testreszabása

1. Adja hozzá az Excel-fájl egy adatforrás.

    1. A jobb oldali ablaktáblában lévő a **adatok** lapra, majd **adatforrás hozzáadása**.

        ![Adatforrás hozzáadása](media/functions-powerapps-scenario/add-data-source.png)

    1. Kattintson a **statikus adatok hozzáadása az alkalmazáshoz**.

        ![Adatforrás hozzáadása](media/functions-powerapps-scenario/add-static-data.png)

        Általában Ehhez olvassa el, és külső forrásból származó adatok írása, de adja hozzá az Excel adatok statikus adatok, mert ez egy minta.

    1. Lépjen az Excel-fájl mentése, válassza ki a **turbinák** tábla, és kattintson a **Connect**.

        ![Adatforrás hozzáadása](media/functions-powerapps-scenario/choose-table.png)

1. Adja hozzá az egyéni API-t egy adatforrás.

    1. Az a **adatok** lapra, majd **adatforrás hozzáadása**.

    1. Kattintson a **turbinás javítási**.

        ![Turbinás javítási összekötő](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Az alkalmazás adatainak megtekintéséhez vezérlők hozzáadása
Most, hogy az adatforrások elérhető az alkalmazásban, hozzáadhat egy olyan képernyőt az alkalmazás szeretné megjeleníteni a turbinás adatokat.

1. Az a **Home** lapra, majd **új képernyő** > **lista képernyő**.

    ![Képernyő](media/functions-powerapps-scenario/list-screen.png)

    PowerApps ad hozzá egy képernyő, amely tartalmazza a *gyűjtemény* elemeket, és más vezérlők, amelyek lehetővé teszik a keresés, rendezés és szűrés megjelenítéséhez.

1. Módosítsa a címsort `Turbine Repair`, és úgy van-e rajta további vezérlők, méretezze át a gyűjtemény.

    ![Cím módosítása és átméretezése gyűjteménye](media/functions-powerapps-scenario/gallery-title.png)

1. A gyűjtemény jelölve, a jobb oldali ablaktáblában kattintson a a **adatok** lapján módosítsa az adatforrást **CustomGallerySample** való **turbinák**.

    ![Változások adatforrása](media/functions-powerapps-scenario/change-data-source.png)

    Az adatkészlet nem képfájl, így most módosítja az elrendezés jobban oszlopnál az adatok tartalmazza. 

1. Továbbra is a jobb oldali ablaktáblán módosítsa **elrendezés** való **cím, alcíme és fő**.

    ![Gyűjteményelem elrendezés módosítása](media/functions-powerapps-scenario/change-layout.png)

1. Utolsó lépésként a jobb oldali ablaktáblán módosítsa a mezőket, amelyeknek jelennek meg a gyűjteményben.

    ![Gyűjteményelem mezők módosítása](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** cím = 

1. A kiválasztott gyűjtemény, állítsa be a **TemplateFill** tulajdonság a következő képlet: `If(ThisItem.IsSelected, Orange, White)`.

    ![Sablon kitöltés képlet](media/functions-powerapps-scenario/formula-fill.png)

    Most már láthatja, melyik galériabeli elem van kiválasztva.

    ![Kijelölt elem](media/functions-powerapps-scenario/selected-item.png)

1. Nem kell az eredeti képernyő az alkalmazásban. A bal oldali ablaktáblán mutasson az egérrel **képernyő1**, kattintson a **...** , és **törlése**.

    ![Képernyő törlése](media/functions-powerapps-scenario/delete-screen.png)

Általában egy éles alkalmazásban kellene tennie formázási sok, de ehhez a forgatókönyvhöz - függvény hívása helyezzük be a fontos része.

## <a name="add-controls-to-call-the-function-and-display-data"></a>A függvény ki és jelenítheti meg az adatok vezérlők hozzáadása
Rendelkezik egy alkalmazást, amely minden turbinás összegzési adatait jeleníti meg, ezért most ideje hozzáadása szabályozza, amely hívja meg a létrehozott függvényt, és a visszaadott adatok jelennek meg. Nevezze el a OpenAPI definícióban; módjában függvény elérheti Ebben az esetben van `TurbineRepair.CalculateCosts()`.

1. Kattintson a menüszalagon a a **beszúrása** lapra, majd **gomb**. Ugyanazon a lapon, kattintson a **címke**

    ![Beszúrás gomb és a felirat](media/functions-powerapps-scenario/insert-controls.png)

1. Húzza a gombra, és a felirat alatt a katalógusban, és méretezése a felirat. 

1. Válassza ki az Akciógomb szövegét, és módosítsa úgy, hogy `Calculate costs`. Az alkalmazás az alábbi képen hasonlóan kell kinéznie.

    ![Alkalmazás gomb](media/functions-powerapps-scenario/move-button-label.png)

1. Kattintson a gombra, és írja be a következő képlet a gomb **OnSelect** tulajdonság.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    A képlet hajt végre, amikor a gombra kattint, és az a következőket hajtja végre a kijelölt gyűjteményelem-e egy **ServiceRequired** értékének `Yes`:

    + Törli a *gyűjtemény* `DetermineRepair` előző hívásokat adatok eltávolítása. A gyűjtemény egy táblázatos változó.

    + A gyűjtemény rendel a függvény által visszaadott adatok `TurbineRepair.CalculateCosts()`. 
    
        Az átadott értékek származnia, a függvény a **EstimatedEffort** és **MaxOutput** mezőket a gyűjtemény a kijelölt elem. Ezek a mezők nem jelennek meg a katalógusban, de továbbra is elérhetőek a képletekben használatára.

1. Jelölje ki a címkét, és adja meg a címke a következő képlet **szöveg** tulajdonság.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    A képlet használja a `First()` függvény az első (és csak) sorát eléréséhez a `DetermineRepair` gyűjtemény. Ezt követően megjeleníti a következő három érték, amely a függvény visszaadja: `message`, `costToFix`, és `revenueOpportunity`. Ezek az értékek nincsenek megadva, az alkalmazás első futtatása előtt.

    A kész alkalmazásról az alábbi képen hasonlóan kell kinéznie.

    ![Befejezett alkalmazás futtatása előtt](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Az alkalmazás futtatása
Rendelkezik egy teljes app! Most már idő futtatáshoz, és tekintse meg a függvény meghívja a működés közben.

1. A PowerApps Studio jobb felső sarkában kattintson a Futtatás gombra: ![Futtassa az alkalmazást gomb](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Válassza ki a értékű turbina `Yes` a **ServiceRequired**, majd kattintson a **költségek kiszámításához** gombra. Az alábbi képen hasonlóan eredményt kell megjelennie.

    ![A powerapps segítségével végzett alkalmazás](media/functions-powerapps-scenario/finished-app.png)

1. Próbálja meg a más turbinák, hogy mi van a függvény által visszaadott minden alkalommal, amikor.

## <a name="next-steps"></a>Következő lépések
Ez a témakör megtanulta, hogyan:

> [!div class="checklist"]
> * Készítse elő a mintaadatok az Excel programban.
> * Exportálja egy API-definíció.
> * Kapcsolat hozzáadása az API-t.
> * Hozzon létre egy alkalmazást, és vegye fel adatforrásokat.
> * Az alkalmazás adatainak megtekintéséhez vezérlők hozzáadása.
> * A függvény ki és jelenítheti meg az adatok vezérlők hozzáadása
> * Futtassa az alkalmazást, annak megállapításához, hogy a javítási költséghatékony.

PowerApps kapcsolatos további információkért lásd: [Bevezetés a PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Használja az Azure Functions más érdekes forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [meghívni a függvényt a Microsoft Flow](functions-flow-scenario.md) és [hozzon létre egy függvényt, amely az Azure Logic Apps](functions-twitter-email.md).