---
title: Az első kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban
description: Kiszolgáló nélküli alkalmazások létrehozása, üzembe helyezése és kezelése az Azure Logic Apps és az Azure Functions használatával a Visual Studióban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981161"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Az első kiszolgáló nélküli alkalmazás létrehozása az Azure Logic Apps és az Azure Functions használatával a Visual Studióban

Az Azure-beli kiszolgáló nélküli eszközökkel és képességekkel, például az [Azure Logic Apps és](../logic-apps/logic-apps-overview.md) az Azure Functions használatával gyorsan fejleszthet és telepíthet [felhőalapú](../azure-functions/functions-overview.md)alkalmazásokat. Ez a cikk bemutatja, hogyan lehet elkezdeni egy kiszolgáló nélküli alkalmazás, amely egy Azure-függvényt meghívja, a Visual Studióban egy logikai alkalmazást használ. Ha többet szeretne megtudni az Azure-beli kiszolgáló nélküli megoldásokról, olvassa el az [Azure Serverless functions és logikai alkalmazások témakört.](../logic-apps/logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>Előfeltételek

Kiszolgáló nélküli alkalmazás visual studióban való létrehozásához a következőkre van szükség:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A következő eszközök. Ha még nem rendelkezik velük, töltse le és telepítse őket.

  * [Visual Studio 2019, 2017 vagy 2015 (közösségi vagy más kiadás)](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > A Visual Studio 2019 vagy 2017 telepítésekor győződjön meg arról, hogy az **Azure fejlesztési** számítási feladatait választja.

  * [Microsoft Azure SDK a .NET-hez (2.9.1-es vagy újabb verzió).](https://azure.microsoft.com/downloads/) 
  További információ az [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) készlettel kapcsolatban.

  * [Az Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Az Azure Logic Apps Tools a kívánt Visual Studio-verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

  * [Az Azure Functions alapvető eszközei](https://www.npmjs.com/package/azure-functions-core-tools) a helyi hibakeresési függvényekhez.

* A beágyazott Logikai alkalmazástervező használata közben a webhez való hozzáférés.

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. 
  Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

## <a name="create-a-resource-group-project"></a>Erőforráscsoport-projekt létrehozása

Első lépésekhez hozzon létre egy [Azure Resource Group-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a kiszolgáló nélküli alkalmazáshoz. Az Azure-ban erőforrásokat hozhat létre egy *erőforráscsoporton*belül, amely egy logikai gyűjtemény, amelyet egy teljes alkalmazás erőforrásainak egyetlen eszközként történő rendszerezésére, kezelésére és üzembe helyezésére használhat. Az Azure-ban egy kiszolgáló nélküli alkalmazás esetén az erőforráscsoport az Azure Logic Apps és az Azure Functions erőforrásait is tartalmazza. További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/management/overview.md).

1. Indítsa el a Visual Studio alkalmazást, és jelentkezzen be Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet.

   ![Új projekt létrehozása a Visual Studióban](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Ezután válassza a **Cloud** > **Azure Resource Group lehetőséget.**

   > [!NOTE]
   > Ha a **Felhő** kategória vagy az **Azure Resource Group** projekt nem létezik, győződjön meg arról, hogy telepítette az Azure SDK a Visual Studio.

   A Visual Studio 2019 használata esetén kövesse az alábbi lépéseket:

   1. Az **Új projekt létrehozása** mezőben válassza az **Azure Resource Group** projektsablont visual C# vagy Visual Basic esetén, majd kattintson a **Tovább**gombra.

   1. Adja meg a nevet és az Azure erőforráscsoporthoz használni kívánt egyéb projektadatokat. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. Adjon nevet és helyet a projektnek, majd kattintson **az OK gombra.**

   A Visual Studio kéri, hogy válasszon ki egy sablont a sablonok listájából. 
   Ez a példa egy Azure QuickStart-sablont használ, így létrehozhat egy kiszolgáló nélküli alkalmazást, amely tartalmaz egy logikai alkalmazást és egy Azure-függvény hívását.

   > [!TIP]
   > Olyan esetekben, ahol nem szeretné előre telepíteni a megoldást egy Azure-erőforráscsoportba, használhatja az üres Logic App-sablont, amely csak egy üres logikai alkalmazást hoz létre. **Logic App**

1. Az **ebből a helylistából sablonok megjelenítése** listában válassza az Azure **QuickStart (github.com/Azure/azure-quickstart-templates)** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "logic-app" kifejezést. Az eredmények közül válassza ki a **101-logic-app-and-function-app** sablont.

   ![Válassza az Azure quickstart sablont](./media/logic-apps-serverless-get-started-vs/select-template.png)

   A Visual Studio létrehoz és megnyit egy megoldást az erőforráscsoport-projekthez. 
   A kiválasztott Azure QuickStart sablon létrehoz egy azuredeploy.json nevű központi telepítési sablont az erőforráscsoport-projekten belül. Ez a központi telepítési sablon tartalmazza egy egyszerű logikai alkalmazás definícióját, amelyet egy HTTP-kérelem vált ki, meghív egy Azure-függvényt, és az eredményt HTTP-válaszként adja vissza.

   ![Új kiszolgáló nélküli megoldás](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Ezután telepítse a megoldást az Azure-ba. Ezt meg kell tennie, mielőtt megnyitná a központi telepítési sablont, és áttekintheti a kiszolgáló nélküli alkalmazás erőforrásait.

## <a name="deploy-your-solution"></a>A megoldás üzembe helyezése

Mielőtt megnyitná a logikai alkalmazást a Visual Studióban a Logic App Designerben, rendelkeznie kell egy Azure-erőforráscsoporttal, amely már telepítve van az Azure-ban. A tervező ezután kapcsolatokat hozhat létre az erőforrásokkal és szolgáltatásokkal a logikai alkalmazásban. Ehhez a feladathoz kövesse az alábbi lépéseket a megoldás visual studióból az Azure Portalra való üzembe helyezéséhez:

1. A Megoldáskezelőben az erőforrásprojekt helyi menüjében válassza **az Új telepítése parancsot.** > **New**

   ![Új központi telepítés létrehozása az erőforráscsoportszámára](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Ha még nincs enek kiválasztva, válassza ki az Azure-előfizetést és azt az erőforráscsoportot, amelyre telepíteni szeretné. Ezután válassza **a Telepítés lehetőséget.**

   ![Központi telepítési beállítások](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazáshoz és az Azure-függvényalkalmazáshoz a központi telepítéskor használandó erőforrásneveket, majd mentse a beállításokat. Győződjön meg arról, hogy globálisan egyedi nevet használ a függvényalkalmazáshoz.

   ![Nevek biztosítása a logikai alkalmazásnak és a függvényalkalmazásnak](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Amikor a Visual Studio megkezdi a telepítést a megadott erőforráscsoportba, a megoldás telepítési állapota megjelenik a Visual Studio **kimeneti** ablakában. 
   A központi telepítés befejezése után a logikai alkalmazás él az Azure Portalon.

## <a name="edit-your-logic-app-in-visual-studio"></a>A logikai alkalmazás szerkesztése a Visual Studióban

A logikai alkalmazás központi telepítés után történő szerkesztéséhez nyissa meg a logikai alkalmazást a Visual Studio Logic App Designer használatával.

1. A Megoldáskezelőben az azuredeploy.json fájl helyi menüjében válassza a **Megnyitás a Logikai alkalmazástervezővel parancsot.**

   ![Open azuredeploy.json a Logic App Designerben](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

1. Miután a **Logic App Properties (Logikai alkalmazás tulajdonságai)** mező jelenik meg, **az Előfizetés**csoportban válassza ki az Azure-előfizetést, ha még nincs kiválasztva. Az **Erőforráscsoport**csoportban válassza ki azt az erőforráscsoportot és helyet, ahol a megoldást telepítette, majd kattintson az **OK gombra.**

   ![A logikai alkalmazás tulajdonságai](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   A Logic App Designer megnyitása után folytathatja a lépések hozzáadását, vagy módosíthatja a munkafolyamatot, és mentheti a frissítéseket.

   ![Megnyitott logikai alkalmazás a Logic App Designer ben](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Az Azure Functions projekt létrehozása

A Functions projekt és függvény JavaScript, Python, F#, PowerShell, Batch vagy Bash használatával történő létrehozásához kövesse az [Azure Functions core eszközök használata](../azure-functions/functions-run-local.md)című szakaszlépéseit. Az Azure-függvény fejlesztéséhez a C# használatával a megoldáson belül, használja a C# osztály könyvtár a [.NET osztálykönyvtár közzététele függvényalkalmazásként](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)című lépéseit követve.

## <a name="deploy-functions-from-visual-studio"></a>Funkciók üzembe helyezése a Visual Studióból

A központi telepítési sablon telepíti a megoldásban a Git-tárházban található, az azuredeploy.json fájlban megadott Azure-függvényeket. Ha létrehozza és létrehozza a Functions projektet a megoldásában, ellenőrizheti, hogy a projekt a Git-forrásvezérlőbe (például A GitHub vagy az Azure DevOps), majd frissítse a `repo` változót, hogy a sablon üzembe helyezze az Azure-függvényt.

## <a name="manage-logic-apps-and-view-run-history"></a>Logikai alkalmazások kezelése és futtatási előzmények megtekintése

Az Azure-ban már telepített logikai alkalmazások esetében továbbra is szerkesztheti, kezelheti, megtekintheti a futtatási előzményeket, és letilthatja ezeket az alkalmazásokat a Visual Studióból.

1. A Visual Studio **Nézet** menüjében nyissa meg a **Cloud Explorer**t .

1. A **Minden előfizetés**csoportban válassza ki a kezelni kívánt logikai alkalmazásokhoz társított Azure-előfizetést, majd válassza az **Alkalmaz**lehetőséget.

1. A **Logic Apps csoportban**válassza ki a logikai alkalmazást. Az alkalmazás helyi menüjében válassza a **Megnyitás logikai alkalmazásszerkesztővel**lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

Most már letöltheti a már közzétett logikai alkalmazást az erőforráscsoport-projektbe. Így bár előfordulhat, hogy elindított egy logikai alkalmazást az Azure Portalon, továbbra is importálhatja és kezelheti az alkalmazást a Visual Studióban. További információt a Logic Apps kezelése a Visual Studio segítségével című [témakörben talál.](../logic-apps/manage-logic-apps-with-visual-studio.md)

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazások kezelése a Visual Studióval](manage-logic-apps-with-visual-studio.md)
