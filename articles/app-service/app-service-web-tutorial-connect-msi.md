---
title: Biztonságos SQL Database-hozzáférés felügyelt identitással – Azure App Service | Microsoft Docs
description: Ismerje meg, hogyan teheti biztonságossá az adatbázis-kapcsolatot egy felügyelt identitás használatával, és hogyan alkalmazhatja azt más Azure-szolgáltatásokra is.
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
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177076"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Az Azure SQL Database-kapcsolat biztonságossá tétele az App Service-ből felügyelt identitás segítségével

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban a felügyelt identitást a következő oktatóanyagok egyikében létrehozott minta webalkalmazáshoz fogja hozzáadni: 

- [Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Az oktatóanyagban szereplő lépések a következő verziókat támogatják:
> 
> - .NET-keretrendszer 4.7.2 és újabb verziók.
> - .NET Core 2,2 és újabb verziók.
>

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

> [!NOTE]
>Az Azure AD-hitelesítés _különbözik_ a helyszíni Active Directory (AD DS) [integrált Windows-hitelesítéstől](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) . AD DS és az Azure AD teljesen különböző hitelesítési protokollokat használ. További információ: [Azure ad Domain Services dokumentáció](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az [oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure](app-service-web-tutorial-dotnet-sqldatabase.md) -ban a SQL Database vagy [oktatóanyag: ASP.net Core és SQL Database alkalmazás létrehozása Azure app Service](app-service-web-tutorial-dotnetcore-sqldb.md). Ha még nem tette meg, kövesse az első két oktatóanyag egyikét. Azt is megteheti, hogy a saját .NET-alkalmazásának lépéseit a SQL Database használatával is módosíthatja.

Ha a háttérrendszer használatával szeretne hibakeresést végezni az alkalmazásban SQL Database, győződjön meg arról, hogy engedélyezte az ügyfélkapcsolatot a számítógépről. Ha nem, adja hozzá az ügyfél IP-címét a [kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)című témakör lépéseit követve.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Adatbázis-hozzáférés biztosítása az Azure AD-felhasználóhoz

Először engedélyezze az Azure AD-hitelesítés SQL Database egy Azure AD-felhasználó kiosztásával a SQL Database-kiszolgáló Active Directory rendszergazdájaként. Ez a felhasználó különbözik az Azure-előfizetéshez való regisztrációhoz használt Microsoft-fióktól. Az Azure AD-ben létrehozott, importált, szinkronizált vagy meghívott felhasználónak kell lennie. További információ az engedélyezett Azure AD-felhasználókról: [Azure ad-szolgáltatások és-korlátozások a SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Ha az Azure AD-bérlő még nem rendelkezik felhasználóval, hozzon létre egyet a [felhasználók hozzáadása vagy törlése a Azure Active Directory használatával](../active-directory/fundamentals/add-users-azure-active-directory.md)című rész lépéseit követve.

Keresse meg az Azure AD-felhasználó objektumazonosítót a [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) paranccsal, és cserélje le a *\<user-principal-name >* . Az eredmény egy változóba lesz mentve.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Ha szeretné megtekinteni az összes egyszerű felhasználónevet az Azure AD-ben, futtassa a `az ad user list --query [].userPrincipalName` parancsot.
>

Adja hozzá ezt az Azure AD-felhasználót Active Directory-rendszergazdaként a Cloud Shell [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) parancsának használatával. A következő parancsban cserélje le a *\<kiszolgáló üzemmódját-name >t* a SQL Database-kiszolgáló nevére (a `.database.windows.net` utótag nélkül).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory-rendszergazda hozzáadásával kapcsolatos további információkért lásd: [Azure Active Directory-rendszergazda létesítése a Azure SQL Database-kiszolgálóhoz](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>A Visual Studio beállítása

### <a name="windows"></a>Windows
A Windowshoz készült Visual Studio integrálva van az Azure AD-hitelesítéssel. A fejlesztés és a hibakeresés a Visual Studióban való engedélyezéséhez adja hozzá az Azure AD-felhasználót a Visual Studióban, ehhez válassza a menü **fájl** > **Fiókbeállítások** elemét, majd kattintson a **fiók hozzáadása**lehetőségre.

Az Azure AD-felhasználó Azure-szolgáltatásbeli hitelesítéshez való beállításához válassza az **eszközök** > **Beállítások lehetőséget** a menüben, majd válassza az **Azure szolgáltatás-hitelesítés** > **fiók**lehetőséget. Válassza ki a hozzáadott Azure AD-felhasználót, és kattintson **az OK**gombra.

Most már készen áll az alkalmazás fejlesztésére és hibakeresésére SQL Database a háttérben, az Azure AD-hitelesítés használatával.

### <a name="macos"></a>macOS

Visual Studio for Mac nincs integrálva az Azure AD-hitelesítéssel. Azonban a későbbiekben használni kívánt [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) függvénytár használhat jogkivonatokat az Azure CLI-ből. A fejlesztés és a hibakeresés a Visual Studióban való engedélyezéséhez először [telepítenie kell az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t a helyi gépre.

Miután telepítette az Azure CLI-t a helyi gépen, jelentkezzen be az Azure CLI-be az alábbi paranccsal az Azure AD-felhasználó használatával:

```bash
az login --allow-no-subscriptions
```
Most már készen áll az alkalmazás fejlesztésére és hibakeresésére SQL Database a háttérben, az Azure AD-hitelesítés használatával.

## <a name="modify-your-project"></a>A projekt módosítása

A projekthez követett lépések attól függnek, hogy ASP.NET-vagy ASP.NET Core-projektről van-e szó.

- [ASP.NET módosítása](#modify-aspnet)
- [ASP.NET Core módosítása](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

A *web. config*fájlban a fájl elejére, és végezze el a következő módosításokat:

- A `<configSections>` mezőben adja hozzá a következő szakasz deklarációját:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- a záró `</configSections>` címke alatt adja hozzá a következő XML-kódot a `<SqlAuthenticationProviders>` értékhez.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a `MyDbConnection` nevű kapcsolati karakterláncot, és cserélje le a `connectionString` értéket a `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` értékre. Cserélje le a _\<kiszolgáló üzemmódját-name >_ és a _\<db-Name > nevet_ a kiszolgáló nevére és az adatbázis nevére.

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database-kiszolgálót később kell beállítania, hogy engedélyezze a kapcsolódást a App Service alkalmazás felügyelt identitásával.

Írja be a `Ctrl+F5` parancsot az alkalmazás futtatásához. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

### <a name="modify-aspnet-core"></a>ASP.NET Core módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

A [ASP.net Core és SQL Database oktatóanyagban](app-service-web-tutorial-dotnetcore-sqldb.md)az `MyDbConnection` kapcsolatok karakterlánca egyáltalán nem használatos, mert a helyi fejlesztési környezet SQLite-adatbázisfájlt használ, és az Azure éles környezete app Service-ból származó kapcsolatok karakterláncot használ. Active Directory hitelesítéssel mindkét környezet ugyanazt a kapcsolódási karakterláncot szeretné használni. A *appSettings. JSON*fájlban cserélje le az `MyDbConnection` kapcsolatok karakterlánc értékét a alábbiakra:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

A *Startup.cs*távolítsa el a korábban hozzáadott kód szakaszt:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

És cserélje le a következő kódra:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Ezután adja meg a Entity Framework adatbázis-környezetet a SQL Database hozzáférési jogkivonatával. A *Data\MyDatabaseContext.cs*-ben adja hozzá a következő kódot az üres `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktor kapcsos zárójelében:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Ez a demonstrációs kód az érthetőség kedvéért szinkronban van. További információ: [aszinkron útmutató a konstruktorokhoz](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database-kiszolgálót később kell beállítania, hogy engedélyezze a kapcsolódást a App Service alkalmazás felügyelt identitásával. A `AzureServiceTokenProvider` osztály gyorsítótárazza a memóriában lévő tokent, és lekéri azt az Azure AD-ből közvetlenül a lejárat előtt. Nincs szüksége egyéni kódra a jogkivonat frissítéséhez.

> [!TIP]
> Ha az Ön által konfigurált Azure AD-felhasználó több bérlőhöz fér hozzá, a megfelelő hozzáférési jogkivonat lekéréséhez hívja meg a `GetAccessTokenAsync("https://database.windows.net/", tenantid)` értéket a kívánt bérlői AZONOSÍTÓval.

Írja be a `Ctrl+F5` parancsot az alkalmazás futtatásához. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

## <a name="use-managed-identity-connectivity"></a>Felügyelt identitású kapcsolat használata

Ezután konfigurálja a App Service alkalmazást úgy, hogy az SQL Databasehoz kapcsolódjon egy rendszerhez rendelt felügyelt identitással.

### <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot a Cloud Shellben. A következő parancsban cserélje le a *\<app-name >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Íme egy példa a kimenetre:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Felügyelt identitás hozzáadása egy Azure AD-csoporthoz

Ahhoz, hogy ez az identitás hozzáférhessen a SQL Databasehoz, hozzá kell adnia azt egy [Azure ad-csoporthoz](../active-directory/fundamentals/active-directory-manage-groups.md). A Cloud Shell vegye fel azt egy új, _myAzureSQLDBAccessGroup_nevű csoportba, amely a következő parancsfájlban látható:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Ha minden egyes parancsnál meg szeretné tekinteni a JSON-kimenetet, hagyja el a `--query objectId --output tsv` paramétereket.

### <a name="grant-permissions-to-azure-ad-group"></a>Engedélyek megadása az Azure AD-csoportnak

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le a _\<kiszolgáló üzemmódját-name >t_ a SQL Database kiszolgálónévre, a _\<db-Name >ra_ az alkalmazás által használt adatbázis nevével, és _\<aad-user-name >_ és _\<aad-Password >_ az Azure ad-felhasználó hitelesítő adatok.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

A kívánt adatbázishoz tartozó SQL-parancssorban futtassa az alábbi parancsokat az Azure AD-csoport hozzáadásához, és adja meg az alkalmazás igényeinek megfelelő engedélyeket. Például: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy a *web. config* vagy a *appSettings. JSON* fájlban elvégzett módosítások a felügyelt identitással működnek, így az egyetlen teendő, ha el szeretné távolítani a meglévő kapcsolati karakterláncot a app Serviceban, amelyet a Visual Studio létrehozta az alkalmazás üzembe helyezését az első idő. Használja az alábbi parancsot, de cserélje le az *\<app-name >* az alkalmazás nevére.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

**Ha az [oktatóanyagból származik: ASP.NET-alkalmazás létrehozása az Azure-ban a SQL Databaseval](app-service-web-tutorial-dotnet-sqldatabase.md)** , tegye közzé a módosításokat a Visual Studióban. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. 

**Ha [oktatóanyag: hozzon létre egy ASP.NET Core és SQL Database alkalmazást a Azure app Serviceban](app-service-web-tutorial-dotnetcore-sqldb.md)** , tegye közzé a módosításokat a git használatával a következő parancsokkal:

```bash
git commit -am "configure managed identity"
git push azure master
```

Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)
