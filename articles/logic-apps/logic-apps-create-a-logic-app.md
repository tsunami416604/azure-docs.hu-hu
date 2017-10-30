---
title: "Rendszerek és felhőszolgáltatások közötti munkafolyamatok automatizálása – Azure Logic Apps | Microsoft Docs"
description: "Logikai alkalmazásokat hozhat létre a rendszer-integrációs és vállalati alkalmazásintegrációs (EAI) forgatókönyvek munkafolyamatainak automatizálásához"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "munkafolyamat, felhőalapú alkalmazások, felhőszolgáltatások, üzleti folyamatok, rendszerintegráció, vállalati alkalmazásintegráció, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Az első munkafolyamat automatizálása adatok feldolgozásához logikai alkalmazással

Ha kívánja szeretné integrálni a rendszereket és szolgáltatásokat a cégébe, az [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) használatával automatizálhatja a munkafolyamatokat és üzleti folyamatokat. Ez a gyors útmutató bemutatja, hogyan építhet fel és futtathat egyszerűen automatizált munkafolyamatokat egy logikai alkalmazás létrehozásával. A példaalkalmazás bemutatja, hogyan automatizálhat egy munkafolyamatot, amely új elemeket keres egy webhely RSS-hírcsatornájában, és minden elemről e-mailt küld.

Ez a minta logikai alkalmazás a következőhöz hasonló e-mailt küld:

![Az új RSS-hírcsatornaelemről küldött e-mail](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Itt láthatja a logikai alkalmazás munkafolyamatának áttekintését:

![Áttekintés – példa logikai alkalmazás](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

Ezen rövid útmutató segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Eseményindító hozzáadása a munkafolyamat elindításához, ha új elem jelenik meg az RSS-hírcsatornában.
> * Művelet megadása, amely elküld e-mailt az RSS-hírcsatornaelem részleteiről.
> * A logikai alkalmazás munkafolyamatának futtatása.

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/) az eljárás megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy e-mail-fiók az Azure Logic Apps által támogatott bármely e-mail-szolgáltatónál az értesítések küldéséhez. Használhat például Office 365 Outlook-, Outlook.com- vagy Gmail-fiókot. A többi támogatott e-mail összekötőt [az összekötők listájában](https://docs.microsoft.com/connectors/) találja. Ebben a gyors útmutatóban az Office 365 Outlookot használjuk.

  > [!TIP]
  > Ha rendelkezik személyes [Microsoft-fiókkal](https://account.microsoft.com/account), akkor Outlook.com-fiókja van. Ha Azure munkahelyi vagy iskolai fiókkal rendelkezik, akkor Office 365 Outlook-fiókja van.

* Egy webhely RSS-hírcsatornájára mutató hivatkozás. Ez a példa a [Reuters webhelyének legfontosabb híreit tartalmazó RSS-hírcsatornát használja](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`

Ez a gyors útmutató nem igényel kódírást, de a Logic Apps más, kódot használó forgatókönyveket is támogat, például hogy saját kódot futtasson egy logikai alkalmazásból az [Azure Functions](../azure-functions/functions-overview.md) használatával.

## <a name="create-a-blank-logic-app"></a>Üres logikai alkalmazás létrehozása 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") 

2. Az Azure fő menüjéből válassza az **Új** > **Enterprise Integration** > **Logic App** elemet.

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a következő kép alatti táblázatban szereplő beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetésének-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportjának-neve* | Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) ehhez a logikai alkalmazáshoz, illetve az alkalmazással társított összes erőforrás rendszerezéséhez. | 
   | **Hely** | *az-Ön-Azure-adatközpont-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez, például az USA nyugati régióját. | 
   | **Log Analytics** | Ki | A logikai alkalmazásához kapcsolja be a diagnosztikai naplózást, de ehhez a gyors útmutatóhoz hagyja **kikapcsolva** a beállítást. | 
   |||| 

4. Amikor végzett, válassza a **Rögzítés az irányítópulton** lehetőséget. Így a logikai alkalmazás automatikusan megjelenik az Azure-irányítópulton, és megnyílik az üzembe helyezés után. Válassza a **Létrehozás** elemet.

   > [!NOTE]
   > Ha nem szeretné rögzíteni a logikai alkalmazást, a folytatáshoz manuálisan kell megkeresnie és megnyitnia a logikai alkalmazást az üzembe helyezés után.

   Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót tartalmazó oldalt jelenít meg. 
   A videó alatt megtalálja a gyakori logikaialkalmazás-minták sablonjait. 
   Ez a gyors útmutató az alapoktól építi fel a logikai alkalmazást. 

5. Görgessen át a bemutató videón és a gyakori eseményindítókon. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikaialkalmazás-sablon kiválasztása](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   A Logic Apps Designerben megjelennek az elérhető összekötők és az eseményindítóik, amelyek a logikai alkalmazások munkafolyamatainak elindítására használhatók.

   ![A logikai alkalmazások eseményindítói](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Új elemeket észlelő eseményindító hozzáadása

A logikai alkalmazás minden munkafolyamata egy [eseményindítóval](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) indul. Az eseményindítót egy meghatározott esemény váltja ki, vagy ha egy új adat megfelel a megadott követelményeknek. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

1. A keresőmezőbe írja be szűrőként az „rss” kifejezést. Válassza ki a következő eseményindítót: **RSS – Művelet hírcsatornaelem közzétételekor**. 

   ![Eseményindító kiválasztása: „RSS – Művelet hírcsatornaelem közzétételekor”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Adja meg be a monitorozni kívánt RSS-hírcsatornára mutató hivatkozást, például: `http://feeds.reuters.com/reuters/topNews`. Adja meg az ismétlés időközét és gyakoriságát. Ez a példa ötpercenként ellenőrzi a hírcsatornát.

   ![Eseményindító beállítása RSS-hírcsatornával, gyakorisággal és időközzel](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   A Logic Apps létrehoz egy kapcsolatot az RSS-hírcsatornával.

   > [!TIP]
   > A tervezőben a nézet leegyszerűsítése érdekében összecsukhatja és elrejtheti az alakzatok részleteit – csak kattintson az alakzat címsorára.

3. Mentse a munkáját. A tervező eszköztárán válassza a **Mentés** parancsot. 

   ![A logikai alkalmazás mentése](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres az RSS-hírcsatornában. Úgyhogy adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="add-an-action-to-send-email"></a>E-mail-küldési művelet hozzáadása

Most, hogy van eseményindítója, adjon hozzá egy olyan [műveletet](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), amely e-mailt küld, ha új elem jelenik meg az RSS-hírcsatornában. A munkafolyamat az eseményindító elindulása után hajtja végre ezt a műveletet.

1. A Logic Apps Designerben az eseményindító területén válassza a **+ Új lépés** > **Művelet hozzáadása** elemet.

   ![Művelet hozzáadása](./media/logic-apps-create-a-logic-app/add-new-action.png)

   A tervező az eseményindító indulásakor a logikai alkalmazás által elvégezhető műveleteket jeleníti meg.

   ![Kiválasztás a műveletek listájáról](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. A keresőmezőbe írja be szűrőként az „e-mail küldése” kifejezést. Keresse meg és válassza ki a használni kívánt e-mail-összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőhöz. Példa: 

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 
   * Gmail-fiókok esetében válassza a Gmailt. 

   Ebben a gyors útmutatóban az Office 365 Outlookot használjuk. 
   Ha másik e-mail-szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület eltérhet. 

   ![Válassza ezt a műveletet: „Office 365 Outlook – e-mail küldése”](./media/logic-apps-create-a-logic-app/actions.png)

3. Ha a rendszer hitelesítő adatok megadását kéri, jelentkezzen be az e-mail-fiókjához tartozó felhasználónévvel és jelszóval. 

   A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

4. Adja meg az adatokat, amelyeket hozzá kíván adni az e-mailhez. 

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. 
   Tesztelési célokra használhatja a saját e-mail-címét.

   2. A **Tárgy** mezőbe írja be az e-mail tárgyát. 
   Ebben a példában írja be az „Új RSS-elem:” szöveget a következőképpen:

      ![Írja be az e-mail tárgyát](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Amikor a szerkesztés mezőbe kattint, megnyílik a **Dinamikus tartalom hozzáadása lista**, ahol kiválaszthatja a műveletbe foglalni kívánt elérhető adatmezőket. 
      Ha a dinamikus tartalom lista nem nyílik meg, a megfelelő szerkesztőmezőben válassza a **Dinamikus tartalom hozzáadása** lehetőséget.

   3. A **Dinamikus tartalom hozzáadása** listában válassza a **Hírcsatorna címe** elemet, amely hozzáadja az elem címét az e-mailhez.

      ![Írja be az e-mail tárgyát](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![Hozzáadott hírcsatorna címe](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Ha olyan mezőt választ ki, amely egy tömböt tartalmaz, például a **categories-item** elemet, a tervező automatikusan hozzáad egy „Mindegyikre” hurkot a mezőre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajthatja az adott műveletet. 
      > 
      > A hurok eltávolításához válassza a hurok címsorán lévő három pontot (**...**), majd válassza a **Törlés** lehetőséget.

   4. A **Törzs** mezőbe írja be az e-mail törzsének tartalmát. 
   Ebben a példában írja be ezt a szöveget és válassza ki ezeket a mezőket:

      ![Tartalom hozzáadása e-mail törzséhez](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Mező | Leírás | 
      | ----- | ----------- | 
      | **Hírcsatorna címe** | Az elem címét jeleníti meg. | 
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátumát és idejét jeleníti meg. | 
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címét jeleníti meg. | 
      ||| 

      > [!TIP]
      > Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt. 
      
5. Mentse a munkáját. A tervező eszköztárán válassza a **Mentés** parancsot.

   ![Az elkészült logikai alkalmazás](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** elemet. Azt is megvárhatja, hogy a logikai alkalmazás a beállított ütemezés szerint fusson.

![Logikai alkalmazás futtatása](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Itt van például egy, a logikai alkalmazás által küldött minta e-mail az Outlookból:

![Az új RSS-hírcsatornaelemről küldött e-mail](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Ha a hírcsatornában nincsenek új elemek, a logikai alkalmazás kihagyja az e-mail küldésére vonatkozó lépést, vár a következő esedékes időpontig, és újra elvégzi az ellenőrzést. 

> [!TIP]
> Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozta és futtatta az első logikai alkalmazását. Ez a gyors útmutató bemutatta, hogyan hozhat létre egyszerűen és gyorsan automatikus munkafolyamatokat a rendszerek és szolgáltatások integrálásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A logikai alkalmazás mindaddig fut és módosításokat végezhet az Azure-előfizetésén, amíg ki nem kapcsolja vagy nem törli. Ezenkívül amikor kapcsolatokat hoz létre a logikai alkalmazáshoz, ezek a kapcsolatok megmaradnak a logikai alkalmazás törlése után is. 

Miután végzett, mindenképp tiltsa le vagy inaktiválja azokat az erőforrásokat, amelyek költségeit nem kívánja fizetni, vagy amelyeket nem szeretne megtartani. Az ehhez a gyors útmutatóhoz létrehozott összes erőforrás törléséhez törölje az ehhez a logikai alkalmazáshoz létrehozott Azure-erőforráscsoportot. 

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Ha nem szeretné megőrizni a logikai alkalmazáshoz kapcsolódó egyik elemet sem, törölje az ehhez a gyors útmutatóhoz létrehozott erőforráscsoportot és az összes kapcsolódó erőforrást. További információ az [Azure-erőforráscsoportok kezeléséről](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. Az Azure menüben válassza az **Erőforráscsoportok** lehetőséget.

2. Válassza ki a törölni kívánt erőforráscsoportot. Az erőforráscsoport menüben válassza az **Áttekintés** lehetőséget, ha még nincs kiválasztva. 

3. Tekintse át a törölni kívánt csoportban lévő összes erőforrást. Amikor elkészült, válassza az erőforráscsoport eszköztárán lévő **Erőforráscsoport törlése** lehetőséget.

### <a name="turn-off-logic-app"></a>A logikai alkalmazás kikapcsolása

Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. 

A logikai alkalmazás menüjében válassza az **Áttekintés** elemet. Az eszköztáron válassza a **Letiltás** parancsot.

  ![A logikai alkalmazás kikapcsolása](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

### <a name="delete-logic-app"></a>Logikai alkalmazás törlése

Törölheti csak a logikai alkalmazást, de megtarthatja az összes többi kapcsolódó erőforrást, például a létrehozott kapcsolatokat.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az eszköztáron válassza a **Törlés** parancsot. 

   ![A logikai alkalmazás törlése](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

2. Erősítse meg a logikai alkalmazás törlését, majd válassza a **Törlés** lehetőséget.

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazás munkafolyamatok létrehozása előre összeállított sablonokból](../logic-apps/logic-apps-create-logic-apps-from-templates.md)