---
title: "Hozzon létre egyéni összekötők webes API-- Azure Logic Apps |} Microsoft Docs"
description: "Egyéni összekötők felépíteni webes API-k"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Hozzon létre egyéni összekötők a webes API-k

Az Azure Logic Apps, a Microsoft Flow vagy a Microsoft PowerApps használható egyéni összekötő létrehozásához először létre kell hoznia egy webes API, amely üzemeltetni az Azure Web Apps, hitelesítéséhez az Azure Active Directoryban, és regisztrálja egy összekötőt a Logic Apps, folyamata, vagy A powerapps segítségével. Ez az oktatóanyag bemutatja, hogyan épület egy ASP.NET Web API-alkalmazást a feladatok végrehajtásához. További, amelyek megjelenítik a connector eseményindítók és műveletek, a kódot is felépítésének minták: [hozzon létre egyéni API-k, amelyek a logic app munkafolyamatok hívása](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio 2013 vagy újabb verzió](https://www.visualstudio.com/vs/). Ez az oktatóanyag a Visual Studio 2015-öt használja.

* A webes API kódját. Ha nincs, próbálja meg ez az oktatóanyag: [Ismerkedés az ASP.NET Web API 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, Kezdésként használhatja az egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/). Ellenkező esetben regisztráljon egy [használatalapú fizetés előfizetés](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Hozzon létre, és az ASP.NET webalkalmazás telepítése az Azure

Ebben az oktatóanyagban Visual C# ASP.NET-webalkalmazás létrehozása. 

1. Nyissa meg a Visual Studio, majd kattintson a **fájl** > **új projekt**.

   1. Bontsa ki a **telepített**, és **sablonok** > **Visual C#** > **webes**, és válassza ki **ASP .NET-webalkalmazás**.

   2. Adja meg a projekt nevét, helyét és az alkalmazás megoldás nevét, majd kattintson **OK**.

   Példa:

   ![Visual C# ASP.NET-webalkalmazás létrehozása](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. Az a **új ASP.NET-webalkalmazás** mezőben válassza a **Web API** sablont. Ha nincs bejelölve, jelölje ki a **a felhőben lévő gazdagéphez**. Válasszon **hitelesítés módosítása**.

   ![Jelölje be "Webes API" sablon "Üzemeltetni a felhőben található", "Hitelesítés módosítása"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Válassza ki **nem hitelesítési**, és válassza a **OK**. Hitelesítési később állíthat be.

   ![Válassza ki a "Nincs hitelesítés"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Ha a **új ASP.NET-webalkalmazás** mezőben ismét megjelenik, válassza a **OK**. 

5. Az a **létrehozása az App Service** mezőben ellenőrizze a megadott üzemeltetési beállításokat lásd a táblázatot, hajtsa végre a módosításokat, és válassza a **létrehozása**. 

   Egy [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) alkalmazások az Azure-előfizetéshez üzemeltetéséhez használt fizikai erőforrások gyűjteményét jelképezi. További tudnivalók [alkalmazásszolgáltatási](../app-service/app-service-value-prop-what-is.md).

   ![Az App Service létrehozása](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | A Azure munkahelyi, iskolai fiókkal vagy személyes Microsoft-fiókja | *a felhasználói fiók* | Válassza ki a felhasználói fiókot. | 
   | **Webes alkalmazás neve** | *egyéni-webalkalmazás-api-alkalmazás-neve* vagy az alapértelmezett név | Adjon meg a nevet az webes API-alkalmazás, amely szerepel az alkalmazás URL-CÍMÉT, például: http://*web-api-alkalmazás-neve*. | 
   | **Előfizetés** | *Azure-előfizetés-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *Azure-erőforrás-csoportnév* | Jelöljön ki egy meglévő Azure-erőforráscsoportot, vagy ha még nem tette meg, hozzon létre egy erőforráscsoportot. <p>**Megjegyzés:**: az Azure-erőforráscsoport rendszerezi az Azure-előfizetéshez az Azure-erőforrások. | 
   | **App Service-csomag** | *App-Service-csomag-neve* | Válassza ki a meglévő App Service-csomagot, vagy ha még nem tette meg, hozzon létre egy csomagot. | 
   |||| 

   Ha egy App Service-csomag létrehozásához adja meg ezeket a beállításokat:

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Hely** | *központi telepítés-terület* | Válassza ki a régiót, az alkalmazás telepítéséhez. | 
   | **Méret** | *App Service-csomag-méretű* | Válassza ki a terv méretét, amely megadja, hogy a költségek és számítási erőforrásainak kapacitását a service-csomag. | 
   |||| 

   Állítsa be az alkalmazás által igényelt összes erőforrását, válassza a **további Azure-szolgáltatások felfedezés**.

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Erőforrás típusa** | *Azure-erőforrás-típusa* | Válassza ki, és állítsa be az alkalmazás által igényelt minden további erőforrásokat. | 
   |||| 

6. Miután a Visual Studio telepíti a projektet, hozhat létre. az alkalmazás kódját.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Hozzon létre egy OpenAPI (Swagger) fájlt, amely leírja a webes API

A webes API-alkalmazás Logic Apps való kapcsolódáshoz szükséges egy [OpenAPI (korábbi nevén Swagger) fájl](http://swagger.io/) , amely leírja, hogy az API-műveleteket. Írhat az API-t saját OpenAPI definíciója a [online szerkesztőbe Swagger](http://editor.swagger.io/), de ez az oktatóanyag használja egy nyílt forráskódú eszköz nevű [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Ha még nem tette meg, telepítse a Swashbuckle Nuget csomagot a Visual Studio-projekt.

   1. A Visual Studio felületén válassza **eszközök** > **NuGet-Csomagkezelő** > 
    **Csomagkezelő konzol**.

   2. Az a **Csomagkezelő konzol**, keresse fel az alkalmazás projekt könyvtárat, ha már nem létezik (futtatása `Set-Location "project-path"`), és futtassa a PowerShell-parancsmag segítségével: 
   
      `Install-Package Swashbuckle`

      Példa:

      ![A Package Manager Console, telepítse a Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Az alkalmazás futtatása a Swashbuckle telepítése után, ha a Swashbuckle egy OpenAPI fájlban a következő URL-címet állít elő: 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > Swashbuckle is hoz létre a következő URL-címen a felhasználói felület: 
   > 
   > http://*{your-web-api-app-root-URL}*  /swagger

3. Amikor készen áll, a webes API-alkalmazás közzététele az Azure-bA. Közzéteszi a Visual Studio eszközből, kattintson a jobb gombbal a webes projekthez a Megoldáskezelőben, válassza a **közzététel...** , és kövesse a megjelenő utasításokat.

   > [!IMPORTANT]
   > Ismétlődő műveleti azonosítókat ellenőrizze az OpenAPI dokumentum érvénytelen. Ha használta a C# mintasablon, a sablon *kétszer megismétli a Műveletazonosító*:`Values_Get` 
   > 
   > A probléma megoldásához módosítsa egy példány `Value_Get` és közzé.

4. A OpenAPI lekérni keresse meg a következő helyről: 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   Emellett letöltheti a [minta OpenAPI dokumentum](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) az oktatóanyagot. 
   Győződjön meg arról, hogy távolítsa el a megjegyzéseket, mely kezdve "/ /", a dokumentum használata előtt.

5. A tartalom mentését JSON-fájlként. A böngésző alapján, lehetséges, hogy másolja és illessze be a szöveget egy üres szöveges fájlba.

## <a name="next-steps"></a>Következő lépések

* [Az egyéni összekötők hitelesítés beállítása](../logic-apps/custom-connector-azure-active-directory-authentication.md)











