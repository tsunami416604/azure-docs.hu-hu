---
title: Munkafolyamatok létrehozása és kezelése a Visual Studio Code-Azure Logic Apps
description: Rövid útmutató – Logic app JSON-definíciók létrehozása és kezelése a Visual Studio Code-ban (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: 34de50517580c84c78ee1f192daa908b3f262670
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035445"
---
# <a name="quickstart-create-and-manage-logic-app-definitions-by-using-visual-studio-code"></a>Gyors útmutató: Logic app-definíciók létrehozása és kezelése a Visual Studio Code használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a Visual Studio Code használatával olyan logikai alkalmazásokat hozhat létre és kezelhet, amelyek segítségével automatizálhatja a feladatokat, munkafolyamatokat és folyamatokat az alkalmazások, adatok, rendszerek és szolgáltatások integrálásához a szervezetek és a vállalatok között. Ez a rövid útmutató bemutatja, hogyan hozhat létre és szerkeszthet logikai alkalmazás-munkafolyamati definíciókat, ha a JavaScript Object Notation (JSON) munkafolyamat-definíciós sémájának használatával programkód-alapú felhasználói felületen keresztül dolgozik. Az Azure-ban már üzembe helyezett meglévő logikai alkalmazások is dolgozhatnak.

Habár ugyanezeket a feladatokat a [Azure Portal](https://portal.azure.com) és a Visual Studióban is elvégezheti, gyorsabban megkezdheti a Visual Studio Code-ban, ha már ismeri a Logic app-definíciókat, és közvetlenül a kódban szeretne dolgozni. Például letilthatja, engedélyezheti, törölheti és frissítheti a már létrehozott Logic apps-alkalmazásokat. Emellett a Logic apps és az integrációs fiókok bármilyen fejlesztői platformon használhatók, ahol a Visual Studio Code fut, például a Linux, a Windows és a Mac.

Ebben a cikkben ugyanezt a logikai alkalmazást hozhatja [létre ebből a](../logic-apps/quickstart-create-first-logic-app-workflow.md)rövid útmutatóból, amely az alapfogalmakra összpontosít. A Visual Studio Code-ban a logikai alkalmazás a következő példához hasonlóan néz ki:

![Példa a logikai alkalmazás munkafolyamatának definíciója](./media/create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapszintű ismeretek a [Logic app-munkafolyamatok meghatározásáról](../logic-apps/logic-apps-workflow-definition-language.md) és azok struktúrájáról, amely JavaScript Object Notationt (JSON) használ

  Ha még nem ismeri a Logic Appst, próbálja [ki ezt a](../logic-apps/quickstart-create-first-logic-app-workflow.md)rövid útmutatót, amely létrehozza az első logikai alkalmazásait a Azure Portalban, és jobban összpontosít az alapfogalmakra.

* Hozzáférés a webhelyhez az Azure-ba való bejelentkezéshez és az Azure-előfizetéshez

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * A [Visual Studio Code 1.25.1 vagy újabb verziója](https://code.visualstudio.com/), amely ingyenes

  * Visual Studio Code-bővítmény a Azure Logic Appshoz

    Ezt a bővítményt letöltheti és telepítheti a [Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) , vagy közvetlenül a Visual Studio Code-ból. Győződjön meg arról, hogy a telepítés után újratölti a Visual Studio Code-ot.

    ![A "Visual Studio Code-bővítmény Azure Logic Apps" keresése](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Annak ellenőrzéséhez, hogy a bővítmény megfelelően van-e telepítve, válassza ki a Visual Studio Code eszköztárán megjelenő Azure-ikont.

    ![A bővítmény megfelelő telepítésének megerősítése](./media/create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    További információ: [bővítmény piactér](https://code.visualstudio.com/docs/editor/extension-gallery). A bővítmény nyílt forráskódú verziójának beszerzéséhez látogasson el a [Azure Logic apps-bővítményt a Visual Studio Code](https://github.com/Microsoft/vscode-azurelogicapps)-hoz a githubon.

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Nyissa meg a Visual Studio Code-ot. A Visual Studio Code eszköztáron válassza az Azure ikont.

   ![Válassza ki az Azure ikont a Visual Studio Code eszköztáron](./media/create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Az Azure ablak **Logic apps**területén válassza a bejelentkezés az **Azure**-ba lehetőséget.

   ![Válassza a "Bejelentkezés az Azure-ba" lehetőséget.](./media/create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   Ekkor a rendszer felszólítja, hogy jelentkezzen be a megadott hitelesítési kód használatával.

1. Másolja a hitelesítési kódot, majd válassza a **másolás & Megnyitás**lehetőséget, amely új böngészőablakot nyit meg.

   ![Az Azure-bejelentkezéshez használandó hitelesítési kód másolása](./media/create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

1. Adja meg a hitelesítési kódot. Ha a rendszer kéri, válassza a **Folytatás**lehetőséget.

   ![Adja meg az Azure-bejelentkezéshez használandó hitelesítési kódot](./media/create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Válassza ki az Azure-fiókját. A bejelentkezés után lezárhatja a böngészőt, és visszatérhet a Visual Studio Code-ba.

   Az Azure ablakban a Logic Apps és az integrációs fiókok panel most megjeleníti az Azure-előfizetéseket a fiókjában.

   ![Azure-előfizetés kiválasztása](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Ha nem látja a várt előfizetéseket, **Logic apps** címke mellett válassza az **előfizetések kiválasztása** (szűrő ikon) lehetőséget. Keresse meg és válassza ki a kívánt előfizetéseket.

1. Az Azure-előfizetésében meglévő logikai alkalmazások vagy integrációs fiókok megtekintéséhez bontsa ki az előfizetését.

   ![Logikai alkalmazások és integrációs fiókok megtekintése](./media/create-logic-apps-visual-studio-code/view-existing-logic-apps-azure.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

1. Ha még nem jelentkezett be az Azure-előfizetésbe a Visual Studio code-on belül, kövesse a cikk lépéseit a [bejelentkezéshez](#sign-in-azure).

1. Az előfizetés helyi menüjében válassza a **Létrehozás**elemet.

   ![Válassza a létrehozás lehetőséget az előfizetés menüből.](./media/create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

1. Válasszon ki egy meglévő erőforráscsoportot, vagy **hozzon létre egy új erőforráscsoportot**az előfizetésben lévő Azure-erőforráscsoportok megjelenítéséhez.

   Ez a példa egy új erőforráscsoportot hoz létre:

   ![Az új Azure-erőforráscsoport létrehozása](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Adja meg az Azure-erőforráscsoport nevét, majd nyomja le az ENTER billentyűt.

   ![Adja meg az Azure-erőforráscsoport nevét](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Válassza ki az adatközpont helyét a logikai alkalmazás metaadatainak mentéséhez.

   ![Válassza ki az Azure-helyet a logikai alkalmazás metaadatainak mentéséhez](./media/create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Adja meg a logikai alkalmazás nevét, majd nyomja le az ENTER billentyűt.

   ![Adja meg a logikai alkalmazás nevét](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Az új logikai alkalmazás most már megjelenik az Azure-előfizetéshez tartozó Azure-ablakban. Most elkezdheti létrehozni a logikai alkalmazás munkafolyamat-definícióját.

1. A logikai alkalmazás helyi menüjében válassza **a Megnyitás a szerkesztőben**lehetőséget.

   ![Logikai alkalmazás megnyitása a Code View Editorban](./media/create-logic-apps-visual-studio-code/open-new-logic-app-visual-studio-code.png)

   A Visual Studio Code egy Logic app munkafolyamat-definíciós sablont (. logicapp. JSON fájlt) nyit meg, így elkezdheti létrehozni a logikai alkalmazás munkafolyamatát.

   ![Új logikai alkalmazás munkafolyamat-definíciója](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. A Logic app munkafolyamat-definíciós sablon fájljában kezdje el felépíteni a logikai alkalmazás munkafolyamat-definícióját.
Technikai útmutató: [Azure Logic apps munkafolyamat-definíció nyelvi sémája](../logic-apps/logic-apps-workflow-definition-language.md).

   Íme egy példa a logikai definícióra. A JSON-elemek általában betűrendben jelennek meg az egyes szakaszokban. A minta azonban ezeket az elemeket nagyjából abban a sorrendben jeleníti meg, ahogy a logikai alkalmazás lépései megjelennek a tervezőben.

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

1. Ha elkészült, mentse a logikai alkalmazás definícióját tartalmazó fájlt. Ha a Visual Studio Code felszólítja, hogy erősítse meg a logikai alkalmazás definíciójának az Azure-előfizetéshez való feltöltését, válassza a **feltöltés**lehetőséget.

   ![Új logikai alkalmazás feltöltése az Azure-előfizetésbe](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Miután a Visual Studio Code közzétette a logikai alkalmazást az Azure-ban, megkeresheti az alkalmazást a Azure Portalban.

   ![Azure Portal közzétett logikai alkalmazás](./media/create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Logikai alkalmazás szerkesztése

Az Azure-ban közzétett logikai alkalmazások működéséhez a logikai alkalmazás definícióját a Visual Studio Code használatával nyithatja meg.

1. Ha még nem jelentkezett be az Azure-előfizetésbe a Visual Studio code-on belül, kövesse a cikk lépéseit a [bejelentkezéshez](#sign-in-azure).

1. Az Azure ablakban **Logic apps**alatt bontsa ki az Azure-előfizetését, és válassza ki a kívánt logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza **a Megnyitás a szerkesztőben**lehetőséget. Vagy a logikai alkalmazás neve mellett kattintson a Szerkesztés ikonra.

   ![A meglévő logikai alkalmazás szerkesztőjének megnyitása](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   A Visual Studio Code megnyitja a logikai alkalmazás munkafolyamat-definíciójának. logicapp. JSON fájlját.

   ![Logikai alkalmazás munkafolyamatának definíciója megnyitva](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Hajtsa végre a módosításokat a logikai alkalmazás definíciójában.

1. Miután végzett, mentse a módosításokat.

1. Ha a Visual Studio Code felszólítja, hogy frissítse a logikai alkalmazás definícióját az Azure-előfizetésében, válassza a **feltöltés**lehetőséget.

   ![A módosítások feltöltése a Logic app-definícióba](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> * [Logikai alkalmazások létrehozása a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)