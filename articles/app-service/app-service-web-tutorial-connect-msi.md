---
title: 'Oktatóanyag: Adatok elérése felügyelt identitással'
description: Ismerje meg, hogyan teheti biztonságosabbá az adatbázis-kapcsolatot egy felügyelt identitás használatával, és hogyan alkalmazhatja azt más Azure-szolgáltatásokra.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc, cli-validate
ms.openlocfilehash: 787809e4132defa101bb82659e8af1a2d9f1b9b6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453794"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Az Azure SQL Database-kapcsolat biztonságossá tétele az App Service-ből felügyelt identitás segítségével

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban felügyelt identitást adhat hozzá a következő oktatóanyagok egyikébe épített mintawebalkalmazáshoz: 

- [Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database használatával](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása az Azure App Service-ben](app-service-web-tutorial-dotnetcore-sqldb.md)

Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Az oktatóanyagban ismertetett lépések a következő verziókat támogatják:
> 
> - .NET keretrendszer 4.7.2
> - .NET Core 2.2
>

Mit fogsz tanulni:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Az Entitáskeretrendszer konfigurálása az Azure AD-hitelesítés SQL-adatbázissal való használatára
> * Csatlakozás sql-adatbázishoz a Visual Studio-ból az Azure AD-hitelesítés használatával

> [!NOTE]
>Az Azure AD-hitelesítés _eltér_ a helyszíni Active Directoryban (AD DS) [lévő integrált Windows-hitelesítéstől.](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) Az AD DS és az Azure AD teljesen más hitelesítési protokollokat használ. További információt az [Azure AD tartományi szolgáltatások dokumentációjában](https://docs.microsoft.com/azure/active-directory-domain-services/)talál.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk ott folytatódik, ahol abbahagyta az [oktatóanyagban: Hozzon létre egy ASP.NET alkalmazást az Azure-ban az SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) vagy [az oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása az Azure App Service-ben.](app-service-web-tutorial-dotnetcore-sqldb.md) Ha még nem tette meg, először kövesse a két oktatóanyag egyikét. Azt is megteheti, hogy az SQL Database segítségével saját .NET alkalmazásához igazítja a lépéseket.

Ha az SQL Database-t háttérrendszerként használó alkalmazást szeretné hibakeresésre használni, győződjön meg arról, hogy engedélyezte az ügyfélkapcsolatot a számítógépről. Ha nem, adja hozzá az ügyfél [IP-címét a Kiszolgálószintű IP-tűzfalszabályok kezelése az Azure Portal használatával című lépésben.](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Adatbázis-hozzáférés megadása az Azure AD-felhasználószámára

Először engedélyezze az Azure AD-hitelesítést az SQL Database-hez egy Azure AD-felhasználó hozzárendelésével az SQL Database-kiszolgáló Active Directory-rendszergazdájaként. Ez a felhasználó eltér az Azure-előfizetéshez való regisztrációhoz használt Microsoft-fióktól. Olyan felhasználónak kell lennie, amelyet az Azure AD-be hozott létre, importált, szinkronizált vagy meghívott. Az engedélyezett Azure AD-felhasználókról az [Azure AD-szolgáltatások és korlátozások az SQL Database ben](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)című témakörben talál további információt.

Ha az Azure AD-bérlő még nem rendelkezik felhasználóval, hozzon létre egyet az [Azure Active Directory használatával felhasználók hozzáadása vagy törlése](../active-directory/fundamentals/add-users-azure-active-directory.md)című rész lépéseit követve.

Keresse meg az Azure AD-felhasználó [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) objektumazonosítóját a felhasználó * \<egyszerű neve>* használatával. Az eredmény egy változóba kerül.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Az Azure AD-ben az összes egyszerű `az ad user list --query [].userPrincipalName`felhasználónév listájának megtekintéséhez futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa a futtassa
>

Adja hozzá ezt az Azure AD-felhasználót Active Directory-rendszergazdaként a Cloud Shell parancshasználatával. [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) A következő parancsban cserélje le `.database.windows.net` * \<a kiszolgálónév->* az SQL Database-kiszolgáló nevére (utótag nélkül).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Az Active Directory-rendszergazda hozzáadásáról az [Azure Active Directory-rendszergazda kiépítése az Azure SQL Database Server kiszolgálóhoz című](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) témakörben talál további információt.

## <a name="set-up-visual-studio"></a>A Visual Studio telepítése

### <a name="windows"></a>Windows
A Visual Studio for Windows integrálva van az Azure AD-hitelesítéssel. Ha engedélyezni szeretné a fejlesztést és a hibakeresést a Visual Studióban, adja hozzá az Azure AD-felhasználót a Visual Studióban a menü > **Fájlfiók-beállítások parancsával,** majd kattintson a **Fiók hozzáadása parancsra.** **File**

Ha az Azure AD-felhasználót az Azure szolgáltatáshitelesítéshez szeretné beállítani, válassza a menü **Eszközök** > **beállításai** parancsát, majd válassza az Azure Service Authentication Account Selection **(Azure Service Authentication** > **Account Selection) lehetőséget.** Válassza ki a hozzáadott Azure AD-felhasználót, és kattintson az **OK**gombra.

Most már készen áll az alkalmazás fejlesztésére és hibakeresésére az SQL-adatbázissal háttérként, az Azure AD-hitelesítés használatával.

### <a name="macos"></a>MacOS

A Visual Studio for Mac nincs integrálva az Azure AD-hitelesítéssel. Azonban a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) könyvtár, amely később használni fogja az Azure CLI-ből származó jogkivonatokat használhatja. A Visual Studio fejlesztésének és hibakeresésének engedélyezéséhez először telepítenie kell az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli) a helyi számítógépre.

Miután az Azure CLI telepítve van a helyi gépen, jelentkezzen be az Azure CLI-be a következő paranccsal az Azure AD-felhasználó használatával:

```bash
az login --allow-no-subscriptions
```
Most már készen áll az alkalmazás fejlesztésére és hibakeresésére az SQL-adatbázissal háttérként, az Azure AD-hitelesítés használatával.

## <a name="modify-your-project"></a>A projekt módosítása

A projekthez követendő lépések attól függnek, hogy ASP.NET projektről vagy ASP.NET Core projektről van-e szó.

- [ASP.NET módosítása](#modify-aspnet)
- [ASP.NET Core módosítása](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a NuGet csomagot [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

A *Web.config*fájlban a fájl tetejéről dolgozva hajtsa végre a következő módosításokat:

- A `<configSections>`alkalmazásban adja hozzá a következő szakaszdeklarációt:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- a záró `</configSections>` címke alatt adja hozzá `<SqlAuthenticationProviders>`a következő XML-kódot a hoz.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a `MyDbConnection` hívott `connectionString` kapcsolati `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`karakterláncot, és cserélje le annak értékét a rendszerre. Cserélje le _ \<a kiszolgálónév->_ és _ \<a db-név>_ a kiszolgáló nevére és az adatbázis nevére.

> [!NOTE]
> Az imént regisztrált SqlAuthenticationProvider a korábban telepített AppAuthentication könyvtár tetején alapul. Alapértelmezés szerint rendszerhez rendelt identitást használ. A felhasználó által hozzárendelt identitás kihasználásához meg kell adnia egy további konfigurációt. Olvassa el az AppAuthentication könyvtár [kapcsolati karakterlánc-támogatásának támogatását.](../key-vault/general/service-to-service-authentication.md#connection-string-support)

Ez minden, amire szüksége van az SQL Database-hez való csatlakozáshoz. Amikor a Visual Studio hibakeresés, a kód az Azure AD-felhasználó konfigurált [a Visual Studio beállítása.](#set-up-visual-studio) Az SQL Database-kiszolgálót később úgy kell beállítani, hogy engedélyezze a kapcsolatot az App Service-alkalmazás felügyelt identitásából.

Írja `Ctrl+F5` be az alkalmazás ismételt futtatását. Ugyanaz a CRUD-alkalmazás a böngészőben most csatlakozik az Azure SQL Database-hez közvetlenül az Azure AD-hitelesítés használatával. Ez a beállítás lehetővé teszi az adatbázis-áttelepítések futtatását a Visual Studio programból.

### <a name="modify-aspnet-core"></a>ASP.NET Core módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a NuGet csomagot [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

A [ASP.NET Core és az SQL Database oktatóanyag,](app-service-web-tutorial-dotnetcore-sqldb.md)a `MyDbConnection` kapcsolati karakterlánc egyáltalán nem használatos, mert a helyi fejlesztési környezet egy Sqlite adatbázisfájlt használ, és az Azure éles környezetben egy kapcsolati karakterláncot az App Service-ből. Az Active Directory-hitelesítés esetén mindkét környezet ugyanazt a kapcsolati karakterláncot használja. Az *appsettings.json ban*cserélje `MyDbConnection` le a kapcsolati karakterlánc értékét a következőre:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

A *Startup.cs*távolítsa el a korábban hozzáadott kódszakaszt:

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

És cserélje ki a következő kódot:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Ezután adja meg az Entitáskeret adatbázis-környezetét az SQL-adatbázis hozzáférési jogkivonatával. A *Data\MyDatabaseContext.cs*fájlban adja hozzá a következő kódot `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` az üres konstruktor kapcsos zárójeleibe:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ez a bemutató kód szinkron az egyértelműség és az egyszerűség érdekében.

Ez minden, amire szüksége van az SQL Database-hez való csatlakozáshoz. Amikor a Visual Studio hibakeresés, a kód az Azure AD-felhasználó konfigurált [a Visual Studio beállítása.](#set-up-visual-studio) Az SQL Database-kiszolgálót később úgy kell beállítani, hogy engedélyezze a kapcsolatot az App Service-alkalmazás felügyelt identitásából. Az `AzureServiceTokenProvider` osztály gyorsítótárazza a jogkivonatot a memóriában, és lekéri azt az Azure AD-ből közvetlenül a lejárat előtt. A jogkivonat frissítéséhez nincs szükség egyéni kódra.

> [!TIP]
> Ha az Azure AD-felhasználó konfigurált hozzáféréssel `GetAccessTokenAsync("https://database.windows.net/", tenantid)` rendelkezik több bérlőhöz, hívja meg a kívánt bérlőazonosítót a megfelelő hozzáférési jogkivonat lekéréséhez.

Írja `Ctrl+F5` be az alkalmazás ismételt futtatását. Ugyanaz a CRUD-alkalmazás a böngészőben most csatlakozik az Azure SQL Database-hez közvetlenül az Azure AD-hitelesítés használatával. Ez a beállítás lehetővé teszi az adatbázis-áttelepítések futtatását a Visual Studio programból.

## <a name="use-managed-identity-connectivity"></a>Felügyelt identitáskapcsolat használata

Ezután konfigurálja az App Service-alkalmazást, hogy a rendszer által hozzárendelt felügyelt identitással csatlakozzon az SQL-adatbázishoz.

> [!NOTE]
> Bár az ebben a szakaszban található utasítások a rendszer által hozzárendelt identitás, a felhasználó által hozzárendelt identitás ugyanolyan könnyen használható. Hogy ezt megtegyem. a kívánt felhasználó `az webapp identity assign command` által hozzárendelt identitás hozzárendeléséhez szükség van a módosításra. Ezután az SQL-felhasználó létrehozásakor ügyeljen arra, hogy a hely neve helyett a felhasználó által hozzárendelt identitás-erőforrás nevét használja.

### <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot a Cloud Shellben. A következő parancsban cserélje le * \<az alkalmazásnév>. *

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

### <a name="grant-permissions-to-managed-identity"></a>Engedélyek megadása felügyelt identitáshoz

> [!NOTE]
> Ha szeretné, hozzáadhatja az identitást egy [Azure AD-csoporthoz,](../active-directory/fundamentals/active-directory-manage-groups.md)majd sql-adatbázis-hozzáférést adhat az Azure AD-csoportnak az identitás helyett. A következő parancsok például hozzáadják az előző lépés felügyelt identitását egy új csoporthoz, a _myAzureSQLDBAccessGroup-hoz:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le _ \<a kiszolgálónévi>_ az SQL Database-kiszolgáló nevére, _ \<a db-név>_ az alkalmazás által használt adatbázisnévre, valamint _ \<az aad-felhasználónév->_ és _ \<aad-password>_ az Azure AD-felhasználó hitelesítő adataival.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

A kívánt adatbázis SQL-kérdésében futtassa a következő parancsokat az Azure AD-csoport hozzáadásához, és adja meg az alkalmazás igényeinek engedélyeket. Például: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

identitásnév->a felügyelt identitás neve az Azure AD-ben. * \<* Ha az identitás rendszer-hozzárendelt, a név mindig megegyezik az App Service-alkalmazás nevét. Az Azure AD-csoport engedélyeinek megadásához használja a csoport megjelenítendő nevét (például *myAzureSQLDBAccessGroup).*

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

> [!NOTE]
> A felügyelt identitások háttérszolgáltatásai is [fenntartegy jogkivonat-gyorsítótárat,](overview-managed-identity.md#obtain-tokens-for-azure-resources) amely csak akkor frissíti a célerőforrás jogkivonatát, ha lejár. Ha hibát követ el az SQL Database-engedélyek konfigurálása, és megpróbálja módosítani az engedélyeket, *miután* megpróbált egy jogkivonatot beszerezni az alkalmazással, valójában nem kap új jogkivonatot a frissített engedélyekkel, amíg a gyorsítótárazott jogkivonat le nem jár.

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy a *Web.config* vagy *az appsettings.json* fájlban végrehajtott módosítások a felügyelt identitással működnek, ezért az egyetlen teendő, ha eltávolítja a meglévő kapcsolati karakterláncot az App Service szolgáltatásból, amelyet a Visual Studio hozott létre az alkalmazás első üzembe helyezéséhez. Használja a következő parancsot, de cserélje le * \<az alkalmazásnevet>* az alkalmazás nevére.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

**Ha [oktatóanyagból származik: Hozzon létre egy ASP.NET alkalmazást az Azure-ban](app-service-web-tutorial-dotnet-sqldatabase.md)** az SQL Database segítségével, tegye közzé a módosításokat a Visual Studióban. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. 

**Ha [oktatóanyagból származik: Hozzon létre egy ASP.NET Core és SQL Database alkalmazást az Azure App Service-ben,](app-service-web-tutorial-dotnetcore-sqldb.md)** tegye közzé a módosításokat a Git használatával a következő parancsokkal:

```bash
git commit -am "configure managed identity"
git push azure master
```

Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Azure-alkalmazás az első áttelepítés kódja után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Az Entitáskeretrendszer konfigurálása az Azure AD-hitelesítés SQL-adatbázissal való használatára
> * Csatlakozás sql-adatbázishoz a Visual Studio-ból az Azure AD-hitelesítés használatával

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzárendelése az Azure App Service szolgáltatáshoz](app-service-web-tutorial-custom-domain.md)
