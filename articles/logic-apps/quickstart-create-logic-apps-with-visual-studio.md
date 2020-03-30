---
title: Feladat-munkafolyamatok automatizálása a Visual Studióban
description: Ismétlődő munkafolyamatok létrehozása, ütemezése és futtatása vállalati integrációhoz az Azure Logic Apps és a Visual Studio használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241653"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rövid útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps alkalmazásokkal – Visual Studio

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Visual Studio segítségével munkafolyamatokat hozhat létre olyan feladatok és folyamatok automatizálására, amelyek alkalmazásokat, adatokat, rendszereket és szolgáltatásokat integrálnak vállalatok és intézmények között. Ez a rövid útmutató bemutatja, hogyan tervezheti meg és hozhat létre ezeket a munkafolyamatokat logikai alkalmazások létrehozásával a Visual Studióban, és telepítheti ezeket az alkalmazásokat az Azure-ba. Bár ezeket a feladatokat az Azure Portalon is elvégezheti, a Visual Studio lehetővé teszi, hogy a logikai alkalmazásokat forrásvezérléshez adja hozzá, különböző verziókat tegyen közzé, és Hozzon létre Azure Resource Manager-sablonokat a különböző központi telepítési környezetekhez.

Ha most jön az Azure Logic Apps, és csak azt az alapvető fogalmakat, próbálja meg a [rövid útmutató t egy logikai alkalmazás az Azure Portalon.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A Logic App Designer hasonlóan működik az Azure Portalon és a Visual Studióban is.

Ebben a rövid útmutatóban ugyanazt a logikai alkalmazást hozza létre a Visual Studióval, mint az Azure Portal gyorsan. Ez a logikai alkalmazás figyeli a webhely RSS-hírcsatornáját, és e-mailt küld az adott hírcsatornában lévő minden új elemhez. A kész logikai alkalmazás így néz ki:

![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – közösségi kiadás vagy újabb](https://aka.ms/download-visual-studio). Ez a rövid útmutató a Visual Studio Community 2017-et használja.

    > [!IMPORTANT]
    > A Visual Studio 2019 vagy 2017 telepítésekor győződjön meg arról, hogy az **Azure fejlesztési** számítási feladatait választja.

  * [Microsoft Azure SDK a .NET (2.9.1 vagy újabb) rendszerhez.](https://azure.microsoft.com/downloads/) További információ az [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * A visual studio-bővítmény legújabb Azure Logic Apps-eszközei a kívánt verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Internet-hozzáférés a beágyazott Logikaialkalmazás-tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége az Azure-ban lévő erőforrások létrehozásához, valamint a logikai alkalmazás összekötőiből származó tulajdonságok és adatok olvasásához. A Dynamics CRM Online-kapcsolatok esetében például a tervező ellenőrzi a CRM-példány alapértelmezett és egyéni tulajdonságait.

* A Logic Apps által támogatott e-mail-fiók, például Office 365 Outlook, Outlook.com vagy Gmail. Más szolgáltatók esetében tekintse át az [összekötők listáját itt.](https://docs.microsoft.com/connectors/) Ez a példa az Office 365 Outlookot használja. Ha más szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/management/overview.md).

1. Indítsa el a Visual Studiót. Jelentkezzen be Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet. (Billentyűzet: Ctrl + Shift + N)

   ![Az Új > Projekt menüpont kiválasztása a Fájl menüben](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza a **Cloud** > **Azure Resource Group lehetőséget.** Adjon nevet a projektnek, például a következőt:

   ![Azure erőforráscsoport-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Az erőforráscsoport nevei csak betűket,`.`számokat,`_`pontokat (`-`), aláhúzást`(`( `)`), kötőjeleket ( és`.`zárójeleket ( , ) tartalmazhatnak, de nem *végződhetnek* pontokkal ( ).
   >
   > Ha **a Cloud** vagy az Azure Resource **Group** nem jelenik meg, telepítse az Azure SDK-t a Visual Studio számára.

   A Visual Studio 2019 használata esetén kövesse az alábbi lépéseket:

   1. Az **Új projekt létrehozása** mezőben válassza ki az **Azure Resource Group** projektet visual C# vagy Visual Basic. Válassza a **Tovább lehetőséget.**

   1. Adja meg a használni kívánt Azure-erőforráscsoport nevét és egyéb projektadatokat. Kattintson a **Létrehozás** gombra.

1. A sablonlistában válassza a **Logikai alkalmazás** sablont. Válassza **az OK gombot.**

   ![Logikaialkalmazás-sablon kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Miután a Visual Studio létrehozta a projektet, megnyílik a Megoldáskezelő, és megjeleníti a megoldást. A megoldásban a **LogicApp.json** fájl nem csak tárolja a logikai alkalmazás definícióját, hanem egy Azure Resource Manager sablon, amely a központi telepítéshez használható.

   ![A Megoldáskezelő az új logikai alkalmazással és az üzembe helyezési fájllal](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

Ha rendelkezik az Azure Resource Group projekt, hozza létre a logikai alkalmazás a **Blank Logic App** sablon.

1. A Megoldáskezelőben nyissa meg a **LogicApp.json** fájl helyi menüjét. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**. (Billentyűzet: Ctrl + L)

   ![A logikai alkalmazás .json-fájljának megnyitása a Logikaialkalmazás-tervezővel](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

   A Visual Studio kéri, hogy az Azure-előfizetésés egy Azure-erőforráscsoport létrehozása és üzembe helyezése a logikai alkalmazás és a kapcsolatok.

1. Előfizetés **esetén**válassza ki az Azure-előfizetést. Az **Erőforráscsoport**esetében válassza **az Új létrehozása lehetőséget** egy másik Azure-erőforráscsoport létrehozásához.

   ![Azure-előfizetés, erőforráscsoport és erőforrás helyének kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Beállítás | Példaérték | Leírás |
   | ------- | ------------- | ----------- |
   | Felhasználói fiók | Fabrikam <br> sophia-owen@fabrikam.com | A Visual Studio-ba való bejelentkezéskor használt fiók |
   | **Előfizetés** | Utólagos, használatalapú fizetés <br> (sophia-owen@fabrikam.com) | Az Azure-előfizetés és a társított fiók neve |
   | **Erőforráscsoport** | MyLogicApp-RG <br> (USA nyugati régiója) | Az Azure-erőforráscsoport és a logikai alkalmazás erőforrásainak tárolásához és üzembe helyezéséhez szükséges hely |
   | **Helyen** | **Megegyezik az erőforráscsoporttal** | A hely típusa és a logikai alkalmazás üzembe helyezésének adott helye. A helytípus egy Azure-régió vagy egy meglévő [integrációs szolgáltatási környezet (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>Ehhez a rövid útmutatóhoz tartsa a helytípust **Régió** és a Hely beállítását **az Erőforráscsoporthoz parancsra.** <p>**Megjegyzés:** Az erőforráscsoport-projekt létrehozása után [módosíthatja a helytípusát és helyét,](manage-logic-apps-with-visual-studio.md#change-location)de a különböző helytípus különböző módokon befolyásolja a logikai alkalmazást. |
   ||||

1. A Logic Apps Designer megnyit egy oldalt, amely egy bevezető videót és a gyakran használt eseményindítókat jeleníti meg. Görgessen le a videó mellett, és aktiválja a **Sablonok**lehetőséget, és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![„Üres logikai alkalmazás” kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának felépítése

Ezután adjon hozzá egy [RSS-eseményindítót,](../logic-apps/logic-apps-overview.md#logic-app-concepts) amely új hírcsatorna-elem megváltásakor aktiválódik. Minden logikai alkalmazás egy eseményindítóval kezdődik, amely akkor aktiválódik, ha bizonyos feltételek teljesülnek. A Logic Apps-motor a trigger minden aktiválásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

1. A Logic App Designer keresőmezőjében válassza az **Összes**lehetőséget. A keresőmezőbe írja be az "rss" szót. Az eseményindítók listájában válassza ezt az eseményindítót: **Hírcsatorna-elem közzétételekor**

   ![Logikai alkalmazás felépítése trigger és műveletek hozzáadásával](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Miután az eseményindító megjelenik a tervezőben, fejezze be a logikai alkalmazás kiépítését az [Azure Portal gyorsindítás](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)munkafolyamat-lépései végrehajtásával, majd térjen vissza ehhez a cikkhez. Az elkészült logikai alkalmazás az alábbihoz hasonlít:

   ![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Mentse a Visual Studio-megoldást. (Billentyűparancs: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logikai alkalmazás üzembe helyezése az Azure-ban

Mielőtt futtathatja és tesztelheti a logikai alkalmazást, telepítse az alkalmazást az Azure-ba a Visual Studióból.

1. A Megoldáskezelő helyi menüjében válassza az**Új** **telepítése parancsot.** >  Ha szükséges, jelentkezzen be Azure-fiókjával.

   ![A logikai alkalmazás üzemelő példányának létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Ebben a központi telepítésben tartsa meg az alapértelmezett Azure-előfizetést, erőforráscsoportot és egyéb beállításokat. Válassza az **Üzembe helyezés** lehetőséget.

   ![Logikai alkalmazás üzembe helyezése Azure-erőforráscsoportra](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazás erőforrásnevét. Mentse a beállításokat.

   ![Adja meg a logikai alkalmazás üzemelő példányának nevét](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot.

   ![Üzembe helyezés állapotának kimenete](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Ha a kiválasztott összekötők bemenetet igényel, a háttérben megnyílik egy PowerShell-ablak, és kéri a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![PowerShell-ablak](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   A központi telepítés befejezése után a logikai alkalmazás él az Azure Portalon, és a megadott ütemezés szerint fut (percenként). Ha az eseményindító új hírcsatorna-elemeket talál, az eseményindító aktiválódik, amely létrehoz egy munkafolyamat-példányt, amely futtatja a logikai alkalmazás műveleteit. A logikai alkalmazás minden új elemhez küld e-mailt. Ellenkező esetben, ha az eseményindító nem talál új elemeket, az eseményindító nem aktiválódik, és "kihagyja" a munkafolyamat létrehozása. A logikai alkalmazás megvárja a következő időközt az ellenőrzés előtt.

   Az alábbiakban a logikai alkalmazás által küldött mintae-maileket olvashatja. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

   ![Az Outlook minden új RSS-elemről e-mailt küld](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulálunk, sikeresen elkészítette és üzembe helyezte a logikai alkalmazást a Visual Studióval. A logikai alkalmazás kezeléséhez és az előzményeinek megtekintéséhez lásd a [logikai alkalmazások Visual Studióval történő kezelésével](../logic-apps/manage-logic-apps-with-visual-studio.md) foglalkozó cikket.

## <a name="add-new-logic-app"></a>Új logikai alkalmazás hozzáadása

Ha egy meglévő Azure Resource Group projekt, hozzáadhat egy új üres logikai alkalmazást, hogy a projekt a JSON Vázlat ablak használatával.

1. A Megoldáskezelőben `<logic-app-name>.json` nyissa meg a fájlt.

1. A **Nézet** menüben válassza az **Egyéb Windows** > **JSON Tagolás lehetőséget.**

1. Ha erőforrást szeretne hozzáadni a sablonfájlhoz, válassza az **Erőforrás hozzáadása** lehetőséget a JSON vázlatablak ának tetején. Vagy a JSON Vázlat ablakban nyissa meg az **erőforrások** helyi menüjét, és válassza **az Új erőforrás hozzáadása**parancsot.

   ![JSON Vázlat ablak](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Az **Erőforrás hozzáadása** párbeszédpanel keresőmezőjében keresse `logic app`meg a elemet, és válassza a **Logikai alkalmazás lehetőséget.** Nevezze el a logikai alkalmazást, és válassza a **Hozzáadás gombot.**

   ![Erőforrás hozzáadása](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a logikai alkalmazással, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazás létrehozásához használt fiókkal.

1. Az Azure Portal menüben válassza az **Erőforráscsoportok**lehetőséget, vagy keressen rá és válasszon **erőforráscsoportokat** bármelyik lapon. Válassza ki a logikai alkalmazás erőforráscsoportját.

1. Az **Áttekintés** lapon válassza az **Erőforráscsoport törlése**lehetőséget. Adja meg az erőforráscsoport nevét megerősítésként, és válassza a **Törlés gombot.**

   ![„Erőforráscsoportok” > „Áttekintés” > „Erőforráscsoport törlése”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Törölje a Visual Studio-megoldást a helyi számítógépről.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy logikai alkalmazást állított össze, helyezett üzembe és futtatott a Visual Studióval. A visual studióval rendelkező logikai alkalmazások speciális központi telepítésének kezeléséről és végrehajtásáról az alábbi cikkekben olvashat:

> [!div class="nextstepaction"]
> [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)
