---
title: Automatizált integrációs munkafolyamat létrehozása
description: Rövid útmutató – az első automatizált munkafolyamat létrehozása Azure Logic Apps használatával a rendszerintegrációs és a vállalati Application Integration (EAI) megoldásokhoz
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: c40bec80d9f61cf46221cbfe7dde80f3a7b46f6f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89658299"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Rövid útmutató: az első automatikus integrációs munkafolyamat létrehozása Azure Logic Apps-Azure Portal használatával

Ez a rövid útmutató bemutatja az első munkafolyamat létrehozásával kapcsolatos alapvető általános fogalmakat [Azure Logic apps](logic-apps-overview.md)használatával, például egy üres logikai alkalmazás létrehozását, egy trigger és egy művelet hozzáadását, majd a logikai alkalmazás tesztelését. Ebben a rövid útmutatóban olyan logikai alkalmazást fog létrehozni, amely rendszeresen ellenőrzi a webhely RSS-hírcsatornáját az új elemekhez. Ha új elemeket talál, a logikai alkalmazás mindegyikről e-mailt küld. Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Fogalmi művészet, amely a logikai alkalmazás munkafolyamatát mutatja be.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Ebben az esetben szüksége van egy Azure-előfizetésre, vagy [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), egy olyan szolgáltatásból származó e-mail-fiókra, amelyet a Azure Logic apps támogat, például Office 365 Outlook, Outlook.com vagy gmail. Más támogatott e-mail szolgáltatások esetén [itt tekintse át az összekötők listáját](/connectors/). Ebben a példában a logikai alkalmazás munkahelyi vagy iskolai fiókot használ. Ha más e-mail szolgáltatást használ, a teljes általános lépések ugyanazok, de a felhasználói felület némileg eltérő lehet.

> [!IMPORTANT]
> Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Azure Portal keresőmezőbe írja be a kifejezést `logic apps` , majd válassza a **Logic apps**lehetőséget.

   ![Képernyőfelvétel: Azure Portal keresőmező "Logic apps" kifejezéssel, és "Logic Apps" a kiválasztott keresési eredményként.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. A **Logic apps** lapon válassza a **Hozzáadás**lehetőséget.

   ![Képernyőfelvétel: a Logic apps-lista és a kijelölt gomb, "Hozzáadás".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a logikai alkalmazás részleteit az alábbi ábrán látható módon.

   ![Képernyőfelvétel: a logikai alkalmazás létrehozási panelje, amely az új logikai alkalmazás részleteit tartalmazza.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*logikai alkalmazás neve*> | A logikai alkalmazás neve, amelynek egyedinek kell lennie a régiók között, és csak betűket, számokat, kötőjeleket ( `-` ), aláhúzást ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a "My-First-Logic-app" kifejezést használja. |
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport-név*> | Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md)neve, amelynek egyedinek kell lennie a régiók között, és a kapcsolódó erőforrások rendszerezésére szolgál. Ez a példa a "My-First-LA-RG" kifejezést használja. |
   | **Hely** | <*Azure-régió*> | A logikai alkalmazás adatainak tárolására szolgáló régió. Ez a példa a "West US"-t használja. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás**lehetőséget. Erősítse meg a megadott adatokat, és válassza a **Létrehozás**lehetőséget.

1. Miután az Azure sikeresen üzembe helyezte az alkalmazást, válassza az **Ugrás az erőforráshoz**lehetőséget.

   ![Képernyőfelvétel: erőforrás-telepítési oldal és kijelölt gomb a "Ugrás az erőforráshoz" gombra.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Képernyőfelvétel: Logic Apps Designer template Gallery és a kiválasztott sablon "üres logikai alkalmazás".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy új RSS-hírcsatornaelem megjelenésekor aktiválódik. Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Azure Logic Apps motor minden alkalommal elindít egy logikai alkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Az RSS-trigger hozzáadása

1. A **Logic app Designerben**a keresőmező alatt válassza az **összes**lehetőséget.

1. Az RSS-összekötő megtalálásához a keresőmezőbe írja be a következőt: `rss` . Az eseményindítók listából válassza ki az RSS-eseményindítót **egy hírcsatorna-elem közzétételekor**.

   ![Képernyőfelvétel: Logic Apps Designer "RSS" a keresőmezőbe és a kiválasztott trigger "a hírcsatorna-elem közzétételekor".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Adja meg az trigger adatait az ebben a lépésben leírtak szerint:

   ![Képernyőfelvétel: Logic Apps Designer RSS-triggeri beállításokkal, beleértve az RSS URL-címet, a gyakoriságot és az intervallumot.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Az RSS-hírcsatorna URL-címe** | <*RSS-hírcsatorna – URL*> | A figyelni kívánt RSS-hírcsatorna hivatkozása. Ez a példa a Wall Street Journal RSS-hírcsatornáját használja `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` , de ha szeretné, használhatja a saját RSS-hírcsatorna URL-címét. |
   | **Intervallum** | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Gyakoriság** | Minute | Az ellenőrzések közötti intervallumok időegysége |
   ||||

   Az intervallum és a gyakoriság együtt határozza meg a logikai alkalmazás eseményindítójának ütemezését. Ez a logikai alkalmazás percenként ellenőrzi a hírcsatornát.

1. Ha most szeretné összecsukni a trigger részleteit, kattintson az trigger címsorára.

   ![Képernyőfelvétel: Logic Apps Designer összecsukott logikai alkalmazás alakzatával.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres az RSS-hírcsatornában. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="add-the-send-email-action"></a>Az "e-mail küldése" művelet hozzáadása

Most adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely e-mailt küld, ha új elem jelenik meg az RSS-hírcsatornában.

1. A **hírcsatorna-elem közzétételének időpontja** alatt válassza az **új lépés**lehetőséget.

   ![Képernyőfelvétel: Logic Apps Designer "új lépés".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. A **művelet kiválasztása** és a keresőmező területen válassza az **összes**lehetőséget.

1. A keresőmezőbe írja be a kifejezést, `send an email` hogy megtalálja a műveletet támogató összekötőket. Ha szűrni szeretné a műveletek listáját egy adott alkalmazásra vagy szolgáltatásra, először kiválaszthatja az alkalmazást vagy a szolgáltatást.

   Ha például Microsoft munkahelyi vagy iskolai fiókot használ, és az Office 365 Outlookot szeretné használni, válassza az **office 365 Outlook**lehetőséget. Ha személyes Microsoft-fiók használ, a Outlook.com is kiválaszthatja. Ez a példa az Office 365 Outlookot folytatja:

   ![Képernyőfelvétel: Logic Apps Designer és a kiválasztott Office 365 Outlook Connector.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Így könnyebben megtalálhatja és kiválaszthatja a használni kívánt műveletet, például `send an email` :

   ![Képernyőfelvétel: Logic Apps Designer és lista szűrt műveletekkel.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Ha a kiválasztott e-mail-összekötő kéri az identitás hitelesítését, fejezze be ezt a lépést, és hozzon létre egy kapcsolatot a logikai alkalmazás és az e-mail-szolgáltatás között.

   > [!NOTE]
   > Ebben a konkrét példában manuálisan hitelesíti a személyazonosságát. A hitelesítést igénylő összekötők azonban különböznek az általuk támogatott hitelesítési típustól. Emellett beállíthatja, hogy miként szeretné kezelni a hitelesítést. Ha például Azure Resource Manager-sablonokat használ a központi telepítéshez, parametrizálja és javíthatja a biztonságot olyan bemeneteken, amelyeket gyakran vagy egyszerűen módosítani szeretne, például a kapcsolódási adatokat. További információt az alábbi témakörökben talál:
   >
   > * [Az üzembe helyezéshez használt sablon paraméterei](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)
   > * [Kapcsolatok hitelesítése a Logic apps üzembe helyezéséhez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Az **E-mail küldése** műveletben adja meg az e-mailben szerepeltetni kívánt adatokat.

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelési célból használhatja az e-mail-címét.

      Egyelőre hagyja figyelmen kívül a **Dinamikus tartalom hozzáadása** megjelenő listáját. Ha rákattint néhány szerkesztési mezőre, ez a lista jelenik meg, és megjeleníti az előző lépésből származó összes elérhető kimenetet, amelyet bemenetként használhat az aktuális művelethez.

   1. A **Tárgy** mezőbe írja be ezt a szöveget egy záró üres szóközzel: `New RSS item: `

      ![Képernyőfelvétel: Logic Apps Designer és "e-mail küldése" művelet és kurzor a "tárgy" tulajdonságban.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. A **dinamikus tartalom hozzáadása** listáról válassza ki a **hírcsatorna címe**elemet, amely az triggerből származó kimenet, "a hírcsatorna-elem közzétételekor", amely lehetővé teszi, hogy az RSS-elem címe elérhető legyen a használatra.

      ![Képernyőfelvétel: Logic Apps Designer az "e-mail küldése" művelettel és a kurzort a "tárgy" tulajdonságban egy nyílt dinamikus tartalmú listával és a kiválasztott kimenettel, "hírcsatorna címével".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Ha a dinamikus tartalmak listájában nem jelenik meg kimenet a "hírcsatorna-elem közzétételekor" triggernél, a művelet fejléce mellett válassza a **továbbiak**lehetőséget.
      > 
      > ![Képernyőfelvétel: Logic Apps Designer megnyitása a dinamikus tartalmak listájáról, és a "továbbiak" lehetőség van kiválasztva az triggerhez.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![Képernyőfelvétel: Logic Apps Designer az "e-mail küldése" művelettel, valamint egy példaként szolgáló e-mail-cím, amely tartalmazza a "hírcsatorna címe" tulajdonságot.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Ha egy „For each” hurok jelenik meg a tervezőn, akkor tömböz tartozó tokent választott ki, például a **categories-item** tokent. Az ilyen tokentípusoknál a tervező automatikusan hozzáadja ezt a hurkot a tokenre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja ugyanazt a műveletet. A hurok eltávolításához válassza a hurok **címsorán lévő három** pontot (**..**.), majd válassza a **Törlés**lehetőséget.

   1. A **Törzs** mezőbe írja be a képen látható szöveget, és válassza ki a lenti tokeneket az e-mail törzséhez. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

      ![Képernyőfelvétel: Logic Apps Designer az "e-mail küldése" művelettel és a kijelölt tulajdonságok a "törzs" mezőben.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Hírcsatorna címe** | Az elem címe |
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátuma és ideje |
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címe |
      |||

1. Mentse a logikai alkalmazást.

A következő lépés a logikai alkalmazás tesztelése.

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget. Azt is megvárhatja, hogy a logikai alkalmazás ellenőrizze az RSS-hírcsatornát a megadott ütemezés szerint (percenként egyszer).

![Képernyőfelvétel: Logic Apps Designer a tervező eszköztáron kiválasztott "Futtatás" gombbal.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Ha nincsenek, a logikai alkalmazás megvárja a következő esedékes időpontot, mielőtt újra elvégezné az ellenőrzést. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

Példa a logikai alkalmazás által küldött e-mailekre.

![Képernyőfelvétel: az új RSS-hírcsatorna megjelenésekor kapott minta e-mailek megjelenítése.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technikailag, amikor a trigger ellenőrzi az RSS-hírcsatornát, és megkeresi az új elemeket, az eseményindítót, és a Azure Logic Apps motor létrehoz egy példányt a logikai alkalmazás munkafolyamatában, amely futtatja a munkafolyamatban lévő műveleteket. Ha a trigger nem talál elemeket, akkor nem indul el, és „kihagyja” a munkafolyamat-példány létrehozását.

Gratulálunk, sikeresen felépítette és futtatta az első logikai alkalmazását a Azure Portal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség erre a mintára, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Az Azure Search mezőbe írja be a kifejezést `resource groups` , majd válassza az **erőforráscsoportok**lehetőséget.

   ![A "erőforráscsoportok" keresési kifejezéssel rendelkező Azure Portal keresőmezőt ábrázoló képernyőkép.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Keresse meg és válassza ki a logikai alkalmazáshoz tartozó erőforráscsoportot. Az **Áttekintés** panelen válassza az **erőforráscsoport törlése**elemet.

   ![Képernyőfelvétel: Azure Portal a kiválasztott erőforráscsoporthoz és a "erőforráscsoport törlése" gombra kattintva.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítő ablaktábla, adja meg az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

   ![Képernyőfelvétel: Azure Portal a megerősítő ablaktáblával, és a törlendő erőforráscsoport-név.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta az első logikai alkalmazását, amely RSS-frissítéseket keres a megadott ütemezés alapján (percenként), és adott műveletet végez (e-mailt küld), amikor új tartalmakat talál. Ha további ismeretekre szeretne szert tenni, folytassa ezzel az oktatóanyaggal, amely összetettebb, ütemezésalapú munkafolyamatokat hoz létre:

> [!div class="nextstepaction"]
> [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
