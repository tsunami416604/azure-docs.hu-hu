---
title: A Power Automate-ből az Azure Logic Apps-be irányuló folyamatok exportálása
description: A Power Automate-ből az Azure Logic-alkalmazásokba történő tranzakciók áttelepítése Azure Resource Manager-sablonokként történő exportálással
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428885"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Folyamatok exportálása a Power Automate-ből és üzembe helyezés az Azure Logic Appsben

A folyamat képességeinek bővítéséhez és bővítéséhez áttelepítheti a [Power Automate](https://flow.microsoft.com) szolgáltatásból az [Azure Logic Apps szolgáltatásba.](../logic-apps/logic-apps-overview.md) Exportálhatja a folyamatot egy logikai alkalmazás Azure Resource Manager-sablonjaként, üzembe helyezheti a logikai alkalmazássablont egy Azure-erőforráscsoportba, majd megnyithatja a logikai alkalmazást a Logic App Designerben.

> [!NOTE]
> Nem minden Power Automate-összekötő érhető el az Azure Logic Apps alkalmazásban. Importálhat olyan [folyamatokat,](../connectors/apis-list.md) amelyek egyenértékű összekötők az Azure Logic Apps. Például a gomb eseményindító, a jóváhagyási összekötő és az értesítési összekötő a Power Automate-re jellemző.
>
> A Power Automate programból exportált OpenAPI-alapú folyamatok jelenleg nem támogatottak logikai alkalmazássablonként történő üzembe helyezéshez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Power Automate programból exportálni kívánt folyamat

## <a name="export-a-flow"></a>Folyamat exportálása

1. Jelentkezzen be a [Power Automate](https://flow.microsoft.com) **(Saját folyamatok**) elemre, és válassza a Saját folyamatok lehetőséget. Keresse meg és válassza ki a folyamatot. Az eszköztáron jelölje ki a három pont (**...**) gombot. Válassza a Logic Apps **exportálása** > **sablon (.json) lehetőséget.**

   ![Folyamat exportálása](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Mentse a sablont a kívánt helyre.

További információ: [Grow up to Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Sablon üzembe helyezése az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiókjával.

1. Az Azure főmenüjében válassza az **Erőforrás létrehozása** lehetőséget. A keresőmezőbe írja be a "sablon központi telepítése" kifejezést. Válassza **a Sablon központi telepítését (üzembe helyezés egyéni sablonokkal)** lehetőséget, majd válassza a **Létrehozás lehetőséget.**

   ![Válassza a "Sablon központi telepítése" lehetőséget](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Az **Egyéni telepítés csoportban**válassza **a Saját sablon létrehozása lehetőséget a szerkesztőben.**

   ![Válassza a "Saját sablon létrehozása a szerkesztőben" lehetőséget](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. A **Sablon szerkesztése** eszköztáron válassza a **Fájl betöltése**lehetőséget. Keresse meg és jelölje ki a Power Automate programból exportált JSON-sablont, és válassza a **Megnyitás**gombot.

   ![Válassza a "Fájl betöltése" lehetőséget](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Miután a szerkesztő megjeleníti a JSON-t, a paramétereket és az erőforrásokat a sablonban, válassza a **Mentés gombot.**
  
   ![Sablon mentése](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Most adja meg a sablon bemeneti paramétereit:

   * Számlázáshoz használt Azure-előfizetés
   * Azure-erőforráscsoport
   * Az Azure-erőforráscsoport helye
   * A logikai alkalmazás-erőforrás neve
   * A logikai alkalmazás-erőforrás helye, ha eltér az Azure erőforráscsoporttól
   * A logikai alkalmazás által újrafelhasználhatja a korábban létrehozott kapcsolatok neve

      Ha az első logikai alkalmazást hozza létre, az összes kapcsolat újként jön létre, így elfogadhatja az alapértelmezett neveket. Ellenkező esetben megadhatja a korábban létrehozott kapcsolatok nevét, amelyeket több logikai alkalmazásban is használhat.

   Miután megadta ezeket az információkat a sablonhoz, tekintse át és fogadja el az Azure Piactér általános szerződési feltételeit a szükséges Azure-erőforrások létrehozásához és az Azure-előfizetés ennek megfelelő számlázásához, majd válassza a **Vásárlás**lehetőséget.
  
   ![A sablon bemeneti paramétereinek megadása](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Az Azure a sablont logikai alkalmazásként telepíti a megadott erőforráscsoportba. A Power Automate-ből áttelepített összes logikai alkalmazás letiltott állapotban van telepítve.

1. A logikai alkalmazás aktiválása előtt engedélyezze az új kapcsolatokat az alábbi lépésekkel:

   1. Nyissa meg a létrehozott logikai alkalmazást. A logikai alkalmazás menüjében válassza a **Logikai alkalmazástervező lehetőséget.**

      Minden hitelesítést igénylő kapcsolaton megjelenik egy figyelmeztető ikon:

      ![Figyelmeztetés ikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Minden olyan lépésnél, amelyhez engedélyezett kapcsolat szükséges, bontsa ki ezt a lépést, és válassza **az Új hozzáadása lehetőséget.**

      ![Új kapcsolat hozzáadása](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Jelentkezzen be az egyes szolgáltatásokba, vagy adja meg a kapcsolat engedélyezéséhez szükséges hitelesítő adatokat.

1. Mentse a logikai alkalmazást. Ha készen áll a logikai alkalmazás aktiválására, a logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget, majd válassza **az Engedélyezés**lehetőséget.

   ![Logikai alkalmazás engedélyezése](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Az ismétlődő munkafolyamatok futtatásának elkerülése érdekében győződjön meg arról, hogy inaktiválja vagy törli az eredeti folyamatot.

## <a name="deploy-template-by-using-visual-studio"></a>Sablon telepítése a Visual Studio használatával

Ha beállította a Visual Studio-t a logikai alkalmazások létrehozásának [előfeltételeivel,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) telepítheti az exportált sablont a Visual Studióból az Azure Logic Apps alkalmazásba.

1. A Visual Studióban nyissa meg a Power Automate programból exportált sablonfájlt.

1. A Visual Studióban hozzon létre egy Azure Resource Group projektet, és válassza ki a **Logikai alkalmazás** sablont a rövid útmutató lépései [szerint: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps - Visual Studio használatával,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)például:

   ![Azure erőforráscsoport-projekt létrehozása](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. A Solution Explorer programban nyissa meg a **LogicApp.json** fájlt, ha a fájl még nincs megnyitva.

1. Másolja a tartalmát az exportált sablonból, és írja felül a **LogicApp.json** fájl tartalmát.

1. A logikai alkalmazás üzembe helyezése előtt engedélyezze az új kapcsolatokat az alábbi lépésekkel:

   1. Nyissa meg a **LogicApp.json** helyi menüt, és válassza **a Megnyitás a Logikai alkalmazástervezővel parancsot.**

      ![Sablon megnyitása a Logic App Designer alkalmazással](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Ha a rendszer kéri, válassza ki az Azure-előfizetés és erőforráscsoport, amely a logikai alkalmazás üzembe helyezéséhez használni kívánt.

      ![Válassza ki az Azure-előfizetést és erőforráscsoportot](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Miután a logikai alkalmazás megjelenik a tervezőben, az engedélyezést igénylő kapcsolatok figyelmeztető ikonokat jelenítenek meg:

      ![Kapcsolatok figyelmeztető ikonokkal](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Minden olyan lépésnél, amelyhez engedélyezett kapcsolat szükséges, bontsa ki ezt a lépést, és válassza **az Új hozzáadása lehetőséget.**

      ![Új kapcsolat hozzáadása](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Jelentkezzen be az egyes szolgáltatásokba, vagy adja meg a kapcsolat engedélyezéséhez szükséges hitelesítő adatokat.

   1. Mentse a megoldást a logikai alkalmazás üzembe helyezése előtt.

1. A Megoldáskezelőben nyissa meg a projekt helyi menüjét, és válassza **az Új telepítése** > **New**lehetőséget. Ha szükséges, jelentkezzen be Azure-fiókjával.

1. Amikor a rendszer kéri, erősítse meg az Azure-előfizetést, az Azure-erőforráscsoportot és a központi telepítéshez használni kívánt egyéb beállításokat, például a sablonparaméter-értékek továbbításához használandó [paraméterfájlt,](../azure-resource-manager/templates/parameter-files.md) majd válassza **a Telepítés lehetőséget.**

   ![Telepítési beállítások megerősítése](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazás-erőforrás nevét az Azure-ban, és válassza a **Mentés lehetőséget.**  

   ![Telepítési paraméterek szerkesztése](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot. Példa:

   ![Kimeneti ablak](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Ha a logikai alkalmazás bármely kapcsolata szükséges a bemeneti öntől, a PowerShell ablak nyílik meg a háttérben, és kéri a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![Kapcsolatok hitelesítése](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   A központi telepítés befejezése után a logikai alkalmazás közzé, de nem aktiválódik az Azure Portalon.

1. Ha készen áll a logikai alkalmazás aktiválására az Azure Portalon, keresse meg és nyissa meg a logikai alkalmazást a Logic App Designerben. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget, majd az **Engedélyezés**lehetőséget.

1. Az ismétlődő munkafolyamatok futtatásának elkerülése érdekében győződjön meg arról, hogy inaktiválja vagy törli az eredeti folyamatot.

Ezekről a telepítési lépésekről további információt a [Gyorsútmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps alkalmazásokkal – Visual Studio című](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure) témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* További információ [az Azure Logic Apps összekötőiről](../connectors/apis-list.md)
* További információ az [Azure Logic-alkalmazásokról](../logic-apps/logic-apps-overview.md)
