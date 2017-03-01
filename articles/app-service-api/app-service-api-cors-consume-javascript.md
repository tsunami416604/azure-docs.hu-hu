---
title: "CORS támogatás az App Service-ben | Microsoft Docs"
description: "Megtudhatja, hogyan használhatja a CORS-támogatást az Azure App Service platformon."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: 4f980a97-b9f5-4d1d-87ab-82b60bb96e1c
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/27/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: a0580f8d303c7ce33a65f0ce6faecf2492f851b0
ms.openlocfilehash: b0b701b7ea7a608f114d3a82f0403c2ae506854f
ms.lasthandoff: 12/21/2016


---
# <a name="consume-an-api-app-from-javascript-using-cors"></a>API-alkalmazások felhasználása JavaScriptből a CORS használatával
Az App Service beépített támogatást nyújt a [Cross Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) szolgáltatáshoz, amely lehetővé teszi, hogy a JavaScript-ügyfelek tartományok között hívjanak meg az API-alkalmazások által nyújtott API-kat. Az App Service segítségével úgy állíthatja be a saját API-ja CORS-elérését, hogy nem kell az API-ba kódot írnia.

Ez a cikk két részből áll:

* [A CORS konfigurálásának módja](#corsconfig) című rész általánosságban ismerteti, hogyan kell a CORS szolgáltatást konfigurálni tetszőleges API-alkalmazáshoz, webalkalmazáshoz vagy mobilalkalmazáshoz. Ez a rész minden, az App Service által támogatott keretrendszerre alkalmazható, beleértve a .NET, a Node.js és a Java keretrendszert. 
* A [.NET-bevezető oktatóanyagok folytatása](#tutorialstart) résztől kezdve a cikk oktató funkciót tölt be, és [az első API-alkalmazásokba való bevezető oktatóanyag](app-service-api-dotnet-get-started.md) tartalmára építve mutatja be a CORS-támogatást. 

## <a name="a-idcorsconfiga-how-to-configure-cors-in-azure-app-service"></a><a id="corsconfig"></a> A CORS konfigurálása az Azure App Service platformon
A CORS szolgáltatást konfigurálhatja az Azure Portalon vagy az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) eszközeinek használatával.

#### <a name="configure-cors-in-the-azure-portal"></a>A CORS konfigurálása az Azure portálon
1. Nyissa meg böngészőben az [Azure Portalt](https://portal.azure.com/).
2. Kattintson az **App Services** lehetőségre, majd kattintson az API-alkalmazás nevére.
   
    ![API-alkalmazás kiválasztása a portálon](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. A **Settings** (Beállítások) panelen (az **API app** (API-alkalmazás) paneltől jobbra) keresse meg az **API** szakaszt, majd kattintson a **CORS** lehetőségre.
   
   ![Válassza a CORS lehetőséget a Beállítások panelen](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. A szövegmezőbe írja be azokat az URL-címeket, amelyekről engedélyezni szeretné a JavaScript-hívásokat.

    Ha például a JavaScript-alkalmazását egy todolistangular nevű webalkalmazásra telepítette, írja be a "https://todolistangular.azurewebsites.net" címet. Ha csillagot (*) ír be, azzal beállíthatja, hogy minden eredettartományból elfogadja a hívásokat.


1. Kattintson a **Save** (Mentés) gombra.
   
   ![Kattintson a Save (Mentés) gombra.](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   A **Save** (Mentés) gombra való kattintás után az API-alkalmazás fogadni fogja a megadott URL-címekről jövő hívásokat.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>A CORS konfigurálása az Azure Resource Manager eszközeinek használatával
A CORS szolgáltatást úgy is beállíthatja egy API-alkalmazáshoz, hogy [Azure Resource Manager-sablonokat](../azure-resource-manager/resource-group-authoring-templates.md) használ a parancssori eszközökben, például az  [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy az [Azure CLI](../xplat-cli-install.md) felületen. 

Ha szeretne példát látni egy olyan Azure Resource Manager-sablonra, amely beállítja a CORS tulajdonságot, nyissa meg az oktatóanyag példaalkalmazását, amely a tárházban az [azuredeploy.json fájl](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Keresse meg a sablonban az a részt, amely az alábbi példára hasonlít:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a name="a-idtutorialstarta-continuing-the-net-getting-started-tutorial"></a><a id="tutorialstart"></a> A .NET-bevezető oktatóanyag folytatása
Ha az API-alkalmazásokhoz készült Node.js vagy Java-bevezető sorozatot követi, akkor elvégezte a bevezetősorozatot. Ugorjon a [További lépések](#next-steps) című részre, ahol tanácsokat találhat az API-alkalmazások bővebb megismeréséhez.

A cikk hátralévő része a .NET-bevezető sorozat folytatása, és feltételezi, hogy Ön sikeresen elvégezte [az első oktatóanyagot](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>A ToDoListAngular projekt telepítése új webalkalmazásra
[Az első oktatóprogramban](app-service-api-dotnet-get-started.md) létrehozott egy középső rétegbeli API-alkalmazást és egy adatrétegbeli API-alkalmazást. Ebben az oktatóanyagban egy egyoldalas webalkalmazást (SPA) fogunk létrehozni, amely a középső rétegbeli API-alkalmazást hívja meg. Az SPA működéséhez engedélyeznie kell a CORS szolgáltatást a középső rétegbeli API-alkalmazásra. 

A [ToDoList példaalkalmazásban](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) a ToDoListAngular projekt egy olyan, az AngularJS ügyfelet bemutató példaprogram, amely a középső rétegbeli ToDoListAPI webes API projektet hívja meg. Az *app/scripts/todoListSvc.js* fájlban lévő JavaScript-kód az AngularJS HTTP-szolgáltató használatával hívja meg az API-t. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 

            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Új webalkalmazás létrehozása a ToDoListAngular projekthez
Az új App Service-webalkalmazások létrehozása és a hozzájuk tartozó projekt telepítése hasonló módon történik, mint az [API-alkalmazások létrehozása és telepítése, amit a sorozat első oktatóanyaga ismertetett](app-service-api-dotnet-get-started.md#createapiapp). Az egyetlen különbség, hogy az alkalmazás típusa **webalkalmazás**, nem pedig **API-alkalmazás**.  A párbeszédpanelek képernyőképeit az alábbi módon érheti el: 

1. A **Solution Explorer** (Megoldáskezelő) területén kattintson a jobb gombbal a ToDoListAngular projektre, majd kattintson a **Publish** (Közzététel) elemre.
2. A **Publish Web** (Weboldal közzététele) varázsló **Profile** (Profile) lapján kattintson a **Microsoft Azure App Service** lehetőségre.
3. Az **App Service** párbeszédpanelen kattintson a **New** (Új) gombra.
4. A **Create App Service** (App Service létrehozása) párbeszédpanel **Hosting** (Üzemeltetés) lapján írjon be egy olyan nevet a **Web App Name** (Webalkalmazás neve) mezőbe, amely egyedi az *azurewebsites.net* tartományban. 
5. Válassza ki a használni kívánt Azure **előfizetést**.
6. A **Resource Group** (Erőforráscsoport) legördülő listában válassza a korábban létrehozott erőforráscsoportot.
7. Az **App Service Plan** (App Service-csomag) legördülő listában válassza a korábban létrehozott csomagot. 
8. Kattintson a **Létrehozás** gombra.
   
    A Visual Studio létrehozza a webalkalmazást és a hozzá tartozó közzétételi profilt, majd megjeleníti a **Publish Web** (Weboldal közzététele) varázsló **Connection** (Kapcsolat) lépését.
   
    Még ne kattintson a **Publish** (Közzététel) elemre. A következő szakaszban beállíthatja, hogy a webalkalmazás az App Service platformon futó, középső rétegbeli API-alkalmazást hívja. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>A középső réteg URL-címének beállítása a webalkalmazás beállításaiban
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/), majd keresse meg a ToDoListAngular projekt (kezelőfelület) üzemeltetésére létrehozott webalkalmazáshoz tartozó **Web App** (Webalkalmazás) panelt.
2. Kattintson a **Settings > Application Settings** (Beállítások > Alkalmazásbeállítások) lehetőségre.
3. Az **App settings** (Alkalmazás beállításai) szakaszban adja meg a következő kulcs-érték párt:
   
   | Kulcs | Érték | Példa |
   | --- | --- | --- |
   | toDoListAPIURL |https://{a középső réteg API-alkalmazásának neve}.azurewebsites.net |https://todolistapi0121.azurewebsites.NET |
4. Kattintson a **Save** (Mentés) gombra.
   
    Amikor a kód lefut az Azure-ban, a rendszer ezzel az értékkel írja felül a *Web.config* fájlban található localhost URL-címet. 
   
    A beállítás értékét lekérdező kód az *index.cshtml* fájlban található:
   
        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>
   
    A *todoListSvc.js* fájlban lévő kód a következő beállítást használja:
   
        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>A ToDoListAngular webes projekt telepítése az új webalkalmazásra
* A Visual Studio **Publish Web** (Webes közzététel) varázslójának **Connection** (Kapcsolat) lépésénél kattintson a **Publish** (Közzététel) elemre.
  
   A Visual Studio telepíti a ToDoListAPI projektet az új webalkalmazásba, és egy böngészőablakban megnyitja a webalkalmazás URL-címét. 

### <a name="test-the-application-without-cors-enabled"></a>Az alkalmazás tesztelése a CORS engedélyezése nélkül
1. A böngésző Fejlesztői eszközök funkciójával nyissa meg a Konzol ablakot.
2. Az AngularJS felhasználói felületet megjelenítő böngészőablakban kattintson a **To Do List** (Tennivalók) hivatkozásra.
   
    A JavaScript-kód megpróbálja meghívni a középső rétegbeli API-alkalmazást, de a hívás sikertelen lesz, mivel a kezelőfelület egy másik tartományban fut, mint a háttéralkalmazás. A böngésző Fejlesztői eszközök funkciójával elérhető konzolablak hibaüzenetet jelenít meg az eltérő eredetről.
   
    ![Hibaüzenet az eltérő eredetről](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>A CORS konfigurálása középső rétegbeli API-alkalmazáshoz
Ebben a szakaszban a középső rétegbeli ToDoListAPI API-alkalmazás CORS beállítását konfiguráljuk az Azure-ban. Ez a beállítás lehetővé teszi, hogy a középső rétegbeli API-alkalmazás JavaScript-hívásokat fogadjon abból a webalkalmazásból, amelyet a ToDoListAngular projekthez létrehozott.

1. Nyissa meg böngészőben az [Azure Portalt](https://portal.azure.com/).
2. Kattintson az **App Services** (Alkalmazásszolgáltatások) lehetőségre, majd a ToDoListAPI (középső réteg) API-alkalmazásra.
   
    ![API-alkalmazás kiválasztása a portálon](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. A **Settings** (Beállítások) panelen (az **API app** (API-alkalmazás) paneltől jobbra) keresse meg az **API** szakaszt, majd kattintson a **CORS** lehetőségre.
   
   ![A CORS kiválasztása a portálon](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. A szövegmezőbe írja be a ToDoListAngular (kezelőfelület) webalkalmazás URL-címét. Ha például a ToDoListAngular projektet egy todolistangular0121 nevű webalkalmazáshoz telepítette, engedélyezze a hívásokat a következő URL-címről: `https://todolistangular0121.azurewebsites.net`.
   
   Ha csillagot (*) ír be, azzal beállíthatja, hogy minden eredettartományból elfogadja a hívásokat.
5. Kattintson a **Save** (Mentés) gombra.
   
   ![Kattintson a Save (Mentés) gombra.](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   A **Save** (Mentés) gombra való kattintás után az API-alkalmazás fogadni fogja a megadott URL-címről jövő hívásokat. Ezen a képernyőképen a ToDoListAPI0223 API-alkalmazás fogja fogadni a ToDoListAngular webalkalmazásból jövő JavaScript-ügyfélhívásokat.

### <a name="test-the-application-with-cors-enabled"></a>Az alkalmazás tesztelése a CORS engedélyezése mellett
* Nyissa meg egy böngészőben a webalkalmazás HTTPS URL-címét. 
  
    Ezúttal az alkalmazás lehetővé teszi tennivalók megtekintését, hozzáadását, szerkesztését és törlését. 
  
    ![A példaalkalmazás To Do List oldala](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>Az App Service CORS és a webes API CORS összehasonlítása
Web API-projektekben a [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet-csomag telepítésével állíthatja be, hogy milyen tartományokból fogadja az API a JavaScript-hívásokat.

A Web API CORS-támogatása rugalmasabb, mint az App Service CORS-támogatása. Például a kódban a különböző műveletekhez különböző elfogadott származási helyeket adhat meg, míg az App Service CORS esetében az API-alkalmazás összes függvényéhez csupán az elfogadott tartományok egyetlen halmazát állíthatja be.

> [!NOTE]
> Egy API-alkalmazásban ne használja a Web API CORS-t és az App Service CORS-t is. Az App Service CORS szolgáltatása elsőbbséget élvez, így a Web API CORS szolgáltatásának nem lesz hatása. Ha például az App Service-ben egyetlen eredettartományt engedélyez, a Web API-kódban pedig az összes tartományt engedélyezi, akkor az Azure API-alkalmazás csak az Azure-ban megadott tartományból fogja a hívásokat fogadni.
> 
> 

### <a name="how-to-enable-cors-in-web-api-code"></a>A CORS engedélyezése Web API-kódban
A Web API CORS-támogatásának engedélyezése az alábbi lépésekkel foglalható össze. További információ: [Enabling Cross-Origin Requests in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) (Az eltérő eredetű kérések engedélyezése az ASP.NET Web API 2-ben).

1. Egy Web API-projektben telepítse a [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet-csomagot.
2. Adja hozzá a `config.EnableCors()` kódsort a **WebApiConfig** osztály **Register** metódusához az alábbi példában látható módon. 
   
        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
   
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
   
                // Web API routes
                config.MapHttpAttributeRoutes();
   
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }
3. A Web API-vezérlőben helyezzen el egy `using` utasítást a `System.Web.Http.Cors` névtérhez, és adja hozzá az `EnableCors` attribútumot a vezérlő osztályhoz vagy az egyes műveletmetódusokhoz. A következő példában a CORS-támogatás a teljes vezérlőre vonatkozik.
   
        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController

## <a name="using-azure-api-management-with-api-apps"></a>Az Azure API Management használata API-alkalmazásokkal
Ha az Azure API Management szolgáltatást egy API-alkalmazással használja, az API-alkalmazás helyett az API Management szolgáltatásban konfigurálja a CORS támogatást. További információkért lásd a következőket:

* [Az Azure API Management áttekintése (videó: a CORS-ról szóló rész 12:10-nél kezdődik)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [Az API Management tartományközi szabályzatai](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az oktatóanyag lépéseinek elvégzése közben hibákba ütközne, olvassa el az alábbi hibaelhárítási tippeket:

* Ellenőrizze, hogy az [Azure SDK for .NET for Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003) legfrissebb verzióját használja-e.
* Győződjön meg arról, hogy `https`-t írt be a CORS beállításba, valamint arról, hogy `https`-t használ a webalkalmazás kezelőfelületének futtatásához.
* Győződjön meg arról, hogy a CORS beállítást a középső rétegbeli API-alkalmazásba, és nem a kezelőfelületbe helyezte el.
* Ha az alkalmazás kódjában és az Azure App Service platformon is konfigurálja a CORS-támogatást, akkor ne feledje, hogy az App Service CORS-beállítása felülírja az alkalmazás kódjában lévő beállítást. 

A Visual Studio hibaelhárítást egyszerűsítő szolgáltatásairól a [Troubleshooting Azure App Service apps in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) (Azure App Service-alkalmazások hibáinak elhárítása Visual Studióban) című szakaszban olvashat bővebben.

## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan engedélyezheti az App Service CORS-támogatását úgy, hogy az ügyfélbeli JavaScript-kód meghívhasson egy másik tartományban lévő API-t. Az API-alkalmazások részletesebb megismeréséhez olvassa el az [App Service-hitelesítésbe való bevezetést](../app-service/app-service-authentication-overview.md), majd nyissa meg az [API-alkalmazásokban való felhasználóhitelesítést](app-service-api-dotnet-user-principal-auth.md) bemutató oktatóanyagot.


