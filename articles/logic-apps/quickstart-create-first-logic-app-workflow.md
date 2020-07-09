---
title: Az első automatizált munkafolyamat létrehozása
description: Rövid útmutató – az első automatizált munkafolyamat létrehozása Azure Logic Apps használatával a rendszerintegrációs és a vállalati Application Integration (EAI) megoldásokhoz
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 0b3559a27fe9fae6c34b07c648a289d205560bd8
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321578"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Rövid útmutató: az első munkafolyamat létrehozása Azure Logic Apps-Azure Portal használatával

Ez a rövid útmutató bemutatja az első munkafolyamat létrehozásával kapcsolatos alapvető általános fogalmakat [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával, például egy üres logikai alkalmazás létrehozását, egy trigger és egy művelet hozzáadását, majd a logikai alkalmazás tesztelését. Ebben a rövid útmutatóban olyan logikai alkalmazást fog létrehozni, amely rendszeresen ellenőrzi a webhely RSS-hírcsatornáját az új elemekhez. Ha új elemeket talál, a logikai alkalmazás mindegyikről e-mailt küld. Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű példa a logikai alkalmazás munkafolyamatára](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Ebben az esetben szüksége van egy Azure-előfizetésre, vagy [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/), egy olyan szolgáltatásból származó e-mail-fiókra, amelyet a Azure Logic apps támogat, például Office 365 Outlook, Outlook.com vagy gmail. Más támogatott e-mail szolgáltatások esetén [itt tekintse át az összekötők listáját](https://docs.microsoft.com/connectors/). Ebben a példában a logikai alkalmazás egy Office 365 Outlook-fiókot használ. Ha más e-mail szolgáltatást használ, a teljes általános lépések ugyanazok, de a felhasználói felület némileg eltérő lehet.

> [!IMPORTANT]
> Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure kezdőlapjának Keresés mezőjében keresse meg és válassza a **Logic apps**lehetőséget.

   ![A "Logic Apps" megkeresése és kiválasztása](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. A **Logic apps** lapon válassza a **Hozzáadás**lehetőséget.

   ![Új logikai alkalmazás hozzáadása](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a logikai alkalmazás részleteit az alábbi ábrán látható módon. Ha elkészült, válassza a **Létrehozás**lehetőséget.

   ![Adja meg az új logikai alkalmazás részleteit](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Name (Név)** | <*logikai alkalmazás neve*> | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a "My-First-Logic-app" kifejezést használja. <p><p>**Megjegyzés**: a Logic apps-beli névnek egyedinek kell lennie a régiók között. |
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport-név*> | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) neve. Ez a példa a "My-First-LA-RG" kifejezést használja. <p><p>**Megjegyzés**: Az erőforráscsoportok neveinek egyedinek kell lenniük a régiók között. |
   | **Hely** | <*Azure-régió*> | A logikai alkalmazás adatainak tárolására szolgáló régió. Ez a példa a "West US"-t használja. |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztáron válassza az **értesítések**  >  **Ugrás az erőforráshoz** lehetőséget a telepített logikai alkalmazáshoz.

   ![Ugrás az újonnan létrehozott Logic app-erőforrásra](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres sablon kiválasztása a logikai alkalmazáshoz](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy új RSS-hírcsatornaelem megjelenésekor aktiválódik. Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Azure Logic Apps motor minden alkalommal elindít egy logikai alkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Az RSS-trigger hozzáadása

1. A **Logic app Designerben**a keresőmező alatt válassza az **összes**lehetőséget.

1. A keresőmezőbe írja be az `rss` RSS-összekötő megkereséséhez. Az eseményindítók listából válassza ki a **hírcsatorna-elem közzétételének** eseményindítóját.

   ![Válassza a "hírcsatorna-elem közzétételekor" triggert](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Adja meg az trigger adatait a következő táblázatban leírtak szerint:

   ![Eseményindító beállítása RSS-hírcsatornával, gyakorisággal és időközzel](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Az RSS-hírcsatorna URL-címe** | <*RSS-hírcsatorna – URL*> | A figyelni kívánt RSS-hírcsatorna hivatkozása. Ez a példa a következőt használja: `http://feeds.reuters.com/reuters/topNews` . |
   | **Időköz** | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Gyakoriság** | Perc | Az ellenőrzések közötti intervallumok időegysége  |
   ||||

   Az intervallum és a gyakoriság együtt határozza meg a logikai alkalmazás eseményindítójának ütemezését. Ez a logikai alkalmazás percenként ellenőrzi a hírcsatornát.

1. Ha most szeretné összecsukni a trigger részleteit, kattintson az trigger címsorára.

   ![A logikai alkalmazás alakzatának összecsukása a részletek elrejtéséhez](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres az RSS-hírcsatornában. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="add-the-send-email-action"></a>Az "e-mail küldése" művelet hozzáadása

Most adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely e-mailt küld, ha új elem jelenik meg az RSS-hírcsatornában.

1. A **hírcsatorna-elem közzétételének időpontja** alatt válassza az **új lépés**lehetőséget.

   ![Az aktiválás alatt válassza az "új lépés" lehetőséget.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. A **művelet kiválasztása** és a keresőmező területen válassza az **összes**lehetőséget.

1. A keresőmezőbe írja be a kifejezést, `send an email` hogy megkeresse a műveletet támogató összekötőket. A műveletek listából válassza ki az "e-mail küldése" műveletet a használni kívánt e-mail-szolgáltatáshoz. Ez a példa az Office 365 Outlook-összekötőt használja, amely az **E-mail küldése** művelettel rendelkezik.

   ![Válassza az "e-mail küldése" műveletet az Office 365 Outlookhoz](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Ha a műveletek listáját meghatározott alkalmazásra vagy szolgáltatásra szeretné szűkíteni, először válassza ki az adott alkalmazást vagy szolgáltatást:

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a kiválasztott e-mail-összekötő kéri az identitás hitelesítését, fejezze be ezt a lépést, és hozzon létre egy kapcsolatot a logikai alkalmazás és az e-mail-szolgáltatás között.

   > [!NOTE]
   > Ebben a konkrét példában manuálisan hitelesíti a személyazonosságát. A hitelesítést igénylő összekötők azonban különböznek az általuk támogatott hitelesítési típustól. Emellett beállíthatja, hogy miként szeretné kezelni a hitelesítést. Ha például Azure Resource Manager-sablonokat használ a központi telepítéshez, parametrizálja és javíthatja a biztonságot olyan bemeneteken, amelyeket gyakran vagy egyszerűen módosítani szeretne, például a kapcsolódási adatokat. További információt az alábbi témakörökben talál:
   >
   > * [Az üzembe helyezéshez használt sablon paraméterei](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)
   > * [Kapcsolatok hitelesítése a Logic apps üzembe helyezéséhez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Az **E-mail küldése** műveletnél adja meg az adatokat, amelyeket hozzá szeretne adni az e-mailhez.

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelési célból használhatja az e-mail-címét.

      Egyelőre hagyja figyelmen kívül a **Dinamikus tartalom hozzáadása** megjelenő listáját. Amikor valamely szerkesztőmezőbe kattint, ez a lista jelenik meg, és tartalmazza az előző lépésből átvett azon paramétereket, amelyeket bemenetként a munkafolyamatba foglalhat.

   1. A **Tárgy** mezőbe írja be ezt a szöveget egy záró üres szóközzel: `New RSS item: `

      ![A "tárgy" tulajdonságban adja meg az e-mail tárgyát](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. A **Dinamikus tartalom hozzáadása** listából válassza ki az RSS-elem címébe foglalni kívánt **Hírcsatorna címe** elemet.

      ![A dinamikus tartalom listából válassza a "hírcsatorna címe" tulajdonságot.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![Befejezett e-mail tárgy – példa a hozzáadott hírcsatorna címére](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Ha egy „For each” hurok jelenik meg a tervezőn, akkor tömböz tartozó tokent választott ki, például a **categories-item** tokent. Az ilyen tokentípusoknál a tervező automatikusan hozzáadja ezt a hurkot a tokenre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja ugyanazt a műveletet. A hurok eltávolításához válassza a hurok **címsorán lévő három** pontot (**..**.), majd válassza a **Törlés**lehetőséget.

   1. A **Törzs** mezőbe írja be a képen látható szöveget, és válassza ki a lenti tokeneket az e-mail törzséhez. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

      ![Válassza ki az e-mail szövegtörzsének tulajdonságait](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Hírcsatorna címe** | Az elem címe |
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátuma és ideje |
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címe |
      |||

1. Mentse a logikai alkalmazást.

A következő lépés a logikai alkalmazás tesztelése.

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget. Azt is megvárhatja, hogy a logikai alkalmazás ellenőrizze az RSS-hírcsatornát a megadott ütemezés szerint (percenként egyszer). Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Ha nincsenek, a logikai alkalmazás megvárja a következő esedékes időpontot, mielőtt újra elvégezné az ellenőrzést. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

Példa a logikai alkalmazás által küldött e-mailekre.

![E-mail küldése, amikor megjelenik az új RSS-hírcsatorna elem](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technikailag, amikor a trigger ellenőrzi az RSS-hírcsatornát, és megkeresi az új elemeket, az eseményindítót, és a Azure Logic Apps motor létrehoz egy példányt a logikai alkalmazás munkafolyamatában, amely futtatja a munkafolyamatban lévő műveleteket. Ha a trigger nem talál elemeket, akkor nem indul el, és „kihagyja” a munkafolyamat-példány létrehozását.

Gratulálunk, sikeresen felépítette és futtatta az első logikai alkalmazását a Azure Portal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség erre a mintára, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Az Azure fő menüjében válassza az **erőforráscsoportok**lehetőséget, majd válassza ki a logikai alkalmazás erőforráscsoportot. Az **Áttekintés** panelen válassza az **erőforráscsoport törlése**elemet.

   ![Erőforráscsoport keresése, kiválasztása és törlése](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítő ablaktábla, adja meg az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

   ![A törlés megerősítéséhez válassza a Törlés lehetőséget.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta az első logikai alkalmazását, amely RSS-frissítéseket keres a megadott ütemezés alapján (percenként), és adott műveletet végez (e-mailt küld), amikor új tartalmakat talál. Ha további ismeretekre szeretne szert tenni, folytassa ezzel az oktatóanyaggal, amely összetettebb, ütemezésalapú munkafolyamatokat hoz létre:

> [!div class="nextstepaction"]
> [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
