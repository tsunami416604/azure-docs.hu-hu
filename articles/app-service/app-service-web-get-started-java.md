---
title: Java webalkalmazás – az Azure App Service létrehozása
description: Egy alapszintű Java-alkalmazás üzembe helyezésével megtudhatja, hogy miként futtathat webalkalmazásokat az App Service-ben.
services: app-service\web
documentationcenter: ''
author: rmcmurray
manager: routlaw
editor: ''
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: cephalin;robmcm
ms.custom: seodec18
ms.openlocfilehash: 6bf0562d222bce07f74ef5355ad0e3d6b8844c33
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096622"
---
# <a name="create-your-first-java-web-app-in-azure"></a>Az első Java-webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez a gyorsútmutató bemutatja, hogyan helyezhet üzembe Java-webalkalmazásokat az App Service-ben a [Java EE-fejlesztőknek készült Eclipse IDE](https://www.eclipse.org/) használatával.

> [!NOTE]
>
> A rövid útmutató lépései bemutatják, hogyan tehet közzé Java-webalkalmazást az App Service-ben az Eclipse IDE használatával, de az IntelliJ IDEA Ultimate Editiont vagy a Community Editiont is használhatja. További információkat az [Azure-hoz készült Hello World webalkalmazás IntelliJ használatával való létrehozásával](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) foglalkozó témakör tartalmaz.
>

A gyors útmutató befejezését követően alkalmazása webböngészőben megtekintve az alábbi illusztrációra fog hasonlítani:

![„Hello Azure!” példa webalkalmazás](./media/app-service-web-get-started-java/browse-web-app-1.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez a következők telepítése szükséges:

* Az ingyenes, <a href="https://www.eclipse.org/downloads/" target="_blank">Java EE-fejlesztőknek készült Eclipse IDE</a>. Ez a gyorsútmutató az Eclipse Neont használj.
* Az <a href="/java/azure/eclipse/azure-toolkit-for-eclipse-installation" target="_blank">Eclipse-hez készült Azure-eszközkészlet</a>.

> [!NOTE]
>
> A rövid útmutató lépéseinek elvégzéséhez az Eclipse-hez készült Azure-eszközkészlettel be kell jelentkeznie az Azure-fiókba. Ehhez tekintse meg az [Eclipse-hez készült Azure-eszközkészlethez Azure bejelentkezési utasításokat](/java/azure/eclipse/azure-toolkit-for-eclipse-sign-in-instructions) tartalmazó témakört.
>

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Dinamikus webes projekt létrehozása az Eclipse-ben

Az Eclipse-ben válassza a **File (Fájl)** > **New (Új)** > **Dynamic Web Project** (Dinamikus webprojekt) lehetőséget.

A **New Dynamic Web Project** (Új dinamikus webprojekt) párbeszédpanelen adja a **MyFirstJavaOnAzureWebApp** nevet a projektnek, és válassza a **Finish** (Befejezés) lehetőséget.
   
![New Dynamic Web Project (Új dinamikus webprojekt) párbeszédpanel](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

### <a name="add-a-jsp-page"></a>JSP-oldal hozzáadása

Ha a Project Explorer (Projektböngésző) nem jelenik meg, állítsa azt vissza.

![Az Eclipse-hez készült Java EE munkaterület](./media/app-service-web-get-started-java/pe.png)

A Project Explorer (Projektböngésző) nézetben bontsa ki a **MyFirstJavaOnAzureWebApp** projektet.
Kattintson a jobb gombbal az **WebContent** elemre, majd válassza a **(New) Új** > **JSP File (JSP-fájl)** (JSP-fájl) lehetőséget.

![Az új JSP-fájl menüje a Project Explorer (Projektböngésző) nézetben](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

A **New JSP File** (Új JSP-fájl) párbeszédpanelen:

* Nevezze el a fájlt az alábbi módon: **index.jsp**.
* Válassza a **Finish** (Befejezés) elemet.

  ![New JSP File (Új JSP-fájl) párbeszédpanel](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

Az index.jsp fájlban cserélje le a `<body></body>` elemet az alábbi jelöléssel:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Mentse a módosításokat.

> [!NOTE]
>
> Ha az 1. sorban olyan hiba szerepel, amely egy hiányzó Java-servletosztályra vonatkozik, figyelmen kívül hagyhatja.
> 
> ![Ártalmatlan Java-servlethiba](./media/app-service-web-get-started-java/java-servlet-benign-error.png)
>

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

A Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a projektre, majd válassza az **Azure** > **Publish as Azure Web App** (Közzététel Azure-webalkalmazásként) lehetőséget.

![A Publish as Azure Web App (Közzététel Azure-webalkalmazásként) helyi menü](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

Ha megjelenik az **Azure bejelentkezési** párbeszédpanel, akkor az [Eclipse-hez készült Azure-eszközkészletre vonatkozó Azure bejelentkezési utasításokat leíró](/java/azure/eclipse/azure-toolkit-for-eclipse-sign-in-instructions) cikkben lévő lépéseket kell követnie a hitelesítő adatok beírásához.

### <a name="deploy-web-app-dialog-box"></a>Deploy Web App (Webalkalmazás üzembe helyezése) párbeszédpanel

Miután bejelentkezett Azure-fiókjába, megjelenik a **Deploy Web App** (Webalkalmazás üzembe helyezése) párbeszédpanel.

Kattintson a **Létrehozás** gombra.

![Deploy Web App (Webalkalmazás üzembe helyezése) párbeszédpanel](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

### <a name="create-app-service-dialog-box"></a>A Create App Service (App Service létrehozása) párbeszédpanel

Megjelenik a **Create App Service** (App Service létrehozása) párbeszédpanel az alapértelmezett értékekkel. Az alábbi képen látható **170602185241** szám eltérő az Ön párbeszédpanelén.

![A Create App Service (App Service létrehozása) párbeszédpanel](./media/app-service-web-get-started-java/cas1.png)

A **Create App Service** (App Service létrehozása) párbeszédpanelen:

* Adja meg a webalkalmazás egyedi nevét, vagy őrizze meg a létrehozott nevet. Ennek a névnek az Azure-on belül egyedinek kell lennie. Ez a név a webalkalmazáshoz tartozó URL-cím része. Ha például a webalkalmazás neve **MyJavaWebApp**, az URL-cím *myjavawebapp.azurewebsites.net*.
* Ezen rövid útmutatóban tartsa meg az alapértelmezett webes tárolót.
* Válasszon ki egy Azure-előfizetést.
* Az **App service plan** (App Service-csomag) lapon:

  * **Hozzon létre új**: Tartsa meg az alapértelmezett, amely az App Service-csomag neve.
  * **Hely**: Válassza ki **Nyugat-Európa** vagy egy Önhöz közeli helyet.
  * **A tarifacsomag**: Válassza ki az ingyenes lehetőséget. A szolgáltatások díját az [App Service díjszabás](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) részben találja.

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

### <a name="resource-group-tab"></a>Resource group (Erőforráscsoport) lap

Válassza ki a **Resource group** (Erőforráscsoport) lapot. Tartsa meg az erőforráscsoporthoz tartozó alapértelmezetten létrehozott értéket.

![Resource group (Erőforráscsoport) lap](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Kattintson a **Létrehozás** gombra.

<!--
### The JDK tab

Select the **JDK** tab. Keep the default, and then select **Create**.

![Create App Service plan](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)
-->

Az Azure-eszközkészlet létrehozza a webalkalmazást, és megjelenít egy folyamatjelző panelt.

![App Service létrehozásának állapotjelző párbeszédpanelje](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

### <a name="deploy-web-app-dialog-box"></a>Deploy Web App (Webalkalmazás üzembe helyezése) párbeszédpanel

A **Deploy Web App** (Webalkalmazás üzembe helyezése) párbeszédpanelen válassza a **Deploy to root** (Üzembe helyezés a gyökérnél) beállítást. Ha egy App Service a *wingtiptoys.azurewebsites.net* helyen, és nem a gyökérnél végzi el az üzembe helyezést, a **MyFirstJavaOnAzureWebApp** nevű webalkalmazás a *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp* helyen lesz üzembe helyezve.

![Deploy Web App (Webalkalmazás üzembe helyezése) párbeszédpanel](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

A párbeszédpanel megjeleníti az Azure-nál, a JDK-nál és a webes tárolónál kiválasztott beállításokat.

A webalkalmazás Azure-ban történő közzétételéhez válassza a **Deploy** (Üzembe helyezés) lehetőséget.

A közzététel befejezése után válassza a **Published** (Közzétéve) hivatkozást az **Azure Activity Log** (Azure tevékenységnapló) párbeszédpanelen.

![Azure Activity Log (Azure tevékenységnapló) párbeszédpanel](./media/app-service-web-get-started-java/aal.png)

Gratulálunk! Sikeresen végrehajtotta a webalkalmazás üzembe helyezését az Azure-ban. 

![„Hello Azure!” példa webalkalmazás](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="update-the-web-app"></a>A webalkalmazás frissítése

Módosítsa a JSP-mintakódot egy eltérő üzenetre.

```jsp
<body>
<h1><% out.println("Hello again Azure!"); %></h1>
</body>
```

Mentse a módosításokat.

A Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a projektre, majd válassza az **Azure** > **Publish as Azure Web App** (Közzététel Azure-webalkalmazásként) lehetőséget.

Megjelenik a **Deploy Web App** (Webalkalmazás üzembe helyezése) párbeszédpanel, és megjeleníti a korábban létrehozott App Service-t. 

> [!NOTE] 
> Minden egyes közzétételkor válassza a **Deploy to root** (Üzembe helyezés a gyökérnél) beállítást. 
> 

Válassza ki a webalkalmazást, majd a **Deploy** (Üzembe helyezés) lehetőséget, ami közzéteszi a módosításokat.

Amikor megjelenik a **Publishing** (Közzététel) hivatkozás, válassza azt ki a webalkalmazás tallózásához, és tekintse meg a módosításokat.

## <a name="manage-the-web-app"></a>A webalkalmazás kezelése

Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben válassza az **Erőforráscsoportok** elemet.

![Navigálás a portálon az erőforráscsoportokhoz](media/app-service-web-get-started-java/rg.png)

Válassza az erőforráscsoportot. Az oldal megjeleníti a gyorsútmutatóban létrehozott erőforrásokat.

![Erőforráscsoport](media/app-service-web-get-started-java/rg2.png)

Válassza a webalkalmazást (**webapp-170602193915** az előző képen).

Megjelenik az **Áttekintés** oldal. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. A panel bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg. 

![Az App Service lap az Azure Portalon](media/app-service-web-get-started-java/web-app-blade.png)

[!INCLUDE [clean-up-section-portal-web-app](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)
