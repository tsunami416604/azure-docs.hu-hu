---
title: Az ASP.NET Core és SQL Database linuxon – az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan tehet szert az ASP.NET Core-alkalmazás SQL-hez az Azure App Service Linux rendszeren dolgozik.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c90d0d2596eb6b8650e2d9809b23bb0e184d97c0
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547827"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>A linuxon futó Azure App Service-ben az ASP.NET Core és SQL Database alkalmazás készítése

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. Az App Service-ben üzembe _Windows_, lásd: [hozhat létre egy .NET Core és SQL Database az Azure App Service-alkalmazást](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy .NET Core-alkalmazást, és csatlakoztassa egy SQL-adatbázis. Az oktatóanyag eredménye egy, a Linux App Service-ben futó .NET Core MVC-alkalmazás lesz.

![Linux App Service-ben futó alkalmazás](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * SQL Database-adatbázis létrehozása az Azure-ban
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

![sikeres csatlakozás az SQL Database-hez](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Ha bármikor le szeretné állítani a .NET Core-t, nyomja meg a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Éles SQL Database létrehozása

Ebben a lépésben egy SQL Database-adatbázist hozhat létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

Ez az oktatóanyag az SQL Database-hez az [Azure SQL Database-t](/azure/sql-database/) használja.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database logikai kiszolgáló létrehozása

A Cloud Shellben hozzon létre egy SQL Database logikai kiszolgálót az [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) paranccsal.

Cserélje le a  *\<-kiszolgálónév >* helyőrzőt egy egyedi SQL-adatbázis neve. Ezt a nevet a rendszer SQL Database-végpontként (`<server-name>.database.windows.net`) fogja használni, így annak egyedinek kell lennie az összes Azure-beli logikai kiszolgálóban. A név csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet. Továbbá cserélje le  *\<db-username >* és  *\<db-password >* felhasználónévvel és a választott jelszó. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Az SQL Database logikai kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](../../sql-database/sql-database-firewall-configure.md) az [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy [S0 teljesítményszintű](../../sql-database/sql-database-service-tiers-dtu.md) adatbázist a kiszolgálón az [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) parancs használatával.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati sztring létrehozása

Cserélje le a következő karakterláncot a  *\<-kiszolgálónév >*,  *\<db-username >*, és  *\<db-password >* , korábban használt.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Ez a .NET Core-alkalmazás kapcsolati sztringje. Másolja későbbi felhasználás céljára.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe a Linuxon futó App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Környezeti változó konfigurálása

Kapcsolati sztringek az Azure-alkalmazáshoz való beállításához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. A következő parancsban cserélje le a  *\<alkalmazás-neve >*, valamint a  *\<kapcsolati karakterlánc >* paraméter a korábban létrehozott kapcsolati karakterlánccal.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

Következő lépésként állítsa az `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást _Éles_ értékre. Ez a beállítás jelzi, hogy a szolgáltatás az Azure-ban fut-e, mert az SQLite-ot használja a helyi fejlesztési környezethez és az SQL Database-t az Azure-környezethez.

Ez a példa konfigurálja egy `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást az Azure-alkalmazáshoz. Cserélje le a  *\<alkalmazás-neve >* helyőrző.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
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
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Ha ez a kód azt észleli, hogy éles üzemben fut (ami Azure-környezetet jelez), az SQL Database-hez való csatlakozáshoz beállított kapcsolati sztringet használja. Hogyan érhetők el az alkalmazásbeállítások az App Service-ben a további információkért lásd: [hozzáférés a környezeti változókhoz](configure-language-dotnetcore.md#access-environment-variables).

Ha az Azure-ban fut, a `Database.Migrate()` hívás segítséget nyújt, mert automatikusan létrehozza a .NET Core-alkalmazáshoz szükséges adatbázisokat a migrálási konfiguráció alapján.

Mentse a módosításokat, majd véglegesítse őket a Git adattárban.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Az Azure alkalmazás megkeresése tallózással

Keresse meg a telepített alkalmazást, a webböngésző használatával.

```bash
http://<app-name>.azurewebsites.net
```

Adjon hozzá néhány teendőt.

![Linux App Service-ben futó alkalmazás](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gratulálunk!** Egy adatvezérelt .NET Core-alkalmazást futtat a Linuxon futó App Service-ben.

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

Keresse meg a `Create()` metódust, és adja hozzá a `Done` kifejezést a `Bind` attribútum tulajdonságok listájához. Amikor végzett, a `Create()` metódus aláírása a következő kódhoz hasonló:

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

Miután a `git push` befejeződött, lépjen az Azure alkalmazásba, és próbálja ki az új funkciókat.

![Az Azure app Code First Migrálás után](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. Ha ismét közzéteszi a .NET Core-alkalmazást, az SQL Database-ben meglévő adatok nem vesznek el. Emellett az Entity Framework Core Migrations csak az adatsémát módosítja, a meglévő adatokat érintetlenül hagyja.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

A mintaprojekt már követi az útmutatóban talál: [ASP.NET Core naplózás az Azure-ban](https://docs.microsoft.com/aspnet/core/fundamentals/logging#logging-in-azure) két konfigurációs módosítás:

- Tartalmaz egy hivatkozást a `Microsoft.Extensions.Logging.AzureAppServices` a *DotNetCoreSqlDb.csproj*.
- Hívások `loggerFactory.AddAzureWebAppDiagnostics()` a *Startup.cs*.

> [!NOTE]
> A projekt naplózási szint beállítása `Information` a *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Az ASP.NET Core-naplók testreszabásáról további információkért lásd: [az ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

Nyissa meg a [az Azure portal](https://portal.azure.com) szeretné megtekinteni a létrehozott alkalmazást.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Alapértelmezés szerint a portál megjeleníti az alkalmazás **áttekintése** lapot. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazás](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrások:

> [!div class="nextstepaction"]
> [Az ASP.NET Core-alkalmazás konfigurálása](configure-language-dotnetcore.md)
