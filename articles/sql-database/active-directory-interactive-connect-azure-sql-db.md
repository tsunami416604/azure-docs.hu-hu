---
title: Az ActiveDirectoryInteractive az SQL-hez csatlakozik
description: C# Kód példa, magyarázatokkal, az Azure SQL Database-hez az SqlAuthenticationMethod.ActiveDirectoryInteractive mód használatával való csatlakozáshoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692306"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Csatlakozás az Azure SQL-adatbázishoz az Azure többtényezős hitelesítésével

Ez a cikk egy C# programot tartalmaz, amely az Azure SQL Database-hez csatlakozik. A program interaktív módú hitelesítést használ, amely támogatja az [Azure többtényezős hitelesítést.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

Az SQL-eszközök többtényezős hitelesítéstámogatásáról az [Azure Active Directory active directory-támogatása az SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)alkalmazásban című témakörben talál további információt.

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Többtényezős hitelesítés az Azure SQL-adatbázishoz

A . [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) `ActiveDirectoryInteractive` Egy ügyfél C# program, az enum érték irányítja a rendszert, hogy az Azure Active Directory (Azure AD) interaktív mód, amely támogatja a többtényezős hitelesítés egy Azure SQL-adatbázishoz való csatlakozáshoz. A programot futtató felhasználó a következő párbeszédpaneleket látja:

* Egy párbeszédpanel, amely megjeleníti az Azure AD felhasználónevét, és kéri a felhasználó jelszavát.

   Ha a felhasználó tartománya össze van egyítve az Azure AD-vel, ez a párbeszédpanel nem jelenik meg, mert nincs szükség jelszóra.

   Ha az Azure AD-szabályzat többtényezős hitelesítést ír elő a felhasználó számára, a következő két párbeszédpanel jelenik meg.

* Amikor a felhasználó először megy keresztül a többtényezős hitelesítésen, a rendszer megjelenít egy párbeszédpanelt, amely egy mobiltelefonszámot kér, amelyhez szöveges üzeneteket küldhet. Minden üzenet megadja azt az *ellenőrző kódot,* amelyet a felhasználónak meg kell adnia a következő párbeszédpanelen.

* Egy párbeszédpanel, amely többtényezős hitelesítés-ellenőrző kódot kér, amelyet a rendszer mobiltelefonra küldött.

Az Azure AD többtényezős hitelesítés re konkretinójának konfigurálásáról az [Azure többtényezős hitelesítésfelhőben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)való használata című témakörben talál további információt.

A párbeszédpanelek képernyőképeiről az [SQL Server Management Studio és az Azure AD többtényezős hitelesítésének konfigurálása](sql-database-ssms-mfa-authentication-configure.md)című témakörben található.

> [!TIP]
> A .NET Framework API-k ban a [.NET API Browser eszközlapon](https://docs.microsoft.com/dotnet/api/)kereshet.
>
> Közvetlenül is kereshet az [opcionális&lt;?term=&gt; keresési érték paraméterrel.](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Konfigurálja a C# alkalmazást az Azure Portalon

Mielőtt elkezdené, létre kell hoznia és elérhetővé kell tennie egy [Azure SQL Database-kiszolgálót.](sql-database-get-started-portal.md)

### <a name="register-your-app-and-set-permissions"></a>Az alkalmazás regisztrálása és engedélyek beállítása

Az Azure AD-hitelesítés használatához a C# programnak regisztrálnia kell egy Azure AD-alkalmazásként. Egy alkalmazás regisztrálásához azure AD-rendszergazdának vagy az Azure AD *alkalmazásfejlesztői* szerepkörhöz rendelt felhasználónak kell lennie. A szerepkörök hozzárendeléséről további információt a [Rendszergazdai és nem rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryval rendelkező felhasználókhoz című témakörben talál.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

Az alkalmazásregisztráció befejezése létrehoz egy **alkalmazásazonosítót,** és megjeleníti azt. A csatlakozáshoz a programnak tartalmaznia kell ezt az azonosítót.

Az alkalmazáshoz szükséges engedélyek regisztrálása és beállítása:

1. Az Azure Portalon válassza az **Azure Active Directory** > **alkalmazás regisztrációk** > **Új regisztráció.**

    ![Alkalmazásregisztráció](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Az alkalmazás regisztrációjának létrehozása után létrejön és megjelenik az **alkalmazásazonosító** értéke.

    ![Alkalmazásazonosító jelenik meg](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Válassza az API-engedélyek** > **kiválasztása Engedély hozzáadása**.

    ![A regisztrált alkalmazás engedélybeállításai](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Válassza ki **a szervezet által használt API-kat,** > írja be az Azure SQL Database **parancsot** a keresési >, és válassza az **Azure SQL Database lehetőséget.**

    ![Hozzáférés hozzáadása az Azure SQL Database API-jához](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Válassza **a Delegált engedélyek** > **user_impersonation** > **engedélyek hozzáadása lehetőséget.**

    ![Engedélyek delegálása az Azure SQL Database API-jához](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Azure AD-rendszergazda beállítása az SQL Database-kiszolgálóhoz

A C# program futtatásához egy Azure SQL-kiszolgáló rendszergazdájának hozzá kell rendelnie egy Azure AD-rendszergazdát az SQL Database-kiszolgálóhoz. 

Az **SQL Server** lapon válassza az **Active Directory felügyeleti** > **csoport beállítása rendszergazda**lehetőséget.

Az Azure AD-rendszergazdákról és az Azure SQL Database felhasználóiról az [Azure Active Directory-hitelesítés konfigurálása és kezelése sql-adatbázissal](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)című témakörben található képernyőképek ben talál további információt.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Nem rendszergazdai felhasználó hozzáadása egy adott adatbázishoz (nem kötelező)

Egy SQL Database-kiszolgáló Azure AD-rendszergazdája futtathatja a C# példaprogram. Egy Azure AD-felhasználó futtathatja a programot, ha az adatbázisban vannak. Egy Azure AD SQL-rendszergazda vagy egy Azure AD-felhasználó, aki már az adatbázisban, és az `ALTER ANY USER` adatbázis engedélye is hozzáadhat egy felhasználót.

Az SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) paranccsal felvehet egy felhasználót az adatbázisba. Például: `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

További információt az [Azure Active Directory-hitelesítés használata SQL-adatbázissal, felügyelt példányokkal vagy SQL Data Warehouse-ral történő hitelesítéshez című témakörben talál.](sql-database-aad-authentication.md)

## <a name="new-authentication-enum-value"></a>Új hitelesítési felsorítási érték

A C# példa a [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) névtérre támaszkodik. A többtényezős hitelesítés különösen fontos `SqlAuthenticationMethod`a felsorítás , amely a következő értékekkel rendelkezik:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Használja ezt az értéket egy Azure AD-felhasználónév vel a többtényezős hitelesítés megvalósításához. Ez az érték áll a jelen cikk középpontjában. Interaktív élményt nyújt a felhasználói jelszó párbeszédpaneleinek megjelenítésével, majd a többtényezős hitelesítés érvényesítéséhez, ha a felhasználóra többtényezős hitelesítést ír elő. Ez az érték a .

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Ezt az értéket használja *összevont* számlához. Összevont fiók esetén a felhasználónevet a Windows-tartomány ismeri. Ez a hitelesítési módszer nem támogatja a többtényezős hitelesítést.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Használja ezt az értéket a hitelesítéshez, amely egy Azure AD-felhasználónevet és -jelszót igényel. Az Azure SQL Database végzi el a hitelesítést. Ez a módszer nem támogatja a többtényezős hitelesítést.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>C# paraméterértékek beállítása az Azure Portalról

A C# program sikeres futtatásához megfelelő értékeket kell hozzárendelnie a statikus mezőkhöz. Az itt látható mezők példaértékekkel. Is látható az Azure Portalon helyeken, ahol beszerezheti a szükséges értékeket.

| Statikus mezőneve | Példaérték | Hol az Azure Portalon |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-kiszolgálók** > **Név szerint szűrve** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **user** > **Új vendégfelhasználó** |
| Initial_DatabaseName | "myDatabase" | **SQL-kiszolgálók** > **SQL-adatbázisai** |
| Ügyfélalkalmazásazonosító | "a94f9c62-97fe-4d19-b06d-111111111111111111" | **Az Azure Active Directory** > **alkalmazás regisztrációi** > Név szerint**alkalmazásazonosító** **alapján** > keresve |
| ÁtirányításUri | új Uri("https://mywebserver.com/") | **Az Azure Active Directory** > **alkalmazás regisztrációi** > **Név szerint** > *keresve [Az alkalmazás-regisztráció]* > **Beállítások** > **átirányítási URI-i**<br /><br />Ebben a cikkben minden érvényes érték rendben van a RedirectUri számára, mert itt nem használatos. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Ellenőrzés az SQL Server Management Studio-val

A C# program futtatása előtt érdemes ellenőrizni, hogy a beállítások és a konfigurációk helyesek-e az SQL Server Management Studio (SSMS) rendszerben. A C# programhibák ezután leszűkíthetők forráskódra.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Az SQL Database tűzfal IP-címeinek ellenőrzése

Futtassa az SSMS-t ugyanaról a számítógépről, ugyanabban az épületben, ahol a C# programot szeretné futtatni. Ehhez a teszthez minden **hitelesítési** mód rendben van. Ha van bármilyen jele annak, hogy az adatbázis-kiszolgáló tűzfala nem fogadja el az IP-címet, olvassa el az [Azure SQL Database kiszolgálószintű és adatbázis-szintű tűzfalszabályok](sql-database-firewall-configure.md) at.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Az Azure Active Directory többtényezős hitelesítésének ellenőrzése

Futtassa újra az SSMS-t, ezúttal az Active Directory - Universal **beállítással,** **az MFA támogatással.** Ehhez a beállításhoz a 17.5-ös vagy újabb SSMS-verzió szükséges.

További információ: [Többtényezős hitelesítés konfigurálása az SSMS-hez és az Azure AD-hez.](sql-database-ssms-mfa-authentication-configure.md)

> [!NOTE]
> Ha Ön vendégfelhasználó az adatbázisban, meg kell adnia az adatbázis Azure AD-tartománynevét is: Válassza a **Beállítások** > **AD tartománynevet vagy a bérlőazonosítót.** Ha meg szeretné találni a tartománynevet az Azure Portalon, válassza az **Azure Active Directory** > **egyéni tartományneveit.** A C# példa programban nincs szükség tartománynév megadására.

## <a name="c-code-example"></a>C# kód példa

A C# példa program a [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL-szerelvényen alapul.

A csomag telepítéséhez a Visual Studióban válassza a Project Manage NuGet Packages **(Projektkezelés** > **NuGet csomagok) lehetőséget.** A **Microsoft.IdentityModel.Clients.ActiveDirectory**keresése és telepítése.

Ez egy példa a C# forráskódra.

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

Ez egy példa a C# tesztkimenetre.

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
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
