---
title: .NET Core- és SQL Database-webalkalmazás összeállítása az Azure App Service-ben | Microsoft Docs
description: Megismerheti, hogyan tehet szert egy olyan, az Azure App Service-ben működő .NET Core-alkalmazásra, amely csatlakozik az SQL Database-hez.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/11/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 90cd9b4b29c0860355d318201df49262afd82de5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-build-a-net-core-and-sql-database-web-app-in-azure-app-service"></a>Oktatóanyag: .NET Core- és SQL Database-webalkalmazás összeállítása az Azure App Service-ben

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [.NET Core- és SQL Database-webalkalmazás összeállítása a Linuxon futó Azure App Service-ben](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Az [App Service](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre .NET Core-webalkalmazást, és hogyan csatlakoztathatja azt egy SQL Database-adatbázishoz. Az oktatóanyag eredménye egy, az App Service-ben futó .NET Core MVC-alkalmazás lesz.

![App Service-ben futó alkalmazás](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * SQL Database-adatbázis létrehozása az Azure-ban
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [A .NET Core telepítése](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>Helyi .NET Core-alkalmazás létrehozása

Ebben a lépésben a helyi .NET Core-projektet állíthatja be.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa az alábbi parancsokat a mintaadattár klónozásához és a gyökerére való módosításhoz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

A mintaprojekt egy, az [Entity Framework Core](https://docs.microsoft.com/ef/core/) szolgáltatást használó, alapszintű CRUD (létrehoz-olvas-frissít-töröl) alkalmazást tartalmaz.

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez, adatbázisok migrálásához és az alkalmazás elindításához.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány _teendőt_.

![sikeres csatlakozás az SQL Database-hez](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Ha bármikor le szeretné állítani a .NET Core-t, nyomja meg a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Éles SQL Database létrehozása

Ebben a lépésben egy SQL Database-adatbázist hozhat létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

Ez az oktatóanyag az SQL Database-hez az [Azure SQL Database-t](/azure/sql-database/) használja.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database logikai kiszolgáló létrehozása

A Cloud Shellben hozzon létre egy SQL Database logikai kiszolgálót az [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) paranccsal.

Cserélje le a *\<server_name>* helyőrzőt az SQL Database-adatbázis egyedi nevére. Ezt a nevet a rendszer SQL Database-végpontként (`<server_name>.database.windows.net`) fogja használni, így annak egyedinek kell lennie az összes Azure-beli logikai kiszolgálóban. A név csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet. Valamint cserélje le a *\<db_username>* és a *\<db_password>* helyőrzőket a választott felhasználónévre és jelszóra. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Az SQL Database logikai kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](../sql-database/sql-database-firewall-configure.md) az [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](app-service-ip-addresses.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy [S0 teljesítményszintű](../sql-database/sql-database-service-tiers-dtu.md) adatbázist a kiszolgálón az [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) parancs használatával.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati karakterlánc létrehozása

Cserélje le a következő karakterláncot a korábban használt *\<server_name>*, *\<db_username>* és *\<db_password>* értékre.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Ez a .NET Core-alkalmazás kapcsolati karakterlánca. Másolja későbbi felhasználás céljára.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe az App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Környezeti változó konfigurálása

Kapcsolati karakterláncok az Azure-alkalmazáshoz való beállításához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) parancsot a Cloud Shellben. A következő parancsban cserélje az *\<app name>*, valamint a *\<connections_string>* paramétert a korábban beállított kapcsolati karakterláncra.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Következő lépésként állítsa az `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást _Éles_ értékre. Ez a beállítás jelzi, hogy a szolgáltatás az Azure-ban fut-e, mert az SQLite-ot használja a helyi fejlesztési környezethez és az SQL Database-t az Azure-környezethez.

A következő példa egy `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást konfigurál az Azure-webalkalmazásban. Cserélje le az *\<app_name>* helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Csatlakozás SQL Database-hez éles környezetben

A helyi adattárban nyissa meg a Startup.cs fájlt, és keresse meg a következő kódot:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Cserélje le a következő kódra, amely a korábban beállított környezeti változókat használja.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Ha ez a kód azt észleli, hogy éles üzemben fut (ami Azure-környezetet jelez), az SQL Database-hez való csatlakozáshoz beállított kapcsolati karakterláncot használja.

Ha az Azure-ban fut, a `Database.Migrate()` hívás segítséget nyújt, mert automatikusan létrehozza a .NET Core-alkalmazáshoz szükséges adatbázisokat a migrálási konfiguráció alapján. 

Mentse a módosításokat, majd véglegesítse őket a Git adattárban. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Keresse meg az üzembe helyezett webalkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

Adjon hozzá néhány teendőt.

![App Service-ben futó alkalmazás](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Gratulálunk!** Egy adatvezérelt .NET Core-alkalmazást futtat az App Service-ben.

## <a name="update-locally-and-redeploy"></a>Frissítés helyileg és ismételt üzembe helyezés

Ebben a lépésben módosíthatja az adatbázissémát, és közzéteheti azt az Azure-ban.

### <a name="update-your-data-model"></a>Adatmodell frissítése

A kódszerkesztőben nyissa meg a _Models\Todo.cs_ fájlt. Adja hozzá a következő tulajdonságot a `ToDo` osztályhoz:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First migrálás helyi futtatása

Futtasson néhány parancsot a helyi adatbázis frissítéséhez.

```bash
dotnet ef migrations add AddProperty
```

Frissítse a helyi adatbázist:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Az új tulajdonság használata

Hajtson végre néhány módosítást a kódban a `Done` tulajdonság használatához. Ebben az oktatóanyagban az egyszerűség kedvéért csak az `Index` és a `Create` nézetet módosítja, így láthatja a tulajdonságot működés közben.

Nyissa meg a _Controllers\TodosController.cs_ fájlt.

Keresse meg a `Create([Bind("ID,Description,CreatedDate")] Todo todo)` metódust, és adja hozzá a `Done` kifejezést a `Bind` attribútum tulajdonságok listájához. Amikor végzett, a `Create()` metódus aláírása a következő kódhoz hasonló:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Nyissa meg a _Views\Todos\Create.cshtml_ fájlt.

A Razor-kódban látnia kell a `Description` alá tartozó `<div class="form-group">` elemet és egy másik, `<div class="form-group">` elemet a `CreatedDate` részhez. Közvetlenül ezután a két elem után adjon hozzá egy `<div class="form-group">` elemet a `Done` részhez:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Nyissa meg a _Views\Todos\Index.cshtml_ fájlt.

Keresse meg az üres `<th></th>` elemet. Az elem felett adja hozzá a következő Razor-kódot:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Keresse meg a `asp-action` címke-segítőket tartalmazó `<td>` elemet. Az elem felett adja hozzá a következő Razor-kódot:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Mást nem kell tennie a módosítások megtekintéséhez az `Index` és a `Create` nézetben.

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

Futtassa helyben az alkalmazást.

```bash
dotnet run
```

A böngészőjében lépjen a `http://localhost:5000/` helyre. Most hozzáadhat teendőket, és bejelölheti a **Kész** jelölőnégyzetet. Ezután ennek meg kell jelennie a főoldalon befejezett elemként. Ne feledje, hogy az `Edit` nézetben nem jelenik meg a `Done` mező, mivel az `Edit` nézetet nem módosította.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

```bash
git add .
git commit -m "added done field"
git push azure master
```

Miután a `git push` befejeződött, lépjen az Azure webalkalmazáshoz, és próbálja ki az új funkciót.

![Az Azure webalkalmazás a Code First migrálás után](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. Ha ismét közzéteszi a .NET Core-alkalmazást, az SQL Database-ben meglévő adatok nem vesznek el. Emellett az Entity Framework Core Migrations csak az adatsémát módosítja, a meglévő adatokat érintetlenül hagyja.

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * SQL Database-adatbázis létrehozása az Azure-ban
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
