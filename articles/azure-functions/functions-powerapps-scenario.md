---
title: A függvény hívása a Powerappsből |} A Microsoft Docs
description: Hozzon létre egy egyéni összekötőt, majd ezt az összekötőt használó függvény hívása.
services: functions
keywords: felhőalapú alkalmazások, szolgáltatások, a PowerApps, üzleti folyamatok, felhőalapú üzleti alkalmazás
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 55de3cd8830834a2af512661d5389952d927ef9f
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094315"
---
# <a name="call-a-function-from-powerapps"></a>Függvény hívása a PowerAppsből
A [PowerApps](https://powerapps.microsoft.com) platform benne hagyományos alkalmazáskódot nélküli alkalmazásokat hozhat létre üzleti szakértők lett tervezve. Professzionális fejlesztők számára az Azure Functions használatával kiterjesztheti a PowerApps képességeit a technikai részleteket a PowerApps alkalmazáskészítők sikerei védelme során.

Ebben a témakörben egy karbantartási forgatókönyvben a szél turbina alapuló valamely alkalmazás készítése. Ez a témakör bemutatja, hogyan hívhat meg a függvényt, amelyet a megadott [függvény OpenAPI definíció létrehozása](functions-openapi-definition.md). A függvény meghatározza, hogy egy szélturbina sürgősségi javítása költséghatékony-e.

![Kész alkalmazás a powerapps szolgáltatásban](media/functions-powerapps-scenario/finished-app.png)

Ugyanannak a függvénynek meghívása a Microsoft Flow további információkért lásd: [függvény hívása a Microsoft Flow](functions-flow-scenario.md).

Ebben a témakörben megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Készítse elő a mintaadatokat az Excelben.
> * API-definíció exportálása.
> * Kapcsolat hozzáadása az API-hoz.
> * Hozzon létre egy alkalmazást, és adja hozzá az adatforrásokat.
> * A vezérlőelemek adatok megtekintése az alkalmazásban.
> * A vezérlőelemek a függvény meghívása és az adatok megjelenítéséhez.
> * Futtassa az alkalmazást, annak megállapításához, hogy egy javítási költséghatékony.

## <a name="prerequisites"></a>Előfeltételek

+ Az aktív [PowerApps-fiók](https://docs.microsoft.com/en-us/powerapps/maker/signup-for-powerapps) az Azure-fiók azonos bejelentkezési hitelesítő adatait. 
+ Az Excel és a [minta Excel-fájl](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) , amely az alkalmazás adatforrásaként használandó.
+ Az oktatóanyag elvégzéséhez [függvény OpenAPI definíció létrehozása](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Vegyen fel egy kapcsolatot az API-hoz
Az egyéni API-t (más néven egy egyéni összekötő) érhető el a powerapps szolgáltatásban, de kapcsolatot kell létesíteni az API-hoz, az alkalmazás használata előtt.

1. A [web.powerapps.com](https://web.powerapps.com), kattintson a **kapcsolatok**.

    ![A PowerApps-kapcsolatok](media/functions-powerapps-scenario/powerapps-connections.png)

1. Kattintson a **új kapcsolat**, görgessen le a **turbina javításához** összekötő, és kattintson rá.

    ![Új kapcsolat](media/functions-powerapps-scenario/new-connection.png)

1. Adja meg az API-kulcsot, majd kattintson a **létrehozás**.

    ![Kapcsolat létrehozása](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Ha az alkalmazás megosztja másokkal, minden egyes személy, aki a működik, vagy használja az alkalmazást is meg kell adnia az API-kulcsot az API-hoz való kapcsolódáshoz. Ez a viselkedés a későbbiekben változhatnak, és frissítjük, hogy ez a témakör a változás tükrözése érdekében.

## <a name="create-an-app-and-add-data-sources"></a>Hozzon létre egy alkalmazást, és adatforrások hozzáadása
Most már készen áll az alkalmazás létrehozása a powerapps szolgáltatásban, és adja hozzá az Excel-adatokat, és az egyéni API-t az alkalmazás-adatforrások.

1. A [web.powerapps.com](https://web.powerapps.com) helyen válassza a **Kiindulás üres alkalmazásból** > ![Telefonos alkalmazás ikonja](media/functions-powerapps-scenario/icon-phone-app.png) (telefon) > **Alkalmazás létrehozása** lehetőséget.

    ![Üres folyamat - telefonos alkalmazás indítása](media/functions-powerapps-scenario/create-phone-app.png)

    Az alkalmazás megnyílik a PowerApps Studio webes. Az alábbi képen látható a PowerApps Studio különböző részeit.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) bal oldali navigációs sávban**, amelyben az egyes képernyőkre összes vezérlőelem hierarchikus nézetét láthatja

    **(B) a középső ablaktáblán**, amely mutatja a képernyőt, hogy, amelyen dolgozik

    **(C) jobb ablaktáblában**, ahol beállításokat adhat meg például az elrendezést vagy az adatforrásokat

    **(D) tulajdonság** legördülő listából válassza ki, itt választhatja ki a tulajdonságokat, amelyeket a képletek vonatkoznak

    **(E) a szerkesztőlécen**, Itt veheti fel az alkalmazások működését képleteket (ahogyan az Excelben is)
    
    **(F) menüszalag**, amelyben vehet fel vezérlőket és szabhatja testre a tervelemeket

1. Adja hozzá az Excel-fájlt adatforrásként.

    Az importálni kívánt adatok a következőhöz hasonlóan néz ki:

    ![Excel-adatok importálása](media/functions-powerapps-scenario/excel-table.png)

    1. Az alkalmazásvásznon válassza a **Csatlakozás adatokhoz** lehetőséget.

    1. Az a **adatok** panelen, kattintson a **statikus adatok felvétele az alkalmazásba**.

        ![Adatforrás hozzáadása](media/functions-powerapps-scenario/add-static-data.png)

        Általában akkor olvassa el, és külső forrásból származó adatok írása, de adja hozzá az Excel-adatok statikus adatok, mert ez csupán egy példa.

    1. Keresse meg a mentett Excel-fájlt, jelölje be a **turbina** táblát, és kattintson a **Connect**.

        ![Adatforrás hozzáadása](media/functions-powerapps-scenario/choose-table.png)


1. Adja hozzá az egyéni API-k adatforrásként.

    1. Az a **adatok** panelen, kattintson a **adatforrás hozzáadása**.

    1. Kattintson a **turbina javításához**.

        ![Sürgősségi javítása összekötő](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Az alkalmazás adatainak megtekintéséhez vezérlőelemek hozzáadása
Most, hogy az adatforrások érhetők el az alkalmazást, akkor adjon hozzá egy képernyőt az alkalmazáshoz, megtekintheti a turbina adatokat.

1. Az a **kezdőlap** lapra, majd **új képernyő** > **listaképernyő**.

    ![Listaképernyő](media/functions-powerapps-scenario/list-screen.png)

    A PowerApps ad hozzá egy képernyőt, amely tartalmazza a *katalógus* elemek és más vezérlőket, amelyekkel a keresés, rendezés és szűrés megjelenítéséhez.

1. A címsor módosítása `Turbine Repair`, és méretezze át a katalógust, hogy van-e további vezérlők, annak alapján.

    ![Módosítsa a címet, és méretezze át a katalógusban](media/functions-powerapps-scenario/gallery-title.png)

1. A gyűjtemény kijelölésekor, a jobb oldali ablaktáblában található **tulajdonságok**, kattintson a **CustomGallerySample**.

    ![Adatforrás módosítása](media/functions-powerapps-scenario/change-data-source.png)

1. Az a **adatok** panelen kattintson **turbina** a listából.

    ![Adatforrás kiválasztása](media/functions-powerapps-scenario/select-data-source.png)

    Az adatkészlet képet, így most módosítja a elrendezését jobban az adatok nem tartalmaz. 

1. Még mindig a **adatok** panelen, lépjen abba a **elrendezés** való **cím, alcím és törzs**.

    ![Katalógus elrendezésének módosítása](media/functions-powerapps-scenario/change-layout.png)

1. Utolsó lépéseként a **adatok** panelen, megváltoztatja a katalógusban megjelenő mezőket.

    ![Katalógus mezőinek módosítása](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** = címe 

1. A gyűjtemény kijelölésekor, állítsa be a **TemplateFill** tulajdonságát a következő képletre: `If(ThisItem.IsSelected, Orange, White)`.

    ![Sablon kitöltése képlet](media/functions-powerapps-scenario/formula-fill.png)

    Most Önön könnyebben látható, mely gyűjteményelem van kiválasztva.

    ![Kijelölt elem](media/functions-powerapps-scenario/selected-item.png)

1. Nem kell az eredeti képernyő az alkalmazásban. A bal oldali ablaktáblán mutasson **Screen1**, kattintson a **...** , és **törlése**.

    ![Képernyő törlése](media/functions-powerapps-scenario/delete-screen.png)

1. Kattintson a **fájl**, és nevezze el az alkalmazást. Kattintson a **mentése** a bal oldali menüben, majd kattintson **mentése** a jobb alsó sarokban.

Sok egyéb formázási általában kellene tennie egy éles alkalmazásban, de a forgatókönyv – a függvény hívása következik be a fontos szerepet játszanak.

## <a name="add-controls-to-call-the-function-and-display-data"></a>A vezérlőelemek használatával meghívja a függvényt, és az adatok megjelenítése
Van egy alkalmazása, amely minden egyes turbina összegzési adatait jeleníti meg, tehát most hozzá kell azt szabályozza, amely meghívja a függvényt hozott létre, és a visszaadott adatok megjelenítése. A függvény az OpenAPI-definícióban; neki módja alapján fér hozzá Ebben az esetben van `TurbineRepair.CalculateCosts()`.

1. A menüszalagon található meg a **beszúrása** lapra, majd **gomb**. Ugyanazon a lapon, kattintson a **felirat**

    ![Helyezze be a gombra, és a felirat](media/functions-powerapps-scenario/insert-controls.png)

1. Húzza a gombot, és a címkét a katalógus alá, és méretezze át a címkét. 

1. Válassza ki a gomb szövegét, és módosítsa a következőre `Calculate costs`. Az alkalmazás a következő képhez hasonlóan kell kinéznie.

    ![Alkalmazás gomb](media/functions-powerapps-scenario/move-button-label.png)

1. Válassza ki a gombot, és adja meg a következő képletet a gomb **OnSelect** tulajdonság.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Ez a képlet hajt végre, amikor a gombra kattint, és a következőket hajtja végre a kijelölt Katalóguselem-e egy **ServiceRequired** értékét `Yes`:

    + Törli a *gyűjtemény* `DetermineRepair` adatok eltávolítása az előző hívás. Egy gyűjtemény egy táblázatos változóban.

    + A gyűjtemény hozzárendeli a függvény által visszaadott adatok `TurbineRepair.CalculateCosts()`. 
    
        Átadott értékek a függvény származnak az **EstimatedEffort** és **MaxOutput** az elemet a katalógusban kiválasztott mezőket. Ezek a mezők nem jelennek meg a katalógusban, de továbbra is elérhető a képletekben történő felhasználásához.

1. Válassza ki a címkét, és adja meg a következő képletet a címke **szöveg** tulajdonság.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Ez a képlet a `First()` funkció eléréséhez az első (és egyetlen) sorát a `DetermineRepair` gyűjtemény. A három érték, amely visszaadja a függvény ezután megjeleníti: `message`, `costToFix`, és `revenueOpportunity`. Ezek az értékek nincsenek megadva, az alkalmazás első futtatása előtt.

    A kész alkalmazás a következő képhez hasonlóan kell kinéznie.

    ![Kész alkalmazás futtatása előtt](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Az alkalmazás futtatása
A teljes alkalmazás rendelkezik! Most, a futtatásához, majd tekintse meg a függvényt működés hívások ideje.

1. A PowerApps Studio jobb felső sarkában kattintson a Futtatás gombra: ![Alkalmazás futtatása gombra](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Válassza ki a turbina értékkel `Yes` a **ServiceRequired**, majd kattintson a **költségek kiszámítása** gombra. Egy eredményt a következő képhez hasonlóan kell megjelennie.

    ![Kész alkalmazás a powerapps szolgáltatásban](media/functions-powerapps-scenario/finished-app.png)

1. Próbálja ki a megtekintéséhez, mi az a függvény által visszaadott minden alkalommal, amikor az egyéb turbina.

## <a name="next-steps"></a>További lépések
Ez a témakör a segítségével megtanulta, hogyan:

> [!div class="checklist"]
> * Készítse elő a mintaadatokat az Excelben.
> * API-definíció exportálása.
> * Kapcsolat hozzáadása az API-hoz.
> * Hozzon létre egy alkalmazást, és adja hozzá az adatforrásokat.
> * A vezérlőelemek adatok megtekintése az alkalmazásban.
> * A vezérlőelemek használatával meghívja a függvényt, és az adatok megjelenítése
> * Futtassa az alkalmazást, annak megállapításához, hogy egy javítási költséghatékony.

A PowerApps kapcsolatos további információkért lásd: [Bevezetés a PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Az Azure Functions használó más érdekes forgatókönyvek kapcsolatos további információkért lásd: [függvény hívása a Microsoft Flow](functions-flow-scenario.md) és [Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).
