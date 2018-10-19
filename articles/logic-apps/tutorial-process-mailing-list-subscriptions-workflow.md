---
title: Jóváhagyási munkafolyamatok kialakítása levelezőlisták kérelmeinek feldolgozására – Azure Logic Apps | Microsoft Docs
description: Oktatóanyag – hogyan hozhat létre automatizált jóváhagyási munkafolyamatokat a levelezőlistákra való feliratkozások feldolgozására az Azure Logic Apps segítségével
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 96a151b3de5d59ad74eaf7061b1a3ff91d602759
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044416"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Levelezőlistára vonatkozó kérelmek kezelése az Azure Logic Apps használatával

Az Azure Logic Apps segítségével automatizálhatja a munkafolyamatait, és integrálhatja az adatokat különböző Azure- és Microsoft-szolgáltatások, más szolgáltatott szoftveralkalmazások (SaaS), valamint a helyszíni rendszerek között. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazást](../logic-apps/logic-apps-overview.md), amely feldolgozza a [MailChimp](https://mailchimp.com/) szolgáltatás által kezelt levelezőlistára vonatkozó feliratkozási kérelmeket.
A logikai alkalmazás egy e-mail-fiókban monitorozza ezeket a kérelmeket, elküldi őket jóváhagyásra, majd hozzáadja a jóváhagyott tagokat a levelezőlistához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Trigger hozzáadása az e-mailes feliratkozási kérelmek monitorozására.
> * Művelet hozzáadása, amely elküldi az e-mailes kérelmeket jóváhagyásra vagy elutasításra.
> * Feltétel hozzáadása, amely ellenőrzi a jóváhagyási válaszokat.
> * Művelet hozzáadása, amely hozzáadja a jóváhagyott tagokat a levelezőlistához.
> * Feltétel hozzáadása, amely ellenőrzi, hogy a tagok sikeresen csatlakoztak-e a listához.
> * Művelet hozzáadása, amely megerősítő e-maileket küld arról, hogy az új tagok sikeresen csatlakoztak-e a listához.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű befejezett logikai alkalmazás](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztrálhat egy ingyenes Azure-fiókra</a> az eljárás megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy MailChimp-fiók. Hozzon létre egy „test-members-ML” nevű listát, amelyhez a logikai alkalmazás hozzáadhatja a jóváhagyott tagok e-mail-címeit. Ha nincs fiókja, [hozzon létre egy ingyenes fiókot](https://login.mailchimp.com/signup/), és tekintse meg a [lista létrehozásáról](https://us17.admin.mailchimp.com/lists/#) szóló oktatóanyagot. 

* Egy e-mail-fiók az Office 365 Outlookban vagy Outlook.com-ban, amely támogatja a jóváhagyási munkafolyamatokat. Ez a cikk az Office 365 Outlookot használja. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure fő menüjéből válassza az **Erőforrás létrehozása** > **Enterprise Integration** > **Logic App** elemet.

   ![Logikai alkalmazás létrehozása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás alábbi adatait az itt látható módon. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Logikai alkalmazás adatainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | LA-MailingList | A logikai alkalmazás neve | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az Azure-előfizetés neve | 
   | **Erőforráscsoport** | LA-MailingList-RG | A kapcsolódó erőforrások rendezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) neve | 
   | **Hely** | USA 2. keleti régiója | A logikai alkalmazással kapcsolatos információk tárolására szolgáló régió | 
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. | 
   |||| 

3. Miután az Azure üzembe helyezte az alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót és a gyakori logikaialkalmazás-minták sablonjait tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikaialkalmazás-sablon kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely a feliratkozási kérelmeket tartalmazó beérkező e-maileket figyeli.
Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Az e-mailek monitorozására szolgáló trigger hozzáadása

1. A tervezőben írja be az „e-mail érkezésekor” kifejezést a keresőmezőbe. Válassza ki a triggert az e-mail-szolgáltatóhoz: **<*az-e-mail-szolgáltatója*> - Új e-mail érkezésekor**.
   
   ![Válassza ezt az eseményindítót az e-mail-szolgáltatónál: „Új e-mail érkezésekor”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

2. Ha a rendszer kéri a hitelesítő adatokat, jelentkezzen be az e-mail-fiókjába, hogy a Logic Apps kapcsolatot létesíthessen vele.

3. Most adja meg a feltételeket, amelyeket a trigger ellenőriz az összes új e-mailben.

   1. Adja meg a mappát, az időtartamot és a gyakoriságot az e-mailek ellenőrzéséhez.

      ![A mappa, az időtartam és a gyakoriság megadása az e-mailek ellenőrzéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Beállítás | Érték | Leírás | 
      | ------- | ----- | ----------- | 
      | **Mappa** | Beérkezett üzenetek | A monitorozni kívánt e-mail-mappa | 
      | **Intervallum** | 1 | Az ellenőrzések között kivárt intervallumok száma | 
      | **Gyakoriság** | Óra | Az ellenőrzések közötti intervallumok időegysége  | 
      |  |  |  | 

   2. Válassza a **Speciális beállítások megjelenítése** lehetőséget. A **Tárgyszűrő** mezőbe írja be a szöveget, amelyet a triggernek keresnie kell az e-mailek tárgyában: ```subscribe-test-members-ML```

      ![Speciális beállítások megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Ha egyelőre el szeretné rejteni a trigger részleteit, kattintson a trigger címsorára.

   ![Alakzat összecsukása a részletek elrejtéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

   A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres a bejövő e-mailek között. 
   Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy van egy triggere, adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely elküld egy e-mailt a kérelem jóváhagyásához vagy elutasításához. 

1. A trigger területén válassza a **+ Új lépés** > **Művelet hozzáadása** elemet. Keressen rá a „jóváhagyás” kifejezésre, és válassza ki a következő műveletet: **<*az e-mail-szolgáltatója*> - Jóváhagyó e-mail küldése**

   ![<az e-mail-szolgáltatója> – Jóváhagyó e-mail küldése elem kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Adja meg a művelet adatait az itt bemutatott és leírt módon: 

   ![Jóváhagyó e-mail beállításainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*jóváhagyó-e-mail-címe*> | A jóváhagyó e-mail-címe. Tesztelési célokra használhatja a saját címét. | 
   | **Felhasználói választási lehetőségek** | Jóváhagyás, Elutasítás | A válaszlehetőségek, amelyek közül a jóváhagyó választhat. Alapértelmezés szerint a jóváhagyó a „Jóváhagyás” és az „Elutasítás” közül választhat. | 
   | **Tárgy** | A test-members-ML lista tagsági kérelmének jóváhagyása | Egy leíró e-mail-tárgy | 
   |  |  |  | 

   Egyelőre hagyja figyelmen kívül a dinamikus tartalmak vagy a beágyazott paraméterek listáját, amely megjelenik, amikor az adott szerkesztőmezőkbe kattint. 
   Ebből a listából a korábbi műveletek paramétereit kiválaszthatja ki, amelyeket bemenetként használhat a munkafolyamatban. 
   A böngésző szélessége határozza meg, hogy melyik lista fog megjelenni. 
 
4. Mentse a logikai alkalmazást.

Ezután adjon meg egy feltételt, amely ellenőrzi a jóváhagyó válaszát.

## <a name="check-approval-response"></a>Jóváhagyási válasz ellenőrzése

1. A **Jóváhagyó e-mail küldése** művelet alatt válassza a **+ Új lépés** > **Feltétel hozzáadása** elemet.

   Megjelenik a feltételalakzat és a rendelkezésre álló paraméterek, amelyeket megadhat bemenetként a munkafolyamatban. 

2. Adjon egy leíróbb nevet a feltételnek.

   1. A feltétel címsorán válassza a **három pont** (**...** ) > **Átnevezés** lehetőséget.

      Például, ha a böngésző keskeny nézetben jelenik meg:

      ![Feltétel átnevezése](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Ha a böngésző széles nézetben jelenik meg, és a dinamikus tartalmak listája miatt a három pont (...) nem látható, zárja be a listát a **Dinamikus tartalom hozzáadása** gombra kattintva a feltételen belül.

   2. Nevezze át a feltételt a következő leírással: ```If request approved```

3. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyó a **Jóváhagyás** választ választotta-e:

   1. A feltételen belül kattintson az **Érték választása** mezőbe, amely a bal oldalon (széles böngészőnézet) vagy felül (keskeny böngészőnézet) található.
   Válassza ki a **SelectedOption** mezőt a paraméterlistából vagy a dinamikus tartalmak listájából a **Jóváhagyó e-mail küldése** területen.

      Ha például széles nézetben dolgozik, a feltétel a következő példához hasonlít:

      ![A Jóváhagyó e-mail küldése területen válassza a SelectedOption lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Az összehasonlító operátor mezőjében válassza az **egyenlő** operátort.

   3. A jobb oldalon (széles nézet) vagy lent (keskeny nézet) található **Válasszon egy értéket** mezőbe írja be a következő értéket: ```Approve```

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![Teljes feltétel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Mentse a logikai alkalmazást.

Ezután adja meg a műveletet, amelyet a logikai alkalmazás végrehajt, amikor a felülvizsgáló jóváhagyja a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása MailChimp-listához

Ezután adjon hozzá egy műveletet, amely hozzáadja a jóváhagyott tagot a levelezőlistához.

1. A feltétel **Ha igaz** ágában válassza a **Művelet hozzáadása** lehetőséget.
Keressen rá a „mailchimp” kifejezésre, majd válassza a következő műveletet: **MailChimp – Tag hozzáadása a listához**

   ![A MailChimp – Tag hozzáadása a listához lehetőség kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Ha a rendszer arra kéri, hogy jelentkezzen be a MailChimp-fiókjába, adja meg a MailChimp-fiókjához tartozó hitelesítő adatokat.

4. Adja meg a művelet adatait az itt bemutatott és leírt módon:

   ![A Tag hozzáadása listához művelet adatainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Listaazonosító** | test-members-ML | A MailChimp-levelezőlista neve | 
   | **Állapot** | feliratkozva | Az új tag feliratkozási állapota. További információ: <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">Feliratkozók kezelése a MailChimp API-val</a>. | 
   | **E-mail-cím** | <*new-member-email-address*> | A paraméterek vagy a dinamikus tartalmak listájából válassza ki a **Feladó** mezőt az **Új e-mail érkezésekor** területen, hogy a rendszer átadja az új tag e-mail-címét. 
   |  |  |  | 

5. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amellyel ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezőlistájához. Ily módon a logikai alkalmazás értesítést küld arról, hogy a művelet sikeresen vagy sikertelenül végződik.

## <a name="check-for-success-or-failure"></a>Siker vagy sikertelenség ellenőrzése

1. A **Ha igaz** ágban a **Tag hozzáadása listához** művelet alatt válassza a **Továbbiak...** > **Feltétel hozzáadása** lehetőséget.

2. Nevezze át a feltételt a következő leírásra: ```If add member succeeded```

3. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához:

   1. A feltételen belül kattintson az **Érték választása** mezőbe, amely a bal oldalon (széles böngészőnézet) vagy felül (keskeny böngészőnézet) található.
   A paraméterlistából vagy a dinamikus tartalmak listájából válassza ki az **Állapot** mezőt a **Tag hozzáadása listához** részen.

      Ha például széles nézetben dolgozik, a feltétel a következő példához hasonlít:

      ![A Tag hozzáadása listához területen válassza az Állapot elemet](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Az összehasonlító operátor mezőjében válassza az **egyenlő** operátort.

   3. A jobb oldalon (széles nézet) vagy lent (keskeny nézet) található **Válasszon egy értéket** mezőbe írja be a következő értéket: ```subscribed```

   Ha elkészült, a feltétel a következő példához hasonlít:

   ![Befejezett feltétel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ezután állítsa be az elküldendő e-maileket, amelyek arról értesítik, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához.

## <a name="send-email-if-member-added"></a>E-mail küldése tag hozzáadásakor

1. A **Ha a tag hozzáadása sikeres** feltétel **Ha igaz** ágában válassza a **Művelet hozzáadása** lehetőséget.

   ![A feltétel Ha igaz ágában válassza a Művelet hozzáadása lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Keressen rá az „outlook e-mail küldése” kifejezésre, és válassza ki a következő műveletet: **<*az e-mail-szolgáltatója*> - E-mail küldése**

   ![E-mail küldése művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Nevezze át a műveletet a következő leírásra: ```Send email on success```

4. Adja meg a művelet adatait az itt bemutatott és leírt módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*your-email-address*> | Az e-mail-cím, amelyre a sikeres műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | <*subject-for-success-email*> | A sikeres műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget, és válassza ki a megadott mezőt a paraméterlista vagy a dinamikus tartalomlista **Tag hozzáadása listához** részén: <p>„Sikerült! „Tag hozzáadva a „test-members-ML” listához: **e-mail-cím**” | 
   | **Törzs** | <*body-for-success-email*> | A sikeres műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget, és válassza ki a megadott mezőket a paraméterlista vagy a dinamikus tartalomlista **Tag hozzáadása listához** részén:  <p>„Új tag csatlakozott a „test-members-ML” listához: **e-mail-cím**”</br>„Tag csatlakozási állapota: **állapot**” | 
   | | | | 

5. Mentse a logikai alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha a tag nem lett hozzáadva

1. A **Ha a tag hozzáadása sikeres** feltétel **Ha hamis** ágában válassza a **Művelet hozzáadása** lehetőséget.

   ![A feltétel Ha hamis ágában válassza a Művelet hozzáadása lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Keressen rá az „outlook e-mail küldése” kifejezésre, és válassza ki a következő műveletet: **<*az e-mail-szolgáltatója*> - E-mail küldése**

   ![E-mail küldése művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Nevezze át a műveletet a következő leírásra: ```Send email on failure```

4. Adja meg a művelet adatait az itt bemutatott és leírt módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*your-email-address*> | Az e-mail-cím, amelyre a sikertelen műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | <*subject-for-failure-email*> | A sikertelen műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget, és válassza ki a megadott mezőt a paraméterlista vagy a dinamikus tartalomlista **Tag hozzáadása listához** részén: <p>„A művelet sikertelen. A következő tag nem lett hozzáadva a „test-members-ML” listához: **e-mail-cím**” | 
   | **Törzs** | <*body-for-failure-email*> | A sikertelen műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>„Lehet, hogy a tag már létezik. Ellenőrizze a MailChimp-fiókját.” | 
   | | | | 

5. Mentse a logikai alkalmazást. 

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

 ![Befejezett logikai alkalmazás](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailes kérelmet a saját levelezőlistájához való csatlakozásról.
Várja meg, amíg a kérelem megjelenik a bejövő levelei között.

3. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** elemet. 

   Ha az e-mail tárgya megfelel a trigger tárgyszűrőjének, a logikai alkalmazás küld egy e-mailt arról, hogy van egy jóváhagyásra váró feliratkozási kérelme.

4. A jóváhagyási e-mailben válassza ki a **Jóváhagyás** lehetőséget.

5. Ha a feliratkozó e-mail-címe még nem szerepel a levelezőlistában, akkor a logikai alkalmazás hozzáadja az adott személy e-mail-címét, és az alábbi példához hasonló e-mailt küld Önnek:

   ![Sikeres műveletről tájékoztató e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Ha a logikai alkalmazás nem tudja hozzáadni a feliratkozót, akkor az alábbi példához hasonló e-mailt küld Önnek:

   ![Sikertelen műveletről tájékoztató e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. 
   Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. 
   Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely adatokat integrál különböző Azure- és Microsoft-szolgáltatások, illetve egyéb SaaS-alkalmazások között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot. Az Azure főmenüjében lépjen az **Erőforráscsoportok** elemre, és válassza ki a logikai alkalmazás erőforráscsoportját. Válassza az **Erőforráscsoport törlése** elemet. Megerősítésként írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

![„Áttekintés” > „Erőforráscsoport törlése”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy olyan logikai alkalmazást, amely levelezőlisták kérelmeinek jóváhagyását kezeli. Most megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely e-mail-mellékleteket dolgoz fel és tárol Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával.

> [!div class="nextstepaction"]
> [E-mail-mellékletek feldolgozása](../logic-apps/tutorial-process-email-attachments-workflow.md)