---
title: Ütemterv-alapú automatizált munkafolyamatok összeállítása
description: Oktatóanyag – ütemezett, ismétlődő és automatizált munkafolyamatok létrehozása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 977f138fad1f2eb1eae95049d2bd8a730ba5687e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048734"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: automatizált, ütemezett és ismétlődő munkafolyamatok létrehozása Azure Logic Apps használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazást](../logic-apps/logic-apps-overview.md) , és hogyan automatizálhat egy ütemezett, ismétlődő munkafolyamatot. Ez a példa a logikai alkalmazást minden hétköznap reggel futtatja, és ellenőrzi az utazási időt, beleértve a forgalmat, két hely között. Ha az idő meghaladja a megadott határértéket, a logikai alkalmazás e-mailt küld, amely az utazási időt és az úti cél eléréséhez szükséges többletidőt tartalmazza.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy ismétlődési eseményindítót, amely meghatározza a logikai alkalmazás ütemtervét.
> * Adjon hozzá egy olyan Bing Maps műveletet, amely egy útvonal utazási idejét kapja meg.
> * Olyan műveletet adhat hozzá, amely létrehoz egy változót, az utazási időt másodpercről percre konvertálja, és a változót eredményezi.
> * Feltétel hozzáadása, amely összehasonlítja az utazási időt a megadott határértékkel.
> * Adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a korlátot.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű Logic apps-munkafolyamat – áttekintés](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/) Kezdés előtt.

* Egy Logic Apps által támogatott e-mail-szolgáltatótól (például Office 365 Outlook, Outlook.com vagy Gmail) származó e-mail-fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a rövid útmutató egy Office 365 Outlook-fiókot használ. Ha más e-mail-fiókot használ, az általános lépések változatlanok maradnak, de a felhasználói felület némileg eltérő lehet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Az útvonal megtételéhez szükséges idő lekéréséhez szükség van a Bing Térképek API hozzáférési kulcsára. A kulcs lekéréséhez kövesse a [Bing Térképek-kulcs lekérése](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) lépéseit.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure fő menüjében válassza az **erőforrás létrehozása**  >  **integrációs**  >  **logikai alkalmazás**lehetőséget.

   ![A logikai alkalmazás erőforrásának létrehozása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás alábbi adatait az itt látható módon. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   ![Adja meg a logikai alkalmazással kapcsolatos információkat](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | LA-TravelTime | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a "LA-TravelTime" kifejezést használja. |
   | **Előfizetés** | <*saját-Azure-előfizetés-név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | LA-TravelTime-RG | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md)neve. Ez a példa a "LA-TravelTime-RG" protokollt használja. |
   | **Hely** | USA nyugati régiója | A logikai alkalmazás adatainak tárolására szolgáló AA-régió. Ez a példa a "West US"-t használja. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztáron válassza az **értesítések**  >  **Ugrás az erőforráshoz** lehetőséget a telepített logikai alkalmazáshoz.

   ![Ugrás az új Logic app-erőforrásra](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   Megnyílik a Logic Apps Designer, és egy bemutató videót és gyakran használt triggereket és logikai alkalmazási mintákat tartalmazó oldalt jelenít meg. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Üres logikai alkalmazás sablonjának kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ezután adja hozzá az ismétlődési [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely a megadott ütemezésen alapul. Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Az ismétlődési eseményindító hozzáadása

1. A Logic app Designerben a keresőmezőbe írja be szűrőként az "Ismétlődés" kifejezést. Az **Eseményindítók** listából válassza ki az **ismétlődési** eseményindítót.

   !["Ismétlődés" eseményindító hozzáadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Az **ismétlődési** alakzaton válassza a három pontot (**..**.), **majd az** **Átnevezés**lehetőséget. Nevezze át az eseményindítót a következő leírásra: `Check travel time every weekday morning`

   ![Az ismétlődési eseményindító leírásának átnevezése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Módosítsa ezeket a tulajdonságokat az triggeren belül.

   ![Az ismétlődési eseményindító intervallumának és gyakoriságának módosítása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Intervallum** | Yes | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Gyakoriság** | Yes | Hét | Az ismétlődéshez használni kívánt időegység |
   |||||

1. Az **intervallum** és a **gyakoriság**területen nyissa meg az **új paraméter hozzáadása** listát, és válassza ki ezeket a tulajdonságokat az triggerhez való hozzáadáshoz.

   * **Ezeken a napokon**
   * **Ezekben az órákban**
   * **Ezekben a percekben**

   ![Az ismétlődési eseményindító tulajdonságainak hozzáadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Most állítsa be a további tulajdonságok értékeit az itt látható módon.

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

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

A logikai alkalmazás most már működőképes, de az ismétlődésen kívül nem csinál semmit. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="get-the-travel-time-for-a-route"></a>Útvonal megtételéhez szükséges idő lekérése

Most, hogy van eseményindítója, adjon hozzá egy olyan [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely lekéri az utazási időt két pont között. A Logic Apps biztosít egy összekötőt a Bing Térképek API-hoz, hogy könnyedén lekérhesse ezt az információt. Mielőtt hozzákezdene ehhez a feladathoz, ellenőrizze, hogy rendelkezik-e az oktatóanyag előfeltételeiben említett Bing Térképek API-kulccsal.

1. A Logic app Designerben az trigger alatt válassza az **új lépés**lehetőséget.

1. A **válasszon műveletet**területen válassza a **standard**lehetőséget. A keresőmezőbe írja be szűrőként a "Bing Maps" kifejezést, majd válassza az **Útvonal lekérése** műveletet.

   ![Válassza az "útvonal lekérése" műveletet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Ha nem rendelkezik Bing Térképek-kapcsolattal, a rendszer arra kéri, hogy hozzon létre egyet. Adja meg ezeket a kapcsolati adatokat, és válassza a **Létrehozás**lehetőséget.

   ![A Bing Maps API-hoz való kapcsolódás létrehozása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | BingMapsConnection | Adja meg a kapcsolat nevét. Ez a példa a "BingMapsConnection" kifejezést használja. |
   | **API-kulcs** | Yes | <*a-Bing-Maps-Key*> | Adja meg a korábban kapott Bing Térképek-kulcsot. Ha nem rendelkezik Bing Térképek-kulccsal, tudja meg, [hogyan kérhet le kulcsot](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Nevezze át a műveletet a következő leírásra: `Get route and travel time with traffic`

1. A műveleten belül nyissa meg az **új paraméter hozzáadása listát**, és válassza ki ezeket a tulajdonságokat a művelethez való hozzáadáshoz.

   * **Optimalizálás**
   * **Távolság mértékegysége**
   * **Közlekedési mód**

   ![Tulajdonságok hozzáadása az "útvonal lekérése" művelethez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Most állítsa be a művelet tulajdonságainak értékeit az itt látható módon, az itt leírtak szerint.

   ![Az "útvonal lekérése" művelet részleteinek megadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **1. útvonalpont** | Yes | <*Start – hely*> | Az útvonal kiindulópontja |
   | **2. útvonalpont** | Yes | <*végső hely*> | Az útvonal célállomása |
   | **Optimalizálás** | No | timeWithTraffic | Az útvonal optimalizálására szolgáló paraméter (például távolság, utazási idő a jelenlegi forgalom mellett stb.). Válassza ki a "timeWithTraffic" paramétert. |
   | **Távolság mértékegysége** | No | <*saját preferencia*> | Az útvonalhoz használt távolság-mértékegység. Ez a példa az "Mile" egységet használja egységként. |
   | **Közlekedési mód** | No | Vezetés | Az útvonalhoz használt közlekedési mód. Válassza a "vezetés" módot. |
   ||||

   További információk ezekről a paraméterekről: [Útvonal kiszámítása](/bingmaps/rest-services/routes/calculate-a-route).

1. Mentse a logikai alkalmazást.

Ezután hozzon létre egy változót a jelenlegi utazási idő átalakításához, és másodpercek helyett percekként való tárolásához. Így nem kell megismételnie az átalakítást, és egyszerűbben használhatja az értéket a későbbi lépésekben. 

## <a name="create-a-variable-to-store-travel-time"></a>Az utazási idő tárolására szolgáló változó létrehozása

Időnként előfordulhat, hogy a munkafolyamatban lévő adatokon szeretné futtatni a műveleteket, majd az eredményeket a későbbi műveletekben használja. Ha az egyszerű újrafelhasználás vagy hivatkozás érdekében menteni szeretné az eredményeket, létrehozhat változókat a feldolgozás utáni tárolásukhoz. A logikai alkalmazásban csak a legfelső szinten hozhat létre változókat.

Alapértelmezés szerint az előző **Útvonal lekérése** művelet a jelenlegi utazási időt adja vissza az **utazási időtartam forgalmi** tulajdonságának másodpercben megadott forgalmával. Ha átalakítja, és inkább percekként tárolja az értéket, később átalakítás nélkül, egyszerűbben tudja felhasználni.

1. Az **Útvonal lekérése** művelet alatt válassza az **új lépés**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be a "változók" kifejezést, majd válassza a **változó inicializálása** műveletet.

   ![A "változó inicializálása" művelet kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Nevezze át a műveletet a következő leírásra: `Create variable to store travel time`

1. Adja meg a változó részleteit az itt leírt módon:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Yes | travelTime | A változó neve. Ez a példa a "travelTime" kifejezést használja. |
   | **Típus** | Yes | Egész szám | A változó adattípusa |
   | **Érték** | No| Egy kifejezés, amely átalakítja a jelenlegi utazási időt másodpercekből percekké (lásd a táblázat alatti lépéseket). | A változó kezdeti értéke |
   ||||

   1. Az **érték** tulajdonsághoz tartozó kifejezés létrehozásához kattintson a szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. Ha szükséges, állítsa szélesebbre a böngésző ablakát, amíg meg nem jelenik a lista. A dinamikus tartalom listában válassza a **kifejezés**lehetőséget.

      ![Információ megadása a "változó inicializálása" művelethez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Amikor rákattint néhány szerkesztési mezőre, megjelenik a dinamikus tartalmak listája. Ez a lista a korábbi műveletek azon tulajdonságait jeleníti meg, amelyeket bemenetként használhat a munkafolyamatban. A dinamikus tartalom lista egy kifejezés-szerkesztővel rendelkezik, ahol kiválaszthatja a műveletek futtatásához szükséges függvényeket. A kifejezésszerkesztő csak a dinamikus tartalmak listájában jelenik meg.

   1. A kifejezésszerkesztőbe írja be a következő kifejezést: `div(,60)`

      ![Írja be a következő kifejezést: „div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Vigye az egérmutatót a kifejezésre, a bal oldali zárójel (**(**) és a vessző (**,**) közé. 
   Válassza a **dinamikus tartalom**lehetőséget.

      ![Vigye a kurzort a "dinamikus tartalom" elemre.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. A dinamikus tartalmak listájában válassza az **Utazás időtartama forgalommal** lehetőséget.

      ![Az "utazás időtartama forgalom" tulajdonság kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Ha a tulajdonság értéke feloldódik a kifejezésen belül, válassza az **OK**gombot.

      ![A kifejezés létrehozásához kattintson az OK gombra.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Az **érték** tulajdonság most megjelenik az itt látható módon:

      ![Az "érték" tulajdonság megoldott kifejezéssel jelenik meg](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi, hogy a jelenlegi utazás idő meghaladja-e a megadott határértéket.

## <a name="compare-the-travel-time-with-limit"></a>Az utazási idő összehasonlítása a korláttal

1. Az előző művelet alatt válassza az **új lépés**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be szűrőként a "feltétel" kifejezést. A műveletek listából válassza ki a **feltétel** műveletet.

   ![A "feltétel" művelet kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Nevezze át a feltételt a következő leírásra: `If travel time exceeds limit`

1. Hozzon létre egy olyan feltételt, amely ellenőrzi, hogy a **travelTime** tulajdonság értéke meghaladja-e a megadott korlátot az itt leírt módon:

   1. A feltételben kattintson a feltétel bal oldalán található **érték választása** mezőre.

   1. A megjelenő dinamikus tartalom listából válassza ki a **travelTime** tulajdonságot a **változók**alatt.

      ![A feltétel bal oldali felépítése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. A középső összehasonlítás mezőben válassza a **nagyobb, mint** operátor elemet.

   1. A feltétel jobb oldalán található **érték választása** mezőben adja meg a következő korlátot:`15`

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![Az utazási idő ellenőrzését befejező feltétel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mentse a logikai alkalmazást.

Ezután adja hozzá a futtatandó műveletet, ha az utazási idő meghaladja a korlátot.

## <a name="send-email-when-limit-exceeded"></a>E-mail küldése a határérték túllépésekor

Ezután adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a határértéket. Az e-mail a jelenlegi utazási időt és a megadott útvonal teljesítéséhez szükséges többletidőt tartalmazza.

1. A feltétel **igaz** ága esetén válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen válassza a **standard**lehetőséget. A keresőmezőbe írja be az "e-mail küldése" kifejezést. A lista számos eredményt ad vissza, ezért először válassza ki a kívánt e-mail összekötőt, például:

   ![Válassza ki a kívánt e-mail összekötőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget.
   * Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget.

1. Amikor megjelenik az összekötő műveletei, válassza a használni kívánt e-mail küldése műveletet, például:

   ![Az „e-mail küldése” művelet kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Ha még nem rendelkezik kapcsolattal, a rendszer arra kéri, hogy jelentkezzen be az e-mail-fiókjába.

   A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

1. Nevezze át a műveletet a következő leírásra: `Send email with travel time`

1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelés céljából használja a saját e-mail-címét.

1. A **Tárgy** mezőben adja meg az e-mail tárgyát és a **travelTime** változót.

   1. Írja be a `Current travel time (minutes):` szöveget, záró szóközzel. 

   1. A dinamikus tartalom lista **változók**területén válassza a **továbbiak**lehetőséget.

      !["TravelTime" változó keresése](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Miután a **travelTime** megjelenik a **változók**területen, válassza a **travelTime**lehetőséget.

      ![Írja be a tárgy szövegét, valamint egy kifejezést, amely az utazási időt adja vissza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. A **Törzs** mezőben adja meg az e-mail törzsének tartalmát.

   1. Írja be a `Add extra travel time (minutes):` szöveget, záró szóközzel.

   1. A dinamikus tartalom listában válassza a **kifejezés**lehetőséget.

      ![Kifejezés létrehozása az e-mail törzséhez](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. A kifejezésszerkesztőbe írja be a következő kifejezést, hogy ki tudja számítani a határértéket meghaladó percek számát: ```sub(,15)```

      ![Írja be a kifejezést az utazási idő többletperceinek kiszámításához](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Vigye az egérmutatót a kifejezésre, a bal oldali zárójel (**(**) és a vessző (**,**) közé. Válassza a **dinamikus tartalom**lehetőséget.

      ![Folytassa az utazási idő többletperceit kiszámító kifejezés létrehozását](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. A **Változók** alatt válassza a **travelTime** elemet.

      ![A kifejezésben használandó "travelTime" tulajdonság kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Miután a tulajdonság feloldódik a kifejezésen belül, kattintson az **OK gombra**.

      ![A "Body" tulajdonság feloldása után válassza az OK lehetőséget](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Ekkor megjelenik a **Body (törzs** ) tulajdonság, ahogy az itt látható:

      !["Body" tulajdonság megoldva a kifejezésben](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mentse a logikai alkalmazást.

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![A logikai alkalmazás munkafolyamatának befejezett példája](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget.

* Ha a jelenlegi utazási idő a korlát alatt marad, a logikai alkalmazás nem tesz mást, és megvárja, vagy a következő intervallumot, mielőtt újra ellenőriz. 

* Ha a jelenlegi utazási idő meghaladja a korlátot, egy e-mailt kap az aktuális utazási idővel és a korlát feletti percek számával. Íme egy példa a logikai alkalmazás által küldött e-mailre:

![Példa az utazási időt megjelenítő e-mail küldésére](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy ütemezésen alapuló, ismétlődő logikai alkalmazást. 

Az **ismétlődési** eseményindítót használó egyéb logikai alkalmazások létrehozásához tekintse meg ezeket a sablonokat, amelyek a logikai alkalmazás létrehozása után érhetők el:

* Napi emlékeztetők küldésének beállítása.
* Régebbi Azure-blobok törlése.
* Üzenet hozzáadása egy Azure Storage-üzenetsorhoz.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége a minta logikai alkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot. 

1. Az Azure főmenüjében lépjen az **Erőforráscsoportok** elemre, és válassza ki a logikai alkalmazás erőforráscsoportját.

1. Az erőforráscsoport menüben válassza az Áttekintés az **Overview**  >  **erőforráscsoport törlése**elemet. 

   ![„Áttekintés” > „Erőforráscsoport törlése”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Adja meg az erőforráscsoport nevét megerősítésként, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely ellenőrzi a forgalmat a megadott ütemezés alapján (hétköznap reggelente), és végrehajt egy adott műveletet (e-mailt küld), amikor az utazási idő meghaladja a megadott határértéket. Most megismerheti, hogyan hozhat létre egy olyan logikai alkalmazást az Azure- és Microsoft-szolgáltatások, valamint más SaaS-alkalmazások integrálásával, amely levelezési listára vonatkozó kérelmeket küld el jóváhagyásra.

> [!div class="nextstepaction"]
> [Levelezési listára vonatkozó kérelmek kezelése](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
