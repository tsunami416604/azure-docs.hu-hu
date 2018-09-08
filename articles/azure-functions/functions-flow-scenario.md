---
title: Azure-függvény hívása a Microsoft Flow |} A Microsoft Docs
description: Hozzon létre egy egyéni összekötőt, majd ezt az összekötőt használó függvény hívása.
services: functions
keywords: felhőalapú alkalmazások, szolgáltatások, Microsoft Flow, üzleti folyamatok, felhőalapú üzleti alkalmazás
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 662c78fc7074b0dafc53c393962aa4b578779095
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092258"
---
# <a name="call-a-function-from-microsoft-flow"></a>Függvény meghívása a Microsoft Flow-ból

[Microsoft Flow](https://flow.microsoft.com/) segítségével könnyebben automatizálhatja a munkafolyamatokat és üzleti folyamatokat a kedvenc alkalmazásai és szolgáltatásai között. A hivatásos fejlesztők Azure Functions használatával kiterjesztheti a Microsoft Flow, képességeit a technikai részleteket a flow sikerei védelme során.

Létrehozhat egy folyamatot, ebben a témakörben egy karbantartási forgatókönyvben a szél turbina alapján. Ez a témakör bemutatja, hogyan hívhat meg a függvényt, amelyet a megadott [függvény OpenAPI definíció létrehozása](functions-openapi-definition.md). A függvény meghatározza, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ha költséghatékony, a folyamat küld egy e-mailt, javasoljuk, a helyreállítás.

Ugyanannak a függvénynek hívása a Powerappsből kapcsolatos információkért lásd: [függvény hívása a Powerappsből](functions-powerapps-scenario.md).

Ebben a témakörben megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Lista létrehozása a Sharepointban.
> * API-definíció exportálása.
> * Kapcsolat hozzáadása az API-hoz.
> * E-mail küldése, ha egy javítása költséghatékony folyamat létrehozása.
> * A folyamat futtatása.

## <a name="prerequisites"></a>Előfeltételek

+ Az aktív [Microsoft Flow-fiók](https://flow.microsoft.com/documentation/sign-up-sign-in/) az Azure-fiók azonos bejelentkezési hitelesítő adatait. 
+ A SharePoint, amelyek ennél a folyamatnál adatforrásként használja. Regisztráljon [Office 365 próbaverzióra](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Ha még nem rendelkezik a SharePoint.
+ Az oktatóanyag elvégzéséhez [függvény OpenAPI definíció létrehozása](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Egy SharePoint-lista létrehozása
Először létrehoz egy listát, amely a folyamat adatforrásként használja. A listán a következő oszlopot tartalmaz.

| Listaoszlop     | Adattípus           | Megjegyzések                                    |
|-----------------|---------------------|------------------------------------------|
| **Cím**           | Egysoros szöveg | A turbina neve                      |
| **LastServiceDate** | Dátum                |                                          |
| **MaxOutput**       | Szám              | Kimenet a turbina kWh-ban            |
| **ServiceRequired** | Igen/nem              |                                          |
| **EstimatedEffort** | Szám              | A javítás becsült ideje (óra) |

1. A SharePoint-webhelyen kattintson vagy koppintson a **új**, majd **lista**.

    ![Új SharePoint-lista létrehozása](./media/functions-flow-scenario/new-list.png)

2. Adja meg a nevét `Turbines`, majd kattintson vagy koppintson a **létrehozás**.

    ![Új lista nevének megadása](./media/functions-flow-scenario/create-list.png)

    A **turbina** lista létrejön az alapértelmezett **cím** mező.

    ![Csatlakozó listája](./media/functions-flow-scenario/initial-list.png)

3. Kattintson vagy koppintson a ![új elem ikon](./media/functions-flow-scenario/icon-new.png) majd **dátum**.

    ![Egysoros szövegmező hozzáadása](./media/functions-flow-scenario/add-column.png)

4. Adja meg a nevét `LastServiceDate`, majd kattintson vagy koppintson a **létrehozás**.

    ![LastServiceDate oszlop létrehozása](./media/functions-flow-scenario/date-column.png)

5. Ismételje meg az utolsó két lépést a többi három oszlop a listában:

    1. **Szám** > "MaxOutput"

    2. **Igen/nem** > "ServiceRequired"

    3. **Szám** > "EstimatedEffort"

Ennyi az egész most - rendelkeznie kell egy üres lista, amely a következő képhez hasonlóan néz ki. Adatok hozzáadása a listához a folyamat létrehozása után.

![Üres lista](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Vegyen fel egy kapcsolatot az API-hoz
Az egyéni API-t (más néven egy egyéni összekötő) érhető el a Microsoft Flow, de kapcsolatot kell létesíteni az API-hoz, a folyamat használata előtt.

1. A [flow.microsoft.com](https://flow.microsoft.com), kattintson a fogaskerék ikonra (a jobb felső sarokban), majd kattintson a **kapcsolatok**.

    ![Folyamatkapcsolatok](media/functions-flow-scenario/flow-connections.png)

1. Kattintson a **kapcsolat létrehozása**, görgessen le a **turbina javításához** összekötő, és kattintson rá.

    ![Kapcsolat létrehozása](media/functions-flow-scenario/create-connection.png)

1. Adja meg az API-kulcsot, majd kattintson a **hozzon létre kapcsolatot**.

    ![Adja meg az API-kulcsot, és hozzon létre](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Ha a folyamat megosztja másokkal, minden egyes személy, aki a használható, vagy használja a folyamat is meg kell adnia az API-kulcsot az API-hoz való kapcsolódáshoz. Ez a viselkedés a későbbiekben változhatnak, és frissítjük, hogy ez a témakör a változás tükrözése érdekében.


## <a name="create-a-flow"></a>Folyamat létrehozása

Most már készen áll egy folyamatot, amely az egyéni összekötő és a létrehozott SharePoint-listát használja.

### <a name="add-a-trigger-and-specify-a-condition"></a>Eseményindító hozzáadása, és adja meg a feltétel

Először hozzon létre egy folyamatot sablon nélkül (nélkül egy sablon), és adjon hozzá egy *eseményindító* , amely akkor aktiválódik, ha a SharePoint-lista-elemet hoznak létre. Ezután adjon hozzá egy *feltétel* meghatározni a következő lépésekről.

1. A [flow.microsoft.com](https://flow.microsoft.com), kattintson a **saját folyamatok**, majd **üres folyamat létrehozása**.

    ![Üres folyamat létrehozása](media/functions-flow-scenario/create-from-blank.png)

2. Kattintson a SharePoint-eseményindítóhoz **egy elem létrehozásakor**.

    ![Válasszon triggert](media/functions-flow-scenario/choose-trigger.png)

    Ha még nem jelentkezett be a Sharepointba, erre kéri.

3. A **webhely címe**, adja meg a SharePoint-webhely nevét, és a **listanév**, adja meg a listában, hogy a turbina adatokat tartalmaz.

    ![Válasszon triggert](media/functions-flow-scenario/site-list.png)

4. Kattintson a **új lépés**, majd **feltétel hozzáadása**.

    ![Feltétel hozzáadása](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow a flow hozzáadja a két ág: **Ha Igen** és **Ha nincsenek**. Lépések hozzáadása az egyik vagy mindkét ágak után a feltételt, amely egyeztetni kívánt határoz meg.

    ![A feltétel ágak](media/functions-flow-scenario/condition-branches.png)

5. Az a **feltétel** kártyát, kattintson az első mezőt, majd válassza ki **ServiceRequired** származó a **dinamikus tartalom** párbeszédpanel bezárásához.

    ![Válassza ki a feltétel mezőt](media/functions-flow-scenario/condition1-field.png)

6. Adjon meg egy értéket a `True` a feltételhez.

    ![Adja meg a feltétel igaz](media/functions-flow-scenario/condition1-yes.png)

    Az érték jelenik meg `Yes` vagy `No` a SharePoint a listában, de van tárolva egy *logikai*, vagy `True` vagy `False`. 

7. Kattintson a **folyamat létrehozása** az oldal tetején. Ügyeljen arra, hogy kattintson **frissítés Flow** rendszeres időközönként.

A listában létrehozott elemeket, a folyamat ellenőrzi, hogy a **ServiceRequired** mező értéke `Yes`, halad a **Ha Igen** ág vagy az **Ha nincs** ág a megfelelő. Időmegtakarítás, csak adja meg a műveleteket ebben a témakörben a **Ha Igen** ágat.

### <a name="add-the-custom-connector"></a>Az egyéni összekötő hozzáadása

Most adja hozzá az egyéni összekötő, amely meghívja a függvényt az Azure-ban. Az egyéni összekötő hozzáadása a folyamatot, mint egy standard szintű összekötővel. 

1. Az a **Ha Igen** ágban, kattintson a **művelet hozzáadása**.

    ![Művelet hozzáadása](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Az a **válasszon ki egy műveletet** párbeszédpanel, és keressen `Turbine Repair`, majd válassza ki a műveletet **turbina javításához - kiszámítja a költségek**.

    ![Válasszon műveletet](media/functions-flow-scenario/choose-turbine-repair.png)

    Az alábbi képen látható, amely szerepel a folyamat a kártya. A mezők és -leírások az összekötő OpenAPI-definíciójának származnak.

    ![Kiszámítja a költségek alapértelmezései](media/functions-flow-scenario/calculates-costs-default.png)

3. Az a **kiszámítja a költségek** kártya, használja a **dinamikus tartalom** párbeszédpanel segítségével kiválaszthatja a függvény bemenetei között. Microsoft Flow mutatja numerikus mezők, de nem a dátummező, mivel az OpenAPI-definíció azt jelenti, hogy **óra** és **kapacitás** numerikus.

    A **óra**, jelölje be **EstimatedEffort**, és a **kapacitás**válassza **MaxOutput**.

    ![Válasszon műveletet](media/functions-flow-scenario/calculates-costs-fields.png)

     Most adjon hozzá egy másik feltétel, a függvény kimenete alapján.

4. Alsó részén a **Ha Igen** ágban, kattintson a **további**, majd **feltétel hozzáadása**.

    ![Feltétel hozzáadása](media/functions-flow-scenario/condition2-add.png)

5. Az a **feltétel 2** kártyát, kattintson az első mezőt, majd válassza ki **üzenet** származó a **dinamikus tartalom** párbeszédpanel bezárásához.

    ![Válassza ki a feltétel mezőt](media/functions-flow-scenario/condition2-field.png)

6. Adjon meg egy értéket a `Yes`. A folyamat a következő Ugrás **Ha Igen** ág vagy **Ha nincs** ág alapján-e a függvény által visszaadott üzenet a Igen (legyen a helyreállítás) vagy no (ne a javítás). 

    ![Írja be a yes feltétel](media/functions-flow-scenario/condition2-yes.png)

A folyamat a következő képhez hasonlóan kell kinéznie.

![Írja be a yes feltétel](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>A függvény eredménye alapján e-mail küldése

Ezen a ponton a folyamatban, a függvény adott vissza egy **üzenet** értékét `Yes` vagy `No` , a funkciót, valamint egyéb információkat a költségek és a lehetséges bevételt. Az a **Ha Igen** ág a második feltétel egy e-mailt küld, de a dolgokat, mint a SharePoint-listához való visszaíráshoz vagy indítása tetszőleges számú ezt egy [jóváhagyási folyamat](https://flow.microsoft.com/documentation/modern-approvals/).

1. Az a **Ha Igen** ág a második feltétel kattintson **művelet hozzáadása**.

    ![Művelet hozzáadása](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Az a **válasszon ki egy műveletet** párbeszédpanel, és keressen `email`, majd válassza ki egy e-mail-küldési művelet (ez megkülönbözteti a kis Outlook használja) e-mail rendszer alapján.

    ![Az Outlook küldése e-mailt](media/functions-flow-scenario/outlook-send-email.png)

3. Az a **e-mail küldése** kártya, a compose egy e-mailt. Adjon meg egy érvényes nevet a szervezet számára a **való** mező. Az alábbi képen láthatja, a következő szöveg és tokenek a kombinációja a **dinamikus tartalom** párbeszédpanel bezárásához. 

    ![E-mail mezők](media/functions-flow-scenario/email-fields.png)

    A **cím** jogkivonat származik, a SharePoint-listához, és **CostToFix** és **RevenueOpportunity** a függvény által visszaadott.

    A kész folyamat a következő képhez hasonlóan kell kinéznie (szándékosan azt az első **Ha nincsenek** ág számára, hogy ezzel helyet).

    ![A folyamat befejezése](media/functions-flow-scenario/complete-flow.png)

4. Kattintson a **frissítés Flow** az oldal tetején, majd kattintson **kész**.

## <a name="run-the-flow"></a>A folyamat futtatása

Most, hogy a folyamat befejeződött, adjon hozzá egy sort a SharePoint-listához, és tekintse meg, hogyan reagál a folyamat.

1. Lépjen vissza a SharePoint-listához, és kattintson a **Gyorsszerkesztés**.

    ![Gyors Szerkesztés](media/functions-flow-scenario/quick-edit.png)

2. Adja meg a következő értékeket a Szerkesztés rácsban.

    | Listaoszlop     | Érték           |
    |-----------------|---------------------|
    | **Cím**           | 60 turbina |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Igen |
    | **EstimatedEffort** | 10 |

3. Kattintson a **Done** (Kész) gombra.

    ![Gyorsszerkesztés kész](media/functions-flow-scenario/quick-edit-done.png)

    Az elem hozzáadásakor elindítja a folyamatot, amely, tekintse át a Tovább gombra.

4. A [flow.microsoft.com](https://flow.microsoft.com), kattintson a **saját folyamatok**, majd kattintson a létrehozott folyamatot.

    ![Saját folyamatok](media/functions-flow-scenario/my-flows.png)

5. A **FUTTATÁSI előzmények**, kattintson a folyamat futtatásához.

    ![Előzmények](media/functions-flow-scenario/run-history.png)

    Ha a Futtatás sikeres volt, a flow-műveletek a következő oldalon áttekintheti. Ha a Futtatás bármilyen okból meghiúsult a következő oldalon elhárításával kapcsolatban biztosít információkat.

6. Bontsa ki a kártyák, hogy mi történt a folyamat során. Bontsa ki például a **kiszámítja a költségek** való bemeneteit és kimeneteit a függvény megtekintéséhez. 

    ![Kiszámítja a költségek bemenetek és kimenetek](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Ellenőrizze az e-mail-fiók a megadott személy a **való** mezőjében a **e-mail küldése** kártya. Az e-mailt küld a folyamat a következő képhez hasonlóan kell kinéznie.

    ![A folyamat e-mailben](media/functions-flow-scenario/flow-email.png)

    Láthatja, hogyan jogkivonatok felváltotta a megfelelő értékeket a SharePoint-lista és a függvény.

## <a name="next-steps"></a>További lépések
Ez a témakör a segítségével megtanulta, hogyan:

> [!div class="checklist"]
> * Lista létrehozása a Sharepointban.
> * API-definíció exportálása.
> * Kapcsolat hozzáadása az API-hoz.
> * E-mail küldése, ha egy javítása költséghatékony folyamat létrehozása.
> * A folyamat futtatása.

A Microsoft Flow kapcsolatos további információkért lásd: [Ismerkedés a Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Az Azure Functions használó más érdekes forgatókönyvek kapcsolatos további információkért lásd: [függvény hívása a Powerappsből](functions-powerapps-scenario.md) és [Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).
