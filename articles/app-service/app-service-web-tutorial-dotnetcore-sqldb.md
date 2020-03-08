---
title: 'Oktatóanyag: ASP.NET Core a SQL Database'
description: Megismerheti, hogyan tehet szert egy olyan, az Azure App Service-ben működő .NET Core-alkalmazásra, amely csatlakozik az SQL Database-hez.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 3ad011529f8b4be90fc0c108a2049c30d1c69302
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897316"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása Azure App Service

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _linuxon_app Service való üzembe helyezéssel kapcsolatban lásd: [.net Core-és SQL Database-alkalmazás létrehozása Linux rendszeren Azure app Service](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. Ez az oktatóanyag bemutatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz. Az oktatóanyag eredménye egy, az App Service-ben futó .NET Core MVC-alkalmazás lesz.

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
* [.NET Core SDK telepítése](https://dotnet.microsoft.com/download)

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

Ez az oktatóanyag az SQL-adatbázisokhoz az [Azure SQL Database-t](/azure/sql-database/) használja.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database logikai kiszolgáló létrehozása

A Cloud Shellben hozzon létre egy SQL Database logikai kiszolgálót az [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) paranccsal.

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

Hozzon létre egy [kiszolgálószintű Azure SQL Database-tűzfalszabályt](../sql-database/sql-database-firewall-configure.md) az [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy [S0 teljesítményszintű](../sql-database/sql-database-service-tiers-dtu.md) adatbázist a kiszolgálón az [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) parancs használatával.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati sztring létrehozása

Cserélje le a következő sztringet a korábban használt *\<server_name&gt;* , *\<db_username&gt;* és *\<db_password&gt;* értékre.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Ez a .NET Core-alkalmazás kapcsolati sztringje. Másolja későbbi felhasználás céljára.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe az App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>A kapcsolatok karakterláncának konfigurálása

Kapcsolati sztringek az Azure-alkalmazáshoz való beállításához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. A következő parancsban cserélje az *\<app name&gt;* , valamint a *\<connections_string&gt;* paramétert a korábban beállított kapcsolati sztringre.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

ASP.NET Core a standard minta használatával, például az *appSettings. JSON*fájlban megadott kapcsolatok karakterláncával (`MyDbConnection`) elnevezett kapcsolatok karakterláncát használhatja. Ebben az esetben a `MyDbConnection` a *appSettings. JSON*fájlban is meg van adva. Ha App Service fut, a App Serviceban definiált kapcsolati karakterlánc elsőbbséget élvez a *appSettings. JSON*fájlban megadott kapcsolati karakterlánccal szemben. A kód a *appSettings. JSON* értéket használja a helyi fejlesztés során, és ugyanaz a kód a app Service értéket használja az üzembe helyezéskor.

Ha szeretné megtudni, hogyan hivatkoznak a kapcsolati karakterláncra a kódban, tekintse meg a [kapcsolódás SQL Database éles](#connect-to-sql-database-in-production)környezetben című témakört.

### <a name="configure-environment-variable"></a>Környezeti változó konfigurálása

Következő lépésként állítsa az `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást _Éles_ értékre. Ezzel a beállítással megtudhatja, hogy az Azure-ban fut-e, mert az SQLite-t használja a helyi fejlesztési környezethez, és SQL Database az Azure-környezethez.

Az alábbi példa egy `ASPNETCORE_ENVIRONMENT` alkalmazás beállítását konfigurálja az Azure-alkalmazásban. Cserélje le az *\<app_name>* helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Ha szeretné megtudni, hogyan hivatkoznak a környezeti változóra a kódban, tekintse meg a [kapcsolódás SQL Database éles](#connect-to-sql-database-in-production)környezetben című témakört.

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

Ha ez a kód azt észleli, hogy éles üzemben fut (amely az Azure-környezetet jelzi), akkor a SQL Databasehoz való kapcsolódáshoz konfigurált kapcsolati karakterláncot használja.

A `Database.Migrate()` hívás segít, ha az Azure-ban fut, mert automatikusan létrehozza a .NET Core-alkalmazás által igényelt adatbázisokat az áttelepítési konfiguráció alapján. 

> [!IMPORTANT]
> A bővíteni kívánt éles alkalmazások esetében kövesse az [áttelepítés éles környezetben való alkalmazásának](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)ajánlott eljárásait.
> 

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

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

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

A Razor-kódban látnia kell a `<div class="form-group">` alá tartozó `Description` elemet és egy másik, `<div class="form-group">` elemet a `CreatedDate` részhez. Közvetlenül ezután a két elem után adjon hozzá egy `<div class="form-group">` elemet a `Done` részhez:

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

Keresse meg a `<td>` címke-segítőket tartalmazó `asp-action` elemet. Az elem felett adja hozzá a következő Razor-kódot:

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

A `git push` befejezését követően navigáljon a App Service alkalmazáshoz, és próbálkozzon a teendők hozzáadásával, és jelölje be a **kész**lehetőséget.

![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. A .NET Core-alkalmazás újbóli közzétételekor a SQL Database meglévő adatai nem vesznek el. Emellett az Entity Framework Core Migrations csak az adatsémát módosítja, a meglévő adatokat érintetlenül hagyja.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Amíg a ASP.NET Core alkalmazás a Azure App Service fut, a konzolon lévő naplók beszerezhetők a Cloud Shell. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A minta projekt már a következő útmutatást követi: [ASP.net Core naplózás az Azure-ban](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) két konfigurációs módosítással:

- Hivatkozást tartalmaz a *DotNetCoreSqlDb. csproj*`Microsoft.Extensions.Logging.AzureAppServices`.
- Meghívja `loggerFactory.AddAzureWebAppDiagnostics()` a *program.cs*.

A ASP.NET Core [naplózási szintjének](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) app Service az alapértelmezett `Error`szintről `Information`ra való beállításához használja a`az webapp log config`[Cloud Shell](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) parancsát.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> A projekt naplózási szintje már be van állítva a *appSettings. JSON*fájlban `Information`ra.
> 

A naplóstreamelés indításához használja az [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) parancsot a Cloud Shellben.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

A log streaming elindítása után frissítse az Azure-alkalmazást a böngészőben a webes forgalom eléréséhez. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak. Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt: `Ctrl`+`C`.

A ASP.NET Core naplók testreszabásával kapcsolatos további információkért tekintse meg a [ASP.net Core naplózása](https://docs.microsoft.com/aspnet/core/fundamentals/logging)című témakört.

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás megtekintéséhez a [Azure Portal](https://portal.azure.com)keresse meg és válassza a **app Services**lehetőséget.

![Azure Portal App Services kiválasztása](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán láthatók a különböző megnyitható konfigurációs lapok.

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

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)
