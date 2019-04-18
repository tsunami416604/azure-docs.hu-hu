---
title: ActiveDirectoryInteractive csatlakozhat az SQL |} A Microsoft Docs
description: C#-kód például magyarázatok SqlAuthenticationMethod.ActiveDirectoryInteractive mód segítségével kezeli az Azure SQL Database csatlakoztatásához.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850171"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Csatlakozás az Azure SQL Database az Azure multi-factor Authentication

Ez a cikk ismerteti egy C# Azure SQL Database-adatbázishoz csatlakozó program. A program interaktív módú hitelesítést, amely támogatja a [Azure multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

A multi-factor Authentication szolgáltatás támogatása az SQL-eszközökkel kapcsolatos további információkért lásd: [Azure Active Directory-támogatás az SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Többtényezős hitelesítés az Azure SQL Database-hez

.NET-keretrendszer verziója 4.7.2, a enumerálás kezdődően [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) egy új értékkel rendelkezik: `ActiveDirectoryInteractive`. Az ügyfél C# program, a felsorolási érték esetén a rendszer az Azure Active Directory (Azure AD), amely támogatja a multi-factor Authentication szolgáltatás egy Azure SQL Database-adatbázishoz csatlakozni interaktív módot használja. Futtatja a programot a felhasználó számára jelenik meg a következő párbeszédpanelen:

* Egy párbeszédpanel, amely egy Azure AD felhasználói nevét jeleníti meg, és kéri a jelszót.

   Ha a felhasználó tartománya össze van vonva az Azure ad-vel, ezen a párbeszédpanelen nem jelenik meg, mert nincs jelszó van szükség.

   Ha az Azure AD-szabályzat ró a felhasználó a multi-factor Authentication szolgáltatás, a következő két párbeszédpanelek jelennek meg.

* Az első alkalommal a felhasználó végighalad a többtényezős hitelesítés, a rendszer egy párbeszédpanel, amely kéri a szöveges üzenetek küldéséhez mobiltelefonszám jeleníti meg. Minden üzenetet biztosít a *ellenőrzőkódot* , amely a felhasználónak meg kell adnia, a következő párbeszédpanelen.

* A multi-factor Authentication ellenőrző kódot, amelyet a rendszer elküldte a mobiltelefonra kérő párbeszédpanel.

Többtényezős hitelesítés kötelezővé tétele az Azure AD konfigurálásával kapcsolatos további információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

A párbeszédpanelek pillanatképeiért lásd: [többtényezős hitelesítés beállítása az SQL Server Management Studio és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> A .NET Framework API-k segítségével megkeresheti a [.NET API böngésző eszköz lap](https://docs.microsoft.com/dotnet/api/).
>
> Közvetlenül is kereshet a [választható? kifejezés =&lt;érték keresés&gt; paraméter](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurálja a C# alkalmazás az Azure Portalon

Mielőtt hozzákezd, rendelkeznie kell egy [Azure SQL Database-kiszolgáló](sql-database-get-started-portal.md) létrehozott és elérhető.

### <a name="register-your-app-and-set-permissions"></a>Az alkalmazás regisztrálásához és az engedélyek beállítása

Az Azure AD-hitelesítés használatára a C# program rendelkezik egy Azure AD-alkalmazást, regisztrálni. Ha alkalmazásokat szeretne regisztrálni, kell lennie vagy egy Azure AD-rendszergazda vagy egy felhasználó hozzárendelve az Azure AD *alkalmazásfejlesztő* szerepkör. További információ a szerepkörök hozzárendelése: [rendszergazda és a nem rendszergazdai szerepkörök hozzárendelése az Azure Active Directory felhasználók](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Az alkalmazásregisztráció befejezését állít elő, és megjeleníti az **Alkalmazásazonosító**. A program ezt az Azonosítót való csatlakozáshoz szükséges van.

Regisztráljon, és az alkalmazáshoz szükséges engedélyek beállítása:

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

    ![Appok regisztrálása](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Az alkalmazásregisztráció létrehozása után a **Alkalmazásazonosító** érték jön létre és jelennek meg.

    ![Alkalmazás-azonosító megjelenítése](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Válassza ki **regisztrált alkalmazás** > **beállítások** > **szükséges engedélyek** > **Hozzáadás**.

    ![Regisztrált alkalmazás engedélyek beállításai](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Válassza ki **szükséges engedélyek** > **Hozzáadás** > **API kiválasztása** > **Azure SQL Database**.

    ![API-t az Azure SQL Database-hozzáférés hozzáadása](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Válassza ki **API-hozzáférés** > **engedélyek kiválasztása** > **delegált engedélyek**.

    ![API-t az Azure SQL Database-engedélyek delegálása](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Állítsa be az Azure AD-rendszergazdát az SQL Database-kiszolgáló

Az a C# program futtatását, egy Azure SQL server-rendszergazda hozzá kell rendelnie az SQL Database-kiszolgálóhoz az Azure AD-rendszergazdát. 

Az a **SQL Server** lapon jelölje be **Active Directory-rendszergazda** > **rendszergazda beállítása**.

Az Azure SQL Database az Azure AD-rendszergazdák és felhasználók kapcsolatos további információkért tekintse meg a képernyőképeket [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Nem rendszergazdai felhasználók hozzáadása egy adott adatbázishoz (nem kötelező)

Egy SQL Database-kiszolgálóhoz az Azure AD-rendszergazdát futtathatja a C# példaprogram. Azure AD-felhasználót is futtassa a programot, ha vannak az adatbázisban. Az Azure AD SQL-rendszergazda vagy egy Azure AD-felhasználó, aki már létezik az adatbázisban, és rendelkezik a `ALTER ANY USER` engedéllyel is hozzáadhat egy felhasználót.

Az adatbázis az SQL-lel is hozzáadhat egy felhasználó [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) parancsot. Például: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

További információkért lásd: [használata az Azure Active Directory-hitelesítés az SQL Database felügyelt példányába vagy az SQL Data Warehouse-hitelesítéshez](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Új hitelesítés Felsorolásérték

A C# példa támaszkodik a [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) névtér. A multi-factor Authentication érdeklik a enumerálás van `SqlAuthenticationMethod`, amely rendelkezik a következő értékeket:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Használja ezt az értéket egy Azure AD felhasználói névvel multi-factor Authentication hitelesítés megvalósításához. Ez az érték a fókusz a jelen cikk. Interaktív élményt párbeszédpanel a felhasználói jelszót, majd a többtényezős hitelesítés érvényesítés megjelenítésével, ha a multi-factor Authentication ezt a felhasználót a várakozásnak hoz létre. Ezt az értéket a .NET-keretrendszer verziója 4.7.2 kezdődően elérhető.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Használja ezt az értéket a egy *összevont* fiókot. Összevont fiókok a felhasználó nevét ismert, hogy a Windows-tartományhoz. Ez a hitelesítési módszer nem támogatja a többtényezős hitelesítést.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Ezt az értéket használja a hitelesítéshez, amely egy Azure AD-felhasználónevet és jelszót igényel. Az Azure SQL Database szolgáltatás végzi a hitelesítést. Ez a módszer nem támogatja a többtényezős hitelesítést.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Állítsa be C# paraméterértékeket az Azure Portalról

Az a C# programot sikeresen futtatni kell, hogy a statikus mezők rendelje hozzá a megfelelő értékekre. Itt látható a mezők példaértékek láthatók. Az Azure-portál helyek hol lehet beszerezni a szükséges értékeket is látható vannak.

| Statikus mező neve | Példaérték | Ha az Azure Portalon |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL Server-kiszolgálók** > **Szűrés név alapján** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Az Azure Active Directory** > **felhasználói** > **új vendégfelhasználó** |
| Initial_DatabaseName | "myDatabase." | **SQL Server-kiszolgálók** > **SQL-adatbázisok** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Az Azure Active Directory** > **alkalmazásregisztrációk** > **Keresés név alapján** > **alkalmazás azonosítója** |
| RedirectUri | új Uri ("https://mywebserver.com/") | **Az Azure Active Directory** > **alkalmazásregisztrációk** > **Keresés név alapján** > *[Your App-regisztrációs]*  >  **Beállítások** > **RedirectURIs**<br /><br />Ebben a cikkben minden olyan érvényes érték nem okoz gondot az RedirectUri, mert itt nem használja. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Ellenőrizze az SQL Server Management Studióval

Mielőtt futtatja a C# program, akkor célszerű, hogy ellenőrizze, hogy a beállítás és konfiguráció megfelelő-e az SQL Server Management Studio (SSMS). Bármely C# program hiba majd is leszűkül forráskódjához.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Ellenőrizze az SQL Database-tűzfal IP-címek

Futtassa az ssms-t ugyanazon a számítógépen, ugyanabban az épületben, ahonnan futtatni szeretné a C# program. Ebben a tesztben bármely **hitelesítési** üzemmód az OK gombra. A végrehajtás, hogy az adatbázis-kiszolgáló tűzfal nem fogad az IP-címe esetén olvassa el [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md) segítséget.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Az Azure Active Directory multi-factor Authentication hitelesítés ellenőrzése

Futtassa újra az ssms-ben, ezúttal az **hitelesítési** beállítása **Active Directory - MFA-támogatással rendelkező univerzális**. Ez a beállítás SSMS 17.5 vagy újabb verziója szükséges.

További információkért lásd: [multi-factor Authentication konfigurálása az ssms-ben és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Ha az adatbázis egy vendég felhasználók is kell az adatbázist az Azure AD-tartomány nevet kell adnia: Válassza ki **beállítások** > **AD tartomány felhasználónév vagy a bérlői azonosító**. Az Azure Portalon keresse meg a tartomány nevét, válassza a **Azure Active Directory** > **egyéni tartománynevek**. Az a C# példaprogram biztosít egy tartomány név nem szükséges.

## <a name="c-code-example"></a>C#-kód példa

A példában C# program támaszkodik a [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL sestavení.

Ez a csomag telepítéséhez a Visual Studióban, válassza ki a **projekt** > **NuGet-csomagok kezelése**. Keresse meg és telepítse **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Ez a példa C# forráskódját.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Ez a példa a C# kimenet tesztelése.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
