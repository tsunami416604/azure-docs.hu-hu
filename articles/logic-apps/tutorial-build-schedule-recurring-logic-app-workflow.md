---
title: Ütemezésalapú automatizált munkafolyamatok létrehozása
description: Oktatóanyag – Ütemezésalapú, ismétlődő és automatizált munkafolyamat létrehozása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456633"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: Automatizált, ütemezésalapú, ismétlődő munkafolyamatok létrehozása az Azure Logic Apps használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazást,](../logic-apps/logic-apps-overview.md) és automatizálhat egy ütemezés szerint futó ismétlődő munkafolyamatot. Pontosabban ez a példa logikai alkalmazás fut minden hétköznap reggel, és ellenőrzi az utazási idő, beleértve a forgalmat, két hely között. Ha az idő meghaladja a megadott határértéket, a logikai alkalmazás e-mailt küld, amely az utazási időt és az úti cél eléréséhez szükséges többletidőt tartalmazza.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy ismétlődési eseményindítót, amely meghatározza a logikai alkalmazás ütemezését.
> * Adjon hozzá egy Bing Térképek-műveletet, amely lekéri egy útvonal utazási idejét.
> * Adjon hozzá egy műveletet, amely létrehoz egy változót, átalakítja az utazási időt másodpercről percre, és tárolja a változót eredményező adatokat.
> * Feltétel hozzáadása, amely összehasonlítja az utazási időt a megadott határértékkel.
> * Adjon hozzá egy műveletet, amely e-mailt küld Önnek, ha az utazási idő meghaladja a korlátot.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű logikai alkalmazás-munkafolyamat – áttekintés](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, a kezdés előtt [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* E-mail fiók egy e-mail szolgáltatótól, amelyet a Logic Apps támogat, például az Office 365 Outlook, a Outlook.com vagy a Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ez a rövid útmutató Office 365 Outlook-fiókot használ. Ha másik e-mail fiókot használ, az általános lépések változatlanok maradnak, de a felhasználói felület kissé eltérhet.

* Az útvonal megtételéhez szükséges idő lekéréséhez szükség van a Bing Térképek API hozzáférési kulcsára. A kulcs lekéréséhez kövesse a [Bing Térképek-kulcs lekérése](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) lépéseit.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure főmenüjében válassza az > **Erőforrás-integrációs** > **logikai alkalmazás** **létrehozása**lehetőséget.

   ![A logikai alkalmazás-erőforrás létrehozása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás alábbi adatait az itt látható módon. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   ![A logikai alkalmazással kapcsolatos információk biztosítása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | LA-TravelTime | A logikai alkalmazás neve, amely csak betűket,`-`számokat, kötőjeleket ( ), aláhúzást (`_`), zárójeleket (`(`, `)`és pontokat (`.`tartalmazhat. Ez a példa az "LA-TravelTime" kifejezést használja. |
   | **Előfizetés** | <*azure-előfizetés-neve*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | LA-TravelTime-RG | Az Azure [erőforráscsoport](../azure-resource-manager/management/overview.md)neve, amely a kapcsolódó erőforrások rendszerezésére szolgál. Ez a példa az "LA-TravelTime-RG" értéket használja. |
   | **Helyen** | USA nyugati régiója | TA régió, ahol a logikai alkalmazás adatait tárolja. Ez a példa a "West USA" (USA nyugati) |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza **az Értesítések** > **ugrás az erőforráshoz** lehetőséget az üzembe helyezett logikai alkalmazáshoz.

   ![Ugrás az új logikai alkalmazás-erőforrásra](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást a név beírásával a keresőmezőbe.

   A Logic Apps Designer megnyílik, és egy oldalt jelenít meg egy bevezető videót és a gyakran használt eseményindítókat és logikai alkalmazásmintákat. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikai alkalmazássablon kijelölése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ezután adja hozzá az Ismétlődés [eseményindítót,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy megadott ütemezés alapján aktiválódik. Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Az Ismétlődés eseményindító hozzáadása

1. A Logic App Designer keresőmezőjében adja meg az "ismétlődés" kifejezést szűrőként. Az **Eseményindítók** listában válassza az **Ismétlődés** eseményindítót.

   !["Ismétlődés" eseményindító hozzáadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Az **Ismétlődés** alakzaton jelölje ki a **három pont** (**...**) gombot, majd kattintson **az Átnevezés gombra.** Nevezze át az eseményindítót a következő leírásra: `Check travel time every weekday morning`

   ![Az Ismétlődés eseményindító leírásának átnevezése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Az eseményindítón belül módosítsa ezeket a tulajdonságokat.

   ![Az Ismétlődés eseményindító időközének és gyakoriságának módosítása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Intervallum** | Igen | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Frekvencia** | Igen | Hét | Az ismétlődéshez használni kívánt időegység |
   |||||

1. Az **Intervallum** és **gyakoriság csoportban**nyissa meg az **Új paraméter hozzáadása** listát, és jelölje ki azokat a tulajdonságokat, amelyeket hozzá szeretne adni az eseményindítóhoz.

   * **Ezeken a napokon**
   * **Ezekben az órákban**
   * **Ezekben a percekben**

   ![Tulajdonságok hozzáadása az Ismétlődés eseményindítóhoz](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Most állítsa be a további tulajdonságok értékeit az itt látható és leírt módon.

   ![Az ütemezés és az ismétlődés részleteinek megadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Ezeken a napokon** | Hétfő, kedd, szerda, csütörtök, péntek | Csak akkor érhető el, ha a **Gyakoriság** értéke „Hét” |
   | **Ezekben az órákban** | 7, 8, 9 | Csak akkor érhető el, ha a **Gyakoriság** értéke „Hét” vagy „Nap”. Válassza ki, hogy a nap melyik órájában fusson az ismétlődés. Ez a példa 7, 8 és 9 órakor fut. |
   | **Ezekben a percekben** | 0, 15, 30, 45 | Csak akkor érhető el, ha a **Gyakoriság** értéke „Hét” vagy „Nap”. Válassza ki, hogy a nap melyik percében fusson az ismétlődés. Ez a példa óra egésztől kezdve 15 percenként fut. |
   ||||

   Ez az eseményindító minden hétköznap, 15 percenként aktiválódik, 7:00-tól egészen 9:45-ig. Az **Előnézet** mező az ismétlődési ütemezést mutatja. További információkért lásd: [Feladatok és a munkafolyamatok ütemezése](../connectors/connectors-native-recurrence.md) és [Munkafolyamat-műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Ha egyelőre el szeretné rejteni az eseményindító részleteit, kattintson az alakzat címsorába.

   ![Alakzat összecsukása a részletek elrejtéséhez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

A logikai alkalmazás most már működőképes, de az ismétlődésen kívül nem csinál semmit. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="get-the-travel-time-for-a-route"></a>Útvonal megtételéhez szükséges idő lekérése

Most, hogy van eseményindítója, adjon hozzá egy olyan [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely lekéri az utazási időt két pont között. A Logic Apps biztosít egy összekötőt a Bing Térképek API-hoz, hogy könnyedén lekérhesse ezt az információt. Mielőtt hozzákezdene ehhez a feladathoz, ellenőrizze, hogy rendelkezik-e az oktatóanyag előfeltételeiben említett Bing Térképek API-kulccsal.

1. A Logic App Designer az eseményindító alatt válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása**csoportban válassza a **Normál**lehetőséget. A keresőmezőbe írja be szűrőként a "bing térképek" kifejezést, és válassza az **Útvonal beírása** műveletet.

   ![Válassza az "Útvonal bekerülése" műveletet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Ha nem rendelkezik Bing Térképek-kapcsolattal, a rendszer arra kéri, hogy hozzon létre egyet. Adja meg ezeket a kapcsolatrészleteket, és válassza **a Létrehozás gombot.**

   ![Kapcsolat létrehozása a Bing Maps API-val](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | BingMapsConnection | Adja meg a kapcsolat nevét. Ez a példa a "BingMapsConnection" szolgáltatást használja. |
   | **API-kulcs** | Igen | <*a-Bing-Maps-kulcs*> | Adja meg a korábban kapott Bing Térképek-kulcsot. Ha nem rendelkezik Bing Térképek-kulccsal, tudja meg, [hogyan kérhet le kulcsot](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Nevezze át a műveletet a következő leírásra: `Get route and travel time with traffic`

1. A műveleten belül nyissa meg az **Új paraméter hozzáadása listát**, és jelölje ki azokat a tulajdonságokat, amelyeket hozzá szeretne adni a művelethez.

   * **Optimalizálja**
   * **Távolság mértékegysége**
   * **Közlekedési mód**

   ![Tulajdonságok hozzáadása az "Útvonal bekerülése" művelethez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Most állítsa be a művelet tulajdonságainak értékeit az itt látható és leírt módon.

   ![Adja meg az "Útvonal bekerülése" művelet részleteit](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **1. útvonalpont** | Igen | <*kezdő hely*> | Az útvonal kiindulópontja |
   | **2. útvonalpont** | Igen | <*végpont-hely*> | Az útvonal célállomása |
   | **Optimalizálja** | Nem | timeWithTraffic | Az útvonal optimalizálására szolgáló paraméter (például távolság, utazási idő a jelenlegi forgalom mellett stb.). Válassza ki a "timeWithTraffic" paramétert. |
   | **Távolság mértékegysége** | Nem | <*az ön által előnyben részesített*> | Az útvonalhoz használt távolság-mértékegység. Ez a példa a "Mérföld" az egység. |
   | **Közlekedési mód** | Nem | Vezetés | Az útvonalhoz használt közlekedési mód. Válassza a "Vezetés" módot. |
   ||||

   További információk ezekről a paraméterekről: [Útvonal kiszámítása](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route).

1. Mentse a logikai alkalmazást.

Ezután hozzon létre egy változót a jelenlegi utazási idő átalakításához, és másodpercek helyett percekként való tárolásához. Így nem kell megismételnie az átalakítást, és egyszerűbben használhatja az értéket a későbbi lépésekben. 

## <a name="create-a-variable-to-store-travel-time"></a>Változó létrehozása az utazási idő tárolásához

Előfordulhat, hogy a munkafolyamatban lévő adatokon szeretne műveleteket futtatni, majd az eredményeket későbbi műveletekben használni. Ha az egyszerű újrafelhasználás vagy hivatkozás érdekében menteni szeretné az eredményeket, létrehozhat változókat a feldolgozás utáni tárolásukhoz. A logikai alkalmazásban csak a legfelső szinten hozhat létre változókat.

Alapértelmezés szerint az előző **Útvonal begetése** művelet az aktuális utazási időt adja vissza a forgalommal másodpercben az **Utazási időtartam forgalom** tulajdonságból. Ha átalakítja, és inkább percekként tárolja az értéket, később átalakítás nélkül, egyszerűbben tudja felhasználni.

1. Az **Útvonal beszerezése** művelet csoportban válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja be a "változók" szót, és válassza a **Változó inicializálása** műveletet.

   ![Válassza a "Változó inicializálása" műveletet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Nevezze át a műveletet a következő leírásra: `Create variable to store travel time`

1. Adja meg a változó részleteit az itt leírt módon:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Igen | travelTime | A változó neve. Ez a példa a "travelTime" szót használja. |
   | **Típus** | Igen | Egész szám | A változó adattípusa |
   | **Érték** | Nem| Egy kifejezés, amely átalakítja a jelenlegi utazási időt másodpercekből percekké (lásd a táblázat alatti lépéseket). | A változó kezdeti értéke |
   ||||

   1. Az **Érték** tulajdonság kifejezésének létrehozásához kattintson a mezőben, hogy a dinamikus tartalomlista megjelenjen. Ha szükséges, állítsa szélesebbre a böngésző ablakát, amíg meg nem jelenik a lista. A dinamikus tartalomlistában válassza a **Kifejezés**lehetőséget.

      ![Információ biztosítása a "Változó inicializálása" művelethez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Ha néhány szerkesztési mezőben kattint, megjelenik a dinamikus tartalomlista. Ez a lista a korábbi műveletek azon tulajdonságait jeleníti meg, amelyeket bemenetként használhat a munkafolyamatban. A dinamikus tartalomlistában van egy kifejezésszerkesztő, ahol kiválaszthatja a műveletek futtatásához szükséges függvényeket. A kifejezésszerkesztő csak a dinamikus tartalmak listájában jelenik meg.

   1. A kifejezésszerkesztőbe írja be a következő kifejezést: `div(,60)`

      ![Írja be a következő kifejezést: „div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Vigye az egérmutatót a kifejezésre, a bal oldali zárójel (**(**) és a vessző (**,**) közé. 
   válassza **a Dinamikus tartalom**lehetőséget.

      ![Pozíciókurzor, válassza a "Dinamikus tartalom" lehetőséget](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. A dinamikus tartalmak listájában válassza az **Utazás időtartama forgalommal** lehetőséget.

      ![Válassza ki az "Utazási időtartam forgalom" tulajdonságot](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Miután a tulajdonságértéke feloldódik a kifejezésen belül, válassza az **OK gombot.**

      ![A kifejezés létrehozásának befejezéséhez válassza az "OK" lehetőséget.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Az **Érték** tulajdonság most az itt látható módon jelenik meg:

      ![Az "Érték" tulajdonság feloldott kifejezéssel jelenik meg](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi, hogy a jelenlegi utazás idő meghaladja-e a megadott határértéket.

## <a name="compare-the-travel-time-with-limit"></a>Az utazási idő összehasonlítása a limittel

1. Az előző művelet alatt válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja be szűrőként a "feltétel" szót. A műveletek listájában válassza a **Feltétel** műveletet.

   ![Válassza a "Feltétel" műveletet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Nevezze át a feltételt a következő leírásra: `If travel time exceeds limit`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a **travelTime** tulajdonság értéke meghaladja-e a megadott korlátot az itt leírtak és látható módon:

   1. Ebben az állapotban kattintson a Feltétel bal oldalán lévő **Érték kiválasztása** mezőben.

   1. A megjelenő dinamikus tartalomlistában a **Változók**csoportban válassza ki a **travelTime** tulajdonságot.

      ![Építsd meg az állapot bal oldalát](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. A középső összehasonlító mezőben válassza ki a **nagyobb operátort, mint** a kezelő.

   1. A **Feltétel** jobb oldalán található Érték kiválasztása mezőjébe írja be ezt a korlátot:`15`

      Ha elkészült, a feltétel így néz ki:

      ![Kész állapot az utazási idő ellenőrzéséhez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mentse a logikai alkalmazást.

Ezután adja hozzá a futáshoz, ha az utazási idő meghaladja a korlátot.

## <a name="send-email-when-limit-exceeded"></a>E-mail küldése a határérték túllépésekor

Ezután adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a határértéket. Az e-mail a jelenlegi utazási időt és a megadott útvonal teljesítéséhez szükséges többletidőt tartalmazza.

1. A **Feltétel Ha igaz** ága esetén válassza a Művelet **hozzáadása**lehetőséget.

1. A **Művelet kiválasztása**csoportban válassza a **Normál**lehetőséget. A keresőmezőbe írja be az "E-mail küldése" szót. A lista sok eredményt ad vissza, ezért először válassza ki a kívánt e-mail-összekötőt, például:

   ![Válassza ki a kívánt e-mail-összekötőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Azure-munkahelyi vagy iskolai fiókok esetén válassza az **Office 365 Outlook**lehetőséget.
   * Személyes Microsoft-fiókok esetén válassza **a Outlook.com**lehetőséget.

1. Amikor megjelennek az összekötő lépései, válassza a használni kívánt "e-mail küldése művelet" lehetőséget, például:

   ![Az „e-mail küldése” művelet kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Ha még nem rendelkezik kapcsolattal, a rendszer arra kéri, hogy jelentkezzen be az e-mail-fiókjába.

   A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

1. Nevezze át a műveletet a következő leírásra: `Send email with travel time`

1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelés céljából használja a saját e-mail-címét.

1. A **Tárgy** mezőben adja meg az e-mail tárgyát és a **travelTime** változót.

   1. Írja be a `Current travel time (minutes):` szöveget, záró szóközzel. 

   1. A dinamikus tartalomlista **Változók**területén válassza a **Tovább**lehetőséget.

      ![A "travelTime" változó megkeresése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Miután **a TravelTime** megjelenik **a Változók**csoportban, válassza a **travelTime**lehetőséget.

      ![Írja be a tárgy szövegét, valamint egy kifejezést, amely az utazási időt adja vissza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. A **Törzs** mezőben adja meg az e-mail törzsének tartalmát.

   1. Írja be a `Add extra travel time (minutes):` szöveget, záró szóközzel.

   1. A dinamikus tartalomlistában válassza a **Kifejezés**lehetőséget.

      ![Kifejezés létrehozása az e-mail törzséhez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. A kifejezésszerkesztőbe írja be a következő kifejezést, hogy ki tudja számítani a határértéket meghaladó percek számát: ```sub(,15)```

      ![Írja be a kifejezést az utazási idő többletperceinek kiszámításához](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Vigye az egérmutatót a kifejezésre, a bal oldali zárójel (**(**) és a vessző (**,**) közé. Válassza **a Dinamikus tartalom lehetőséget**.

      ![Folytassa az utazási idő többletperceit kiszámító kifejezés létrehozását](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. A **Változók** alatt válassza a **travelTime** elemet.

      ![A kifejezésben használni kívánt "travelTime" tulajdonság kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Miután a tulajdonság feloldódik a kifejezésen belül, válassza az **OK gombot.**

      ![A "Body" tulajdonság feloldása után válassza az "OK" lehetőséget](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A **Body** tulajdonság most az itt látható módon jelenik meg:

      ![Megoldott "Törzs" tulajdonság a kifejezésben](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mentse a logikai alkalmazást.

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Kész példa logikai alkalmazás munkafolyamata](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárának futtatása lehetőségre kattintson a **Futtatás gombra.**

* Ha az aktuális utazási idő a korlát alatt marad, a logikai alkalmazás nem csinál semmi mást, és vár, vagy a következő intervallum ellenőrzése előtt újra. 

* Ha az aktuális utazási idő meghaladja a korlátot, e-mailt kap az aktuális utazási időről és a korlátot meghaladó percek számáról. Íme egy példa a logikai alkalmazás által küldött e-mailre:

![Példa küldött e-mailre, amely az utazási időt mutatja](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy ütemezésen alapuló, ismétlődő logikai alkalmazást. 

Az **Ismétlődés eseményindítót** használó egyéb logikai alkalmazások létrehozásához tekintse meg ezeket a sablonokat, amelyek a logikai alkalmazás létrehozása után érhetők el:

* Napi emlékeztetők küldésének beállítása.
* Régebbi Azure-blobok törlése.
* Üzenet hozzáadása egy Azure Storage-üzenetsorhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a minta logikai alkalmazás, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoport. 

1. Az Azure főmenüjében lépjen az **Erőforráscsoportok** elemre, és válassza ki a logikai alkalmazás erőforráscsoportját.

1. Kattintson az erőforráscsoport menü **Áttekintés** > **törlése Erőforráscsoport parancsára.** 

   ![„Áttekintés” > „Erőforráscsoport törlése”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Adja meg az erőforráscsoport nevét megerősítésként, és válassza a **Törlés gombot.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely ellenőrzi a forgalmat a megadott ütemezés alapján (hétköznap reggelente), és végrehajt egy adott műveletet (e-mailt küld), amikor az utazási idő meghaladja a megadott határértéket. Most megismerheti, hogyan hozhat létre egy olyan logikai alkalmazást az Azure- és Microsoft-szolgáltatások, valamint más SaaS-alkalmazások integrálásával, amely levelezési listára vonatkozó kérelmeket küld el jóváhagyásra.

> [!div class="nextstepaction"]
> [Levelezési listára vonatkozó kérelmek kezelése](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
