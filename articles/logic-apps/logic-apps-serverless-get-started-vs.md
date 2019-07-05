---
title: A Visual Studio Azure Logic Apps és az Azure Functions használatával kiszolgáló nélküli alkalmazásokat készíthet
description: Elkészítheti, telepítheti és kezelheti az első kiszolgáló nélküli alkalmazás a Visual Studio Azure Logic Apps és az Azure Functions használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444868"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Az első kiszolgáló nélküli alkalmazás létrehozása az Azure Logic Apps és az Azure Functions Visual Studio használatával

Gyorsan fejleszthet és helyezhet üzembe felhőalkalmazásokat használatával kiszolgáló nélküli eszközöket és funkciókat az Azure-ban, mint például [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [Azure Functions](../azure-functions/functions-overview.md). Ez a cikk bemutatja, hogyan kezdjen el létrehozni egy kiszolgáló nélküli alkalmazás, amely egy logikai alkalmazást, amely meghívja ezt a Visual Studióban egy Azure-függvényt használja. Az Azure-beli kiszolgáló nélküli megoldásokkal kapcsolatos további információkért lásd: [Logic Apps és Functions kiszolgáló nélküli Azure](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Előfeltételek

A Visual Studióban egy kiszolgáló nélküli alkalmazás készítése, az alábbiak szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A következő eszközök. Ha Ön még nincsenek telepítve, töltse le és telepítse őket.

  * [A Visual Studio 2019, 2017 vagy 2015 (közösségi vagy egyéb kiadás)](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > Visual Studio 2019 vagy 2017 telepítésekor győződjön meg arról, hogy kiválasztja a **Azure-fejlesztési** számítási feladatot.

  * [A Microsoft Azure SDK for .NET (2.9.1-es verzió vagy újabb)](https://azure.microsoft.com/downloads/). 
  További információ az [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Az Azure Logic Apps Tools a Visual Studio verziójának szeretné:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

  * [Az Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) helyi hibakeresési funkciók.

* Hozzáférés a beágyazott Logikaialkalmazás-Tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. 
  Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

## <a name="create-a-resource-group-project"></a>Egy erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) a kiszolgáló nélküli alkalmazáshoz. Az Azure-on belüli erőforrások létrehozása egy *erőforráscsoport*, azaz olyan logikai gyűjtemény, rendezése, kezelésére és üzembe helyezni erőforrásokat egyetlen eszközként egy teljes alkalmazás használ. Az Azure-beli kiszolgáló nélküli alkalmazások esetén az erőforráscsoport az Azure Logic Apps és az Azure Functions forrásokat tartalmazza. További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/resource-group-overview.md).

1. Indítsa el a Visual Studiót, és jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet.

   ![Új projekt létrehozása a Visual Studióban](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Ezután válassza ki **felhőalapú** > **Azure-erőforráscsoport**.

   > [!NOTE]
   > Ha a **felhőalapú** kategória vagy **Azure-erőforráscsoport** projekt nem létezik, győződjön meg arról, hogy az Azure SDK for Visual Studio telepítve van.

   Ha a Visual Studio 2019 használja, kövesse az alábbi lépéseket:

   1. Az a **hozzon létre egy új projektet** jelölje ki a **Azure-erőforráscsoport** webesprojekt-sablon vagy a Vizualizáció C# vagy Visual Basic, és válassza ki **tovább**.

   1. Adja meg a nevét és más az Azure-erőforráscsoportot használni kívánt projekt adatait. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A projekthez adjon egy nevet és a egy olyan helyre, és válassza ki **OK**.

   A Visual Studio megkéri, hogy válasszon ki egy sablont a sablonok listájának. 
   Ebben a példában egy Azure-Gyorssablonok sablont használ, így hozhat létre kiszolgáló nélküli alkalmazás, amely tartalmazza a Logic Apps-alkalmazás és a egy hívás egy Azure-függvényt.

   > [!TIP]
   > Olyan esetekben, ha nem szeretné előre telepítsen a megoldás az Azure-erőforráscsoportban, használhatja az üres **logikai alkalmazás** sablon, amely egyszerűen létrehoz egy üres logikai alkalmazást.

1. Az a **erről a helyről sablon megjelenítése** listáról válassza ki **Azure gyors üzembe helyezés (github.com/Azure/azure-quickstart-templates)** .

1. A Keresés mezőbe írja be a "logikai alkalmazás" szűrőként. Az eredmények közül válassza ki a **101-logic-app-and-function-app** sablont.

   ![Válassza ki az Azure gyorsindítási sablon](./media/logic-apps-serverless-get-started-vs/select-template.png)

   A Visual Studio létrehozza, és megnyitja az erőforráscsoport-projekt megoldást. 
   A kiválasztott Azure gyorsindítási sablon hoz létre a központi telepítési sablont azuredeploy.json belül az erőforráscsoport-projekt. A központi telepítési sablont egy HTTP-kérelem által aktivált, meghív egy Azure-függvényt, és visszaadja az eredményt egy HTTP-választ, egyszerű logikai alkalmazás definícióját tartalmazza.

   ![Új kiszolgáló nélküli megoldás](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Ezután üzembe helyezheti megoldását az Azure-bA. Ezt azért kell megtennie, mielőtt nyissa meg a központi telepítési sablont, és tekintse át az erőforrásokat a kiszolgáló nélküli alkalmazáshoz.

## <a name="deploy-your-solution"></a>A megoldás üzembe helyezése

A logikai alkalmazás megnyitása a Visual studióban a Logikaialkalmazás-tervezőben, előtt rendelkeznie kell egy Azure-erőforráscsoportot, amely már üzembe helyezte az Azure-ban. A Tervező majd hozhatnak létre kapcsolatokat erőforrásokat és szolgáltatásokat a logikai alkalmazásban. Ebben a feladatban kövesse az alábbi lépéseket a megoldást a Visual Studióból az Azure Portalra való üzembe helyezéséhez:

1. A Megoldáskezelőben, az erőforráscsoport-projekt helyi menüjén válassza **telepítés** > **új**.

   ![Erőforráscsoporthoz. új üzemelő példány létrehozása](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Ha azok még nem tette, válassza ki az Azure-előfizetés és az erőforráscsoportot, amely számára telepíteni kívánja. Ezután válassza ki **telepítés**.

   ![Központi telepítési beállítások](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Ha a **paraméterek szerkesztése** mezőben jelenik meg, adja meg a logikai alkalmazás és az Azure-függvényalkalmazás üzembe helyezéskor használandó erőforrás nevét, majd mentse a beállításokat. Ellenőrizze, hogy az a függvényalkalmazás globálisan egyedi név.

   ![Adja meg a nevet a logikai alkalmazás és a függvényalkalmazás](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   A megadott erőforráscsoportba irányuló üzembe helyezés a Visual Studio indulásakor a megoldás üzembe helyezés állapota látható-e a Visual Studio **kimeneti** ablak. 
   Üzembe helyezés befejezése után a logikai alkalmazás aktiválódik az Azure Portalon.

## <a name="edit-your-logic-app-in-visual-studio"></a>A Visual Studióban a logikai alkalmazás szerkesztése

Üzembe helyezés után a logikai alkalmazás szerkesztéséhez nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező használatával a Visual Studióban.

1. A Megoldáskezelőben az azuredeploy.json fájl a helyi menüből válassza ki **megnyitása a Logikaialkalmazás-Tervező**.

   ![Nyissa meg az azuredeploy.json Logic App Designerben](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019, ellenőrizze, hogy van-e a legújabb frissítéseket a Visual Studióhoz.

1. Miután a **logikai alkalmazás tulajdonságai** mező jelenik meg, a **előfizetés**, ha még nincs kiválasztva, válassza ki az Azure-előfizetés. A **erőforráscsoport**, az erőforráscsoportot és a megoldás üzembe helyezésének helye, majd válassza ki és **OK**.

   ![Logikai alkalmazás tulajdonságai](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Miután megnyílik a Logikaialkalmazás-Tervező, hozzáadni a lépéseket vagy módosítsa a munkafolyamatot, és mentse a módosításokat.

   ![Logic App Designerben megnyitott logikai alkalmazás](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Az Azure Functions-projekt létrehozása

A JavaScript, Python, a Functions-projekt és a függvény létrehozásához F#, PowerShell, a Batch vagy a Bash, kövesse a [együttműködik az Azure Functions Core Tools](../azure-functions/functions-run-local.md). Fejleszthet az Azure-függvény használatával C# belül a megoldás használatához egy C# osztálytár a lépéseket követve [közzététele a .NET osztálytár, a Függvényalkalmazás](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>A Visual Studio functions üzembe helyezése

A központi telepítési sablont bármely az Azure functions, amely rendelkezik a megoldásban a változókat az azuredeploy.json fájlban megadott Git-tárház helyez üzembe. Ha hoz létre, és hozhat létre a Functions-projektet a megoldásban, tekintsen meg, hogy a projekt Git-verziókövetési (például a GitHub vagy az Azure DevOps), és frissítse a `repo` változó, úgy, hogy a sablon üzembe helyez az Azure-függvény.

## <a name="manage-logic-apps-and-view-run-history"></a>Kezelheti a logic apps és a futtatási előzmények megtekintése

Már telepítve van az Azure logic Apps Ön is továbbra is szerkesztése, kezelése, a futtatási előzmények megtekintése és tiltsa le ezeket az alkalmazásokat a Visual Studióból.

1. Az a **nézet** elemét a Visual Studióban, a nyílt **Cloud Explorer**.

1. A **minden előfizetés**, válassza ki az Azure-előfizetést, amelyet szeretne kezelni, és válassza ki a logikai alkalmazások társított **alkalmaz**.

1. A **Logic Apps**, válassza ki a logikai alkalmazást. Az alkalmazás helyi menüjén válassza **nyissa meg a logikai alkalmazás szerkesztő**.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019, ellenőrizze, hogy van-e a legújabb frissítéseket a Visual Studióhoz.

Letöltheti a már közzétett logikai alkalmazás most már be az erőforráscsoport-projekt. Ezért bár előfordulhat, hogy megkezdte egy logikai alkalmazást az Azure Portalon, továbbra is importálni és kezelése a Visual Studio alkalmazást. További információkért lásd: [logikai alkalmazások Visual studióval kezelése](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazások kezelése a Visual Studióval](manage-logic-apps-with-visual-studio.md)
