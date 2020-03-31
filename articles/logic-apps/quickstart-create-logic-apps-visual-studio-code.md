---
title: Feladatok automatizálása a Visual Studio-kóddal
description: Logikai alkalmazás létrehozása vagy szerkesztése JSON-definíciók alapján a Visual Studio-kód (VS-kód) használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 819a60887743f39d3c2ffab3c955b2980cee2725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74784833"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Rövid útmutató: Logikai alkalmazás munkafolyamat-definícióinak létrehozása és kezelése a Visual Studio-kód használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Visual Studio Code segítségével olyan logikai alkalmazásokat hozhat létre és kezelhet, amelyek segítségével automatizálhatja a feladatokat, munkafolyamatokat és folyamatokat az alkalmazások, adatok, rendszerek és szolgáltatások szervezetek és vállalatok közötti integrálására. Ez a rövid útmutató bemutatja, hogyan hozhat létre és szerkeszthet az alapul szolgáló munkafolyamat-definíciókat, amelyek JavaScript-objektumnotítást (JSON) használnak a logikai alkalmazásokhoz egy kódalapú felületen keresztül. Az Azure-ba már üzembe helyezett meglévő logikai alkalmazásokon is dolgozhat.

Bár ezeket a feladatokat az [Azure Portalon](https://portal.azure.com) és a Visual Studióban is elvégezheti, gyorsabban elvégezheti a Visual Studio-kódban, ha már ismeri a logikai alkalmazásdefiníciókat, és közvetlenül a kódban szeretne dolgozni. Például letilthatja, engedélyezheti, törölheti és frissítheti a már létrehozott logikai alkalmazásokat. Emellett bármilyen fejlesztői platformról, például Linux, Windows és Mac rendszerből dolgozhat logikai alkalmazásokon és integrációs fiókokon.

Ebben a cikkben létrehozhatja ugyanazt a logikai alkalmazást ebből a [rövid útmutatóból,](../logic-apps/quickstart-create-first-logic-app-workflow.md)amely inkább az alapfogalmakra összpontosít. A Visual Studio-kódban a logikai alkalmazás a következő példához hasonlóan néz ki:

![Példa logikai alkalmazás munkafolyamat-definíciójára](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Mielőtt nekikezdene, győződjön meg arról, hogy rendelkezik a következőkkel:

* Ha nem rendelkezik Azure-fiókkal és előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Alapvető ismeretek a [logikai alkalmazások munkafolyamat-definícióiról](../logic-apps/logic-apps-workflow-definition-language.md) és azok szerkezetéről a JSON-nal leírtak szerint

  Ha most írja be a Logic Apps alkalmazást, próbálja ki ezt a [rövid útmutatót,](../logic-apps/quickstart-create-first-logic-app-workflow.md)amely létrehozza az első logikai alkalmazásokat az Azure Portalon, és inkább az alapfogalmakra összpontosít.

* Az Azure-ba való bejelentkezéshez és az Azure-előfizetésbe való bejelentkezéshez az internethez

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio Code 1.25.1-es vagy újabb verziója](https://code.visualstudio.com/), amely ingyenes

  * Visual Studio-kódbővítmény az Azure Logic-alkalmazásokhoz

    Ezt a bővítményt letöltheti és telepítheti a [Visual Studio Piactérről](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) vagy közvetlenül a Visual Studio-kódból. A telepítés után győződjön meg arról, hogy újratölti a Visual Studio-kódot.

    !["Visual Studio-kódbővítmény az Azure Logic Apps-hez"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Annak ellenőrzéséhez, hogy a bővítmény megfelelően van-e telepítve, válassza ki a Visual Studio-kód eszköztáron megjelenő Azure-ikont.

    ![A bővítmény megfelelően telepítve](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    További információ: [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). A bővítmény nyílt forráskódú verziójához való hozzájáruláshoz keresse fel a [GitHubOn a Visual Studio-kód Azure Logic Apps bővítményét.](https://github.com/Microsoft/vscode-azurelogicapps)

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Nyissa meg a Visual Studio Code-ot. A Visual Studio-kód eszköztáron válassza az Azure ikont.

   ![Válassza az Azure ikonját a Visual Studio Kód eszköztárán](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Az Azure-ablakban a **Logic Apps**csoportban válassza a Bejelentkezés **az Azure-ba**lehetőséget. Amikor a Microsoft bejelentkezési lapja kéri, jelentkezzen be az Azure-fiókjával.

   ![Válassza a "Bejelentkezés az Azure-ba" lehetőséget](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Ha a bejelentkezés a szokásosnál tovább tart, a Visual Studio-kód eszközkód megadásával kéri, hogy jelentkezzen be egy Microsoft-hitelesítési webhelyen keresztül. Ha inkább a kóddal szeretne bejelentkezni, válassza **az Eszközkód használata**lehetőséget.

      ![Folytatás az eszközkóddal](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. A kód másolásához válassza a **Másolás & megnyitás**lehetőséget.

      ![Az Azure-bejelentkezés kódjának másolása](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Új böngészőablak megnyitásához és a hitelesítési webhely megnyitásához válassza a **Hivatkozás megnyitása**lehetőséget.

      ![Böngésző megnyitásának és a hitelesítési webhely megnyitásának megerősítése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. A **Bejelentkezés a fiókba** lapon adja meg a hitelesítési kódot, és válassza a **Tovább**gombot.

      ![Adja meg az Azure bejelentkezéshitelesítési kódját](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Válassza ki az Azure-fiókját. A bejelentkezés után bezárhatja a böngészőt, és visszatérhet a Visual Studio-kódhoz.

   Az Azure-ablaktáblában a **Logic Apps** és **az integrációs fiókok** szakaszok most már a fiókjához társított Azure-előfizetéseket jelenítik meg. Ha azonban nem látja a várt előfizetéseket, vagy ha a szakaszok túl sok előfizetést jelenítenek meg, kövesse az alábbi lépéseket:

   1. Vigye az egérmutatót a **Logic Apps** címke fölé. Amikor megjelenik az eszköztár, válassza az **Előfizetések kiválasztása** (szűrőikon) lehetőséget.

      ![Azure-előfizetések keresése vagy szűrése](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. A megjelenő listában jelölje ki a megjeleníteni kívánt előfizetéseket.

1. A **Logic Apps csoportban**válassza ki a kívánt előfizetést. Az előfizetési csomópont kibontja, és megjeleníti az adott előfizetésben létező logikai alkalmazásokat.

   ![Válassza ki az Azure-előfizetését](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Az **Integrációs fiókok csoportban**az előfizetés kiválasztása az adott előfizetésben létező integrációs fiókokat jeleníti meg.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Új logikai alkalmazás létrehozása

1. Ha még nem jelentkezett be Azure-fiókjába és előfizetésébe a Visual Studio-kódon belülről, kövesse az előző lépéseket a [bejelentkezéshez.](#sign-in-azure)

1. A Visual Studio-kód **Logic Apps**területén nyissa meg az előfizetés helyi menüjét, és válassza **a Logikai alkalmazás létrehozása**lehetőséget.

   ![Az előfizetés menüjében válassza a "Logikai alkalmazás létrehozása" lehetőséget.](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Megjelenik egy lista, amely az előfizetésben lévő Azure-erőforráscsoportokat jeleníti meg.

1. Az erőforráscsoport-listából válassza az **Új erőforráscsoport vagy egy** meglévő erőforráscsoport létrehozása lehetőséget. Ebben a példában hozzon létre egy új erőforráscsoportot.

   ![Új Azure-erőforráscsoport létrehozása](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Adja meg az Azure-erőforráscsoport nevét, és nyomja le az ENTER billentyűt.

   ![Az Azure-erőforráscsoport nevének megadására](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Válassza ki azt az Azure-régiót, ahol menteni szeretné a logikai alkalmazás metaadatait.

   ![Válassza ki az Azure helyét a logikai alkalmazások metaadatainak mentéséhez](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Adja meg a logikai alkalmazás nevét, és nyomja le az Enter billentyűt.

   ![A logikai alkalmazás nevének megadása](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Az Azure-ablakban az Azure-előfizetés alatt megjelenik az új és üres logikai alkalmazás. A Visual Studio Code egy JSON (.logicapp.json) fájlt is megnyit, amely egy csontváz munkafolyamat-definíciót tartalmaz a logikai alkalmazáshoz. Most már elkezdheti manuálisan a logikai alkalmazás munkafolyamat-definícióját ebben a JSON-fájlban. A munkafolyamat-definíció szerkezetével és szintaxisával kapcsolatos technikai tudnivalókat az [Azure Logic Apps munkafolyamat-definíciós nyelvi sémájában található.](../logic-apps/logic-apps-workflow-definition-language.md)

   ![Logikai alkalmazás munkafolyamat-definíciójának JSON-fájljának kiürítése](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Itt például egy mintalogikai alkalmazás munkafolyamat-definíciója látható, amely rss-eseményindítóval és Office 365 Outlook-művelettel kezdődik. A JSON-elemek általában betűrendben jelennek meg az egyes szakaszokban. Ez a minta azonban nagyjából abban a sorrendben jeleníti meg ezeket az elemeket, ahogy a logikai alkalmazás lépései megjelennek a tervezőben.

   > [!IMPORTANT]
   > Ha újra fel szeretné használni ezt a mintalogikai alkalmazásdefiníciót, szüksége @fabrikam.comvan egy Office 365 szervezeti fiókra, például. Győződjön meg arról, hogy a fiktív e-mail címet a saját e-mail címére cserélte. Egy másik e-mail-összekötő, például Outlook.com `Send_an_email_action` vagy a Gmail használatához cserélje le a műveletet egy hasonló műveletre, amely az [Azure Logic Apps által támogatott e-mail-összekötőből](../connectors/apis-list.md)érhető el.

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
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Ha elkészült, mentse a logikai alkalmazás munkafolyamat-definícióját. (A Fájl menü > mentés, vagy nyomja le a Ctrl+S billentyűkombinációt)

1. Amikor a rendszer kéri, hogy töltse fel a logikai alkalmazást az Azure-előfizetésébe, válassza a **Feltöltés lehetőséget.**

   Ez a lépés közzéteszi a logikai alkalmazást az [Azure Portalon,](https://portal.azure.com)amely a logikát élőben és futtatva az Azure-ban teszi közzé.

   ![Új logikai alkalmazás feltöltése azure-előfizetésbe](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Logikai alkalmazás megtekintése a tervezőben

A Visual Studio-kódban a logikai alkalmazást csak olvasható tervezőnézetben nyithatja meg. Bár a tervezőben nem szerkesztheti a logikai alkalmazást, vizuálisan ellenőrizheti a logikai alkalmazás munkafolyamatát a tervezői nézet használatával.

Az Azure-ablakban a **Logic Apps**csoportban nyissa meg a logikai alkalmazás helyi menüjét, és válassza **a Megnyitás tervezőben**lehetőséget.

Az írásvédett tervező egy külön ablakban nyílik meg, és megjeleníti a logikai alkalmazás munkafolyamatát, például:

![Logikai alkalmazás megtekintése írásvédett tervezőben](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Megtekintés az Azure Portalon

A logikai alkalmazás azure portalon való áttekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazásához társított azure-fiók és előfizetés használatával.

1. Az Azure Portal keresőmezőjébe írja be a logikai alkalmazások nevét. Az eredmények listájából válassza ki a logikai alkalmazást.

   ![Az új logikai alkalmazás az Azure Portalon](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

Ha a Visual Studio-kódban szerkeszt egy közzétett logikai alkalmazást, és menti a módosításokat, *felülírja* a már telepített alkalmazást. A logikai alkalmazás éles környezetben való megszakításának elkerülése és a megszakítások minimalizálása érdekében először inaktiválja a logikai alkalmazást. Ezután reaktívvá teheti a logikai alkalmazást, miután megerősítette, hogy a logikai alkalmazás továbbra is működik.

1. Ha még nem jelentkezett be Azure-fiókjába és előfizetésébe a Visual Studio-kódon belülről, kövesse az előző lépéseket a [bejelentkezéshez.](#sign-in-azure)

1. Az Azure-ablakban a **Logic Apps**csoportban bontsa ki az Azure-előfizetést, hogy az adott előfizetésben lévő összes logikai alkalmazást megtekinthesse.

   1. A kívánt logikai alkalmazás letiltásához nyissa meg a logikai alkalmazás menüjét, és válassza **a Letiltás parancsot.**

      ![A logikai alkalmazás letiltása](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Ha készen áll a logikai alkalmazás újraaktiválására, nyissa meg a logikai alkalmazás menüjét, és válassza az **Engedélyezés**lehetőséget.

      ![A logikai alkalmazás engedélyezése](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Telepített logikai alkalmazás szerkesztése

A Visual Studio-kódban megnyithatja és szerkesztheti egy már üzembe helyezett logikai alkalmazás munkafolyamat-definícióját az Azure-ban.

> [!IMPORTANT] 
> Mielőtt éles környezetben szerkesztene egy aktívan futó logikai alkalmazást, kerülje el a logikai alkalmazás megszakításának kockázatát, és minimalizálja a megszakítást [a logikai alkalmazás letiltásával.](#disable-enable-logic-app)

1. Ha még nem jelentkezett be Azure-fiókjába és előfizetésébe a Visual Studio-kódon belülről, kövesse az előző lépéseket a [bejelentkezéshez.](#sign-in-azure)

1. Az Azure-ablakban a **Logic Apps**csoportban bontsa ki az Azure-előfizetést, és válassza ki a kívánt logikai alkalmazást.

1. Nyissa meg a logikai alkalmazás menüjét, és válassza **a Megnyitás a szerkesztőben**lehetőséget. Vagy a logikai alkalmazás neve mellett válassza a szerkesztés ikont.

   ![Szerkesztő megnyitása meglévő logikai alkalmazáshoz](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   A Visual Studio Code megnyitja a .logicapp.json fájlt a helyi ideiglenes mappában, így megtekintheti a logikai alkalmazás munkafolyamat-definícióját.

   ![A közzétett logikai alkalmazás munkafolyamat-definíciójának megtekintése](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Hajtsa végre a módosításokat a logikai alkalmazás munkafolyamat-definíciójában.

1. Miután végzett, mentse a módosításokat. (A Fájl menü > mentés, vagy nyomja le a Ctrl+S billentyűkombinációt)

1. Amikor a rendszer kéri a módosítások feltöltését és a meglévő logikai alkalmazás *felülírását* az Azure Portalon, válassza a **Feltöltés**lehetőséget.

   Ez a lépés közzéteszi a frissítéseket a logikai alkalmazás az [Azure Portalon.](https://portal.azure.com)

   ![Szerkesztések feltöltése a logikai alkalmazásdefinícióba az Azure-ban](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Más verziók megtekintése vagy előléptetése

A Visual Studio-kódban megnyithatja és áttekintheti a logikai alkalmazás korábbi verzióit. Egy korábbi verziót is előléptethet az aktuális verzióra.

> [!IMPORTANT] 
> Mielőtt módosítana egy aktívan futó logikai alkalmazást éles környezetben, kerülje el a logikai alkalmazás megszakításának kockázatát, és minimalizálja a zavarokat [a logikai alkalmazás letiltásával.](#disable-enable-logic-app)

1. Az Azure-ablakban a **Logic Apps**csoportban bontsa ki az Azure-előfizetést, hogy az adott előfizetésben lévő összes logikai alkalmazást megtekinthesse.

1. Az előfizetés alatt bontsa ki a logikai alkalmazást, és bontsa ki **a Verziók csomópontot.**

   A **Verziók** lista a logikai alkalmazás korábbi verzióit jeleníti meg, ha vannak ilyenek.

   ![A logikai alkalmazás korábbi verziói](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Egy korábbi verzió megtekintéséhez jelölje be bármelyik lépést:

   * A JSON-definíció megtekintéséhez a **Verziók**csoportban válassza ki a definíció verziószámát. Vagy nyissa meg a verzió helyi menüjét, és válassza **a Megnyitás a szerkesztőben**lehetőséget.

     Egy új fájl nyílik meg a helyi számítógépen, és megjeleníti a verzió JSON-definícióját.

   * Ha csak olvasható tervezői nézetben szeretné megtekinteni a verziót, nyissa meg a verzió helyi menüjét, és válassza **a Megnyitás tervezőben**lehetőséget.

1. Ha egy korábbi verziót az aktuális verzióra szeretne előléptetni, kövesse az alábbi lépéseket:

   1. A **Verziók csoportban**nyissa meg a korábbi verzió helyi menüjét, és válassza **a Előléptetés**lehetőséget.

      ![Korábbi verzió népszerűsítése](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Ha azt szeretné, hogy a Visual Studio-kód megerősítést kér, válassza az **Igen**lehetőséget.

      ![Korábbi verzió előléptetésének megerősítése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      A Visual Studio-kód a kijelölt verziót az aktuális verzióra lépteti, és új számot rendel az előléptetett verzióhoz. A korábban aktuális verzió most antól megjelenik az előléptetett verzió alatt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások létrehozása a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)