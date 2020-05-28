---
title: 'Oktatóanyag: ASP.NET Core a SQL Database'
description: Megtudhatja, hogyan szerezhet be egy Azure App Service-ben működő .NET Core-alkalmazást SQL Databasehoz való kapcsolódással.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/23/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 4e032ae5b5133bf1dab05bdccdc81af7bd9855ca
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013278"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása Azure App Service

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _linuxon_app Service való üzembe helyezéssel kapcsolatban lásd: [.net Core-és SQL Database-alkalmazás létrehozása Linux rendszeren Azure app Service](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. Ez az oktatóanyag bemutatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható a SQL Databasehoz. Az oktatóanyag eredménye egy, az App Service-ben futó .NET Core MVC-alkalmazás lesz.

![App Service-ben futó alkalmazás](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Adatbázis létrehozása Azure SQL Database
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">A legújabb .NET Core 3,1 SDK telepítése</a>

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

### <a name="run-the-application"></a>Alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez, adatbázisok migrálásához és az alkalmazás elindításához.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány _teendőt_.

![sikeres csatlakozás az SQL Database-hez](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Ha bármikor le szeretné állítani a .NET Core-t, nyomja meg a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-database-in-azure-sql-database"></a>Adatbázis létrehozása Azure SQL Database

Ebben a lépésben egy adatbázist hoz létre [Azure SQL Databaseban](/azure/sql-database/). Az alkalmazás az Azure-ban való üzembe helyezésekor ezt az adatbázist használja.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-server-in-azure-sql-database"></a>Kiszolgáló létrehozása Azure SQL Database

A Cloud Shellben hozzon létre egy [kiszolgálót](../azure-sql/database/logical-servers.md) a Azure SQL Databaseban a [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) paranccsal. A kiszolgálók olyan logikai szerkezetek, amelyek csoportként kezelt adatbázisok egy csoportját tartalmazzák.

Cserélje le a *\<server-name>* helyőrzőt *egyedi* névre. Ezt a nevet használja a globálisan egyedi SQL Database végpont részeként `<server-name>.database.windows.net` . Érvényes karakterek:, `a` - `z` `0` - `9` `-` . Továbbá cserélje le a *\<db-username>* és a *\<db-password>* nevet az Ön által választott felhasználónévre és jelszóra.

```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

A kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [kiszolgálói szintű tűzfalszabály](../azure-sql/database/firewall-configure.md) parancsot a [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](overview-inbound-outbound-ips.md#find-outbound-ips).

A Cloud Shell futtassa újra a parancsot, hogy engedélyezze a hozzáférést a helyi számítógépről a *\<your-ip-address>* [helyi IPv4 IP-címére](https://www.whatsmyip.org/)való lecseréléssel.

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database-in-azure-sql-database"></a>Adatbázis létrehozása Azure SQL Database

Hozzon létre egy [S0 teljesítményszintű](../azure-sql/database/service-tiers-dtu.md) adatbázist a kiszolgálón az [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) parancs használatával.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati sztring létrehozása

A parancs használatával szerezze be a kapcsolatok karakterláncát [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) .

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

A parancs kimenetében cserélje le a és a parancsot *\<username>* *\<password>* a korábban használt adatbázis-rendszergazdai hitelesítő adatokkal.

Ez a .NET Core-alkalmazás kapcsolati sztringje. Másolja későbbi felhasználás céljára.

### <a name="configure-app-to-connect-to-the-database-in-azure"></a>Az alkalmazás konfigurálása az adatbázishoz való kapcsolódásra az Azure-ban

A helyi adattárban nyissa meg a Startup.cs fájlt, és keresse meg a következő kódot:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Cserélje le a következő kódra.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> A bővíteni kívánt éles alkalmazások esetében kövesse az [áttelepítés éles környezetben való alkalmazásának](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)ajánlott eljárásait.

### <a name="run-database-migrations-to-the-database-in-azure"></a>Adatbázis-áttelepítés futtatása az Azure-adatbázisba

Az alkalmazás jelenleg egy helyi SQLite-adatbázishoz csatlakozik. Most, hogy konfigurált egy Azure SQL Database, hozza létre újra a kezdeti áttelepítést, hogy megcélozza azt.

A tárház gyökerében futtassa a következő parancsokat. Cserélje le *\<connection-string>* a szót a korábban létrehozott kapcsolatok-karakterláncra.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Alkalmazás futtatása új konfigurációval

Most, hogy az adatbázis-Migrálás az éles adatbázisban fut, tesztelje az alkalmazást a következő futtatásával:

```
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány _teendőt_. Az alkalmazás most beolvassa és beírja az adatait az éles adatbázisba.

Véglegesítse a helyi módosításokat, majd véglegesítse azt a git-tárházban.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Most már készen áll a kód üzembe helyezésére.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe az App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

### <a name="configure-connection-string"></a>A kapcsolatok karakterláncának konfigurálása

Az Azure-alkalmazáshoz tartozó kapcsolódási karakterláncok beállításához használja a [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud shell. A következő parancsban cserélje le a (z) és a (z *\<app-name>* *\<connection-string>* ) paramétert a korábban létrehozott kapcsolattípus-karakterláncra.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

A ASP.NET Coreban ezt a névvel ellátott kapcsolatok karakterláncát ( `MyDbConnection` ) használja a standard mintával, például az *appSettings. JSON*fájlban megadott kapcsolatok karakterláncával. Ebben az esetben a `MyDbConnection` *appSettings. JSON*fájlban is meg van adva. Ha App Service fut, a App Serviceban definiált kapcsolati karakterlánc elsőbbséget élvez a *appSettings. JSON*fájlban megadott kapcsolati karakterlánccal szemben. A kód a *appSettings. JSON* értéket használja a helyi fejlesztés során, és ugyanaz a kód a app Service értéket használja az üzembe helyezéskor.

Ha szeretné megtudni, hogyan hivatkoznak a kapcsolati karakterláncra a kódban, tekintse meg az [alkalmazás konfigurálása éles adatbázishoz való kapcsolódáshoz](#configure-app-to-connect-to-the-database-in-azure)című témakört.

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app-name>.azurewebsites.net
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

### <a name="rerun-database-migrations"></a>Adatbázis-áttelepítés újrafuttatása

Futtasson néhány parancsot az éles adatbázis frissítésének elvégzéséhez.

```bash
dotnet ef migrations add AddProperty
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

A `git push` befejezését követően navigáljon a app Service alkalmazáshoz, és próbálkozzon a teendők hozzáadásával, és jelölje be a **kész**lehetőséget.

![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. ASP.NET Core alkalmazás újbóli közzétételekor a SQL Database meglévő adatai nem vesznek el. Emellett az Entity Framework Core Migrations csak az adatsémát módosítja, a meglévő adatokat érintetlenül hagyja.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Amíg a ASP.NET Core alkalmazás a Azure App Service fut, a konzolon lévő naplók beszerezhetők a Cloud Shell. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A minta projekt már a következő útmutatást követi: [ASP.net Core naplózás az Azure-ban](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) két konfigurációs módosítással:

* Hivatkozást tartalmaz a `Microsoft.Extensions.Logging.AzureAppServices` *DotNetCoreSqlDb. csproj*-ben.
* Hívások `loggerFactory.AddAzureWebAppDiagnostics()` a *program.cs*-ben.

A ASP.NET Core [naplózási szintjének](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) app Service az `Information` alapértelmezett szintről történő beállításához használja a `Error` [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) Cloud shell parancsot.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> A projekt naplózási szintje már be van állítva `Information` a *appSettings. JSON*fájlban.

A log streaming elindításához használja a [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) parancsot a Cloud Shellban.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

A log streaming elindítása után frissítse az Azure-alkalmazást a böngészőben a webes forgalom eléréséhez. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak. Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt: `Ctrl` + `C` .

A ASP.NET Core naplók testreszabásával kapcsolatos további információkért tekintse meg a [ASP.net Core naplózása](https://docs.microsoft.com/aspnet/core/fundamentals/logging)című témakört.

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás megtekintéséhez a [Azure Portal](https://portal.azure.com)keresse meg és válassza a **app Services**lehetőséget.

![Azure Portal App Services kiválasztása](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
>
> * Adatbázis létrehozása Azure SQL Database
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](app-service-web-tutorial-custom-domain.md)
