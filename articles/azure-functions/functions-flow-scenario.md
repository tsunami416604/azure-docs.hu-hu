---
title: Egy Azure függvény hívása a Microsoft Flow |} Microsoft Docs
description: Hozzon létre egy egyéni összekötőt, akkor ezt az összekötőt használó függvény.
services: functions
keywords: a felhőalapú alkalmazások, szolgáltatások, a Microsoft Flow, üzleti folyamatok, a felhő üzleti alkalmazás
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 57d80ad836a16b8821ba0cce42c822728c654dfd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234801"
---
# <a name="call-a-function-from-microsoft-flow"></a>Függvény meghívása a Microsoft Flow-ból

[Microsoft Flow](https://flow.microsoft.com/) megkönnyíti a munkafolyamatok és a kedvenc alkalmazások és szolgáltatások közötti üzleti folyamatok automatizálásához. Professzionális fejlesztők számára az Azure Functions segítségével Microsoft Flow képességekkel bővíthetik technikai részleteket a folyamat szerkesztők védelme során.

A folyamat ebben a témakörben a szél turbinák karbantartási forgatókönyv alapján hoz létre. Ez a témakör bemutatja, hogyan, amelyet a megadott függvény [létrehozása a következő függvényt egy OpenAPI definíciója](functions-openapi-definition.md). A függvény meghatározza, hogy a szél turbina egy helyreállítási költséghatékony. Ha költséghatékony, a folyamat küld egy e-mailek javasoljuk a javítás.

Információ a PowerApps előhívásának ugyanezt a funkciót: [meghívni a függvényt a PowerApps](functions-powerapps-scenario.md).

Ebben a témakörben elsajátíthatja, hogyan:

> [!div class="checklist"]
> * Lista létrehozása a SharePoint.
> * Exportálja egy API-definíció.
> * Kapcsolat hozzáadása az API-t.
> * Hozzon létre egy folyamatot, az e-mailek küldése, ha egy javítási költséghatékony.
> * A folyamat fut.

## <a name="prerequisites"></a>Előfeltételek

+ Az aktív [Microsoft Flow fiók](https://flow.microsoft.com/documentation/sign-up-sign-in/) a azonos bejelentkezési hitelesítő adatokat az Azure-fiókként. 
+ A SharePoint, amely ebben az adatfolyamban adatforrásként használja. Regisztráljon [az Office 365 próbaverziójával](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Ha még nem rendelkezik a SharePoint.
+ Az oktatóanyag befejezése [létrehozása a következő függvényt egy OpenAPI definíciója](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Egy SharePoint-lista létrehozása
A folyamat az adatforrásként használt lista létrehozásával kiindulásképpen meg. A lista a következő oszlopot tartalmaz.

| Lista oszlop     | Adattípus           | Megjegyzések                                    |
|-----------------|---------------------|------------------------------------------|
| **Cím**           | Egysoros szövegmező | A turbinás neve                      |
| **LastServiceDate** | Dátum                |                                          |
| **MaxOutput**       | Szám              | A turbinás kWh-ban kimenete            |
| **ServiceRequired** | Igen/nem              |                                          |
| **EstimatedEffort** | Szám              | A javítási becsült ideje (óra) |

1. A SharePoint-webhely kattintson vagy koppintson **új**, majd **lista**.

    ![Új SharePoint-lista létrehozása](./media/functions-flow-scenario/new-list.png)

2. Adja meg a nevét `Turbines`, majd kattintson vagy koppintson **létrehozása**.

    ![Új lista nevének megadása](./media/functions-flow-scenario/create-list.png)

    A **turbinák** lista alapértelmezett jön létre, a **cím** mező.

    ![Turbinák listája](./media/functions-flow-scenario/initial-list.png)

3. Kattintson vagy koppintson ![új elem ikonja](./media/functions-flow-scenario/icon-new.png) majd **dátum**.

    ![Egysoros szövegmező hozzáadása](./media/functions-flow-scenario/add-column.png)

4. Adja meg a nevét `LastServiceDate`, majd kattintson vagy koppintson **létrehozása**.

    ![LastServiceDate oszlop létrehozása](./media/functions-flow-scenario/date-column.png)

5. Ismételje meg az utolsó két lépést a többi három oszlop a listában:

    1. **Szám** > "MaxOutput"

    2. **Igen/nem** > "ServiceRequired"

    3. **Szám** > "EstimatedEffort"

Ennyi az egész most - rendelkeznie kell, hogy az alábbi illusztráción láthatóhoz hasonló listája üres. Adatok hozzáadása a listához a folyamat létrehozása után.

![Üres lista](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Kapcsolat hozzáadása az API-t
Az egyéni API-t (más néven egyéni összekötő) a Microsoft Flow érhető el, de kapcsolatot kell létesíteni az API-hoz, egy folyamatában használatba vétele előtt.

1. A [flow.microsoft.com](https://flow.microsoft.com), kattintson (a jobb felső) fogaskerék ikonra, majd kattintson a **kapcsolatok**.

    ![Adatfolyam-kapcsolatok](media/functions-flow-scenario/flow-connections.png)

1. Kattintson **kapcsolat létrehozása**, görgessen le a **turbinás javítási** összekötő, és kattintson rá.

    ![Kapcsolat létrehozása](media/functions-flow-scenario/create-connection.png)

1. Adja meg az API-kulcsot, majd kattintson a **hozható létre kapcsolat**.

    ![Adja meg az API-kulcs és létrehozása](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Ha a folyamat megosztja másokkal, minden egyes személy, aki működéséről, vagy használja a folyamat is meg kell adnia az API-kulcsot a API-val való kapcsolódáshoz. Ez a viselkedés a későbbiekben változhat, és ez a témakör, amely megfelelően frissítjük.


## <a name="create-a-flow"></a>A folyamat létrehozása

Most már készen áll a folyamat által használt egyéni összekötőt és a SharePoint-lista létrehozott létrehozásához.

### <a name="add-a-trigger-and-specify-a-condition"></a>Adja hozzá egy eseményindító, és adja meg a feltétel

Először a folyamat az üres (sablon) nélkül, és ad hozzá egy *eseményindító* , amely a SharePoint-lista egy elem létrehozásakor következik be. Majd adja hozzá a *feltétel* annak meghatározásához, hogy mi a következő lépés.

1. A [flow.microsoft.com](https://flow.microsoft.com), kattintson a **saját Forgalomáramlás**, majd **hozza létre az üres**.

    ![Üres létrehozása](media/functions-flow-scenario/create-from-blank.png)

2. Kattintson a SharePoint eseményindító **elem létrehozásakor**.

    ![Válasszon triggert](media/functions-flow-scenario/choose-trigger.png)

    Ha még nem volt a SharePoint, kérni fogja erre.

3. A **webhely címe**, adja meg a SharePoint-webhely nevét, és a **neve**, adja meg a turbinás adatokat tartalmazó lista.

    ![Válasszon triggert](media/functions-flow-scenario/site-list.png)

4. Kattintson a **új lépés**, majd **feltétel hozzáadása**.

    ![Feltétel hozzáadása](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow két ág hozzáadja a folyamatot: **Ha Igen** és **Ha nincs**. Akkor vegyen fel olyan lépéseket egyik vagy mindkét ágak a körülményt, amely egyeztetni kívánt meghatározása után.

    ![Az ágakkal rendelkező feltétel](media/functions-flow-scenario/condition-branches.png)

5. Az a **feltétel** kártya, kattintson az első mezőben, majd válassza ki **ServiceRequired** a a **dinamikus tartalom** párbeszédpanel megnyitásához.

    ![Válassza ki a feltétel mező](media/functions-flow-scenario/condition1-field.png)

6. Adjon meg egy értéket a `True` a feltételnek.

    ![Adja meg a feltétel igaz](media/functions-flow-scenario/condition1-yes.png)

    Az érték jelenik meg `Yes` vagy `No` a SharePoint a listában, de van tárolva a *logikai*, vagy `True` vagy `False`. 

7. Kattintson a **folyamat létrehozása** az oldal tetején. Ügyeljen arra, hogy kattintson **frissítés Flow** rendszeres időközönként.

A listában a létrehozott elemeket, a folyamat ellenőrzi, hogy a **ServiceRequired** mező értéke `Yes`, megnyitja a **Ha Igen** ág vagy a **Ha nincs** fiókirodai a megfelelő. Időmegtakarítás ebben a témakörben, csak adja meg a műveleteket a **Ha Igen** ág.

### <a name="add-the-custom-connector"></a>Az egyéni összekötő hozzáadása

Most adja hozzá az egyéni összekötő, amely behívja a függvény az Azure-ban. Csakúgy, mint a standard összekötők folyamat hozzáadja az egyéni összekötő. 

1. Az a **Ha Igen** fiókirodai, kattintson a **művelet hozzáadása**.

    ![Művelet hozzáadása](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Az a **művelet kiválasztását** párbeszédpanelen keresse meg `Turbine Repair`, majd válassza ki a **turbinás javítás - kiszámítja a költségek**.

    ![Válasszon műveletet](media/functions-flow-scenario/choose-turbine-repair.png)

    Az alábbi ábrán az a folyamat hozzáadott kártya. A mezők és leírások határozza meg az összekötő OpenAPI definíciója.

    ![Kiszámítja a költségek alapértelmezései](media/functions-flow-scenario/calculates-costs-default.png)

3. Az a **kiszámítja a költségek** kártya, használja a **dinamikus tartalom** párbeszédpanel segítségével kiválaszthatja a függvény a bemeneti adatok. Microsoft Flow jeleníti meg a numerikus, de nem a mező, mert az OpenAPI-definíció adható meg, hogy **óra** és **kapacitás** numerikus.

    A **óra**, jelölje be **EstimatedEffort**, és a **kapacitás**, jelölje be **MaxOutput**.

    ![Válasszon műveletet](media/functions-flow-scenario/calculates-costs-fields.png)

     Most adja hozzá a kimeneti függvény alapján egy másik feltételt.

4. Alján a **Ha Igen** fiókirodai, kattintson a **további**, majd **feltétel hozzáadása**.

    ![Feltétel hozzáadása](media/functions-flow-scenario/condition2-add.png)

5. Az a **feltétel 2** kártya, kattintson az első mezőben, majd válassza ki **üzenet** a a **dinamikus tartalom** párbeszédpanel megnyitásához.

    ![Válassza ki a feltétel mező](media/functions-flow-scenario/condition2-field.png)

6. Adjon meg egy értéket a `Yes`. A folyamat a következő Ugrás **Ha Igen** ág vagy **Ha nincs** fiókirodai alapján, hogy az üzenet, a függvény által visszaadott Igen (ügyeljen a javítás) vagy a nem (ne ellenőrizze a javítási). 

    ![Adja meg a feltétel Igen](media/functions-flow-scenario/condition2-yes.png)

A folyamat most az alábbi képen hasonlóan kell kinéznie.

![Adja meg a feltétel Igen](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>A függvény eredménye alapján e-mailek küldése

Ezen a ponton a folyamat, a függvény adott vissza egy **üzenet** értékének `Yes` vagy `No` a függvény, valamint egyéb információkat a költségeket és a lehetséges bevétel. Az a **Ha Igen** fiókirodai a második feltétel elküld egy e-mailt, de tetszőleges számú többek között a írása vissza a SharePoint-lista vagy indítása sikerült tegye egy [jóváhagyási folyamat](https://flow.microsoft.com/documentation/modern-approvals/).

1. Az a **Ha Igen** fiókirodai a második feltétel kattintson **művelet hozzáadása**.

    ![Művelet hozzáadása](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Az a **művelet kiválasztását** párbeszédpanelen keresse meg `email`, majd válassza ki az e-mailek rendszer (Ez esetben Outlook használja) alapján a Küldés e-mail művelethez.

    ![Outlook küldése e-mailt](media/functions-flow-scenario/outlook-send-email.png)

3. Az a **egy e-mailek küldése** kártya, egy e-mailt létrehozni. Adjon meg egy érvényes nevet a szervezet számára a **való** mező. Az alábbi ábrán láthatja a következő szöveg és a tokeneket a **dinamikus tartalom** párbeszédpanel megnyitásához. 

    ![E-mailek mezők](media/functions-flow-scenario/email-fields.png)

    A **cím** token származik, a SharePoint-listát, és **CostToFix** és **RevenueOpportunity** a függvény által visszaadott.

    A befejezett folyamata a következő kép hasonlóan kell kinéznie (azt balra az első **Ha nincs** felszabadítása ág).

    ![Teljes folyamata](media/functions-flow-scenario/complete-flow.png)

4. Kattintson a **frissítés Flow** az oldal tetején, majd kattintson a **végzett**.

## <a name="run-the-flow"></a>A folyamat futtatása

Most, hogy a folyamat befejeződött, adhat hozzá új sort a SharePoint-listát, és tekintse meg, hogyan válaszol az a folyamat.

1. Lépjen vissza a SharePoint-listát, és kattintson a **gyors szerkesztése**.

    ![Gyors szerkesztése](media/functions-flow-scenario/quick-edit.png)

2. Adja meg a következő értékeket a Szerkesztés rácsban.

    | Lista oszlop     | Érték           |
    |-----------------|---------------------|
    | **Cím**           | 60 turbina |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Igen |
    | **EstimatedEffort** | 10 |

3. Kattintson a **Done** (Kész) gombra.

    ![Gyorsszerkesztés kész](media/functions-flow-scenario/quick-edit-done.png)

    Ha az elem hozzáadásához elindítja a folyamatot, amely akkor tekintse meg a Tovább.

4. A [flow.microsoft.com](https://flow.microsoft.com), kattintson a **saját Forgalomáramlás**, majd kattintson a létrehozott folyamatot.

    ![Az adatfolyamok](media/functions-flow-scenario/my-flows.png)

5. A **futtatása előzmények**, kattintson a folyamatot futtatni.

    ![Előzmények](media/functions-flow-scenario/run-history.png)

    Ha a Futtatás sikeres volt, a folyamat műveletek a következő lapon tekintheti meg. Ha a Futtatás bármilyen okból meghiúsult, a következő oldalon elhárításával kapcsolatban biztosít információkat.

6. Bontsa ki a kártyákat megjelenítéséhez, mi történt a folyamat során. Bontsa ki például a **kiszámítja a költségek** megtekintéséhez a való be- és kimenetekkel, a függvény. 

    ![Kiszámítja a költségek bemenetekhez és kimenetekhez](media/functions-flow-scenario/calculates-costs-outputs.png)

7. A megadott személynek az e-mail fiók ellenőrzése a **való** mezőjét a **egy e-mailek küldése** kártya. Az a folyamat által küldött e-mailt az alábbi képen hasonlóan kell kinéznie.

    ![E-mailek folyamata](media/functions-flow-scenario/flow-email.png)

    Láthatja, hogyan a jogkivonatok helyett a SharePoint-lista és a megfelelő értékeivel.

## <a name="next-steps"></a>További lépések
Ez a témakör megtanulta, hogyan:

> [!div class="checklist"]
> * Lista létrehozása a SharePoint.
> * Exportálja egy API-definíció.
> * Kapcsolat hozzáadása az API-t.
> * Hozzon létre egy folyamatot, az e-mailek küldése, ha egy javítási költséghatékony.
> * A folyamat fut.

Microsoft Flow kapcsolatos további információkért lásd: [Ismerkedés a Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Használja az Azure Functions más érdekes forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [meghívni a függvényt a PowerApps](functions-powerapps-scenario.md) és [hozzon létre egy függvényt, amely az Azure Logic Apps](functions-twitter-email.md).