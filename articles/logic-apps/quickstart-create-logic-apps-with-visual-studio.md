---
title: Automatizált munkafolyamatok létrehozása a Visual Studióval – Azure Logic Apps
description: A vállalati integrációhoz szükséges feladatok, üzleti folyamatok és munkafolyamatok automatizálása a Azure Logic Apps és a Visual Studio használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.manager: carmonm
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/25/2019
ms.openlocfilehash: 194004362449f2f59a53d45e18e8dd72a29cc79a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029575"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Gyors útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása a Azure Logic Apps-Visual Studióval

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Visual Studio segítségével munkafolyamatokat hozhat létre olyan feladatok és folyamatok automatizálására, amelyek alkalmazásokat, adatokat, rendszereket és szolgáltatásokat integrálnak vállalatok és intézmények között. Ez a rövid útmutató bemutatja, hogyan alakíthatja ki és építheti fel ezeket a munkafolyamatokat a Visual Studióban logikai alkalmazások létrehozásával és az alkalmazások Azure-ba történő üzembe helyezésével. Bár ezeket a feladatokat a Azure Portal is elvégezheti, a Visual Studio lehetővé teszi a logikai alkalmazások hozzáadását a verziókövetés számára, különböző verziók közzétételét, valamint Azure Resource Manager sablonok létrehozását különböző központi telepítési környezetekhez.

Ha még nem ismeri a Azure Logic Appst, és csak az alapvető fogalmakat szeretné megtekinteni, próbálkozzon a [logikai alkalmazás létrehozásával a Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Logic app Designer hasonlóan működik a Azure Portal és a Visual Studióban is.

Ebben a rövid útmutatóban ugyanazt a logikai alkalmazást hozza létre a Visual Studióval Azure Portal rövid útmutatóként. Ez a logikai alkalmazás figyeli a webhely RSS-hírcsatornáját, és e-mailt küld az adott hírcsatorna minden új eleménél. A kész logikai alkalmazás a következő magas szintű munkafolyamathoz hasonlít:

![Magas szintű Logic apps-munkafolyamat – áttekintés](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – Community Edition vagy újabb](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017-et használja.

    > [!IMPORTANT]
    > A Visual Studio 2019-es vagy 2017-es vagy-es telepítésekor ügyeljen arra, hogy kiválassza az **Azure-fejlesztési** számítási feladatot.

  * [Microsoft Azure SDK for .net (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/). 
  További információ az [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps a Visual Studio-verzióhoz használni kívánt eszközöket:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Internet-hozzáférés a beágyazott Logikaialkalmazás-tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége az erőforrások létrehozásához az Azure-ban, illetve a logikai alkalmazásban található összekötők tulajdonságainak és adatainak olvasásához. 
  A Dynamics CRM Online-kapcsolatok esetében például a tervező ellenőrzi a CRM-példányt az alapértelmezett és az egyéni tulajdonságok esetében.

* A Logic Apps által támogatott e-mail-fiók, például Office 365 Outlook, Outlook.com vagy Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ez a példa az Office 365 Outlookot használja. Ha más szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/resource-group-overview.md).

1. Indítsa el a Visual Studiót. Jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet. Billentyűzet Ctrl + Shift + N)

   ![Új Visual Studio-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza a **Felhő** > **Azure-erőforráscsoport** elemet. Adjon nevet a projektnek, például a következőt:

   ![Azure erőforráscsoport-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Az erőforráscsoportok nevei csak betűket, számokat, pontokat (`.`), aláhúzást (`_`), kötőjelet (`-`) és zárójeleket (`(`, `)`) tartalmazhatnak, de nem *végződhet* ponttal (`.`).
   >
   > Ha a **felhő** vagy az **Azure-erőforráscsoport** nem jelenik meg, győződjön meg róla, hogy telepíti a Visual studióhoz készült Azure SDK-t.

   Ha a Visual Studio 2019-et használja, kövesse az alábbi lépéseket:

   1. Az **új projekt létrehozása** mezőben válassza ki az **Azure erőforráscsoport** -projektet a vizualizációhoz C# vagy a Visual Basic. Kattintson a **Tovább** gombra.

   1. Adja meg a használni kívánt Azure-erőforráscsoport nevét és a projekt egyéb információit. Válassza a **Létrehozás** lehetőséget.

1. A sablon listából válassza ki a **logikai alkalmazás** sablonját. Válassza az **OK** gombot.

   ![A "Logic app" sablon kiválasztása a projekt létrehozásához](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Miután a Visual Studio létrehozta a projektet, megnyílik a Megoldáskezelő, és megjeleníti a megoldást. 
   A megoldásban a **LogicApp. JSON** fájl nem csak a Logic app-definíciót tárolja, hanem egy Azure Resource Manager sablon is, amelyet az üzembe helyezéshez használhat.

   ![A Megoldáskezelő az új logikai alkalmazással és az üzembe helyezési fájllal](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

Ha rendelkezik az Azure erőforráscsoport-projekttel, hozzon létre egy logikai alkalmazást az **üres logikai alkalmazás** sablonnal.

1. A Megoldáskezelőban nyissa meg a **LogicApp. JSON** fájl helyi menüjét. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**. Billentyűzet Ctrl+L)

   ![A logikai alkalmazás .json-fájljának megnyitása a Logikaialkalmazás-tervezővel](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   A Visual Studio az Azure-előfizetését és egy Azure-erőforráscsoportot kér a logikai alkalmazás és a kapcsolatok erőforrásainak létrehozásához és üzembe helyezéséhez.

1. Az **előfizetés**mezőben válassza ki az Azure-előfizetését. Az **erőforráscsoport**területen válassza az **új létrehozása** lehetőséget egy új Azure-erőforráscsoport létrehozásához.

   ![Azure-előfizetés, erőforráscsoport és erőforrás helyének kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Beállítás | Példaérték | Leírás |
   | ------- | ------------- | ----------- |
   | Felhasználói profilok listája | Contoso <br> jamalhartnett@contoso.com | Alapértelmezés szerint a bejelentkezéshez használt fiók |
   | **Előfizetés** | Utólagos, használatalapú fizetés <br> (jamalhartnett@contoso.com) | Az Azure-előfizetés és a társított fiók neve |
   | **Erőforráscsoport** | MyLogicApp-RG <br> (USA nyugati régiója) | A logikai alkalmazás erőforrásainak tárolásához és üzembe helyezéséhez használt Azure-erőforráscsoport és-hely |
   | **Location** | MyLogicApp-RG2 <br> (USA nyugati régiója) | Egy másik hely, ha nem az erőforráscsoport helyét szeretné használni |
   ||||

1. A Logic Apps Designer egy olyan oldalt nyit meg, amely bemutatja a bemutató videót és a gyakran használt eseményindítókat. Görgessen le a videó **és az eseményindítók között, és**válassza az **üres logikai alkalmazás**lehetőséget.

   ![„Üres logikai alkalmazás” kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának felépítése

Ezután adjon hozzá egy RSS- [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely akkor aktiválódik, amikor megjelenik egy új hírcsatorna elem. Minden logikai alkalmazás egy eseményindítóval kezdődik, amely az adott feltételek teljesülése esetén következik be. A Logic Apps-motor a trigger minden aktiválásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

1. A Logic app Designerben a keresőmező alatt válassza az **összes**lehetőséget.
A keresőmezőbe írja be az "RSS" kifejezést. Az eseményindítók listából válassza ki a következő eseményindítót: **Hírcsatorna-elemek közzétételekor – RSS**

   ![Logikai alkalmazás felépítése trigger és műveletek hozzáadásával](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Miután az trigger megjelenik a tervezőben, fejezze be a logikai alkalmazás kiépítése a [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)rövid útmutató munkafolyamat lépéseit követve, majd térjen vissza ehhez a cikkhez. Az elkészült logikai alkalmazás az alábbihoz hasonlít:

   ![A logikai alkalmazás munkafolyamatának befejezett példája](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Mentse a Visual Studio-megoldást. Billentyűzet CTRL + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logikai alkalmazás üzembe helyezése az Azure-ban

A logikai alkalmazás futtatása és tesztelése előtt telepítse az alkalmazást az Azure-ba a Visual studióból.

1. A Megoldáskezelőben a projekt helyi menüjéből válassza az **Üzembe helyezés** > **Új** elemet. Ha szükséges, jelentkezzen be Azure-fiókjával.

   ![Új logikai alkalmazás központi telepítésének létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Ehhez a központi telepítéshez tartsa meg az alapértelmezett Azure-előfizetést, erőforráscsoportot és egyéb beállításokat. Válassza a **telepítés**lehetőséget.

   ![Logikai alkalmazás üzembe helyezése Azure-erőforráscsoportra](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adjon meg egy erőforrás-nevet a logikai alkalmazás számára. Mentse a beállításokat.

   ![Adja meg a logikai alkalmazás üzemelő példányának nevét](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot.

   ![Központi telepítés állapota a Visual Studio kimeneti ablakában](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Ha a kiválasztott összekötőknek bemenetre van szüksége, a háttérben megnyílik egy PowerShell-ablak, és megkérdezi a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![PowerShell-kérés jelszavakhoz vagy titkos kulcsokhoz](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Az üzembe helyezés befejeződése után a logikai alkalmazás a Azure Portalban fut, és a megadott ütemterven (percenként) fut. Ha az eseményindító új hírcsatorna-elemeket talál, az eseményindító tüzek, amely létrehoz egy munkafolyamat-példányt, amely futtatja a logikai alkalmazás műveleteit. A logikai alkalmazás minden új elemmel elküld e-mailt. Ellenkező esetben, ha az trigger nem talál új elemeket, az trigger nem gyullad ki, és "kihagyja" a munkafolyamat példányát. Az ellenőrzés előtt a logikai alkalmazás a következő intervallumig vár.

   Itt láthatja a logikai alkalmazás által küldött e-maileket. 
   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

   ![Az Outlook minden új RSS-elemről e-mailt küld](./media/quickstart-create-logic-apps-with-visual-studio/example-outlook-email.png)

Gratulálunk, sikeresen felépítette és üzembe helyezte a logikai alkalmazást a Visual Studióban. A logikai alkalmazás kezeléséhez és az előzményeinek megtekintéséhez lásd a [logikai alkalmazások Visual Studióval történő kezelésével](../logic-apps/manage-logic-apps-with-visual-studio.md) foglalkozó cikket.

## <a name="add-new-logic-app"></a>Új logikai alkalmazás hozzáadása

Ha rendelkezik egy meglévő Azure-erőforráscsoport-projekttel, a JSON-vázlat ablak használatával hozzáadhat egy új üres logikai alkalmazást a projekthez.

1. A Megoldáskezelőban nyissa meg a `<logic-app-name>.json` fájlt.

1. A **nézet** menüben válassza a **További Windows** > **JSON-körvonal**elemet.

1. Ha hozzá szeretne adni egy erőforrást a sablonfájl számára, válassza a JSON-vázlat ablak tetején található **erőforrás hozzáadása** lehetőséget. A JSON-vázlat ablak, kattintson a jobb gombbal, vagy **erőforrások**, és válassza ki **új erőforrás hozzáadása**.

   ![A JSON-vázlat ablakban vegyen fel új erőforrást](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Az a **erőforrás hozzáadása** párbeszédpanelen keresse meg és válassza ki **logikai alkalmazás**. Nevezze el a logikai alkalmazást, és válassza a **Hozzáadás**.

   ![Új logikai alkalmazás erőforrásának hozzáadása a projekthez](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a logikai alkalmazással, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazás létrehozásához használt fiókkal.

1. Az Azure főmenüjében válassza az **Erőforráscsoportok** lehetőséget.
Válassza ki a logikai alkalmazáshoz tartozó erőforráscsoportot, és válassza az **Áttekintés**lehetőséget.

1. Az **Áttekintés** lapon válassza az **Erőforráscsoport törlése** lehetőséget. Megerősítésként írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

   ![Logikai alkalmazás erőforráscsoportjának törlése](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Törölje a Visual Studio-megoldást a helyi számítógépről.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy logikai alkalmazást állított össze, helyezett üzembe és futtatott a Visual Studióval. A Logic apps és a Visual Studio használatával végzett speciális üzembe helyezés kezelésével és végrehajtásával kapcsolatos információkért tekintse meg a következő cikkeket:

> [!div class="nextstepaction"]
> * [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)