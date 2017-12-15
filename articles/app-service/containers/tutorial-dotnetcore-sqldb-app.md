---
title: "A .NET Core és az SQL-adatbázis webalkalmazás az Azure App Service létrehozása Linux rendszeren |} Microsoft Docs"
description: "Útmutató a .NET Core alkalmazást az Azure App Service Linux, SQL adatbázis-kapcsolat használata."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d6c679518bfc712e6a08ffae722b0cc5d2b038aa
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>A .NET Core és az SQL-adatbázis webalkalmazás az Azure App Service létrehozása Linux-kiszolgálón

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan .NET Core-webalkalmazás létrehozása, és csatlakoztassa egy SQL-adatbázisban. Amikor elkészült, akkor a .NET Core MVC alkalmazás az App Service-ben futó Linux kell.

![az alkalmazás az App Service-ben futó Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Amiről tanulni hogyan:

> [!div class="checklist"]
> * SQL-adatbázis létrehozása az Azure-ban
> * Csatlakozás a .NET Core alkalmazásnak, hogy SQL-adatbázis
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>Helyi .NET Core-alkalmazás létrehozása

Ebben a lépésben beállíthatja a helyi .NET Core-projektet.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A Terminálszolgáltatások ablakban `cd` egy működő könyvtárba.

A következő parancsokat a minta-tárház klónozása és a legfelső szintű módosítására.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

A minta-projekt tartalmazza egy alapvető CRUD (hozzon létre-olvasás-Módosítás-Törlés) alkalmazás használatával [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Az alkalmazás futtatása

A következő parancsokat a szükséges csomagok telepítése, futtassa az adatbázis áttelepítése és az alkalmazás indításához.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Válassza ki a **hozzon létre új** hivatkozásra, és hozzon létre több _tennivaló_ elemeket.

![sikeresen csatlakozik az SQL-adatbázis](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

A .NET Core bármikor leállításához nyomja le az `Ctrl+C` a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Az üzemi SQL-adatbázis létrehozása

Ebben a lépésben az Azure SQL adatbázis létrehozása. Az alkalmazás telepítésekor az Azure-ba, használja a felhő adatbázis.

Az SQL Database esetén ez az oktatóanyag használja [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database logikai kiszolgáló létrehozása

A felhő rendszerhéj az SQL Database logikai kiszolgáló létrehozása a [létrehozása az sql server](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) parancsot.

Cserélje le a  *\<kiszolgáló_neve >* egyedi SQL-adatbázis nevű helyőrző. Ez a név az SQL-adatbázis végpont részeként használatos `<server_name>.database.windows.net`, így a nevének egyedinek kell lennie az Azure-ban minden logikai kiszolgáló között. A név csak kisbetűket, számokat és a kötőjel (-) karaktert kell tartalmaznia, és 3 – 50 karakter közé kell esnie. Továbbá cserélje le  *\<db_username >* és  *\<db_password >* felhasználónévvel és jelszóval az Ön által választott. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Ha az SQL Database logikai kiszolgáló létrehozása az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló:

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

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](../../sql-database/sql-database-firewall-configure.md) az [az sql server firewall create](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create) parancs használatával. Ha mind a kezdő IP-Címét, és a záró IP-0.0.0.0 van beállítva, a tűzfal csak van megnyitva más Azure-erőforrások. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy [S0 teljesítményszintű](../../sql-database/sql-database-service-tiers.md) adatbázist a kiszolgálón az [az sql db create](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) paranccsal.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati karakterlánc létrehozása

Cserélje le a következő karakterláncot a  *\<kiszolgáló_neve >*,  *\<db_username >*, és  *\<db_password >* meg korábban használt.

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Ez a .NET Core alkalmazás a kapcsolati karakterláncot. Másolja a használatra később.

## <a name="deploy-app-to-azure"></a>Alkalmazás telepítése az Azure-bA

Ebben a lépésben a Linux App Service .NET Core SQL Database-kompatibilis alkalmazást telepít központilag.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Egy környezeti változó beállítása

Kapcsolati karakterláncok az Azure alkalmazás beállításához használja a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) a felhő rendszerhéj parancsot. A következő parancsban cserélje le  *\<alkalmazás neve >*, valamint a  *\<connection_string >* paraméter a korábban létrehozott kapcsolati karakterlánccal.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Következő lépésként állítsa `ASPNETCORE_ENVIRONMENT` Alkalmazásbeállítás _éles_. Ezzel a beállítással azt jelzi, hogy fut az Azure, mert használ SQLLite az a helyi fejlesztési környezet és az SQL Database az Azure környezetben.

A következő példa egy `ASPNETCORE_ENVIRONMENT` Alkalmazásbeállítás az Azure web app alkalmazásban. Cserélje le a  *\<alkalmazás_neve >* helyőrző.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Csatlakozás az SQL Database éles környezetben

A helyi tárházban nyissa meg a Startup.cs, és keresse meg a következő kódot:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Cserélje le az alábbi kód, amely a környezeti változókat, amelyek korábban konfigurált használja.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

Ha ez a kód észleli, hogy (amely azt jelzi, hogy az Azure környezetbe) éles környezetben fut, majd használja a kapcsolati karakterlánc konfigurált az SQL-adatbázishoz való kapcsolódáshoz.

A `Database.Migrate()` hívás segítséget nyújt az Azure futtatásakor automatikusan létrehozza az adatbázisokat, mert .NET Core alkalmazás igényeinek, az áttelepítés konfiguráció alapján. 

Mentse a módosításokat.

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
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Keresse meg az Azure-webalkalmazásban

Tallózással keresse meg a telepített webalkalmazás webböngészővel.

```bash
http://<app_name>.azurewebsites.net
```

Adjon hozzá néhány Tennivalólista elemein.

![az alkalmazás az App Service-ben futó Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gratulálunk!** A .NET Core adatvezérelt alkalmazás az App Service Linux futtatja.

## <a name="update-locally-and-redeploy"></a>Helyileg frissítse és telepítse újra

Ebben a lépésben lehet módosítani az adatbázis-séma, és közzéteszi az Azure-bA.

### <a name="update-your-data-model"></a>Az adatmodell frissítése

Nyissa meg _Models\Todo.cs_ kód-szerkesztőben. A következő tulajdonság hozzáadása a `ToDo` osztály:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First áttelepítést helyileg történő futtatása

Futtassa a frissítések a helyi adatbázis néhány parancsokat.

```bash
dotnet ef migrations add AddProperty
```

A helyi adatbázis frissítése:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Az új tulajdonsággal

Néhány módosítást használata a kódban a `Done` tulajdonság. Az egyszerűség kedvéért ebben az oktatóanyagban, csak fogja módosítani a `Index` és `Create` nézeteket, művelet: a tulajdonság.

Nyissa meg _Controllers\TodosController.cs_.

Keresés a `Create()` metódust, és adja hozzá `Done` a tulajdonságok listájának a `Bind` attribútum. Amikor elkészült, a `Create()` metódus-aláírás néz ki a következő kódot:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Nyissa meg _Views\Todos\Create.cshtml_.

A Razor kódban kell megjelennie a `<div class="form-group">` eleme `Description`, és majd egy másik `<div class="form-group">` elem `CreatedDate`. Ez a két elem követő hozzáadása egy másik `<div class="form-group">` eleme `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Nyissa meg _Views\Todos\Index.cshtml_.

Keresse meg az üres `<th></th>` elemet. Ez az elem felett adja hozzá a következő Razor-kódot:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Keresés a `<td>` elem, amely tartalmazza a `asp-action` segítő címkét. Ez az elem felett adja hozzá a következő Razor-kódot:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

Ez minden szeretne látni a változtatásokat a `Index` és `Create` nézetek.

### <a name="test-your-changes-locally"></a>A módosításokat a helyi tesztelése

Az alkalmazás futtatása helyben.

```bash
dotnet run
```

A böngészőben navigáljon `http://localhost:5000/`. Ezután egy teendő hozzáadása és ellenőrzése **végzett**. Ezután azt kell jelennek meg az Ön újragondolt befejezett elemként. Ne feledje, hogy a `Edit` nézet nem jeleníti meg a `Done` mezőben, mert nem módosítja a `Edit` nézet.

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

```bash

git commit -am "added done field"
git push azure master
```

Egyszer a `git push` befejezése, navigáljon az Azure web app és az új funkciók kipróbálásához.

![Azure-webalkalmazásban kód első áttelepítés után](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

A meglévő Tennivalólista elemein továbbra is megjelennek. Ha a .NET Core alkalmazás közzétételéhez meglévő az SQL-adatbázis adatai nem elveszett. Entity Framework Core áttelepítések is, csak a Adatséma változik, és a meglévő adatok sértetlenek.

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Lépjen a [Azure-portálon](https://portal.azure.com) létrehozott webalkalmazás megjelenítéséhez.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Alapértelmezés szerint a portál megjeleníti a webalkalmazás **áttekintése** lap. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán a lapok megnyithatja a különböző konfigurációs lapok megjelenítése.

![Az App Service lap az Azure Portalon](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Hogy mit tudott:

> [!div class="checklist"]
> * SQL-adatbázis létrehozása az Azure-ban
> * Csatlakozás a .NET Core alkalmazásnak, hogy SQL-adatbázis
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Stream naplók az Azure-ból a Terminálszolgáltatások számára
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)