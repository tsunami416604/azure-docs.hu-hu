---
title: Logikai alkalmazások kezelése a Visual Studio használatával – Azure Logic Apps
description: Logic apps és más Azure-eszközök kezelése a Visual Studio Cloud Explorerben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: dd6cd16302c69266a954816868c04c8507762717
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801272"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logikai alkalmazások kezelése a Visual Studióval

Habár logikai alkalmazásokat hozhat létre, szerkeszthet, kezelhet és helyezhet üzembe a [Azure Portalban](https://portal.azure.com), a Visual studiót is használhatja, ha a logikai alkalmazásokat hozzá kívánja adni a verziókövetéshoz, különböző verziókat tesz közzé, és [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) sablonokat hoz létre a következőhöz: különböző üzembe helyezési környezetek. A Visual Studio Cloud Explorer segítségével megkeresheti és kezelheti a logikai alkalmazásait más Azure-erőforrásokkal együtt. Például megnyithatja, letöltheti, szerkesztheti, futtathatja, megtekintheti a futtatási előzményeket, letilthatja és engedélyezheti a Azure Portalban már üzembe helyezett Logic apps-alkalmazásokat. Ha még nem ismeri a Azure Logic Apps használatát a Visual Studióban, Ismerje meg, [hogyan hozhat létre logikai alkalmazásokat a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> A Logic apps üzembe helyezése vagy közzététele a Visual Studióban felülírja az alkalmazás verzióját a Azure Portalban. Ha tehát a megtartani kívánt Azure Portal módosításokat hajt végre, akkor a Visual studióból a következő üzembe helyezéskor vagy a Visual studióból való közzététel előtt [frissítse a logikai alkalmazást](#refresh) a Azure Portal.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket: 

  * [Visual Studio 2019, 2017 vagy 2015 – Community Edition vagy újabb](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > A Visual Studio 2019-es vagy 2017-es vagy-es telepítésekor ügyeljen arra, hogy kiválassza az **Azure-fejlesztési** számítási feladatot.
    > További információ: [Az Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorerben](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    A Cloud Explorer for Visual Studio 2015 telepítéséhez [töltse le a Cloud Explorer alkalmazást a Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    További információ: [Az Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorerben (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps a Visual Studio-verzióhoz használni kívánt eszközöket:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Hozzáférés a világhálóhoz a Embedded Logic Apps Designer használatával

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. 
  Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Logikai alkalmazások keresése

A Visual Studióban megtalálhatja az Azure-előfizetéshez társított összes logikai alkalmazást, és a Azure Portal a Cloud Explorer használatával telepítheti azokat.

1. Nyissa meg a Visual Studiót. A **nézet** menüben válassza a **Cloud Explorer**lehetőséget.

1. A Cloud Explorerben válassza a **fiókok kezelése**lehetőséget. Válassza ki a logikai alkalmazásokhoz társított Azure-előfizetést, majd válassza az **alkalmaz**lehetőséget. Példa:

   ![A "Fiókkezelés" kiválasztása](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Attól függően, hogy **erőforráscsoportok** vagy **erőforrástípusok**alapján keres, kövesse az alábbi lépéseket:

   * **Erőforráscsoportok**: Az Azure-előfizetése alatt a Cloud Explorer megjeleníti az adott előfizetéshez társított összes erőforráscsoportot. 
   Bontsa ki a logikai alkalmazást tartalmazó erőforráscsoportot, majd válassza ki a logikai alkalmazást.

   * **Erőforrástípusok**: Az Azure-előfizetése alatt bontsa ki a **Logic apps**elemet. Miután a Cloud Explorer megjeleníti az előfizetéshez társított összes telepített logikai alkalmazást, válassza ki a logikai alkalmazást.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Megnyitás Visual Studio-ban

A Visual Studióban megnyithatja a korábban létrehozott és üzembe helyezett Logic apps-alkalmazásokat közvetlenül a Azure Portal vagy Azure Resource Group-projektekkel a Visual Studióval.

1. Nyissa meg a Cloud Explorert, és keresse meg a logikai alkalmazást. 

1. A logikai alkalmazás helyi menüjében válassza a **Megnyitás a Logic app Editor**használatával lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   Ez a példa az erőforrástípus alapján mutatja be a Logic Apps szolgáltatást, így a logikai alkalmazások a **Logic apps** szakaszban jelennek meg.

   ![Üzembe helyezett logikai alkalmazás megnyitása Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Miután a logikai alkalmazás megnyílik Logic Apps Designerben, a tervező alján kiválaszthatja a **kód nézetet** , hogy áttekintse az alapul szolgáló logikai alkalmazás definíciós struktúráját. 
   Ha a logikai alkalmazáshoz központi telepítési sablont szeretne létrehozni, Ismerje meg, [hogyan tölthet le egy Azure Resource Manager sablont](#download-logic-app) a logikai alkalmazáshoz. További információ a [Resource Manager-sablonokról](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Letöltés az Azure-ból

A logikai alkalmazásokat letöltheti a [Azure Portalból](https://portal.azure.com) , és mentheti őket [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) sablonként. Ezután helyileg szerkesztheti a sablonokat a Visual Studióval, és testreszabhatja a logikai alkalmazásokat a különböző üzembe helyezési környezetekhez. A Logic apps letöltése automatikusan *felparaméterezi* a definíciókat a [Resource Manager-sablonokban](../azure-resource-manager/template-deployment-overview.md), amely a JavaScript Object Notation (JSON) is használja.

1. A Visual Studióban nyissa meg a Cloud Explorer alkalmazást, majd keresse meg és válassza ki az Azure-ból letölteni kívánt logikai alkalmazást.

2. Az alkalmazás helyi menüjében válassza **a Megnyitás a Logic app Editor**használatával lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   Megnyílik a Logic app Designer, és megjeleníti a logikai alkalmazást. 
   A logikai alkalmazás alapjául szolgáló definíció és struktúra áttekintéséhez kattintson a tervező alján található **kód nézet**elemre. 

3. A tervező eszköztárán válassza a **Letöltés**lehetőséget.

   ![Válassza a "Letöltés" lehetőséget.](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Amikor a rendszer megkéri a helyet, keresse meg a helyet, és mentse a logikai alkalmazás definíciójának Resource Manager-sablonját JSON-formátumban (. JSON). 

A logikai alkalmazás definíciója a Resource `resources` Manager-sablon alszakaszában jelenik meg. Most már szerkesztheti a Logic app-definíciót és a Resource Manager-sablont a Visual Studióval. A sablont [Azure erőforráscsoport-projektként](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) is hozzáadhatja egy Visual Studio-megoldáshoz. Ismerkedjen meg a [Visual Studióban található Logic apps-projektekkel kapcsolatos Azure Resource Group-projektekkel](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Integrációs fiókra mutató hivatkozás

A vállalatok közötti (B2B) vállalati integrációs forgatókönyvek logikai alkalmazásainak létrehozásához összekapcsolhatja a logikai alkalmazást egy korábban létrehozott [integrációs fiókkal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely ugyanabban a régióban található, mint a logikai alkalmazás. Az integrációs fiók B2B-összetevőket, például kereskedelmi partnereket, szerződéseket, sémákat és térképeket tartalmaz, és lehetővé teszi, hogy a logikai alkalmazás VÁLLALATKÖZI összekötőket használjon az XML-érvényesítéshez és a sima fájl kódolásához vagy dekódolásához. Bár [ezt a hivatkozást a Azure Portal használatával is létrehozhatja](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), a Visual studiót az [Előfeltételek](#requirements)teljesítése után is használhatja, és a logikai alkalmazás JSON-(. JSON-) fájlként is létezik egy [Azure erőforráscsoport-projektben](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Ismerkedjen meg a [Visual Studióban található Logic apps-projektekkel kapcsolatos Azure Resource Group-projektekkel](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. A Visual Studióban nyissa meg az Azure erőforráscsoport-projektet, amely tartalmazza a logikai alkalmazást.

1. A Megoldáskezelőban nyissa meg a **< Logic-app-name >. JSON** fájl helyi menüjét, és válassza a **Megnyitás a Logic app Designerben**lehetőséget. Billentyűzet Ctrl+L)

   ![Logikai alkalmazás. JSON fájljának megnyitása a Logic app Designerrel](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

1. Annak ellenőrzéséhez, hogy a Logic app Designer fókuszban van-e, válassza a tervező lapja vagy felületét, hogy a Tulajdonságok ablaktáblán a logikai alkalmazás **integrációs fiók** tulajdonsága látható legyen.

   ![A Tulajdonságok ablaktábla az "integrációs fiók" tulajdonságot jeleníti meg](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties.png)

1. Nyissa meg az **integrációs fiók** listát, és válassza ki a logikai alkalmazáshoz csatolni kívánt integrációs fiókot, például:

   ![Az "integrációs fiók" tulajdonságok listájának megnyitása](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Ha elkészült, ne felejtse el menteni a Visual Studio-megoldást.

Ha az **integrációs fiók** tulajdonságot a Visual Studióban állítja be, és Azure Resource Manager sablonként menti a logikai alkalmazást, a sablon a kiválasztott integrációs fiókhoz tartozó paraméter-deklarációt is tartalmazza. A sablon paramétereinek és a Logic apps szolgáltatással kapcsolatos további [információkért lásd: Áttekintés: A logikai alkalmazások telepítésének](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)automatizálása.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Frissítés az Azure-ból

Ha a Azure Portal módosítja a logikai alkalmazást, és szeretné megtartani ezeket a módosításokat, akkor győződjön meg arról, hogy a módosításokkal frissíti az alkalmazás verzióját a Visual Studióban. 

* A Visual Studióban, a Logic app Designer eszköztárán válassza a **frissítés**lehetőséget.

  – vagy –

* A Visual Studio Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **frissítés**lehetőséget.

![Logikai alkalmazás frissítése a frissítésekkel](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Logikai alkalmazások frissítéseinek közzététele

Ha készen áll a logikai alkalmazás frissítéseinek üzembe helyezésére a Visual studióból az Azure-ba, akkor a Logic app Designer eszköztárán válassza a **Közzététel**lehetőséget.

![Frissített logikai alkalmazás közzététele](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>A logikai alkalmazás manuális futtatása

Az Azure-ban üzembe helyezett Logic apps-t manuálisan is aktiválhatja a Visual studióból. A Logic app Designer eszköztárán válassza az **trigger futtatása**lehetőséget.

![Logikai alkalmazás manuális futtatása](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

A Logic app-futtatások állapotának ellenőrzéséhez és a problémák diagnosztizálásához tekintse át a Visual Studióban futó alkalmazások részleteit, például bemeneteit és kimeneteit.

1. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **futtatási előzmények megnyitása**lehetőséget.

   ![Futtatási előzmények megnyitása](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Egy adott Futtatás részleteinek megtekintéséhez kattintson duplán egy futtatásra. Példa:

   ![Részletes futtatási előzmények](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > A tábla tulajdonság szerinti rendezéséhez válassza ki az oszlop fejlécét az adott tulajdonsághoz. 

1. Bontsa ki azokat a lépéseket, amelyek bemeneteit és kimeneteit szeretné áttekinteni. Példa:

   ![Az egyes lépésekhez tartozó bemenetek és kimenetek megtekintése](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

A logikai alkalmazás törlése nélkül leállíthatja az indítást a következő alkalommal, amikor a trigger feltétele teljesül. A logikai alkalmazás letiltásával megakadályozható, hogy a Logic Apps motor a logikai alkalmazás jövőbeli munkafolyamat-példányait hozza létre és futtassa.
A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Letiltás**lehetőséget.

![Logikai alkalmazás letiltása](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Ha letilt egy logikai alkalmazást, a rendszer nem hoz létre új futtatásokat. Az összes folyamatban lévő és függőben lévő Futtatás a befejezésig folytatódni fog, ami hosszabb időt is igénybe vehet. 

Ha készen áll arra, hogy a logikai alkalmazás folytassa a műveletet, aktiválja újra a logikai alkalmazást. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza az **Engedélyezés**lehetőséget.

![Logikai alkalmazás engedélyezése](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>A logikai alkalmazás törlése

Ha törölni szeretné a logikai alkalmazást a Azure Portal, a Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Törlés**lehetőséget.

![A logikai alkalmazás törlése](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat. 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Logic app-projektet a Logic Apps Designerben nyitja meg, előfordulhat, hogy nem kapja meg az Azure-előfizetés kiválasztásának lehetőségét. Ehelyett a logikai alkalmazás egy olyan Azure-előfizetéssel nyílik meg, amelyet nem a használni kívánt. Ez azért fordulhat elő, mert a Logic apps. JSON fájljának megnyitása után a Visual Studio gyorsítótárazza az első kiválasztott előfizetést későbbi használatra. A probléma megoldásához próbálja meg a következő lépések egyikét:

* Nevezze át a logikai alkalmazás. JSON fájlját. Az előfizetés gyorsítótára a fájl nevétől függ.

* Ha el szeretné távolítani a korábban kiválasztott előfizetéseket a megoldásban szereplő *összes* logikai alkalmazáshoz, törölje a rejtett Visual Studio Settings mappát (. vs) a megoldás könyvtárában. Ez a hely tárolja az előfizetési adatokat.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezelheti a telepített Logic apps-alkalmazásokat a Visual Studióval. Következő lépésként megismerheti a Logic app-definíciók az üzembe helyezéshez való testreszabását:

> [!div class="nextstepaction"]
> [Logic app-definíciók készítése a JSON-ban](../logic-apps/logic-apps-author-definitions.md)
