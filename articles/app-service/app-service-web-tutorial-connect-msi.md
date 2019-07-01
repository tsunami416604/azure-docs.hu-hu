---
title: Biztonságos kapcsolatot az SQL Database felügyelt identitás – az Azure App Service |} A Microsoft Docs
description: Tegye biztonságosabbá adatbázis-kapcsolatát felügyelt identitás segítségével, és alkalmazza ezt egyéb Azure-szolgáltatásoknál is.
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481019"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Az App Service-ben egy felügyelt identitás használata Azure SQL Database-kapcsolat biztonságossá tétele érdekében

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban felügyelt identitás fog hozzáadni a mintául szolgáló ASP.NET webalkalmazást, a beépített [oktatóanyag: Az Azure SQL Database és az ASP.NET-alkalmazás létrehozása](app-service-web-tutorial-dotnet-sqldatabase.md). Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg támogatott .NET-keretrendszer 4.7.2 vagy újabb. A [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) nem támogatja ezt a forgatókönyvet, de ez még nem szerepel az App Service alapértelmezett rendszerképeiben. 
>

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework használata az Azure AD-hitelesítés az SQL Database konfigurálása
> * Csatlakozás az SQL Database, a Visual Studióból az Azure AD-hitelesítés használatával

> [!NOTE]
>Az Azure AD-hitelesítés _különböző_ a [integrált Windows-hitelesítés](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) a helyszíni Active Directory (AD DS). Active Directory tartományi szolgáltatások és az Azure AD teljesen eltérő hitelesítési protokollt használják. További információkért lásd: [az Azure AD Domain Services – dokumentáció](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk továbbra is fennáll, ahol abbahagyta a [oktatóanyag: Az Azure SQL Database és az ASP.NET-alkalmazás létrehozása](app-service-web-tutorial-dotnet-sqldatabase.md). Ha még nem tette meg, kövesse az oktatóanyag utasításait. Alternatív megoldásként hozzáigazíthatja a lépéseket a saját ASP.NET-alkalmazása az SQL Database-zel való használatához.

Az SQL Database a háttéralkalmazás alkalmazás hibakeresése, ellenőrizze, hogy [engedélyezett ügyfélkapcsolat a számítógépről](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Támogatás az Azure AD felhasználói adatbázis eléréséhez

Hozzárendelése egy Azure AD-felhasználót az Active Directory-rendszergazda az SQL Database-kiszolgáló, először engedélyeznie az SQL Database az Azure AD-hitelesítés. Ez a felhasználó különbözik a Microsoft-fiók, amellyel iratkozzon fel az Azure-előfizetésében. Felhasználó létrehozása, importálása, szinkronizált vagy meghívjuk az Azure AD-be kell lennie. További információt az Azure AD-felhasználók engedélyezett talál [Azure AD-funkciók és az SQL Database korlátozások](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Keresse meg a használatával az Azure AD-felhasználó Objektumazonosítóját a [ `az ad user list` ](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) , és cserélje le  *\<felhasználói-principal-name >* . Az eredményt egy változóban menti.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Az Azure ad-ben az összes egyszerű felhasználónevek listájának megtekintéséhez futtassa `az ad user list --query [].userPrincipalName`.
>

Adja hozzá az Azure AD-felhasználót egy Active Directory felügyeleti a [ `az sql server ad-admin create` ](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) parancsot a Cloud Shellben. A következő parancsban cserélje le a  *\<-kiszolgálónév >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Egy Active Directory-rendszergazda hozzáadására vonatkozó további információkért lásd: [üzembe helyezése az Azure SQL Database-kiszolgálóhoz az Azure Active Directory-rendszergazda](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Állítsa be a Visual Studióban

Engedélyezéséhez fejlesztés és hibakeresés a Visual Studióban adjon hozzá az Azure AD-felhasználót a Visual Studióban válassza **fájl** > **fiókbeállításokat** a menüben, majd kattintson a **hozzáadása egy fiók**.

Az Azure AD-felhasználót az Azure service-hitelesítés beállításához válassza **eszközök** > **beállítások** a menüben, majd válassza ki **Azure szolgáltatás hitelesítési**  >  **Számla kiválasztása**. Válassza ki az Azure AD-felhasználót adott hozzá, és kattintson a **OK**.

Készen áll a fejlesztés és hibakeresés az alkalmazás és az SQL Database, a háttérben az Azure AD-hitelesítés használatával.

## <a name="modify-aspnet-project"></a>ASP.NET-projekt módosítása

A Visual Studióban nyissa meg a Package Manager Console, és adja hozzá a NuGet-csomag [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

A *Web.config*, a fájl elejéhez bárhonnan, és hajtsa végre a következő módosításokat:

- A `<configSections>`, adja hozzá a következő szakaszban nyilatkozat:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- alább a záró `</configSections>` címkével, adja hozzá a következő XML-kódja `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a kapcsolati karakterláncot nevű `MyDbConnection` , és cserélje le a `connectionString` értéket `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Cserélje le  _\<-kiszolgálónév >_ és  _\<db-name >_ a kiszolgáló nevét és az adatbázis nevét.

Típus `Ctrl+F5` újra futtatni az alkalmazást. Az azonos CRUD alkalmazása a böngészőben most kapcsolódik az Azure SQL Database közvetlenül, Azure AD-hitelesítés használatával. Ez a beállítás lehetővé teszi adatbázis-migrálási parancsait futtatását. Később, a módosítások az App Service, az azonos beállítások kezelése a felügyelt identitás az alkalmazás központi telepítésekor.

## <a name="use-managed-identity-connectivity"></a>Kapcsolat felügyelt identitás használata

Ezután állítsa be az App Service-alkalmazás kapcsolódni az SQL Database felügyelt rendszer által hozzárendelt identitással.

### <a name="enable-managed-identity-on-app"></a>Az alkalmazás felügyelt identitás engedélyezése

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot a Cloud Shellben. A következő parancsban cserélje le a  *\<alkalmazás-neve >* .

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

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Az Azure AD-csoportok felügyelt identitás hozzáadása

Az SQL Database az identitás hozzáférést biztosítani, adja hozzá a kell egy [Azure AD-csoport](../active-directory/fundamentals/active-directory-manage-groups.md). A Cloud shellben adja hozzá azt egy új nevű csoporthoz _myAzureSQLDBAccessGroup_, az alábbi szkriptben látható módon:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Ha minden egyes parancsnál meg szeretné tekinteni a JSON-kimenetet, hagyja el a `--query objectId --output tsv` paramétereket.

### <a name="grant-permissions-to-azure-ad-group"></a>Engedélyek megadása az Azure AD-csoporthoz

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le  _\<-kiszolgálónév >_ az SQL Database-kiszolgáló nevével  _\<db-name >_ az adatbázissal nevezze el az alkalmazást használja, és  _\< aad-user-name >_ és  _\<aad-password >_ az Azure AD-felhasználó hitelesítő adataival.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Az SQL-parancssorban a használni kívánt adatbázist futtassa a következő parancsok hozzáadása az Azure AD csoportból, és adja meg az engedélyeket az alkalmazás van szüksége. Például: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy azonos végrehajtott módosítások a `Web.config` a felügyelt identitás együttműködik, így egyedül ehhez távolítsa el a meglévő kapcsolati karakterlánc az alkalmazásba, mely Visual Studio által létrehozott először az alkalmazás üzembe helyezéséhez. Használja a következő parancsot, de a csere  *\<alkalmazás-neve >* az alkalmazás nevére.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Az Azure app Code First Migrálás után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework használata az Azure AD-hitelesítés az SQL Database konfigurálása
> * Csatlakozás az SQL Database, a Visual Studióból az Azure AD-hitelesítés használatával

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése az Azure App Service-ben](app-service-web-tutorial-custom-domain.md)
