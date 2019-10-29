---
title: Jóváhagyási alapú automatizált munkafolyamatok létrehozása – Azure Logic Apps
description: Oktatóanyag – jóváhagyási alapú automatikus munkafolyamat létrehozása, amely a levelezőlista-előfizetéseket dolgozza fel Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: f720e22542533d17fc7ab581f8ba8d9c03a89570
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025603"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: automatikus jóváhagyási alapú munkafolyamatok létrehozása Azure Logic Apps használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan [logikai alkalmazást](../logic-apps/logic-apps-overview.md) , amely automatizálja a jóváhagyási alapú munkafolyamatokat. Pontosabban, ez a logikai alkalmazás dolgozza fel a [MailChimp](https://mailchimp.com/) szolgáltatás által kezelt levelezőlistára vonatkozó előfizetési kérelmeket. A logikai alkalmazás egy e-mail-fiókban monitorozza ezeket a kérelmeket, elküldi őket jóváhagyásra, majd hozzáadja a jóváhagyott tagokat a levelezőlistához.

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

![Magas szintű kész logikai alkalmazások – áttekintés](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/) Kezdés előtt.

* Olyan MailChimp-fiók, amely "test-Members-ML" nevű listát tartalmaz, ahol a logikai alkalmazás hozzáadhat e-mail-címeket a jóváhagyott tagokhoz. Ha nem rendelkezik fiókkal, [regisztráljon egy ingyenes fiókra](https://login.mailchimp.com/signup/), és Ismerje meg, [hogyan hozhat létre MailChimp-listát](https://us17.admin.mailchimp.com/lists/#).

* Egy e-mail-fiók az Office 365 Outlook vagy Outlook.com, amely támogatja a jóváhagyási munkafolyamatokat. Ez a cikk az Office 365 Outlookot használja. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure fő menüjében válassza az **erőforrás létrehozása** > **integráció** > **logikai alkalmazás**lehetőséget.

   ![Új logikai alkalmazás erőforrásának létrehozása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás létrehozása** területen adja meg a logikai alkalmazás alábbi adatait az itt látható módon. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   ![Adja meg a logikai alkalmazással kapcsolatos információkat](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Name (Név)** | LA-MailingList | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket (`-`), aláhúzást (`_`), zárójeleket (`(`, `)`) és pontokat (`.`) tartalmazhat. Ez a példa a "LA-MailingList" kifejezést használja. |
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | LA-MailingList-RG | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md)neve. Ez a példa a "LA-MailingList-RG" protokollt használja. |
   | **Hely** | USA nyugati régiója | A logikai alkalmazás adatainak tárolására szolgáló AA-régió. Ez a példa a "West US"-t használja. |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza az **értesítések**  >  a telepített logikai alkalmazás**erőforrásának keresése** lehetőséget.

   ![Ugrás az új Logic app-erőforrásra](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   Megnyílik a Logic Apps Designer, és egy bemutató videót és gyakran használt triggereket és logikai alkalmazási mintákat tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikai alkalmazás sablonjának kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely a feliratkozási kérelmeket tartalmazó beérkező e-maileket figyeli. Minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie, amely egy adott esemény bekövetkezésekor következik be, vagy amikor az új adatértékek megfelelnek egy adott feltételnek. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Az e-mailek monitorozására szolgáló trigger hozzáadása

1. A Logic app Designerben a keresőmezőbe írja be a `when email arrives` szűrőt. Az **Eseményindítók** listából válassza ki az **új e-mail-cím megérkezésének** eseményindítóját az e-mail-szolgáltatóhoz.

   Ez a példa az Office 365 Outlook triggert használja:

   ![Válassza az "új e-mail érkezésekor" triggert az e-mail-szolgáltatóhoz](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába a hitelesítő adataival, hogy a Logic Apps létre tudja hozni az e-mail-fiókjával létesített kapcsolatokat.

1. Az triggerben adja meg az összes új e-mail ellenőrzésének feltételeit.

   1. Adja meg a mappát, az időtartamot és a gyakoriságot az e-mailek ellenőrzéséhez.

      ![A mappa, az időtartam és a gyakoriság megadása az e-mailek ellenőrzéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Tulajdonság | Value (Díj) | Leírás |
      |----------|-------|-------------|
      | **Mappa** | `Inbox` | A monitorozni kívánt e-mail-mappa |
      | **Intervallum** | `1` | Az ellenőrzések között kivárt intervallumok száma |
      | **Gyakoriság** | `Hour` | Az ismétlődéshez használni kívánt időegység |
      ||||

   1. Most adjon hozzá egy másik tulajdonságot az triggerhez, így szűrheti az e-mail tárgy sorát. Nyissa meg az **új paraméter hozzáadása listát**, és válassza a **tulajdonos szűrő** tulajdonságot.

      ![A "tulajdonos szűrő" tulajdonság hozzáadása az aktiváláshoz](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Az trigger tulajdonságaival kapcsolatos további információkért tekintse meg az [Office 365 Outlook Connector-referenciát](https://docs.microsoft.com/connectors/office365/) vagy az [Outlook.com-összekötő referenciáját](https://docs.microsoft.com/connectors/outlook/).

   1. Miután a tulajdonság megjelenik az triggerben, írja be a következő szöveget: `subscribe-test-members-ML`

      ![Adja meg a "tulajdonos szűrő" tulajdonság szövegét](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Ha egyelőre el szeretné rejteni a trigger részleteit, kattintson a trigger címsorára.

   ![Alakzat összecsukása a részletek elrejtéséhez](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres a bejövő e-mailek között. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy van egy triggere, adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely elküld egy e-mailt a kérelem jóváhagyásához vagy elutasításához.

1. Az trigger alatt válassza az **új lépés**lehetőséget. 

1. A **válasszon műveletet**területen a keresőmezőbe írja be a `approval` szűrőt. A műveletek listából válassza a **jóváhagyó E-mail küldése** műveletet az e-mail-szolgáltatóhoz. 

   Ez a példa az Office 365 Outlook műveletet használja:

   ![A "jóváhagyó e-mail küldése" művelet kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Adja meg a művelet adatait a következő módon: 

   ![Jóváhagyó e-mail tulajdonságainak küldése](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Címzett** | <*your-email-address*> | A jóváhagyó e-mail-címe. Tesztelési célokra használhatja a saját címét. Ez a példa a kitalált "sophia.owen@fabrikam.com" e-mail-címet használja. |
   | **Tárgy** | `Approve member request for test-members-ML` | Egy leíró e-mail-tárgy |
   | **Felhasználói választási lehetőségek** | `Approve, Reject` | A jóváhagyó által választható válaszok. Alapértelmezés szerint a jóváhagyó a válaszként a "jóváhagyás" vagy az "elutasítás" lehetőséget is választhatja. |
   ||||

   Egyelőre hagyja figyelmen kívül a dinamikus tartalmak listáját, amely akkor jelenik meg, amikor rákattint a megadott szerkesztési mezőkre. Ez a lista lehetővé teszi, hogy az előző műveletekből származó elérhető kimenetet válassza, amelyet bemenetként használhat a munkafolyamatban.

   A művelet tulajdonságaival kapcsolatos további információkért tekintse meg az [Office 365 Outlook Connector-referenciát](https://docs.microsoft.com/connectors/office365/) vagy az [Outlook.com-összekötő referenciáját](https://docs.microsoft.com/connectors/outlook/).
 
1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt a jóváhagyó kiválasztott válaszának vizsgálatához.

## <a name="check-approval-response"></a>Jóváhagyási válasz ellenőrzése

1. A **jóváhagyó E-mail küldése** művelet alatt válassza az **új lépés**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `condition` szűrőt. A műveletek listából válassza ki a **feltétel** műveletet.

   ![A "feltétel" művelet megkeresése és kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Adjon egy leíróbb nevet a feltételnek.

   1. A feltétel címsorán kattintson a **három pontra (** **..** .) > **Átnevezés**gombra.

      ![A feltétel átnevezésének leírása](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Nevezze át a feltételt a következő leírással: `If request approved`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyó kiválasztotta-e a **jóváhagyást**.

   1. A feltételben kattintson a feltétel bal oldalán található **érték választása** mezőre.

   1. A megjelenő dinamikus tartalom listából válassza ki a **SelectedOption** tulajdonságot a **jóváhagyó e-mail küldése**területen.

      ![A dinamikus tartalmak listájából válassza a "SelectedOption" elemet.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. A középső összehasonlítás mezőben válassza az **egyenlő** operátorként lehetőséget.

   1. A feltétel jobb oldalán található **érték választása** mezőben adja meg a következő szöveget: `Approve`

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![A jóváhagyott példa befejezett feltétele](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mentse a logikai alkalmazást.

Ezután adja meg a műveletet, amelyet a logikai alkalmazás végrehajt, amikor a felülvizsgáló jóváhagyja a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása MailChimp-listához

Most adjon hozzá egy műveletet, amely hozzáadja a jóváhagyott tagot a levelezési listához.

1. A feltétel **igaz** ága esetén válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen adja meg `mailchimp` szűrőként, majd válassza a **tag hozzáadása a listához** műveletet.

   ![Válassza a "tag hozzáadása a listához" műveletet.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Ha a rendszer kéri a MailChimp-fiók elérését, jelentkezzen be a MailChimp hitelesítő adataival.

1. Adja meg a művelet adatait az itt bemutatott módon:

   ![A Tag hozzáadása listához művelet adatainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Tulajdonság | Szükséges | Value (Díj) | Leírás |
   |----------|----------|-------|-------------|
   | **Listaazonosító** | Igen | `test-members-ML` | A MailChimp levelezési lista neve. Ez a példa a "test-Members-ML" kifejezést használja. |
   | **Állapot** | Igen | `subscribed` | Válassza ki az új tag előfizetési állapotát. Ez a példa az "előfizetett" kifejezést használja. <p>További információ: [Feliratkozók kezelése a MailChimp API-val](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-mail-cím** | Igen | <*new-member-email-address*> | A dinamikus tartalmak listájából **válassza ki az** új **üzenet érkezésekor**lehetőséget, amely az új tag e-mail-címét adja meg. |
   ||||

   További információ a művelet tulajdonságairól: [MailChimp-összekötő leírása](https://docs.microsoft.com/connectors/mailchimp/).

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amellyel ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezőlistájához. Ily módon a logikai alkalmazás értesítést küld arról, hogy a művelet sikeresen vagy sikertelenül végződik.

## <a name="check-for-success-or-failure"></a>Siker vagy sikertelenség ellenőrzése

1. A **Ha igaz** ág alatt, a **tag hozzáadása listához** művelet alatt válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `condition` szűrőt. A műveletek listából válassza a **feltétel**elemet.

1. Nevezze át a feltételt a következő leírásra: `If add member succeeded`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához:

   1. A feltételben kattintson az **érték választása** mezőre, amely a feltétel bal oldalán található. A dinamikus tartalom listából válassza ki az **állapot** tulajdonságot a **tag hozzáadása a listához**területen.

      A feltétel például a következő példához hasonlít:

      ![A Tag hozzáadása listához területen válassza az Állapot elemet](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. A középső összehasonlítás mezőben válassza az **egyenlő** operátorként lehetőséget.

   1. A feltétel jobb oldalán található **érték választása** mezőben adja meg a következő szöveget: `subscribed`

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![Az előfizetett példa befejezett állapota](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ezután állítsa be az elküldendő e-maileket, amelyek arról értesítik, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához.

## <a name="send-email-if-member-added"></a>E-mail küldése tag hozzáadásakor

1. A **Ha a tag hozzáadása sikeres** feltétel alatt a **Ha igaz** ág területen válassza a **művelet hozzáadása**lehetőséget.

   ![A "ha igaz" ág esetében válassza a "művelet hozzáadása" lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. A **válasszon műveletet**területen a keresőmezőbe írja be a `outlook send email` szűrőt, és válassza az **e-mail küldése** műveletet.

   !["E-mail küldése" művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on success`

1. Adja meg a művelet adatait az itt bemutatott és leírt módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Tulajdonság | Szükséges | Value (Díj) | Leírás |
   |----------|----------|-------|-------------|
   | **Címzett** | Igen | <*your-email-address*> | Az e-mail-cím, amelyre a sikeres műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | <*subject-for-success-email*> | A sikeres műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Success! Member added to "test-members-ML": ` <p>A dinamikus tartalom listából válassza ki az **E-mail cím** tulajdonságot a **tag hozzáadása a listához**területen. |
   | **Törzs** | Igen | <*body-for-success-email*> | A sikeres műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`New member has joined "test-members-ML":` <p>A dinamikus tartalom listából válassza ki az **E-mail cím** tulajdonságot. <p>A következő sorban adja meg a következő szöveget: `Member opt-in status: ` <p> A dinamikus tartalom listából válassza ki az **állapot** tulajdonságot a **tag hozzáadása a listához**területen. |
   |||||

1. Mentse a logikai alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha a tag nem lett hozzáadva

1. A **Ha a tag hozzáadása sikeres** feltétel alatt a **Ha hamis** ág területen válassza a **művelet hozzáadása**lehetőséget.

   ![A "If false" (hamis) ág esetében válassza a "művelet hozzáadása" lehetőséget.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. A **válasszon műveletet**területen a keresőmezőbe írja be a `outlook send email` szűrőt, és válassza az **e-mail küldése** műveletet.

   ![E-mail küldése művelet hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on failure`

1. Adja meg a művelet adatait az itt bemutatott módon:

   ![Adja meg a sikeres műveletet jelző e-mail információit](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Tulajdonság | Szükséges | Value (Díj) | Leírás |
   |----------|----------|-------|-------------|
   | **Címzett** | Igen | <*your-email-address*> | Az e-mail-cím, amelyre a sikertelen műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | <*subject-for-failure-email*> | A sikertelen műveletet jelző e-mail tárgya. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Failed, member not added to "test-members-ML": ` <p>A dinamikus tartalom listából válassza ki az **E-mail cím** tulajdonságot a **tag hozzáadása a listához**területen. |
   | **Törzs** | Igen | <*body-for-failure-email*> | A sikertelen műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Mentse a logikai alkalmazást. 

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Példa a Logic app-munkafolyamat befejezésére](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailes kérelmet a saját levelezőlistájához való csatlakozásról. Várja meg, amíg a kérelem megjelenik a bejövő levelei között.

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget. 

   Ha az e-mail tárgya megfelel a trigger tárgyszűrőjének, a logikai alkalmazás küld egy e-mailt arról, hogy van egy jóváhagyásra váró feliratkozási kérelme.

1. A jóváhagyási e-mailben válassza a **jóváhagyás**lehetőséget.

1. Ha a feliratkozó e-mail-címe még nem szerepel a levelezőlistában, akkor a logikai alkalmazás hozzáadja az adott személy e-mail-címét, és az alábbi példához hasonló e-mailt küld Önnek:

   ![Példa e-mail – sikeres előfizetés](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Ha a logikai alkalmazás nem tudja hozzáadni a feliratkozót, akkor az alábbi példához hasonló e-mailt küld Önnek:

   ![Példa e-mail – sikertelen előfizetés](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely adatokat integrál különböző Azure- és Microsoft-szolgáltatások, illetve egyéb SaaS-alkalmazások között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a minta logikai alkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot. 

1. Az Azure főmenüjében lépjen az **Erőforráscsoportok** elemre, és válassza ki a logikai alkalmazás erőforráscsoportját.

1. Az erőforráscsoport menüben válassza az **áttekintés** > az **erőforráscsoport törlése**elemet. 

   ![„Áttekintés” > „Erőforráscsoport törlése”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Adja meg az erőforráscsoport nevét megerősítésként, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy olyan logikai alkalmazást, amely levelezőlisták kérelmeinek jóváhagyását kezeli. Most megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely e-mail-mellékleteket dolgoz fel és tárol Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával.

> [!div class="nextstepaction"]
> [E-mail-mellékletek feldolgozása](../logic-apps/tutorial-process-email-attachments-workflow.md)
