---
title: Folyamatok exportálása Microsoft Flowról Azure Logic Apps
description: Áttelepíti a folyamatokat a Microsoft Flowról a Azure Logic Appsra Azure Resource Manager sablonokkal való exportálással
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 82c4e55eff36a7da70e0304fc8152491a8030e04
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441012"
---
# <a name="export-flows-from-microsoft-flow-and-deploy-to-azure-logic-apps"></a>Folyamatok exportálása a Microsoft Flow-ból és üzembe helyezés az Azure Logic Appsben

A flow képességeinek bővítéséhez és kibővítéséhez áttelepítheti a folyamatot [](https://flow.microsoft.com) Microsoft Flowról [Azure Logic Appsre](../logic-apps/logic-apps-overview.md). Exportálhatja a folyamatot Azure Resource Manager sablonként egy logikai alkalmazáshoz, üzembe helyezheti a logikai alkalmazás sablonját egy Azure-erőforráscsoporthoz, majd megnyithatja a logikai alkalmazást a Logic app Designerben.

> [!NOTE]
> Nem minden Microsoft Flow összekötő érhető el Azure Logic Appsban. Olyan folyamatokat is importálhat, amelyek Azure Logic Appsban [egyenértékű összekötővel](../connectors/apis-list.md) rendelkeznek. Például a gomb trigger, a jóváhagyási összekötő és az értesítési összekötő a Microsoft Flowra vonatkozik.
>
> A Microsoft Flowból exportált OpenAPI-alapú folyamatok jelenleg nem támogatottak a Logic app-sablonokként való üzembe helyezéshez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az Microsoft Flowból exportálni kívánt folyamat

## <a name="export-a-flow"></a>Folyamat exportálása

1. Jelentkezzen be [Microsoft Flowba](https://flow.microsoft.com), és válassza **a saját folyamatok**lehetőséget. Keresse meg és válassza ki a folyamatot. Az eszköztáron kattintson az ellipszisek ( **..** .) gombra. Válassza az **Exportálás** > **Logic apps sablon (. JSON)** lehetőséget.

   ![Folyamat exportálása](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Mentse a sablont a kívánt helyre.

További információ: [Grow up to Azure Logic apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Sablon üzembe helyezése a Azure Portal használatával

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) Azure-fiókjával.

1. Az Azure főmenüjében válassza az **Erőforrás létrehozása** lehetőséget. A keresőmezőbe írja be a "sablon központi telepítése" kifejezést. Válassza a **template Deployment (üzembe helyezés egyéni sablonok használatával)** lehetőséget, majd válassza a **Létrehozás**lehetőséget.

   ![Válassza a "Template deployment" lehetőséget](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Az **Egyéni telepítés**területen válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

   ![Válassza a "saját sablon létrehozása a szerkesztőben" lehetőséget.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. A **Sablon szerkesztése** eszköztárban válassza a **fájl betöltése**lehetőséget. Keresse meg és válassza ki a Microsoft Flowból exportált JSON-sablont, majd kattintson a **Megnyitás**gombra.

   ![Válassza a "fájl betöltése" lehetőséget.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Miután a szerkesztő megjeleníti a sablonban található JSON-, paramétereket és erőforrásokat, válassza a **Mentés**lehetőséget.
  
   ![Sablon mentése](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Most adja meg a következő bemeneti paramétereket a sablonhoz:

   * A számlázáshoz használandó Azure-előfizetés
   * Azure-erőforráscsoport
   * Az Azure-erőforráscsoport helye
   * A logikai alkalmazás erőforrásának neve
   * A logikai alkalmazás erőforrásának helye, ha eltér az Azure-erőforráscsoport
   * A logikai alkalmazás által újra felhasználható korábban létrehozott kapcsolatok neve

      Az első logikai alkalmazás létrehozásakor minden kapcsolat új néven jön létre, így elfogadhatja az alapértelmezett neveket. Ellenkező esetben megadhatja a korábban létrehozott kapcsolatok nevét, amelyeket több logikai alkalmazás között használhat.

   Miután megadta ezeket az információkat a sablonhoz, tekintse át és fogadja el az Azure Marketplace használati feltételeit, hogy meghozza a szükséges Azure-erőforrásokat, és az Azure-előfizetését ennek megfelelően számlázza, majd válassza a **vásárlás**lehetőséget.
  
   ![Adja meg a sablon bemeneti paramétereit](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Az Azure a sablont logikai alkalmazásként telepíti a megadott erőforráscsoporthoz. Az Microsoft Flowról áttelepített összes logikai alkalmazás letiltott állapotban van.

1. A logikai alkalmazás aktiválása előtt engedélyezze a következő lépéseket az új kapcsolatok engedélyezéséhez:

   1. Nyissa meg a létrehozott logikai alkalmazást. A logikai alkalmazás menüjében válassza a **Logic app Designer**elemet.

      Az engedélyezést igénylő minden egyes kapcsolaton figyelmeztető ikon jelenik meg:

      ![Figyelmeztető ikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Minden olyan lépéshez, amelyhez engedélyezve van a kapcsolat, bontsa ki ezt a lépést, és válassza az **új hozzáadása**lehetőséget.

      ![Új kapcsolatok hozzáadása](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Jelentkezzen be az egyes szolgáltatásokhoz, vagy adja meg a szükséges hitelesítő adatokat a kapcsolódás engedélyezéséhez.

1. Mentse a logikai alkalmazást. Ha készen áll a logikai alkalmazás aktiválására, a logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget, majd válassza az **Engedélyezés**lehetőséget.

   ![Logikai alkalmazás engedélyezése](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Az ismétlődő munkafolyamatok futtatásának elkerüléséhez győződjön meg arról, hogy az eredeti folyamatot inaktiválja vagy törli.

## <a name="deploy-template-by-using-visual-studio"></a>Sablon üzembe helyezése a Visual Studióval

Ha a Visual studiót a Logic apps létrehozásához szükséges előfeltételekkel állította be, az exportált sablont a Visual studióból is üzembe helyezheti Azure Logic Apps. [](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)

1. A Visual Studióban nyissa meg a Microsoft Flowból exportált sablonfájlt.

1. A Visual Studióban hozzon létre egy Azure erőforráscsoport-projektet, és válassza ki a **logikai alkalmazás** sablonját [a gyors útmutató lépéseinek követésével: Hozzon létre automatizált feladatokat, folyamatokat és munkafolyamatokat Azure Logic apps-](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)Visual Studióval, például:

   ![Azure erőforráscsoport-projekt létrehozása](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. A Megoldáskezelőban nyissa meg a **LogicApp. JSON** fájlt, ha a fájl még nincs megnyitva.

1. Másolja a tartalmat az exportált sablonból, és írja felül a **LogicApp. JSON** fájl tartalmát.

1. A logikai alkalmazás üzembe helyezése előtt engedélyezze a következő lépéseket az új kapcsolatok engedélyezéséhez:

   1. Nyissa meg a **LogicApp. JSON** helyi menüt, majd válassza a **Megnyitás a Logic app Designerben**lehetőséget.

      ![Sablon megnyitása a Logic app Designerrel](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Ha a rendszer kéri, válassza ki azt az Azure-előfizetést és erőforráscsoportot, amelyet a logikai alkalmazás üzembe helyezéséhez használni kíván.

      ![Válassza ki az Azure-előfizetést és az erőforráscsoportot](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Miután a logikai alkalmazás megjelenik a tervezőben, az engedélyezést igénylő összes kapcsolat figyelmeztető ikonokat jelenít meg:

      ![Figyelmeztető ikonnal rendelkező kapcsolatok](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Minden olyan lépéshez, amelyhez engedélyezve van a kapcsolat, bontsa ki ezt a lépést, és válassza az **új hozzáadása**lehetőséget.

      ![Új kapcsolatok hozzáadása](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Jelentkezzen be az egyes szolgáltatásokhoz, vagy adja meg a szükséges hitelesítő adatokat a kapcsolódás engedélyezéséhez.

   1. Mentse a megoldást a logikai alkalmazás üzembe helyezése előtt.

1. A Megoldáskezelőban nyissa meg a projekt helyi menüt, és válassza az**új** **telepítése** > lehetőséget. Ha szükséges, jelentkezzen be Azure-fiókjával.

1. Amikor a rendszer kéri, erősítse meg az Azure-előfizetést, az Azure-erőforráscsoportot, valamint az üzembe helyezéshez használni kívánt egyéb beállításokat, például a sablon paramétereinek átadásához használandó [paramétereket](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) , majd válassza a **telepítés**lehetőséget.

   ![Központi telepítési beállítások megerősítése](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazás erőforrásának nevét az Azure-ban, majd válassza a **Mentés**lehetőséget.  

   ![Telepítési paraméterek szerkesztése](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot. Példa:

   ![Kimeneti ablak](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Ha a logikai alkalmazásban lévő bármelyik kapcsolatnak meg kell adnia a bemenetét, a háttérben megnyílik egy PowerShell-ablak, és megkérdezi a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![Kapcsolatok hitelesítése](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Az üzembe helyezés befejeződése után a logikai alkalmazás közzé van téve, de nincs aktiválva a Azure Portal.

1. Ha készen áll a logikai alkalmazás aktiválására a Azure Portalban, keresse meg és nyissa meg a logikai alkalmazást a Logic app Designerben. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget, majd válassza az **Engedélyezés**lehetőséget.

1. Az ismétlődő munkafolyamatok futtatásának elkerüléséhez győződjön meg arról, hogy az eredeti folyamatot inaktiválja vagy törli.

További információ ezekről a telepítési lépésekről: [gyors útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása a Azure Logic Apps-Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>További lépések

* További információ az [Azure Logic apps-összekötők számára](../connectors/apis-list.md)
* További információ a [Azure Logic apps](../logic-apps/logic-apps-overview.md)
