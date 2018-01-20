---
title: "Jóváhagyási munkafolyamatok levelezési lista kérelmeket - Azure Logic Apps |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan automatikus jóváhagyási munkafolyamatokat levelezési lista előfizetések az Azure Logic Apps feldolgozása"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 26ef6f69ef2f2d50628f4d0b021159526c9a04a7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="manage-mailing-list-requests-with-a-logic-app"></a>A logikai alkalmazás levelezési lista kérelmek kezelése

Az Azure Logic Apps segítségével munkafolyamatainak automatizálásához és adatok integrálása az Azure-szolgáltatások, a Microsoft-szolgáltatások, a más szoftver-,--szolgáltatás (SaaS) alkalmazások között, és a helyszíni rendszereket. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazás](../logic-apps/logic-apps-overview.md) által felügyelt levelezési listáját előfizetés kérelmeket dolgozza fel, amely a [MailChimp](https://mailchimp.com/) szolgáltatás.
A logikai alkalmazás egy e-mail fiókot ezeket a kéréseket figyeli, a jóváhagyási kérelmeket küld, és jóváhagyott tagokat vesz fel a levelezési lista.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy eseményindítót figyelő előfizetési kérelmek e-maileket.
> * Új jóváhagyása vagy elutasítása ezeket a kéréseket e-maileket küldő művelet.
> * Adjon hozzá olyan feltétel, amely ellenőrzi a jóváhagyási választ.
> * Jóváhagyott tagokat vesz fel a levelezési lista művelet hozzáadása.
> * Adjon hozzá olyan feltétel, amely ellenőrzi, hogy az ezekről a tagokról sikeresen csatlakozott-e a listában.
> * Új művelet által küldött e-mailek meggyőződött arról, hogy az ezekről a tagokról sikeresen csatlakozott-e a listában.

Amikor elkészült, a logikai alkalmazás néz ki a munkafolyamat magas szinten:

![Magas szintű végzett logikai alkalmazás](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztrálhat egy ingyenes Azure-fiókra</a> az eljárás megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A MailChimp-fiók. "Test-tagok-ML" ahol a Logic Apps alkalmazást adhat hozzá e-mail címek jóváhagyott tagok nevű lista létrehozása. Ha nincs fiókja, [regisztráljon egy ingyenes fiókot](https://login.mailchimp.com/signup/) és további [lista létrehozása](https://us17.admin.mailchimp.com/lists/#). 

* Az Office 365 Outlook vagy Outlook.com-os, amely támogatja a jóváhagyási munkafolyamatok e-mail fiókot. Ebben a cikkben az Office 365 Outlook. Ha egy másik e-mail fiókot használja, az általános lépéseket ugyanaz maradjon, de a felhasználói felület némileg eltérő jelenhet meg.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a> a Azure-fiók hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure fő menüjéből válassza az **Új** > **Enterprise Integration** > **Logic App** elemet.

   ![Logikai alkalmazás létrehozása](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. A **hozzon létre logikai alkalmazás**, ez a logikai alkalmazás látható és ismertetett ismertetik. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Adja meg a logic app információk](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | LA-MailingList | A logikai alkalmazás nevét | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az Azure-előfizetés nevét | 
   | **Erőforráscsoport** | LA-MailingList-RG | A nevet a [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) használatával rendszerezhetők kapcsolódó erőforrások | 
   | **Hely** | USA 2. keleti régiója | A régió a Logic Apps alkalmazást kapcsolatos információk tárolására szolgáló | 
   | **Log Analytics** | Ki | Tartsa a **ki** diagnosztikai naplózás beállítása. | 
   |||| 

3. Miután Azure telepíti az alkalmazást, a Logic Apps Designer megnyílik, és egy bevezető videó és sablonok közös logic app mintákat egy lapját jeleníti meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikaialkalmazás-sablon kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Ezután adja hozzá a [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely figyeli a bejövő e-mailek, az előfizetés által.
Minden logikai alkalmazás egy eseményindítót, amely akkor fordul elő, amikor egy adott esemény következik be, vagy amikor új adatok megfelel-e egy adott feltétel kell kezdődnie. További információkért lásd: [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adja hozzá az e-mailek figyelése eseményindító

1. A Tervező adja meg "érkezésekor e-mail" be a keresőmezőbe. Válassza ki az eseményindítót az e-mailt Provider:  **< *az e-mailt provider*> – Ha egy új e-mailek megérkeznek**
   
   ![Válassza ki az e-mailt Provider eseményindító: "Egy új e-mailt fogadásakor"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

2. Ha megkérdezi, hogy a hitelesítő adatokat, jelentkezzen be az e-mail fiókjába, hogy a Logic Apps alkalmazásokat hozhat létre kapcsolatot az e-mail fiókjába.

3. Most adja meg a feltételeket, amelyek az eseményindító ellenőrzi az összes új e-mailben.

   1. Adja meg a mappát, időköz és az e-mailek ellenőrzéséhez gyakoriságának.

      ![Adja meg a mappa, időköz és az üzenetek ellenőrzése a következő gyakorisága](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Beállítás | Érték | Leírás | 
      | ------- | ----- | ----------- | 
      | **Folder** | Beérkezett fájlok | Az e-mail mappa figyelése | 
      | **Időköz** | 1 | Ellenőrzések között intervallumok száma | 
      | **Gyakoriság** | Óra | Ellenőrzi a egysége minden közötti távolság  | 
      |  |  |  | 

   2. Válasszon **speciális beállítások megjelenítése**. Az a **tulajdonos szűrő** mezőbe írja be ezt a szöveget az eseményindító az e-mail tárgyában kereséséhez:```subscribe-test-members-ML```

      ![Speciális beállításainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Az eseményindító részletek most elrejtéséhez kattintson az eseményindító címsorában.

   ![Részletek elrejtéséhez alakzat összecsukása](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Mentse a Logic Apps alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

   A Logic Apps alkalmazást most élő, de nem végez jelölőnégyzet csakis a bejövő e-mailt. 
   Igen új művelet, amely válaszol, amikor az eseményindító következik be.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy egy eseményindítót, adja hozzá egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) jóváhagyása vagy elutasítása a kérelem-mailt küld, amely. 

1. Válassza ki az eseményindító **+ új lépés** > **művelet hozzáadása**. "Jóváhagyási", és adja meg a művelet:  **< *az e-mailt provider*>-jóváhagyási e-mailek küldése**

   ![Válassza a "< saját e-mail-szolgáltató > - jóváhagyási e-mailek küldése"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Adja meg a művelet látható és leírt adatokat: 

   ![Jóváhagyási e-mail beállításainak megadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*approver-email-address*> | A jóváhagyó e-mail címet. Tesztelési célokra használhatja saját címét. | 
   | **Felhasználói beállítások** | Jóváhagyás, elutasítás | A válasz a jóváhagyó választható lehetőségek. Alapértelmezés szerint kiválaszthatja a jóváhagyó vagy "jóváhagyása" vagy "Elutasítása", a válaszban. | 
   | **Tárgy** | A test-tagok-ML tag kérelem jóváhagyása | A leíró e-mail tulajdonos | 
   |  |  |  | 

   Most figyelmen kívül hagyja a dinamikus tartalom vagy szövegközi paraméter listában, amikor adott Szerkesztés mezőkbe belül kattint. 
   Ebben a listában válassza ki a paramétereket, melyekkel bemeneteként a munkafolyamat előző műveletekből teszi lehetővé. 
   A böngésző szélesség határozza meg, melyik listája jelenik meg. 
 
4. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá a jóváhagyó választott válasz olyan feltétellel.

## <a name="check-approval-response"></a>Ellenőrizze a jóváhagyási válasz

1. Az a **jóváhagyása e-mailek küldése** művelet, válassza a **+ új lépés** > **feltétel hozzáadása**.

   A feltétel alakzat jelenik meg, és a rendelkezésre álló paramétereket, amelyeket megadhat a munkafolyamat számára. 

2. Nevezze át a feltételt, a jobb leírását.

   1. Válassza ki a feltétel címsoron **folytatást jelző pontokra** (**...** ) gomb > **átnevezése**.

      Ha például a böngésző keskeny nézetben van:

      ![Nevezze át a feltétel](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Ha a böngésző széles nézetben, és a dinamikus tartalom listába blokkolja a hárompontozást jelző gombra való hozzáférést, zárja be a lista kiválasztásával **dinamikus tartalom hozzáadása az** belül a feltétel.

   2. Nevezze át a leírást a feltételei:```If request approved```

3. Olyan feltétel, amely ellenőrzi, hogy a jóváhagyó kijelölt Build **jóváhagyás**:

   1. Belül a feltétellel, kattintson a **adjon meg értéket** csoportban, amely a bal oldali (széles böngésző nézet) vagy (keskeny böngésző nézet) felül.
   A paraméterlista vagy a dinamikus tartalom listába, válassza ki a **SelectedOption** eleménél **jóváhagyása e-mailek küldése**.

      Például ha széles nézetben dolgozik, a feltétel a következőképpen néz ebben a példában:

      ![A "E-mail küldése jóváhagyási" válassza a "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Összehasonlító operátor, válassza az ezzel a művelettel: **egyenlő**

   3. Az alsó (keskeny nézet) vagy jobb oldalán (széles nézet) **adjon meg értéket** mezőbe írja be ezt az értéket:```Approve```

      Amikor elkészült, a feltétel néz ki ebben a példában:

      ![Teljes feltétel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Mentse a Logic Apps alkalmazást.

Ezt követően adja meg a műveletet, a logikai alkalmazás hajtja végre, amikor a felülvizsgáló hagyja jóvá a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása a MailChimp listája

Ezután adja hozzá egy műveletet, amely a jóváhagyott tagot ad hozzá a levelezési listához.

1. A feltétel belül **igaz értéke esetén** fiókirodai, válassza a **művelet hozzáadása**.
"Mailchimp", és adja meg a műveletet: **MailChimp - tag hozzáadása a listához**

   ![Jelölje ki "MailChimp - tag hozzáadása a listához"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Ha azonosítóját, jelentkezzen be a MailChimp-fiókba, jelentkezzen be a MailChimp-fiókjával.

4. Itt adja meg a művelet látható és leírt adatokat:

   ![Adja meg a "Tag hozzáadása a listához" információi](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Azonosítója** | ML-tagok-teszt | A MailChimp levelezési lista nevét | 
   | **Állapot** | az előfizetett | Az előfizetési állapotot, az új tag. További információkért lásd: <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">kezelése a MailChimp API-előfizetők</a>. | 
   | **E-mail cím** | <*new-member-email-address*> | Válassza ki a paraméterlista vagy a dinamikus tartalom listába **a** alatt **egy új üzenet fogadásakor**, amely átadja az e-mail címet az új tag. 
   |  |  |  | 

5. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá egy feltétel, így ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezési listájára. Ily módon a logikai alkalmazás értesítést küld, hogy ez a művelet sikeres vagy sikertelen lesz.

## <a name="check-for-success-or-failure"></a>Sikeres vagy sikertelen ellenőrzése

1. Az a **igaz értéke esetén** a fiókirodai a **lista tag hozzáadása** művelet, válassza a **több...**   >  **Feltétel hozzáadása**.

2. Nevezze át a leírást a feltételei:```If add member succeeded```

3. Olyan feltétel, amely ellenőrzi, hogy a jóváhagyott tag sikeres vagy sikertelen a csatlakozás a levelezési lista létrehozása:

   1. Belül a feltétellel, kattintson a **adjon meg értéket** csoportban, amely a bal oldali (széles böngésző nézet) vagy (keskeny böngésző nézet) felül.
   A paraméterlista vagy a dinamikus tartalom listába, válassza ki a **állapot** eleménél **lista tag hozzáadása**.

      Például ha széles nézetben dolgozik, a feltétel a következőképpen néz ebben a példában:

      ![A "Tag hozzáadása a listához", "Status" kiválasztása](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Összehasonlító operátor, válassza az ezzel a művelettel: **egyenlő**

   3. Az alsó (keskeny nézet) vagy jobb oldalán (széles nézet) **adjon meg értéket** mezőbe írja be ezt az értéket:```subscribed```

   Amikor elkészült, a feltétel néz ki ebben a példában:

   ![Az állapot Befejezett](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

A következő beállítása az e-mailt küld, ha a jóváhagyott tag sikeres vagy sikertelen a csatlakozás levelezési listáját.

## <a name="send-email-if-member-added"></a>E-mail küldése, ha tagjává

1. Az a **igaz értéke esetén** ág a következő feltételt: **Ha sikerült tagot hozzáadni**, válassza a **művelet hozzáadása**.

   ![A "Ha az értéke igaz" ág feltétel, válassza a "Művelet hozzáadása"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. "E-mail küldése az outlook", és adja meg a műveletet:  **< *az e-mailt provider*>-egy e-mailek küldése**

   ![A "Küldési egy e-mailek" műveletének hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Nevezze át a műveletet. Ez a leírás a:```Send email on success```

4. Adja meg a művelet látható és leírt adatokat:

   ![Adja meg az e-mailek sikeres adatokat](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*your-email-address*> | Az e-mail cím helyéről, a sikeres e-mailek küldése. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | <*subject-for-success-email*> | A sikeres e-mail tárgyát. A jelen oktatóanyag esetében adja meg ezt a szöveget, és válassza ki a megadott mező alatt **lista tag hozzáadása** a paraméterlista vagy a dinamikus tartalom listából: <p>"Success! "Test-tagok-ML" hozzáadandó tag: **E-mail cím**" | 
   | **Törzs** | <*body-for-success-email*> | A sikeres e-mail törzse tartalmát. A jelen oktatóanyag esetében adja meg ezt a szöveget, és válassza ki a megadott mezőket a **lista tag hozzáadása** a paraméterlista vagy a dinamikus tartalom listából:  <p>"Új tag csatlakozott a"test-tagok-ML": **E-mail cím**"</br>"Tag jóváhagyási állapotát: **állapot**" | 
   | | | | 

5. Mentse a Logic Apps alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha nem tagjává

1. Az a **hamis** ág a következő feltételt: **Ha sikerült tagot hozzáadni**, válassza a **művelet hozzáadása**.

   ![A "Ha false" ág feltétel, válassza a "Művelet hozzáadása"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. "E-mail küldése az outlook", és adja meg a műveletet:  **< *az e-mailt provider*>-egy e-mailek küldése**

   ![A "Küldési egy e-mailek" műveletének hozzáadása](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Nevezze át a műveletet. Ez a leírás a:```Send email on failure```

4. Itt adja meg a művelet látható és leírt adatokat:

   ![Adja meg az e-mailek nem sikerült adatokat](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Címzett** | <*your-email-address*> | Az e-mail cím helyéről, a hiba e-mailek küldése. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | <*subject-for-failure-email*> | A hiba e-mail tárgyát. A jelen oktatóanyag esetében adja meg ezt a szöveget, és válassza ki a megadott mező alatt **lista tag hozzáadása** a paraméterlista vagy a dinamikus tartalom listából: <p>"Nem sikerült, nem tagjává"test-tagok-ML": **E-mail cím**" | 
   | **Törzs** | <*body-for-failure-email*> | A hiba e-mail törzse tartalmát. A jelen oktatóanyag esetében adja meg ezt a szöveget: <p>"Tag előfordulhat, hogy már létezik. Ellenőrizze a MailChimp-fiókját." | 
   | | | | 

5. Mentse a Logic Apps alkalmazást. 

Ezt követően tesztelje a Logic Apps alkalmazást, amely most hasonlít-e ebben a példában:

 ![Befejezett logikai alkalmazás](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. A levelezési lista csatlakozni egy e-mailek kérés küldése saját maga.
Várja meg a Beérkezett üzenetek jelennek meg a kérést.

3. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** elemet. 

   Ha az e-maileket a tulajdonosa megegyezik a trigger elem tulajdonos szűrő, a logikai alkalmazás küld, e-mailben elküldi az előfizetés kérés jóváhagyásával igazolhatja.

4. A jóváhagyási e-mailben, válassza ki a **jóváhagyás**.

5. Ha az előfizető e-mail cím a levelezési listáján nem létezik, a Logic Apps alkalmazást ad hozzá, hogy a címzett e-mail címét, és ebben a példában például egy e-mailt küld Önnek:

   ![E-mailek sikeres](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Ha a logikai alkalmazás nem vehető fel az előfizető, ebben a példában például egy e-mailt kap:

   ![Hibás e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Ha nem minden e-mailek, ellenőrizze az e-maileket Levélszemét mappát. 
   Az e-mailek szűrőt átirányítási előfordulhat, hogy az ilyen típusú e-mailt. 
   Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, a most létrehozott, és futtassa a logikai alkalmazást, amely egyesíti az adatokat különböző Azure, a Microsoft és más SaaS-alkalmazásokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor már nincs szükség, a csoport, amely tartalmazza a logikai alkalmazás és a kapcsolódó erőforrások törléséhez. Lépjen az Azure főmenü **erőforráscsoportok**, és válassza ki az erőforrás a Logic Apps alkalmazást. Válasszon **erőforrás csoport törlése**. Adja meg az erőforráscsoport neve megerősítő, és válassza a **törlése**.

!["Overview" > "Delete erőforráscsoport"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott logikai alkalmazás, amely felügyeli jóváhagyásait levelezési kérelmek száma. Most megtudhatja, hogyan hozhat létre, amely feldolgozza, és tárolja az e-mail mellékletek integrálja az Azure-szolgáltatások, például az Azure Storage és az Azure Functions logikai alkalmazás.

> [!div class="nextstepaction"]
> [Folyamat az e-mail mellékletek](../logic-apps/tutorial-process-email-attachments-workflow.md)