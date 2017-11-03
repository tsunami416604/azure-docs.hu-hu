---
title: "A logic apps a Visual Studio - Azure Logic Apps alkalmazásokat kezeléséhez |} Microsoft Docs"
description: "A logic apps és az egyéb Azure eszközök Visual Studio Cloud Explorer kezelése"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>A Visual Studio Cloud Explorer a logic Apps alkalmazásokat kezeléséhez

Bár a [Azure-portálon](https://portal.azure.com/) tervezése és kezelése az Azure Logic Apps remek lehetőséget kínál, használhatja a Visual Studio Cloud Explorer sok Azure eszközök, beleértve a logic Apps alkalmazásokat kezeléséhez. Visual Studio Cloud Explorer lehetővé teszi, hogy a Tallózás gombra, kezeléséhez, szerkesztése, és letöltés a logic apps közzé. Felügyeleti feladatok előzményeinek megtekintése, futtassa, engedélyezése és letiltása. 

Mielőtt eléréséhez, és a Visual Studio a logic Apps alkalmazásokat kezeléséhez, telepítése és a Visual Studio eszközök konfigurálása az Azure Logic Apps. 

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2015-öt vagy a Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Legfrissebb Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 vagy újabb)
* [A Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Ha a beágyazott designer segítségével Internet-hozzáféréssel

## <a name="install-visual-studio-tools-for-logic-apps"></a>A Logic Apps a Visual Studio eszközök telepítése

Az előfeltételek telepítése után töltse le és telepítse az Azure Logic Apps eszközök Visual Studio.

1. Nyissa meg a Visual Studiót. Az a **eszközök** menü **bővítmények és frissítések**.
2. Bontsa ki a **Online** kategória a Visual Studio galériában online kereséséhez.
3. Keresse meg **Logic Apps** amíg meg nem látja **Azure Logic Apps Tools for Visual Studio**.
4. Töltse le és telepítse a bővítmény kattintson **letöltése**.
5. Indítsa újra a Visual Studio telepítése után.

> [!NOTE]
> Töltse le az Azure Logic Apps eszközök Visual Studio közvetlenül, keresse fel a [Visual Studio piactér](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Tallózással keresse meg a logic apps Cloud Explorerben

1.  A Cloud Explorer megnyitásához a **nézet** menüben válasszon **Cloud Explorer**.
2.  Tallózással keresse meg a Logic Apps alkalmazást, erőforráscsoporthoz vagy erőforrástípus. 

    * Ha tallózással erőforrástípusok szerint, jelölje be az Azure-előfizetéssel, bontsa ki a **Logic Apps** szakaszt, és válassza ki a Logic Apps alkalmazást. 
    * Ha tallózással erőforráscsoport, bontsa ki az erőforráscsoportot, amely rendelkezik a Logic Apps alkalmazást, és válassza ki a Logic Apps alkalmazást.

    A logikai alkalmazásnak parancsok megtekintéséhez kattintson a jobb gombbal a Logic Apps alkalmazást, vagy Cloud Explorer alján lehetőségek közül választhat a **műveletek** menü.

    ![Tallózással keresse meg a Logic Apps alkalmazást](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>A Logic Apps alkalmazást a Logic Apps-tervezővel szerkesztése

A Cloud Explorer nyissa meg a most üzembe helyezett logikai alkalmazás tervezőben használhatja az Azure portálon. 

* A Logic Apps alkalmazást, a Cloud Explorer szerkesztéséhez kattintson a jobb gombbal a Logic Apps alkalmazást, és válassza **nyissa meg a Logic App szerkesztő**. 

* A frissítések közzétételéhez a felhőbe, válassza a **közzététel**. 

* Egy új futtató indításához válassza **futtatása eseményindító**.

![Logic Apps-Tervező](./media/logic-apps-manage-from-vs/designer.png)

A designer alkalmazásból is **letöltése** logikai alkalmazás. Ez a művelet automatikusan parameterizes a logic app-definíciót, és menti a definíció az Azure Resource Manager központi telepítési sablont. A központi telepítési sablont az Azure erőforráscsoport-projekt is hozzáadhat.

## <a name="browse-your-logic-app-run-history"></a>Keresse meg a Logic Apps alkalmazást futtatási előzményei

A logikai alkalmazásnak futtatási előzményeit megtekintéséhez kattintson a jobb gombbal a Logic Apps alkalmazást, és válassza ki **nyílt futtatási előzményei**. A futtatási előzményei alapján látható tulajdonságok sorrendjének módosításához jelölje ki az oszlop fejlécére.

![futtatási előzményei](media/logic-apps-manage-from-vs/runs.png)

Egy példány futtatási előzményeit megjelenítése, ellenőrizheti a futtatási eredményeket, beleértve a be- és kimenetekkel minden lépésében kattintson duplán a futtatási példányai közül.

![Futtassa a előzménylistát, bemenetei és kimenetei lépéseiből](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Következő lépések

* [Az első logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md)
* [Tervezési, elkészítéséhez és logikai alkalmazások telepítése a Visual Studio](logic-apps-deploy-from-vs.md)
* [Gyakori példák és felhasználási helyzetek megtekintése](logic-apps-examples-and-scenarios.md)
* [Videó: Az Azure Logic Apps üzleti folyamatok automatizálása](http://channel9.msdn.com/Events/Build/2016/T694)
* [Videó: Rendszerintegráció az Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
