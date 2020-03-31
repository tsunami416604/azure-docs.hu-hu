---
title: Az első automatizált munkafolyamat létrehozása
description: Rövid útmutató – Az azure Logic Apps rendszerintegrációs és vállalati alkalmazásintegrációs (EAI) megoldásaisegítségével megépítheti az első automatizált munkafolyamatot
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: c2a26f2f40e0312fbfa0962e69ddfd0cfdabad5f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191283"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Rövid útmutató: Az első munkafolyamat létrehozása az Azure Logic Apps használatával – Azure Portal

Ez a rövid útmutató bemutatja az azure [Logic Apps](../logic-apps/logic-apps-overview.md)használatával az első munkafolyamat létrehozásának alapvető általános fogalmait, például egy üres logikai alkalmazás létrehozását, egy eseményindító és egy művelet hozzáadását, majd a logikai alkalmazás tesztelését. Ebben a rövid útmutatóban egy logikai alkalmazást hoz létre, amely rendszeresen ellenőrzi a webhely RSS-hírcsatornáját az új elemekért. Ha új elemeket talál, a logikai alkalmazás mindegyikről e-mailt küld. Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű példalogikai alkalmazás-munkafolyamat](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Ebben a forgatókönyvben az Azure Logic Apps által támogatott szolgáltatásból, például az Office 365 Outlook, a Outlook.com vagy a Gmail által támogatott szolgáltatásból kell e-mail-fiókot. Más támogatott e-mail szolgáltatások esetén [tekintse át az összekötők listáját itt.](https://docs.microsoft.com/connectors/) Ebben a példában a logikai alkalmazás az Office 365 Outlook ot használja. Ha másik e-mail szolgáltatást használ, az általános lépések megegyeznek, de a felhasználói felület némileg eltérhet.

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure kezdőlapján a keresőmezőben keresse meg és válassza a **Logikai alkalmazások lehetőséget.**

   ![A "Logic Apps" megkeresése és kiválasztása](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. A **Logic Apps (Logikai alkalmazások)** lapon válassza a **Hozzáadás gombot.**

   ![Új logikai alkalmazás hozzáadása](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. A **Logikai alkalmazás** ablaktáblán adja meg a logikai alkalmazás részleteit az alábbiak szerint. Miután elkészült, válassza a **Létrehozás gombot.**

   ![Az új logikai alkalmazás részleteinek biztosítása](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*logikai alkalmazás neve*> | A logikai alkalmazás neve, amely csak betűket,`-`számokat,`_`kötőjeleket (`(`), `)`aláhúzásjeleket`.`( ), zárójeleket ( , és pontokat ( tartalmazhat. Ez a példa a "My-First-Logic-App" (Saját első logikai alkalmazás) alkalmazást használja. |
   | **Előfizetés** | <*Azure-előfizetés-név*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-erőforrás-csoport név*> | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) neve. Ez a példa a "My-First-LA-RG" értéket használja. |
   | **Helyen** | <*Azure-régió*> | Az a régió, ahol a logikai alkalmazás adatait tárolhatja. Ez a példa a "West USA" (USA nyugati) |
   | **Log Analytics** | Ki | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztárán válassza **az Értesítések** > **ugrás az erőforráshoz** lehetőséget az üzembe helyezett logikai alkalmazáshoz.

   ![Ugrás az újonnan létrehozott logikai alkalmazás-erőforrásra](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Vagy megkeresheti és kiválaszthatja a logikai alkalmazást a név beírásával a keresőmezőbe.

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres sablon kijelölése a logikai alkalmazáshoz](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy új RSS-hírcsatornaelem megjelenésekor aktiválódik. Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. Minden alkalommal, amikor az eseményindító aktiválódik, az Azure Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, amely elindítja és futtatja a munkafolyamatot.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Az RSS-eseményindító hozzáadása

1. A **Logikai alkalmazástervező**csoport keresőmezőjében válassza az **Összes**lehetőséget.

1. A keresőmezőbe írja `rss` be az RSS-összekötő megkereséséhez. Az eseményindítók listájában válassza a **Hírcsatorna-elem közzétételekor i.**

   ![Válassza a "Hírcsatorna-elem közzétételekor" eseményindító lehetőséget.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Adja meg ezt az információt az eseményindítóhoz az itt látható és leírt módon:

   ![Eseményindító beállítása RSS-hírcsatornával, gyakorisággal és időközzel](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Az RSS-hírcsatorna URL-címe** | `http://feeds.reuters.com/reuters/topNews` | A monitorozni kívánt RSS-hírcsatornára mutató hivatkozás |
   | **Intervallum** | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Frekvencia** | Perc | Az ellenőrzések közötti intervallumok időegysége  |
   ||||

   Az intervallum és a gyakoriság együtt határozza meg a logikai alkalmazás eseményindítójának ütemezését. Ez a logikai alkalmazás percenként ellenőrzi a hírcsatornát.

1. Az eseményindító részleteinek összecsukásához kattintson az eseményindító címsorára.

   ![Logikai alkalmazásalakzat összecsukása a részletek elrejtéséhez](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres az RSS-hírcsatornában. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="add-the-send-email-action"></a>Az "e-mail küldése" művelet hozzáadása

Most adjon hozzá egy [műveletet,](../logic-apps/logic-apps-overview.md#logic-app-concepts) amely e-mailt küld, amikor egy új elem jelenik meg az RSS-hírcsatornában.

1. A **Hírcsatorna-elem közzétételekor** eseményindító esetén válassza az **Új lépés**lehetőséget.

   ![Az eseményindító alatt válassza az "Új lépés" lehetőséget.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. A Művelet kiválasztása és a keresőmező **csoportban** válassza az **Összes**lehetőséget.

1. A keresőmezőbe írja `send an email` be a műveletet kínáló összekötők megkereséséhez. A műveletek listájában válassza ki a használni kívánt e-mail szolgáltatás "e-mail küldése" műveletét. Ez a példa az Office 365 Outlook-összekötőt használja, amely az **E-mail küldése** műveletet használja.

   ![Válassza az "E-mail küldése" műveletet az Office 365 Outlookban](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Ha a műveletek listáját meghatározott alkalmazásra vagy szolgáltatásra szeretné szűkíteni, először válassza ki az adott alkalmazást vagy szolgáltatást:

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a kiválasztott e-mail-összekötő kéri, hogy hitelesítse az identitását, hajtsa végre ezt a lépést most a logikai alkalmazás és az e-mail szolgáltatás közötti kapcsolat létrehozásához.

   > [!NOTE]
   > Ebben a konkrét példában manuálisan hitelesíti az identitást. A hitelesítést igénylő összekötők azonban különböznek az általuk támogatott hitelesítési típusokban. A hitelesítés kezelésének módját is beállíthatja. Ha például az Azure Resource Manager-sablonokat használja a központi telepítéshez, paraméterezheti és javíthatja a gyakran vagy egyszerűen módosítani kívánt bemenetek biztonságát, például a kapcsolatadatait. További információt az alábbi témakörökben talál:
   >
   > * [Sablonparaméterek a telepítéshez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)
   > * [A logikai alkalmazások központi telepítéséhez szükséges kapcsolatok hitelesítése](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Az **E-mail küldése** műveletnél adja meg az adatokat, amelyeket hozzá szeretne adni az e-mailhez.

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelési célokra használhatja az e-mail címét.

      Egyelőre hagyja figyelmen kívül a **Dinamikus tartalom hozzáadása** megjelenő listáját. Amikor valamely szerkesztőmezőbe kattint, ez a lista jelenik meg, és tartalmazza az előző lépésből átvett azon paramétereket, amelyeket bemenetként a munkafolyamatba foglalhat.

   1. A **Tárgy** mezőbe írja be ezt a szöveget egy záró üres szóközzel: `New RSS item: `

      ![A "Tárgy" tulajdonságban adja meg az e-mail tárgyát](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. A **Dinamikus tartalom hozzáadása** listából válassza ki az RSS-elem címébe foglalni kívánt **Hírcsatorna címe** elemet.

      ![A dinamikus tartalomlistából válassza a "Hírcsatorna címe" tulajdonságot](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![Kész e-mail tárgy például a hozzáadott feed cím](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Ha egy „For each” hurok jelenik meg a tervezőn, akkor tömböz tartozó tokent választott ki, például a **categories-item** tokent. Az ilyen tokentípusoknál a tervező automatikusan hozzáadja ezt a hurkot a tokenre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja ugyanazt a műveletet. A hurok eltávolításához jelölje ki a **három pontot** (**...**) a ciklus címsorában, majd kattintson a **Törlés gombra.**

   1. A **Törzs** mezőbe írja be a képen látható szöveget, és válassza ki a lenti tokeneket az e-mail törzséhez. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

      ![Az e-mail törzstartalmának tulajdonságainak kiválasztása](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Hírcsatorna címe** | Az elem címe |
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátuma és ideje |
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címe |
      |||

1. Mentse a logikai alkalmazást.

A következő lépés a logikai alkalmazás tesztelése.

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárának futtatása lehetőségre kattintson a **Futtatás gombra.** Azt is megvárhatja, hogy a logikai alkalmazás ellenőrizze az RSS-hírcsatornát a megadott ütemezés szerint (percenként egyszer). Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Ha nincsenek, a logikai alkalmazás megvárja a következő esedékes időpontot, mielőtt újra elvégezné az ellenőrzést. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

Példa a logikai alkalmazás által küldött e-mailekre.

![Minta e-mail küldése új RSS-hírcsatorna-elem megtervezésekor](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technikailag, amikor az eseményindító ellenőrzi az RSS-hírcsatornát, és új elemeket talál, az eseményindító aktiválódik, és az Azure Logic Apps motor létrehoz egy példányt a logikai alkalmazás munkafolyamat, amely futtatja a munkafolyamatban végrehajtott műveleteket. Ha a trigger nem talál elemeket, akkor nem indul el, és „kihagyja” a munkafolyamat-példány létrehozását.

Gratulálunk, most már sikeresen megépítette és futtatta az első logikai alkalmazást az Azure Portalon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség erre a mintára, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Az Azure főmenüjében válassza az **Erőforráscsoportok**lehetőséget, majd válassza ki a logikai alkalmazás erőforráscsoportját. Az **Áttekintő** ablaktáblán válassza az **Erőforráscsoport törlése**lehetőséget.

   ![Erőforráscsoport keresése, kijelölése és törlése](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítési ablaktábla, adja meg az erőforráscsoport nevét, és válassza a **Törlés gombot.**

   ![A törlés megerősítéséhez válassza a "Törlés" lehetőséget.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta az első logikai alkalmazását, amely RSS-frissítéseket keres a megadott ütemezés alapján (percenként), és adott műveletet végez (e-mailt küld), amikor új tartalmakat talál. Ha további ismeretekre szeretne szert tenni, folytassa ezzel az oktatóanyaggal, amely összetettebb, ütemezésalapú munkafolyamatokat hoz létre:

> [!div class="nextstepaction"]
> [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
