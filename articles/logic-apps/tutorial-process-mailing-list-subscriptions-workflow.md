---
title: Jóváhagyásalapú automatizált munkafolyamatok létrehozása
description: Oktatóanyag – Jóváhagyásalapú automatizált munkafolyamat létrehozása, amely az Azure Logic Apps használatával dolgozza fel a levelezési lista-előfizetéseket
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456611"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: Automatikus, jóváhagyásalapú munkafolyamatok létrehozása az Azure Logic Apps használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazást,](../logic-apps/logic-apps-overview.md) amely automatizálja a jóváhagyásalapú munkafolyamatot. Ez a logikai alkalmazás kifejezetten a [MailChimp](https://mailchimp.com/) szolgáltatás által felügyelt levelezési lista előfizetési kérelmeket dolgozza fel. A logikai alkalmazás egy e-mail-fiókban monitorozza ezeket a kérelmeket, elküldi őket jóváhagyásra, majd hozzáadja a jóváhagyott tagokat a levelezőlistához.

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

![Magas szintű kész logikai alkalmazás – áttekintés](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, a kezdés előtt [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* MailChimp-fiók, amely egy "test-members-ML" nevű listát tartalmaz, ahol a logikai alkalmazás e-mail-címeket adhat hozzá a jóváhagyott tagokhoz. Ha nem rendelkezik fiókkal, [regisztráljon egy ingyenes fiókot,](https://login.mailchimp.com/signup/)és megtudhatja, [hogyan hozhat létre MailChimp-listát.](https://us17.admin.mailchimp.com/lists/#)

* E-mail fiók az Office 365 Outlook ban vagy Outlook.com, amely támogatja a jóváhagyási munkafolyamatokat. Ez a cikk az Office 365 Outlookot használja. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure főmenüjében válassza az > **Erőforrás-integrációs** > **logikai alkalmazás** **létrehozása**lehetőséget.

   ![Az új logikai alkalmazás-erőforrás létrehozása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás alábbi adatait az itt látható módon. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   ![A logikai alkalmazással kapcsolatos információk biztosítása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | LA-MailingList | A logikai alkalmazás neve, amely csak betűket,`-`számokat, kötőjeleket ( ), aláhúzást (`_`), zárójeleket (`(`, `)`és pontokat (`.`tartalmazhat. Ez a példa az "LA-MailingList" szót használja. |
   | **Előfizetés** | <*azure-előfizetés-neve*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | LA-MailingList-RG | Az Azure [erőforráscsoport](../azure-resource-manager/management/overview.md)neve, amely a kapcsolódó erőforrások rendszerezésére szolgál. Ez a példa a "LA-MailingList-RG" értéket használja. |
   | **Helyen** | USA nyugati régiója | TA régió, ahol a logikai alkalmazás adatait tárolja. Ez a példa a "West USA" (USA nyugati) |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza **az Értesítések** > **ugrás az erőforráshoz** lehetőséget az üzembe helyezett logikai alkalmazáshoz.

   ![Ugrás az új logikai alkalmazás-erőforrásra](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást a név beírásával a keresőmezőbe.

   A Logic Apps Designer megnyílik, és egy oldalt jelenít meg egy bevezető videót és a gyakran használt eseményindítókat és logikai alkalmazásmintákat. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikai alkalmazássablon kijelölése](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely a feliratkozási kérelmeket tartalmazó beérkező e-maileket figyeli. Minden logikai alkalmazásnak egy eseményindítóval kell kezdenie, amely egy adott esemény bekövetkeztekor vagy amikor új adatok találkoznak egy adott feltétellel. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Az e-mailek monitorozására szolgáló trigger hozzáadása

1. A Logic App Designer keresőmezőjébe `when email arrives` írja be szűrőként. Az **Eseményindítók** listában válassza a **Amikor új e-mail érkezik** az e-mail szolgáltatóhoz, válassza az Eseményindítók lehetőséget.

   Ez a példa az Office 365 Outlook-eseményindítót használja:

   ![Válassza az "Új e-mail érkezésekor" eseményindító tikett-e az e-mail szolgáltatóhoz](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a rendszer kéri, jelentkezzen be az e-mail fiókjába a hitelesítő adataival, hogy a Logic Apps kapcsolatot hozhasson létre az e-mail fiókjával.

1. Az eseményindítóban adja meg az összes új e-mail ellenőrzésének kritériumait.

   1. Adja meg a mappát, az időtartamot és a gyakoriságot az e-mailek ellenőrzéséhez.

      ![A mappa, az időtartam és a gyakoriság megadása az e-mailek ellenőrzéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Tulajdonság | Érték | Leírás |
      |----------|-------|-------------|
      | **Mappa** | `Inbox` | A monitorozni kívánt e-mail-mappa |
      | **Intervallum** | `1` | Az ellenőrzések között kivárt intervallumok száma |
      | **Frekvencia** | `Hour` | Az ismétlődéshez használni kívánt időegység |
      ||||

   1. Most adjon hozzá egy másik tulajdonságot az eseményindítóhoz, hogy szűrhessen az e-mail tárgysorára. Nyissa **meg**az Új paraméter hozzáadása listát , és válassza a **Tárgyszűrő tulajdonságot.**

      !["Tárgyszűrő" tulajdonság hozzáadása az aktiváláshoz](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Az eseményindító tulajdonságairól az Office [365 Outlook-összekötő hivatkozási vagy](https://docs.microsoft.com/connectors/office365/) [Outlook.com összekötőhivatkozás című témakörben talál](https://docs.microsoft.com/connectors/outlook/)további információt.

   1. Miután a tulajdonság megjelenik az eseményindítóban, írja be a következő szöveget:`subscribe-test-members-ML`

      !["Tárgyszűrő" tulajdonság szövegének megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Ha egyelőre el szeretné rejteni a trigger részleteit, kattintson a trigger címsorára.

   ![Alakzat összecsukása a részletek elrejtéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres a bejövő e-mailek között. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy van egy triggere, adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely elküld egy e-mailt a kérelem jóváhagyásához vagy elutasításához.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.** 

1. A **Művelet kiválasztása csoport**keresőmezőjében `approval` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza ki a **Jóváhagyási e-mail küldése** műveletet az e-mail szolgáltatójához. 

   Ez a példa az Office 365 Outlook műveletet használja:

   ![Válassza a "Jóváhagyási e-mail küldése" műveletet](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Adja meg a művelettel kapcsolatos információkat a leírt szerint: 

   ![Jóváhagyási e-mail tulajdonságainak küldése](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Címzett** | <*e-mail címe*> | A jóváhagyó e-mail-címe. Tesztelési célokra használhatja a saját címét. Ez a példasophia.owen@fabrikam.coma kitalált " " e-mail címet használja. |
   | **Tárgy** | `Approve member request for test-members-ML` | Egy leíró e-mail-tárgy |
   | **Felhasználói választási lehetőségek** | `Approve, Reject` | A jóváhagyó által választható válaszbeállítások. Alapértelmezés szerint a jóváhagyó válaszként választhatja a "Jóváhagyás" vagy az "Elutasítás" lehetőséget. |
   ||||

   Egyelőre hagyja figyelmen kívül a dinamikus tartalomlistát, amely akkor jelenik meg, amikor adott szerkesztési mezőkbe kattint. Ez a lista lehetővé teszi a korábbi műveletekből elérhető kimenetek kiválasztását, amelyeket bemenetként használhat a munkafolyamatban.

   A művelet tulajdonságairól további információt az [Office 365 Outlook-összekötőre vonatkozó útmutatóban](https://docs.microsoft.com/connectors/office365/) vagy [a Outlook.com összekötőhivatkozásban talál.](https://docs.microsoft.com/connectors/outlook/)
 
1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt a jóváhagyó kiválasztott válaszának ellenőrzéséhez.

## <a name="check-approval-response"></a>Jóváhagyási válasz ellenőrzése

1. A **Jóváhagyási e-mail küldése** művelet ben válassza az **Új lépés**".

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `condition` be szűrőként. A műveletek listájában válassza a **Feltétel** műveletet.

   ![A "Feltétel" művelet megkeresése és kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Adjon egy leíróbb nevet a feltételnek.

   1. A feltétel címsorán válassza a három pont (**...**) **gombot** > **Átnevezés .**

      ![Feltétel leírásának átnevezése](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Nevezze át a feltételt a következő leírással: `If request approved`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyó kiválasztott **Jóváhagyása**lehetőséget választotta-e.

   1. Ebben az állapotban kattintson a Feltétel bal oldalán lévő **Érték kiválasztása** mezőben.

   1. A megjelenő dinamikus tartalomlistában a **Jóváhagyási e-mail küldése**csoportban válassza a **SelectedOption** tulajdonságot.

      ![A dinamikus tartalomlistából válassza a "SelectedOption" lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. A középső összehasonlító mezőben válassza ki **a operátornak megfelelő** t.

   1. A **Feltétel** jobb oldalán lévő Érték kiválasztása mezőjébe írja be a következő szöveget:`Approve`

      Ha elkészült, a feltétel így néz ki:

      ![Befejezett feltétel a Jóváhagyott példa](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mentse a logikai alkalmazást.

Ezután adja meg a műveletet, amelyet a logikai alkalmazás végrehajt, amikor a felülvizsgáló jóváhagyja a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása MailChimp-listához

Most vegyen fel egy műveletet, amely hozzáadja a jóváhagyott tagot a levelezési listához.

1. A **Feltétel Ha igaz** ága esetén válassza a Művelet **hozzáadása**lehetőséget.

1. A Művelet kiválasztása `mailchimp` **csoportban**írja be szűrőként a Belépést, és válassza a Tag hozzáadása **listához** műveletet.

   ![Válassza a "Tag hozzáadása a listához" műveletet](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Ha a rendszer hozzáférést kér a MailChimp-fiókjához, jelentkezzen be a MailChimp hitelesítő adataival.

1. Adjon tájékoztatást erről a műveletről az itt látható és leírt módon:

   ![A Tag hozzáadása listához művelet adatainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Listaazonosító** | Igen | `test-members-ML` | A MailChimp levelezési listájának neve. Ez a példa a "test-members-ML" (teszttagok-ML) kifejezést használja. |
   | **Állapot** | Igen | `subscribed` | Válassza ki az új tag előfizetési állapotát. Ez a példa a "subscribed" szót használja. <p>További információ: [Feliratkozók kezelése a MailChimp API-val](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-mail cím** | Igen | <*új tag-e-mail-cím*> | A dinamikus tartalomlistában válassza a **Forrás csoportBan** **Válassza az Új levél érkezésekor**lehetőséget, amely az új tag e-mail címében halad át. |
   ||||

   A művelet tulajdonságairól további információt a [MailChimp összekötő hivatkozási útmutatójában talál.](https://docs.microsoft.com/connectors/mailchimp/)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amellyel ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezőlistájához. Ily módon a logikai alkalmazás értesítést küld arról, hogy a művelet sikeresen vagy sikertelenül végződik.

## <a name="check-for-success-or-failure"></a>Siker vagy sikertelenség ellenőrzése

1. A **Ha igaz** ág **csoportTag hozzáadása listához** művelete csoportban válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `condition` be szűrőként. A műveletek listájában válassza a **Feltétel lehetőséget.**

1. Nevezze át a feltételt a következő leírásra: `If add member succeeded`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához:

   1. Ebben az állapotban kattintson a **Válasszon egy érték** mezőbe, amely a feltétel bal oldalán található. A dinamikus tartalomlistában a **Tag hozzáadása listához csoportban**válassza az **Állapot** tulajdonságot.

      Például az ön állapota a következő példához hasonlóan néz ki:

      ![A Tag hozzáadása listához területen válassza az Állapot elemet](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. A középső összehasonlító mezőben válassza ki **a operátornak megfelelő** t.

   1. A **Feltétel** jobb oldalán lévő Érték kiválasztása mezőjébe írja be a következő szöveget:`subscribed`

      Ha elkészült, a feltétel így néz ki:

      ![Kész állapot az Előfizetett példa számára](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ezután állítsa be az elküldendő e-maileket, amelyek arról értesítik, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához.

## <a name="send-email-if-member-added"></a>E-mail küldése tag hozzáadásakor

1. A **Ha a tag hozzáadása sikeres** feltétel csoportBan a Ha **igaz** ágban válassza a **Művelet hozzáadása**lehetőséget.

   ![A "Ha igaz" ágban válassza a "Művelet hozzáadása" lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. A **Művelet kiválasztása csoportkereső**mezőjében `outlook send email` írja be szűrőként, és válassza az **E-mail küldése** műveletet.

   !["E-mail küldése" művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on success`

1. Adja meg a művelet adatait az itt bemutatott és leírt módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Címzett** | Igen | <*e-mail címe*> | Az e-mail-cím, amelyre a sikeres műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | <*tárgy-for-siker-e-mail*> | A sikeres műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Success! Member added to "test-members-ML": ` <p>A dinamikus tartalomlistában a **Tag hozzáadása listához csoportban**válassza az **E-mail cím tulajdonságot.** |
   | **Törzs** | Igen | <*test-a-siker-e-mail*> | A sikeres műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`New member has joined "test-members-ML":` <p>A dinamikus tartalomlistában válassza az **E-mail cím tulajdonságot.** <p>A következő sorban írja be a következő szöveget:`Member opt-in status: ` <p> A dinamikus tartalomlistában a **Tag hozzáadása listához csoportban**válassza az **Állapot** tulajdonságot. |
   |||||

1. Mentse a logikai alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha a tag nem lett hozzáadva

1. A **Ha a tag hozzáadása sikeres** feltétel csoportban a Ha **hamis** ágban válassza a **Művelet hozzáadása**lehetőséget.

   ![A "Ha hamis" ágban válassza a "Művelet hozzáadása" lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. A **Művelet kiválasztása csoportkereső**mezőjében `outlook send email` írja be szűrőként, és válassza az **E-mail küldése** műveletet.

   ![E-mail küldése művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on failure`

1. Adjon tájékoztatást erről a műveletről az itt látható és leírt módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Címzett** | Igen | <*e-mail címe*> | Az e-mail-cím, amelyre a sikertelen műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | <*tárgy-for-hiba-e-mail*> | A sikertelen műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Failed, member not added to "test-members-ML": ` <p>A dinamikus tartalomlistában a **Tag hozzáadása listához csoportban**válassza az **E-mail cím tulajdonságot.** |
   | **Törzs** | Igen | <*test-for-hiba-e-mail*> | A sikertelen műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Mentse a logikai alkalmazást. 

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Példa a logikai alkalmazás munkafolyamatára](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailes kérelmet a saját levelezőlistájához való csatlakozásról. Várja meg, amíg a kérelem megjelenik a bejövő levelei között.

1. A logikai alkalmazás manuális elindításához a tervező eszköztárának futtatása lehetőségre kattintson a **Futtatás gombra.** 

   Ha az e-mail tárgya megfelel a trigger tárgyszűrőjének, a logikai alkalmazás küld egy e-mailt arról, hogy van egy jóváhagyásra váró feliratkozási kérelme.

1. A jóváhagyási e-mailben válassza a **Jóváhagyás**lehetőséget.

1. Ha a feliratkozó e-mail-címe még nem szerepel a levelezőlistában, akkor a logikai alkalmazás hozzáadja az adott személy e-mail-címét, és az alábbi példához hasonló e-mailt küld Önnek:

   ![Példa e-mail - sikeres előfizetés](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Ha a logikai alkalmazás nem tudja hozzáadni a feliratkozót, akkor az alábbi példához hasonló e-mailt küld Önnek:

   ![Példa e-mail - sikertelen előfizetés](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely adatokat integrál különböző Azure- és Microsoft-szolgáltatások, illetve egyéb SaaS-alkalmazások között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a minta logikai alkalmazás, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoport. 

1. Az Azure főmenüjében lépjen az **Erőforráscsoportok** elemre, és válassza ki a logikai alkalmazás erőforráscsoportját.

1. Kattintson az erőforráscsoport menü **Áttekintés** > **törlése Erőforráscsoport parancsára.** 

   ![„Áttekintés” > „Erőforráscsoport törlése”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Adja meg az erőforráscsoport nevét megerősítésként, és válassza a **Törlés gombot.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy olyan logikai alkalmazást, amely levelezőlisták kérelmeinek jóváhagyását kezeli. Most megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely e-mail-mellékleteket dolgoz fel és tárol Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával.

> [!div class="nextstepaction"]
> [E-mail-mellékletek feldolgozása](../logic-apps/tutorial-process-email-attachments-workflow.md)
