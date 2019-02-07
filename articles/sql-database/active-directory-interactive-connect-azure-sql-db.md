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
ms.date: 01/25/2019
manager: craigg
ms.openlocfilehash: a7f2dbdb089df8035d18db25b3968d63a3c97c0f
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767499"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>Csatlakozás az Azure SQL Database és az Active Directory MFA

Ez a cikk ismerteti egy C# programot, amely csatlakozik a Microsoft Azure SQL Database. A program interaktív módú hitelesítést, amely támogatja a [Azure Active Directory (Azure AD) többtényezős hitelesítés (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Többtényezős hitelesítés támogatása az SQL-eszközökkel kapcsolatos további információkért lásd: [Azure Active Directory-támogatás az SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Többtényezős hitelesítés az Azure SQL Database

.NET-keretrendszer verziója 4.7.2, a enumerálás kezdődően [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) új értéke – `ActiveDirectoryInteractive`. Az ügyfél C# program, a felsorolási érték esetén a rendszer a többtényezős hitelesítés támogatása az Azure AD interaktív mód használatával csatlakozni egy Azure SQL Database. Futtatja a programot a felhasználó számára jelenik meg a következő párbeszédpanelen:

* Egy párbeszédpanel, amely egy Azure AD felhasználói nevét jeleníti meg, és kéri a jelszót.

   Ha a felhasználó tartománya össze van vonva az Azure ad-vel, majd a párbeszédpanel nem jelenik meg jelszavát nem szükséges.

   Ha az Azure AD-szabályzat ró a felhasználó MFA, majd a következő két párbeszédpanelek jelennek meg.

* Az első alkalommal a felhasználó végighalad a többtényezős hitelesítés, a rendszer megjeleníti a szöveges üzenetek küldéséhez mobiltelefonszám kérő párbeszédpanel. Minden üzenetet biztosít a *ellenőrzőkódot* , amely a felhasználónak meg kell adnia, a következő párbeszédpanelen.

* Egy MFA ellenőrző kódot, amelyet a rendszer elküldte a mobiltelefonra kérő párbeszédpanel.

Az Azure AD MFA igénylése konfigurálásával kapcsolatos további információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

A párbeszédpanelek pillanatképeiért lásd: [többtényezős hitelesítés beállítása az SQL Server Management Studio és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> A .NET Framework API-k segítségével megkeresheti az **.NET API böngésző** eszköz oldalon:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Is kereshet közvetlenül és az opcionális **? kifejezés =&lt;érték keresés&gt;**  paramétert:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurálja a C# alkalmazás az Azure Portalon

Mielőtt hozzákezd, rendelkeznie kell egy [Azure SQL Database-kiszolgáló](sql-database-get-started-portal.md) létrehozott és elérhető.

### <a name="register-your-app-and-set-permissions"></a>Az alkalmazás regisztrálásához és az engedélyek beállítása


Az Azure AD-hitelesítés használatára a C# program rendelkezik egy Azure AD-alkalmazást, regisztrálni. Ha alkalmazásokat szeretne regisztrálni, kell lennie vagy egy Azure AD-rendszergazda vagy egy felhasználó hozzárendelve az Azure AD *alkalmazásfejlesztő* szerepkör. További információ a szerepkörök hozzárendelése: [rendszergazda és a nem rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryval a felhasználók számára.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 Az alkalmazásregisztráció befejezését állít elő, és megjeleníti az **Alkalmazásazonosító**. A program ezt az Azonosítót való csatlakozáshoz szükséges van.


Regisztráljon, és az alkalmazáshoz szükséges engedélyek beállítása:

1. Az Azure portal > **Azure Active Directory** > **alkalmazásregisztrációk** > **új alkalmazás regisztrálása**

    ![Appok regisztrálása](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Miután létrejött az alkalmazás regisztrációját, a **Alkalmazásazonosító** érték jön létre és jelennek meg.

    ![Alkalmazás-azonosító megjelenítése](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Regisztrált alkalmazás** > **beállítások** > **szükséges engedélyek** > **hozzáadása**

    ![Regisztrált alkalmazás engedélyek beállításai](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Szükséges engedélyek** > **Hozzáadás** > **API kiválasztása** > **Azure SQL Database**

    ![API-t az Azure SQL Database-hozzáférés hozzáadása](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **API-hozzáférés** > **engedélyek kiválasztása** > **delegált engedélyek**

    ![API-t az Azure SQL Database-engedélyek delegálása](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Állítsa be az Azure AD-rendszergazdát az SQL Database-kiszolgáló


Az a C# program futtatását, egy Azure SQL server-rendszergazda hozzá kell rendelnie az Azure SQL Serverhez Azure AD-rendszergazdát. 

 * **Az SQL Server** > **Active Directory-rendszergazda** > **rendszergazda beállítása**

Az Azure SQL Database az Azure AD-rendszergazdák és felhasználók kapcsolatos további információkért lásd: a képernyőképek a [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), a szakaszban **Azure-beli üzembe helyezése Az Azure SQL Database-kiszolgáló Active Directory-rendszergazda**.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Nem rendszergazdai felhasználók hozzáadása egy adott adatbázishoz (nem kötelező)

Egy SQL Database-kiszolgálóhoz az Azure AD-rendszergazdát futtathatja a C# példaprogram. Azure AD-felhasználót is futtassa a programot, ha vannak az adatbázisban. Az Azure AD SQL-rendszergazda vagy egy Azure AD-felhasználó, aki már létezik az adatbázisban, és rendelkezik a `ALTER ANY USER` engedéllyel is hozzáadhat egy felhasználót.

Az adatbázis az SQL-lel is hozzáadhat egy felhasználó [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017) parancsot. Például: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

További információkért lásd: [használata az Azure Active Directory-hitelesítés az SQL Database felügyelt példányába vagy az SQL Data Warehouse-hitelesítéshez](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Új hitelesítés Felsorolásérték

A C# példa támaszkodik a [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) névtér. A multi-factor authentication érdeklik a enumerálás van `SqlAuthenticationMethod`, amely rendelkezik a következő értékeket:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Ez az érték használata egy Azure ad-ben felhasználónév MFA megvalósítása. Ez az érték a fókusz a jelen cikk. A felhasználó jelszavát, majd a többtényezős hitelesítés érvényesítés párbeszédpanelek megjelenítésével, ha a többtényezős hitelesítés a felhasználó a várakozásnak küld egy interaktív élmény. Ezt az értéket a .NET-keretrendszer verziója 4.7.2 kezdődően elérhető.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Használja ezt az értéket a egy *összevont* fiókot. Összevont fiókok a felhasználó nevét ismert, hogy a Windows-tartományhoz. Ez a hitelesítési módszer nem támogatja a többtényezős hitelesítés.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Ezt az értéket használja a hitelesítéshez, amely egy Azure AD-felhasználónevet és jelszót igényel. Az Azure SQL Database szolgáltatás végzi a hitelesítést. Ez a módszer nem támogatja a többtényezős hitelesítés.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Állítsa be C# paraméterértékeket az Azure Portalról

Az a C# programot sikeresen futtatni kell, hogy a statikus mezők rendelje hozzá a megfelelő értékekre. Itt látható a mezők példaértékek láthatók. A következők az Azure-portál helyek hol lehet beszerezni a szükséges értékeket is látható:

| Statikus mező neve | Példaérték | Ha az Azure Portalon |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL Server-kiszolgálók** > **Szűrés név alapján** |
| AzureAD_UserID | „auser@abc.onmicrosoft.com” | **Az Azure Active Directory** > **felhasználói** > **új vendégfelhasználó** |
| Initial_DatabaseName | "myDatabase." | **SQL Server-kiszolgálók** > **SQL-adatbázisok** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Az Azure Active Directory** > **alkalmazásregisztrációk** > **Keresés név alapján** > **alkalmazás azonosítója** |
| RedirectUri | új Uri ("https://mywebserver.com/") | **Az Azure Active Directory** > **alkalmazásregisztrációk** > **Keresés név alapján** > *[Your-alkalmazás-Regisz]*  >  **Beállítások** > **RedirectURIs**<br /><br />Ebben a cikkben minden olyan érvényes érték nem okoz gondot az RedirectUri, itt nem használ. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>Ellenőrizze az SQL Server Management Studio (SSMS)

Mielőtt futtatja a C# program, egy célszerű ellenőrizni, hogy a beállítás és konfiguráció helyesek az ssms-ben. Bármely C# program hiba majd is leszűkül forráskódjához.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Ellenőrizze az SQL Database-tűzfal IP-címek

Futtassa az ssms-t ugyanazon a számítógépen, ugyanabban az épületben, ahonnan futtatni szeretné a C# program. Ebben a tesztben bármely **hitelesítési** mód rendben. A végrehajtás, hogy az adatbázis-kiszolgáló tűzfal nem fogad az IP-címe esetén olvassa el [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md) segítséget.

### <a name="verify-azure-active-directory-mfa"></a>Az Azure Active Directory MFA ellenőrzése

Futtassa újra az ssms-ben, ezúttal az **hitelesítési** beállítása **Active Directory - MFA-támogatással rendelkező univerzális**. Ez a beállítás SSMS 17.5 vagy újabb verziója szükséges.

További információkért lásd: [multi-factor Authentication hitelesítés beállítása az ssms-ben és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Ha az adatbázis egy vendég felhasználók is szüksége a az Azure AD-tartomány nevét az adatbázis - **beállítások** > **AD tartomány felhasználónév vagy a bérlői azonosító**. Az Azure Portalon keresse meg a tartomány nevét, válassza a **Azure Active Directory** > **egyéni tartománynevek**. Az a C# példaprogram biztosít egy tartomány név nem szükséges.

## <a name="c-code-example"></a>C#-kód példa

A példában C# program támaszkodik a [ *Microsoft.IdentityModel.Clients.ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL sestavení.

Ez a csomag telepítéséhez a Visual Studióban, válassza ki a **projekt** > **NuGet-csomagok kezelése**. Keresse meg és telepítse **Microsoft.IdentityModel.Clients.ActiveDirectory**.

### <a name="c-source-code"></a>C#Forráskód

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
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
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
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>A C# tényleges tesztkimenet

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

- [Az SQL Server Azure AD-rendszergazda adatainak lekérése](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

