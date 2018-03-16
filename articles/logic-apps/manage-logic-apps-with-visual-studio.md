---
title: "A logic apps a Visual Studio - Azure Logic Apps alkalmazásokat kezeléséhez |} Microsoft Docs"
description: "A logic apps és az egyéb Azure eszközök Visual Studio Cloud Explorer kezelése"
author: ecfan
manager: SyntaxC4
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: db847d5fa3d5f5b2b2f0293f1756226870a8b47e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>A Visual Studio logic Apps-alkalmazások kezelése

Is hozhat létre, szerkesztése, kezelése és a logic Apps-alkalmazások telepítése a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, használhatja a Visual Studio is, ha hozzá szeretne adni a forrás-vezérlő, különböző verziói közzététele, és hozzon létre a logic apps [Azure-erőforrás Manager](../azure-resource-manager/resource-group-overview.md) sablonok különböző telepítési környezetekben. A Visual Studio Cloud Explorer található, és a logic Apps alkalmazások és más Azure-erőforrások kezelése. Például nyissa meg, töltse le, szerkesztése, futtatása is futtatási előzményei, tiltsa le és engedélyezze a logic apps már telepített tekintse meg az Azure-portálon. Ha most ismerkedik az Azure Logic Apps a Visual Studio használata, [logic Apps alkalmazások létrehozása a Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Telepítése vagy a Visual Studio logikai alkalmazás közzétételi felülírja az alkalmazás az Azure portálon. Ezért ha módosítja a megőrizni kívánt Azure-portálon, győződjön meg arról, hogy Ön [frissítse a logikai alkalmazást a Visual Studio](#refresh) telepítése, illetve a Visual Studio közzétenni a következő időpont előtt Azure-portálról.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiók</a>.

* Töltse le és telepítse ezeket az eszközöket, ha már nincs rájuk: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">A Visual Studio 2017 vagy Visual Studio 2015 - Community edition vagy nagyobb</a>. 
  A gyors üzembe helyezés ingyenes Visual Studio Community 2017 használja.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Az Azure SDK (2.9.1 vagy újabb)</a> és <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">A Visual Studio 2017 Azure Logic Apps eszközök</a> vagy a <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015-verzió</a> 
  
    Töltse le és telepítse az Azure Logic Apps eszközök közvetlenül a Visual Studio piactérről, vagy további <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">telepítése ezt a bővítményt a Visual Studio belül</a>. 
    Győződjön meg arról, hogy újraindította a Visual Studio telepítése után.

* A beágyazott Logic App-tervező használata során Internet-hozzáféréssel

  A Tervező létrehozni az erőforrásokat az Azure-ban és a tulajdonságok és az adatokat olvasni az összekötők a Logic Apps alkalmazást az internetkapcsolat szükséges. 
  Például ha a Dynamics CRM Online-összekötő használata esetén a Tervező ellenőrzi a CRM példány elérhető alapértelmezett és egyéni tulajdonságokat.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>A logic Apps alkalmazások keresése

A Visual Studióban található összes, az Azure-előfizetéshez társított és az Azure portálon Cloud Explorer használatával telepített logikai alkalmazás.

1. Nyissa meg a Visual Studiót. Az a **nézet** menü **Cloud Explorer**.

2. Válassza a Cloud Explorer **fiókkezelés**. Válassza ki az Azure-előfizetése társítva van a logic Apps alkalmazásokat, majd válassza a **alkalmaz**. Példa:

   ![Válassza ki a "Felhasználóifiók-kezelés"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Alapján keres, hogy által **erőforráscsoportok** vagy **erőforrástípusok**, kövesse az alábbi lépéseket:

   * **Erőforráscsoportok**: az Azure-előfizetéséhez, a Cloud Explorer adott előfizetéshez tartozó összes erőforráscsoport jeleníti meg. 
   Bontsa ki az erőforráscsoportot, amely tartalmazza a Logic Apps alkalmazást, majd válassza ki a Logic Apps alkalmazást.

   * **Erőforrástípus**: bontsa ki az Azure-előfizetéshez **Logic Apps**. Miután Cloud Explorer jeleníti meg a központilag telepített programot minden olyan alkalmazás, az előfizetéshez társított, válassza ki a Logic Apps alkalmazást.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Megnyitás Visual Studióban

A Visual Studio nyissa meg a logic apps korábban létrehozott és telepített, közvetlenül az Azure portálon keresztül vagy a Visual Studio Azure Resource Manager projektként.

1. Cloud Explorer megnyitásához, és keresse a Logic Apps alkalmazást. 

2. Válassza ki a logikai alkalmazás helyi menü, **nyissa meg a Logic App szerkesztő**.

   Ez a példa bemutatja a logic apps erőforrástípusok szerint, így a logic apps csoportban jelennek meg a **Logic Apps** szakasz.

  ![Nyissa meg a telepített logikai alkalmazás Azure-portálon](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   A logikai alkalmazást a Logic App tervezőben, a designer alján megnyitása után dönthet úgy **kódnézetben** , hogy tekintse át a logic app definition struktúráját. 
   Ha szeretne létrehozni a logikai alkalmazást a központi telepítési sablont, [Azure Resource Manager-sablon letöltése](#download-logic-app) az adott logikai alkalmazáshoz. További információ [Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Töltse le az Azure-ból

A logic apps letöltheti a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a> , és mentse őket [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) sablonok. Majd helyileg szerkesztheti a sablonok a Visual Studio és testre szabhatja a logic apps különböző telepítési környezetekben. A logic apps automatikusan letöltése *parameterizes* belül a definíciójukat [Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment), amely JavaScript Object Notation (JSON) is használhatja.

1. A Visual Studio Cloud Explorer megnyitásához, majd keresse meg és jelölje ki a logikai alkalmazást, amely letölti az Azure-ból.

2. Válassza ki az alkalmazás helyi menü, **nyissa meg a Logic App szerkesztő**.

   A Logic App Designer megnyílik, és a logikai alkalmazást jeleníti meg. 
   Tekintse át a logikai alkalmazás alapul szolgáló definíció- és struktúra, a designer alján válassza **kódnézetben**. 

3. A Tervező eszköztáron válassza **letöltése**.

   ![Válassza a "Letöltés"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Amikor a rendszer kéri a helyet, keresse meg a helyet, és mentse a logic app-definíciót a Resource Manager-sablon JSON (.JSON kiterjesztésű) formátumban. 

Megjelenik a logic app-definíciót a `resources` alszakasz a Resource Manager-sablon belül. Most már a logic app-definíciót és szerkesztheti a Visual Studio Resource Manager-sablon. Azt is megteheti a sablon az Azure Resource Manager projekt egy Visual Studio-megoldáshoz. További tudnivalók [erőforrás-kezelő a logic apps a Visual Studio projekt](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Frissítse az Azure-ból

Ha szerkeszti a Logic Apps alkalmazást az Azure portálon, és megtartja ezeket a módosításokat, győződjön meg arról, hogy ezeket a módosításokat a Visual Studióban az alkalmazás verziójával frissíti. 

* A Visual Studio, a Logic App Designer eszköztáron válassza **frissítése**.

  – vagy –

* Visual Studio Cloud Explorerben nyissa meg a Logic Apps alkalmazást helyi menüt, és válassza ki **frissítése**. 

![Frissítse a logikai alkalmazás frissítésekkel](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Logic app frissítések közzétételéhez

Ha készen áll a frissítések központi telepítéséhez a logic app a Visual Studio az Azure-ba, a Logic App Designer eszköztáron válassza **közzététel**.

![Frissített logikai alkalmazás közzététele](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>A Logic Apps alkalmazást manuális futtatása

A Visual Studio Azure szolgáltatásba telepített logikai alkalmazás manuálisan indíthatnak. A Logic App Designer eszköztáron válassza **futtatása eseményindító**.

![Manuálisan futtassa a logikai alkalmazást](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Ellenőrizze az állapotát, és a logic app futtatása kapcsolatos problémák diagnosztizálásához, tekintse át a részleteket, például bemenetekhez és kimenetekhez, azok futtatja a Visual Studióban.

1. Cloud Explorerben nyissa meg a Logic Apps alkalmazást helyi menüt, és válassza ki **nyílt futtatási előzményei**.

   ![Nyissa meg a futtatási előzményei](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. A megadott futtató részleteinek megtekintéséhez kattintson duplán a futtató. Példa:

   ![Részletes futtatási előzményei](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Tulajdonságban a táblázat rendezéséhez válassza ki az oszlop fejlécére, az adott tulajdonságnál. 

3. Bontsa ki a lépéseket, amelyek be- és kimenetekkel meg szeretné tekinteni. Példa:

   ![Be- és kimenetekkel egyes lépéseihez szükséges megtekintése](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás engedélyezése vagy letiltása

A logikai alkalmazás törlése, nélkül az eseményindító művelet ki, a következő alkalommal, amikor a megadott feltétel teljesülésekor a állíthatók le. A logikai alkalmazás letiltása megakadályozza, hogy a létrehozása és a logikai alkalmazásnak jövőbeli munkafolyamat példánya fut a Logic Apps motor.
Cloud Explorerben nyissa meg a Logic Apps alkalmazást helyi menüt, és válassza ki **letiltása**.

![Tiltsa le a Logic Apps alkalmazást](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Ha készen áll a Logic Apps alkalmazást folytatja működését, újraaktiválhatja a Logic Apps alkalmazást. Cloud Explorerben nyissa meg a Logic Apps alkalmazást helyi menüt, és válassza ki **engedélyezése**.

![A logikai alkalmazás engedélyezése](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>A logikai alkalmazás törlése

A Logic Apps alkalmazást törölni az Azure-portálon, a Cloud Explorer, nyissa meg a Logic Apps alkalmazást helyi menüt, majd válassza ki **törlése**.

![A logikai alkalmazás törlése](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezelheti a Visual Studio központilag telepített programot alkalmazásokat. A következő további információk a logikai alkalmazás definícióiról központi telepítés testreszabása:

> [!div class="nextstepaction"]
> [Szerző logikai alkalmazás definícióiról a JSON-ban](../logic-apps/logic-apps-author-definitions.md)