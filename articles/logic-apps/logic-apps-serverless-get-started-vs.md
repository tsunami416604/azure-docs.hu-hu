---
title: "Egy kiszolgáló nélküli alkalmazást a Visual Studio build |} Microsoft Docs"
description: "Ez az útmutató a létrehozás, telepítés és a Visual Studio alkalmazás kezelése az első kiszolgáló nélküli alkalmazást az első lépései."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: d3cd509a0073e45bf910e9a74a279d678ff03b63
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Egy kiszolgáló nélküli alkalmazást a Visual Studio és a Logic Apps és függvények létrehozása

Kiszolgáló nélküli eszközök és a képességek az Azure-ban, hogy a felhőalapú alkalmazások telepítéséhez és gyors fejlesztést.  Ez a dokumentum első lépések a Visual Studio egy kiszolgáló nélküli alkalmazást kialakításának koncentrál.  Az Azure-ban kiszolgáló nélküli áttekintést [ebben a cikkben található](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Felkészülés mindent

Az alábbiakban egy kiszolgáló nélküli alkalmazást a Visual Studio eszközből létrehozásához szükséges előfeltételeket:

* [A Visual Studio 2017](https://www.visualstudio.com/vs/) vagy a Visual Studio 2015 - Közösség, Professional és Enterprise
* [Logic Apps Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Legfrissebb Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 vagy újabb)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Az Azure Functions Core eszközök](https://www.npmjs.com/package/azure-functions-core-tools) hibakeresési működik helyileg
* Ha a beágyazott Logikaialkalmazás-tervezővel Internet-hozzáféréssel

## <a name="getting-started-with-a-deployment-template"></a>A központi telepítési sablont első lépések

Az Azure-ban kezelése erőforrások erőforráscsoporton belül történik.  Egy erőforráscsoportot az erőforrások logikai csoportosítása.  Erőforrás-csoportok lehetővé teszik, telepítését és az erőforrások gyűjteményeinek kezelését.  Egy kiszolgáló nélküli alkalmazást az Azure-ban az erőforráscsoport Azure Logic Apps, mind az Azure Functions tartalmazza.  Az erőforráscsoport-projektet a Visual Studio használatával azt is fejlesztéséhez, kezelése és központi telepítése a teljes alkalmazás egyetlen eszközként.

### <a name="create-a-resource-group-project-in-visual-studio"></a>A Visual Studio erőforráscsoport-projekt létrehozása

1. A Visual Studio Ide kattintva vehet fel egy **új projekt**
1. Az a **felhő** kategória, válassza ki, hozzon létre egy Azure **erőforráscsoport** projekt  
 * Ha nem látja a kategóriát vagy a felsorolt projekt, lehet, hogy az Azure SDK for Visual Studio telepítése
1. Adjon a projekt nevét és helyét, és válassza ki **Ok** létrehozása a Visual Studio kérni fogja a Sablonválasztás.  Kiválaszthatja, hogy az üres indításához indítsa el a logikai alkalmazás vagy egyéb erőforrásokat.  Azonban ebben az esetben azt egy Azure gyors üzembe helyezés sablon történő beolvasásához használja, egy kiszolgáló nélküli alkalmazást használatába.
1. Válassza ki a sablonok megjelenítendő **Azure gyors üzembe helyezési** ![kiválasztásával Azure gyors üzembe helyezési sablonok][1]
1. Válassza ki a kiszolgáló nélküli gyorsindítási sablonon: **101-logic-app-and-function-app** kattintson **Ok**

A következő gyorsindítási sablonon található az erőforráscsoport-projekt hoz létre a központi telepítési sablont.  A sablon tartalmaz egy egyszerű logikai alkalmazás, amely meghívja az Azure Functions, és visszaadja az eredményt.  Ha megnyitja a `azuredeploy.json` fájl a Megoldáskezelőben, tekintse meg a kiszolgáló nélküli alkalmazást az erőforrások.

## <a name="deploying-the-serverless-application"></a>A kiszolgáló nélküli alkalmazás telepítése

A Visual Studio megnyitásához a Logic App vizuális tervező használatára, szükség van egy előre telepített Azure-erőforráscsoportot.  Ez lehetővé teszi, hogy a Tervező létrehozása és a logikai alkalmazást az erőforrások és szolgáltatások használata.  A kezdéshez egyszerűen kell létrehozni a megoldás üzembe helyezéséhez.

1. Kattintson a jobb gombbal a projektre a Visual Studio, válassza ki **telepítés**, és hozzon létre egy **új** telepítési ![kiválasztása az új erőforrások telepítése][2]
1. Adjon meg érvényes Azure-előfizetés és az erőforráscsoport
1. Válasszon ki **telepítés** a megoldás
1. Adja meg a nevet a logikai alkalmazást és az Azure-függvény alkalmazást.  Az Azure-függvény nevét kell lenniük a globálisan egyedi

A kiszolgáló nélküli megoldás telepíti, a megadott erőforrás csoportjához.  Ha megnézi a **kimeneti** a Visual Studióban megtekintheti a központi telepítés állapotát.

## <a name="editing-the-logic-app-in-visual-studio"></a>A logikai alkalmazást a Visual Studio szerkesztése

A megoldás a bármely erőforráscsoport van telepítve, ha a vizuális tervezőt segítségével szerkesztheti, és módosítja a logikai alkalmazást.

1. Kattintson a jobb gombbal a `azuredeploy.json` a Solution Explorer fájlt, és válassza ki **a Logic Apps tervező megnyitása**
1. Válassza ki a **erőforráscsoport** és **hely** a megoldás a telepített, és válassza ki lett **OK**

A logikai alkalmazás vizuális tervező használatára kell a Visual Studio látható.  Továbbra is hozzáadni a lépéseket, a munkafolyamat módosítása és módosítások mentéséhez.  A Visual Studio eszközből a logic apps is létrehozhat.  Ha a jobb gombbal a **erőforrások** sablon navigator választhatja ki hozzáadása egy **logikai alkalmazás** a projekthez.  Üres a logic apps a vizuális tervezőt nélkül betölteni egy előre üzembe helyezés egy erőforráscsoportot.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Kezelése és megtekintése a központilag telepített programot alkalmazások futtatási előzményei

Is kezelése és megtekintése a logic apps Azure szolgáltatásba telepített futtatási előzményeit.  Ha megnyitja a **Cloud Explorer** eszköz a Visual Studióban, kattintson a jobb gombbal a logikai alkalmazást és szerkesztése, a tulajdonságainak megtekintése és tiltsa le, vagy a futtatási előzmények megtekintése.  A Szerkesztés is lehetővé teszi egy közzétett logikai alkalmazás letöltése a Visual Studio erőforráscsoport-projektben.  Ez azt jelenti, hogy akkor is, ha használatba összeállítani saját logikai alkalmazását az Azure portálon, is importálja, és a Visual Studio az adatbázis felügyeletét.

## <a name="developing-an-azure-function-in-visual-studio"></a>A Visual Studio egy Azure függvény fejlesztése

A központi telepítési sablont a megoldást a megadott git-tárházban szereplő Azure funkciók telepíti a `azuredeploy.json` változók.  Ha létrehozhat egy függvény projektet a megoldáson, jelölje be a verziókövetési rendszerrel (GitHub, a Visual Studio Team Services stb.), és a frissítés a `repo` változó, a sablon telepíteni fogja az Azure-függvény.

### <a name="creating-an-azure-function-project"></a>Azure-függvény projekt létrehozása

JavaScript, Python, F #, Bash, kötegelt vagy PowerShell használata esetén kövesse a [szükséges lépések a funkciók CLI](../azure-functions/functions-run-local.md) projekt létrehozásához.  Ha egy függvényt a C# fejleszt, használhatja a [C# osztálytár](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) a jelenleg használt megoldás az Azure-függvény.

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan hozhat létre egy kiszolgáló nélküli közösségi irányítópult](logic-apps-scenario-social-serverless.md)
* [A Visual Studio Cloud Explorer logikai alkalmazás kezelése](manage-logic-apps-with-visual-studio.md)
* [Logic App munkafolyamatdefiníciós nyelve](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
