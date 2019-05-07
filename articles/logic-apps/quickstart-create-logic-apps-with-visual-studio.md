---
title: Automatizált munkafolyamatok létrehozása a Visual Studio használatával – Azure Logic Apps
description: Automatizálhatja a feladatokat, üzleti folyamatokat és munkafolyamatokat a vállalati integráció az Azure Logic Apps és a Visual Studio használatával
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: quickstart
ms.custom: mvc
ms.reviewer: klam, LADocs
ms.suite: integration
ms.date: 04/25/2019
ms.openlocfilehash: afa539bc3369e4f9d9ecf27340436e0be70a03ad
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190572"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Gyors útmutató: Automatizált feladatokat, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps – Visual Studio

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Visual Studio segítségével munkafolyamatokat hozhat létre olyan feladatok és folyamatok automatizálására, amelyek alkalmazásokat, adatokat, rendszereket és szolgáltatásokat integrálnak vállalatok és intézmények között. Ez a rövid útmutató bemutatja, hogyan tervezheti meg és ezek a munkafolyamatok létrehozása a Visual Studióban a logikai alkalmazások létrehozásának és üzembe helyezni azokat az alkalmazásokat az Azure. Bár ezeket a feladatokat az Azure Portalon, a Visual Studio lehetővé teszi a források vezérléséhez, a különböző verziók közzétételéhez és a különböző üzemi környezetek Azure Resource Manager-sablonok létrehozása a logic apps hozzáadását.

Ha ismerkedik az Azure Logic Apps, és csak az alapvető fogalmakat szeretné, próbálja ki a [gyors útmutató: a logikai alkalmazás létrehozása az Azure Portalon](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Logikaialkalmazás-Tervező hasonlóan működik az Azure Portalon és a Visual Studio.

Ebben a rövid útmutatóban létrehozhat ugyanolyan logikai alkalmazást a Visual Studióval, az Azure portal rövid útmutatójában. Ez a logikai alkalmazás egy webhely RSS-Hírcsatornájában figyeli, és minden új elemről e-mailt küld, amely a. A befejezett logikai alkalmazás munkafolyamathoz hasonlít:

![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [A Visual Studio 2019, 2017 vagy 2015 – Community edition vagy nagyobb](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017 használ.

    > [!IMPORTANT]
    > Visual Studio 2019 vagy 2017 telepítésekor győződjön meg arról, hogy kiválasztja a **Azure-fejlesztési** számítási feladatot.

  * [A Microsoft Azure SDK for .NET (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/). 
  További információ az [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Az Azure Logic Apps Tools a Visual Studio verziójának szeretné:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Internet-hozzáférés a beágyazott Logikaialkalmazás-tervező használatakor.

  Az internetkapcsolat-erőforrások létrehozása az Azure-ban és a tulajdonságokat és adatokat olvasni az összekötők a logikai alkalmazásban van szüksége. 
  Például a Dynamics CRM Online-kapcsolatok esetén a Tervező ellenőrzi a CRM-példányon, alapértelmezett és egyéni tulajdonságokat.

* A Logic Apps által támogatott e-mail-fiók, például Office 365 Outlook, Outlook.com vagy Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ebben a példában az Office 365 Outlookot használja. Ha más szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/resource-group-overview.md).

1. Indítsa el a Visual Studiót. Jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet. (Billentyűzet: Ctrl+Shift+N)

   ![Az Új > Projekt menüpont kiválasztása a Fájl menüben](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza a **Felhő** > **Azure-erőforráscsoport** elemet. Adjon nevet a projektnek, például a következőt:

   ![Azure erőforráscsoport-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Ha **felhőalapú** vagy **Azure-erőforráscsoport** nem jelenik meg, ellenőrizze, hogy telepítse az Azure SDK for Visual Studio.

   Ha a Visual Studio 2019 használja, kövesse az alábbi lépéseket:

   1. Az a **hozzon létre egy új projektet** jelölje ki a **Azure-erőforráscsoport** Vizualizáció projekt C# vagy Visual Basic. Kattintson a **Tovább** gombra.

   1. Adja meg a használni kívánt Azure-erőforráscsoportot és egyéb projekt nevét. Válassza a **Létrehozás** elemet.

1. A sablon listából válassza ki a **logikai alkalmazás** sablont. Kattintson az **OK** gombra.

   ![Logikaialkalmazás-sablon kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Miután a Visual Studio létrehozta a projektet, megnyílik a Megoldáskezelő, és megjeleníti a megoldást. 
   A megoldás a **LogicApp.json** fájl nem csak a logikai alkalmazás definícióját tárolja, de is egy Azure Resource Manager-sablon üzembe helyezéshez használható.

   ![A Megoldáskezelő az új logikai alkalmazással és az üzembe helyezési fájllal](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

Ha rendelkezik az Azure-erőforráscsoport projekt, a logikai alkalmazás létrehozása a **üres logikai alkalmazás** sablont.

1. A Megoldáskezelőben nyissa meg a **LogicApp.json** fájlt a helyi menü. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**. (Billentyűzet: Ctrl+L)

   ![A logikai alkalmazás .json-fájljának megnyitása a Logikaialkalmazás-tervezővel](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   A Visual Studio kéri az Azure-előfizetés és az Azure-erőforráscsoport létrehozásához és üzembe helyezni erőforrásokat, a logikai alkalmazás és a kapcsolatokat.

1. A **előfizetés**, válassza ki az Azure-előfizetésében. A **erőforráscsoport**válassza **hozzon létre új** hozhat létre egy új Azure-erőforráscsoportot.

   ![Azure-előfizetés, erőforráscsoport és erőforrás helyének kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Beállítás | Példaérték | Leírás |
   | ------- | ------------- | ----------- |
   | Felhasználói profilok listája | Contoso <br> jamalhartnett@contoso.com | Alapértelmezés szerint a bejelentkezéshez használt fiók |
   | **Előfizetés** | Utólagos, használatalapú fizetés <br> (jamalhartnett@contoso.com) | Az Azure-előfizetés és a társított fiók neve |
   | **Erőforráscsoport** | MyLogicApp-RG <br> (USA nyugati régiója) | Az Azure-erőforráscsoportot és tárolására, és a logikai alkalmazás-erőforrások üzembe helyezése |
   | **Hely** | MyLogicApp-RG2 <br> (USA nyugati régiója) | Egy másik hely, ha nem az erőforráscsoport helyét szeretné használni |
   ||||

1. A Logic Apps Designerben megnyílik egy oldal, amely bemutatja a bemutató videó és a gyakran használt triggereket. Görgessen át a videón és triggereket adjuk ki **sablonok**, és válassza ki **üres logikai alkalmazás**.

   ![„Üres logikai alkalmazás” kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának felépítése

Ezután adjon hozzá egy RSS [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely egy új hírcsatornaelem megjelenésekor aktiválódik. Minden logikai alkalmazás elindul egy eseményindítóval, amely akkor aktiválódik, amikor adott feltételek teljesülése esetén. A Logic Apps-motor a trigger minden aktiválásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

1. A Logikaialkalmazás-Tervező, a keresőmező alatt válassza a **összes**.
A Keresés mezőbe írja be az "rss". Az eseményindítók listában jelölje ki az eseményindító: **Egy új hírcsatornaelem közzétételekor - RSS**

   ![Logikai alkalmazás felépítése trigger és műveletek hozzáadásával](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Miután a trigger megjelenik a tervezőben, Befejezés lépéseit az alábbi munkafolyamat a logikai alkalmazás létrehozása a [Azure portal rövid útmutatójának](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), majd térjen vissza a cikk. Az elkészült logikai alkalmazás az alábbihoz hasonlít:

   ![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

1. Mentse a Visual Studio-megoldás. (Billentyűzet: CTRL + S)

## <a name="deploy-logic-app-to-azure"></a>Logikai alkalmazás üzembe helyezése az Azure-ban

Mielőtt futtatja, és tesztelje a logikai alkalmazást, az alkalmazás telepítése az Azure-bA a Visual Studióból.

1. A Megoldáskezelőben a projekt helyi menüjéből válassza az **Üzembe helyezés** > **Új** elemet. Ha szükséges, jelentkezzen be Azure-fiókjával.

   ![A logikai alkalmazás üzemelő példányának létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Az üzembe helyezéshez tartsa meg az alapértelmezett Azure-előfizetéssel, erőforrás a csoportba, illetve az egyéb beállításokat. Válasszon **üzembe helyezése**.

   ![Logikai alkalmazás üzembe helyezése Azure-erőforráscsoportra](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Ha a **paraméterek szerkesztése** mező jelenik meg, adja meg a logikai alkalmazás erőforrás nevét. A beállítások mentéséhez.

   ![Adja meg a logikai alkalmazás üzemelő példányának nevét](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot.

   ![Üzembe helyezés állapotának kimenete](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Ha a kiválasztott összekötők kell a bemenetet, egy PowerShell-ablakot a háttérben megjelenik, és kérni fogja, bármilyen szükséges jelszavakat és a titkos kulcsok. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![PowerShell-ablakban](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Üzembe helyezés befejezése után a logikai alkalmazás élő az Azure Portalon, és futtatja a megadott ütemezés szerint (percenként). Ha az eseményindító hírcsatorna új elemeket talál, az eseményindítót, amely létrehoz egy munkafolyamat-példányhoz, amelyen a logikai alkalmazás használatának. A logikai alkalmazás minden egyes új elemről e-mailt küld. Ellenkező esetben a trigger nem talál új elemeket, ha az eseményindító nem indul el és "kihagyja" a munkafolyamat hárítható el. A logikai alkalmazás megvárja a következő esedékes időpontot, mielőtt ellenőrzése.

   Az alábbiakban a logikai alkalmazás által küldött minta e-maileket. 
   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

   ![Az Outlook minden új RSS-elemről e-mailt küld](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulálunk, sikeresen felépítette és a logikai alkalmazás a Visual Studio használatával üzembe helyezett. A logikai alkalmazás kezeléséhez és az előzményeinek megtekintéséhez lásd a [logikai alkalmazások Visual Studióval történő kezelésével](../logic-apps/manage-logic-apps-with-visual-studio.md) foglalkozó cikket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, a logikai alkalmazás használatával, a logikai alkalmazást, és a kapcsolódó erőforrásokat tartalmazó erőforráscsoport törléséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazás létrehozásához használt fiókkal.

1. Az Azure főmenüjében válassza az **Erőforráscsoportok** lehetőséget.
Válassza ki a logikai alkalmazás erőforráscsoportot, és válassza ki **áttekintése**.

1. Az **Áttekintés** lapon válassza az **Erőforráscsoport törlése** lehetőséget. Megerősítésként írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

   ![„Erőforráscsoportok” > „Áttekintés” > „Erőforráscsoport törlése”](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Törölje a Visual Studio-megoldást a helyi számítógépről.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy logikai alkalmazást állított össze, helyezett üzembe és futtatott a Visual Studióval. A speciális telepítési végzett a logic apps Visual Studióval végzett kezelésére és kapcsolatos további információkért tanulmányozza a következő cikkeket:

> [!div class="nextstepaction"]
> * [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)