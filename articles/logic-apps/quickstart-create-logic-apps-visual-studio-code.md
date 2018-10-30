---
title: Létrehozása és kezelése a Visual Studio Code – Azure Logic Apps használatával automatizált munkafolyamatok |} A Microsoft Docs
description: 'Gyors útmutató: hogyan hozhat létre, és JSON-ban a Visual Studio Code (a VS Code) rendelkező logikai alkalmazások kezelése'
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229617"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Rövid útmutató: Létrehozása és kezelése, automatikus logic app-munkafolyamatok – Visual Studio Code

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Visual Studio Code-ban hozhat létre és kezelheti a logic apps, amelyek segítségével automatizálhatja a feladatokat, a munkafolyamatok és folyamatok, alkalmazások, adatok, rendszerek és szolgáltatások integrálásához a különböző szervezetek és vállalatok számára. A rövid útmutató bemutatja, hogyan hozhat létre, és használata a kód-alapú élményen keresztül a munkafolyamat-definíciós séma JavaScript Object Notation (JSON) logikaialkalmazás-munkafolyamat definíciók szerkesztéséhez. Emellett használhatja a már üzembe helyezte a meglévő logic apps <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> a felhőben. 

Bár az ugyanezen feladatokat is végezhet a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> és a Visual Studióban, megkönnyítse az első lépéseket a Visual Studio Code, ha már ismeri az logikaialkalmazás-definíciókat, és közvetlenül a kódot szeretne. Például akkor is letiltása, engedélyezése, törlése és a frissítés már létrehozott logic apps. Ezenkívül használhatja a logic apps és az integrációs fiókok bármely fejlesztői platform, amelyen futtatja a Visual Studio Code, például a Linux, Windows és Mac rendszerre.

Ebben a cikkben az ugyanolyan logikai alkalmazást hozhat létre a [gyors útmutató: a logikai alkalmazás létrehozása az Azure Portalon](../logic-apps/quickstart-create-first-logic-app-workflow.md), amely több összpontosít az alapvető fogalmait. A logikai alkalmazás a Visual Studio Code-példához hasonlóan néz ki:

![Befejezett logikai alkalmazás](./media/create-logic-apps-visual-studio-code/overview.png)

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

* Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Alapvető ismeretek szerezhetők [logikai alkalmazás munkafolyamat-definíciókhoz](../logic-apps/logic-apps-workflow-definition-language.md) és azok szerkezetét, amely használja a JavaScript Object Notation (JSON) 

  Ha most ismerkedik a Logic Apps, próbálja ki a rövid útmutató bemutatja [az első logikai alkalmazás létrehozása az Azure Portalon](../logic-apps/quickstart-create-first-logic-app-workflow.md), amely több összpontosít az alapvető fogalmait. 

* A webes hozzáférés az Azure és az Azure-előfizetéshez való bejelentkezéshez

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket: 

  * <a href="https://code.visualstudio.com/" target="_blank">A Visual Studio Code verzió 1.25.1 vagy újabb</a>, ingyenes

  * Azure Logic Apps Visual Studio Code-bővítmény

    Letöltheti és telepítheti a bővítményt a a [Visual Studio-piactér](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) vagy közvetlenül a Visual Studio Code belül. 
    Ellenőrizze, hogy a Visual Studio Code telepítése után újra. 

    ![Keresse meg a "Visual Studio Code-bővítmény az Azure Logic Apps"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Ellenőrizze, hogy a bővítmény megfelelően telepítve az Azure a Visual Studio Code eszköztár ikon látható. 

    ![Kiterjesztés telepítése](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    További információkért lásd: <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">bővítmény Marketplace</a>. Is megtekintheti, és küldje el ezt a bővítményt nyílt forráskódú verzió hozzájárulások meglátogatják a [Azure Logic Apps-bővítmény a Visual Studio Code a Githubon](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Nyissa meg a Visual Studio Code-ot. A Visual Studio Code eszköztáron kattintson az Azure ikonra. 

   ![Válassza ki az Azure ikon](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Az Azure ablakban alatt **Logic Apps**válassza **jelentkezzen be Azure**. 

   ![Válassza a "Bejelentkezés az Azure-bA"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Most jelentkezzen be a megadott hitelesítési kód megadására kéri. 

1. Másolja a hitelesítési kódot, és válassza **másolja ki és nyissa meg a**, amely egy új böngészőablakban megnyitja.

   ![Bejelentkezési kérések jelenhetnek](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Adja meg a hitelesítési kódot. Amikor a rendszer kéri, válassza ki a **Folytatás**.

   ![Írja be a kódot](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Válassza ki az Azure-fiókjával. Miután bejelentkezett, zárja be a böngészőt, és térjen vissza a Visual Studio Code-ot.

   Az Azure ablakban a Logic Apps és integrációs fiókok ablaktábla most megjelenítése az Azure-előfizetést a fiókjában. 

   ![Előfizetés kiválasztása](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Ha nem látja az előfizetések várható, melletti **Logic Apps** felirat, válassza a **kiválasztása az előfizetések** (szűrő ikon). Keresse meg és válassza ki azt a kívánt előfizetést.

1. Bármely meglévő logic apps vagy az integrációs fiókok megtekintése az Azure-előfizetésében, bontsa ki az előfizetést.

   ![A logic apps és az integrációs fiókok megtekintése](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

1. Ha még nem jelentkezett be az Azure-előfizetéshez a Visual Studio Code-ban található kövesse az ebben a cikkben [jelentkezzen be most](#sign-in-azure).

1. Az előfizetés helyi menüből válassza ki a **létrehozás**.

   ![Válassza a "Create"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Jeleníti meg az előfizetés Azure-erőforráscsoportot a listából válasszon ki egy meglévő erőforráscsoportot, vagy **hozzon létre egy új erőforráscsoportot**. 

   Ez a példa létrehoz egy új erőforráscsoportot:

   ![Új erőforráscsoport létrehozása](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Adja meg az Azure-erőforráscsoport nevét, és nyomja le az ENTER BILLENTYŰT.

   ![Az erőforráscsoport neve](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Válassza ki az Adatközpont helyét a logikai alkalmazás metaadatainak mentésének helyét.

   ![Hely kiválasztása](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Adja meg a logikai alkalmazás nevét, és nyomja le az ENTER BILLENTYŰT.

   ![Nevezze el a logikai alkalmazás](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Az új logikai alkalmazás most már megjelenik az Azure ablakában, az Azure-előfizetés. Most már megkezdheti a logikai alkalmazás munkafolyamat-definíció létrehozása.

1. Válassza ki a logikai alkalmazás menüjén **Megnyitás a szerkesztőben**. 

   ![A szerkesztőben megnyitott logikai alkalmazás](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   A Visual Studio Code megnyílik a logikaialkalmazás-munkafolyamat definition sablon (. logicapp.json fájlt) a logikai alkalmazás munkafolyamat létrehozásának megkezdéséhez.

   ![Új logikai alkalmazás munkafolyamat-definíció](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. A logikai alkalmazás munkafolyamat-definíciós sablon fájlt indítsa el a logikai alkalmazás munkafolyamat-definíció létrehozása. Ha technikai, olvassa el a [Azure Logic Apps munkafolyamat-definíciós nyelv sémáját](../logic-apps/logic-apps-workflow-definition-language.md).

   Íme egy példa logika definícióját. Általában a JSON-elemek betűrendbe jelennek meg minden egyes szakaszokon belül, de ez a példa bemutatja ezeket az elemeket nagyjából a logikai alkalmazás lépés jelenik meg a tervezőben a sorrendben.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Ha elkészült, mentse a logikai alkalmazás csomagdefiníciós fájl. Amikor felszólítja, hogy erősítse meg a logikai alkalmazás definíciójának feltöltése az Azure-előfizetéshez, válassza a Visual Studio Code **feltöltése**.

   ![Töltse fel az új logikai alkalmazás](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Miután a Visual Studio Code közzéteszi a logikai alkalmazás az Azure-ba, annak az alkalmazás most élő és az Azure Portalon futó. 

   ![Logikai alkalmazás közzététele az Azure Portalon](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Logikai alkalmazás szerkesztése

Egy meglévő logikai alkalmazás, amely már üzembe helyezte az Azure-ban dolgozik, nyissa meg az alkalmazás a munkafolyamat-definíciós fájl Visual Studio Code-ban.

1. Ha még nem jelentkezett be az Azure-előfizetéshez a Visual Studio Code-ban található kövesse az ebben a cikkben [jelentkezzen be most](#sign-in-azure).

1. Az Azure ablakban alatt **Logic Apps**, bontsa ki az Azure-előfizetéshez, és válassza ki a logikai alkalmazást. 

1. A logikai alkalmazás menüjében válassza **Megnyitás a szerkesztőben**. Vagy a logikai alkalmazás neve, a Szerkesztés ikonra.

   ![Meglévő logikai alkalmazás szerkesztő megnyitása](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Megnyílik a Visual Studio Code-ot a. a logikai alkalmazás munkafolyamat-definíció logicapp.json fájlt.

   ![Megnyitott logikai alkalmazás munkafolyamat-definíció](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Hajtsa végre a módosításokat a logikai alkalmazás definíciójában.

1. Miután végzett, mentse a módosításokat.

1. Ha a Visual Studio Code kéri, hogy frissítse a logikai alkalmazás definíciójának az Azure-előfizetésében, válassza **feltöltése**. 

   ![Töltse fel a módosítások](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

