---
title: SQL csatlakozik ActiveDirectoryInteractive |} Microsoft Docs
description: C#-kódban, például a, a csatlakozás az Azure SQL Database SqlAuthenticationMethod.ActiveDirectoryInteractive módban magyarázatokat.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 6489fb5630e1990c942b461859650e2e469cda73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Azure SQL adatbázishoz való kapcsolódáshoz ActiveDirectoryInteractive mód használata

Ez a cikk futtatható C# kódrészlet például a Microsoft Azure SQL Database adatbázishoz kapcsolódó tartalmazza. A C# program hitelesítést, amely támogatja az Azure AD többtényezős hitelesítés (MFA) interaktív mód. Például a kapcsolódási kísérlet a mobiltelefonjára küldött ellenőrző kódot tartalmazhatnak.

SQL-eszközök MFA-támogatással kapcsolatos további információkért lásd: [Azure Active Directory-támogatás az SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. ActiveDirectoryInteractive felsorolási érték

.NET-keretrendszer verziója 4.7.2, a számbavételi kezdve [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) új értéke **. ActiveDirectoryInteractive**. Amikor egy ügyfél C# program használja, a számbavételi érték arra utasítja a hitelesítéséhez az Azure SQL Database a többtényezős hitelesítés támogatása az Azure AD interaktív módban használja a rendszer. A felhasználó, majd futtatja a programot a következő párbeszédpanelek láthatja:

1. Egy párbeszédpanelt, amely egy Azure AD-felhasználó nevét jeleníti meg, és, amely kéri a jelszót az Azure AD-felhasználó.
    - Ezen a párbeszédpanelen nem jelenik meg, nem kell jelszót van szüksége. Nem kell jelszót van szükség, ha a felhasználó tartománya és az Azure AD össze van vonva.

    Ha a többtényezős hitelesítés az Azure AD-ben beállított házirend elő a felhasználó, a következő párbeszédpanelek jelennek meg mellett.

2. Csak az első alkalommal a felhasználói élmény a többtényezős Hitelesítést a forgatókönyvben a rendszer egy további párbeszédpanel megjelenítése. A párbeszédpanel a szöveges üzeneteket érkezni mobiltelefonszám kér. Minden üzenetet biztosít a *ellenőrzőkódot* , hogy a felhasználónak meg kell adnia a következő párbeszédpanel.

3. Egy másik párbeszédpanelt, amely bekéri azt a rendszer elküldte a mobiltelefon MFA ellenőrzőkódot.

Az Azure AD megkövetelő konfigurálásával kapcsolatos további információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Tekintse meg példaként bemutató képernyőképeket láthat a párbeszédpanelek, [többtényezős hitelesítés beállítása az SQL Server Management Studio és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Az általános keresési oldalra mindenfajta .NET-keretrendszer API-k érhető el a következő hivatkozásra kattintva a hasznos **.NET API böngésző** eszköz:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> A típusnév hozzá a választható fűzött **? kifejezés =** paraméter, a lapon lehet az eredménye, készen áll, és várakozik az USA:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Felkészülés a C#, az Azure portál használatával

Feltételezzük, hogy már rendelkezik egy [létrehozott Azure SQL adatbázis-kiszolgáló](sql-database-get-started-portal.md) és érhető el.


### <a name="a-create-an-app-registration"></a>A. Hozzon létre egy alkalmazás regisztrálása

Az Azure AD-alapú hitelesítés használatához a C# ügyfélprogram adjon meg egy GUID Azonosítót, egy *clientId* amikor a program megpróbál kapcsolódni. Egy alkalmazás regisztráció befejezése hoz létre, és az Azure-portálon jelenik meg a GUID címkézve **Alkalmazásazonosító**. A navigációs lépései a következők:

1. Azure-portálon &gt; **Azure Active Directory** &gt; **alkalmazás regisztrálása**

    ![Appok regisztrálása](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. A **Alkalmazásazonosító** érték jön létre, ezért jelenik meg.

    ![Alkalmazásazonosító jelenik meg](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Regisztrált alkalmazás** &gt; **beállítások** &gt; **szükséges engedélyek** &gt; **hozzáadása**

    ![Engedélybeállítások regisztrált alkalmazás](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Szükséges engedélyek** &gt; **hozzáadása API-hozzáférés** &gt; **API kiválasztása** &gt; **Azure SQL adatbázis**

    ![API-t az Azure SQL Database hozzáférés hozzáadása](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **API-hozzáférés** &gt; **engedélyként válassza** &gt; **delegált engedélyekkel**

    ![Az Azure SQL Database API engedélyének delegálása](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Az Azure AD-rendszergazda beállítása a az SQL Database-kiszolgálóhoz

Minden Azure SQL Database-kiszolgáló a saját logikai SQL-kiszolgáló, az Azure AD el. C# esetünkben meg kell adni az Azure AD-rendszergazda az Azure SQL-kiszolgáló.

1. **SQL Server** &gt; **Active Directory-rendszergazda** &gt; **-rendszergazda beállítása**

    - Az Azure SQL Database az Azure AD-rendszergazdák és felhasználók kapcsolatos további információkért tekintse meg a képernyőképek [konfigurálása és kezelése az Azure Active Directory hitelesítési az SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), szakaszban található **kiépíteni az Azure Az Azure SQL Database-kiszolgálóhoz az Active Directory-rendszergazda**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Készítse elő az adott adatbázishoz való kapcsolódáshoz az Azure AD-felhasználó

Az Azure AD az Azure SQL Database-kiszolgálóhoz jellemző adhat hozzá egy felhasználót, aki egy adott adatbázis hozzáférést kell biztosítani.

További információkért lásd: [Azure Active Directory hitelesítés használata a hitelesítés és az SQL-adatbázis, a példány kezelt vagy az SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. A nem rendszergazda felhasználók hozzáadása az Azure AD

Az Azure AD admin SQL adatbázis-kiszolgáló az SQL Database-kiszolgálóhoz való kapcsolódáshoz használható. Általános esetben azonban a nem rendszergazda felhasználók hozzáadása az Azure AD. A nem rendszergazdaként való kapcsolódásra szolgál, ha a többtényezős hitelesítés során meghívni, ha a többtényezős hitelesítés a felhasználó a elő az Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Az Azure Active Directory Authentication Library (ADAL)

A C# programban támaszkodik a névtér **Microsoft.IdentityModel.Clients.ActiveDirectory**. Az ehhez a névtérhez vannak a szerelvényben ugyanazzal a névvel.

- NuGet segítségével töltse le és telepítse a ADAL szerelvényt.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Adjon hozzá egy, a C# programban egy fordítási támogatásához a szerelvényre mutató hivatkozást.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod enum

Egy névtér, amely a C# példa támaszkodik van **System.Data.SqlClient**. Érdeklik van a felsorolás **SqlAuthenticationMethod**. Az enum rendelkezik a következő értékeket:

- **SqlAuthenticationMethod.ActiveDirectory*interaktív x:&nbsp; ezzel az Azure AD-felhasználó nevét, a multi-factor authentication, MFA eléréséhez.
    - Ez az érték a fókusz a jelen cikk. Az interaktivitási élményének MFA ki a felhasználót, ha a felhasználó jelszavát, majd az MFA érvényesítéshez párbeszédpanelek megjelenítésével hoz létre.
    - Ez az érték a .NET-keretrendszer verziója 4.7.2 kezdődően elérhető.

- **SqlAuthenticationMethod.ActiveDirectory*integrált x:&nbsp; ezen a egy *összevont* fiók. Összevont fiók esetén a felhasználónév ismert, hogy a Windows-tartományhoz. Ez a metódus nem támogatja a többtényezős Hitelesítést.

- **SqlAuthenticationMethod.ActiveDirectory*jelszó x:&nbsp; használja ezt a hitelesítéshez, amely egy Azure AD-felhasználó és a jelszót igényel. Az Azure SQL Database végrehajtja a hitelesítést. Ez a metódus nem támogatja a többtényezős Hitelesítést.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Készítse elő a C# paraméterértékeket az Azure-portálon

A sikeres futtatáshoz a C# programban a következő statikus mezők hozzá kell rendelnie a megfelelő értékeket. A statikus mezők működni, mint paraméterek azt a programba. A mezők itt látható pretend értékekkel. A helyek a megfelelő értékek szerezhet az Azure-portálon is látható vannak:


| Statikus mező neve | Látszólag érték | Ha Azure-portálon |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-kedvenc-sqldb-svr.database.windows.net" | **SQL Server-kiszolgálók** &gt; **Szűrés név alapján** |
| AzureAD_UserID | „user9@abc.onmicrosoft.com” | **Az Azure Active Directory** &gt; **felhasználói** &gt; **új Vendég felhasználó** |
| Initial_DatabaseName | "fő" | **SQL Server-kiszolgálók** &gt; **SQL-adatbázisok** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Az Azure Active Directory** &gt; **App regisztrációk**<br /> &nbsp; &nbsp; &gt; **Keresés név** &gt; **Alkalmazásazonosító** |
| redirectUri | új Uri ("https://bing.com/") | **Az Azure Active Directory** &gt; **App regisztrációk**<br /> &nbsp; &nbsp; &gt; **Keresés név** &gt; *[saját-alkalmazás-regis]* &gt;<br /> &nbsp; &nbsp; **Beállítások** &gt; **RedirectURIs**<br /><br />Ez a cikk az egyetlen érvényes érték a RedirectUri rendben. Az érték nem valóban használt esetünkben. |
| &nbsp; | &nbsp; | &nbsp; |


Attól függően, hogy az adott esetben nem szükség lehet az előző táblázatban a paraméterek értékeit.




## <a name="run-ssms-to-verify"></a>Futtassa az SSMS ellenőrzése

Akkor érdemes futtatni az SQL Server Management Studio (SSMS) a C# program futtatása előtt. Futtassa az SSMS ellenőrzi, hogy helyesek-e a különböző konfigurációkban. Majd a C# programban meghibásodása lehet narrows számára a forráskódot.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Ellenőrizze az SQL-adatbázis tűzfal IP-címek

Futtassa SSMS ugyanabban az épületben, ugyanazon a számítógépen, hogy később futtatja a C# programban. Használhat, amelyik **hitelesítési** úgy érzi, hogy a legegyszerűbb a. Ha bármely arra utal, hogy az adatbázis-kiszolgáló tűzfal nem fogad el az IP-címe, ezt úgy javíthatja ki, ahogy az [Azure SQL Database kiszolgáló- és adatbázis tűzfalszabályok](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Többtényezős hitelesítés (MFA) az Azure AD ellenőrzése

Futtassa újra a szolgáltatáshoz az SSMS, hogy ezúttal az **hitelesítési** beállítása **Active Directory - MFA-támogatással rendelkező univerzális**. Ez a beállítás rendelkeznie kell SSMS 17.5 vagy újabb verziója.

További információkért lásd: [többtényezős hitelesítés beállítása az SSMS és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>C#-példakód

Ebben a példában a C# fordítása, hozzá kell adni a nevű DLL-összeállításra mutató hivatkozást **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Segédanyagok

- **System.Data.SqlClient** névtér:
    - Keresés:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Közvetlen:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** névtér:
    - Keresés:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Közvetlen:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>C# forráskódját, két lépésben

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>A C# programban második felében

Jobb visual jeleníthető meg a C# programban felosztása két kódblokkok. A program futtatásához illessze be a két kódblokkok együtt.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

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

#### <a name="actual-test-output-from-c"></a>A C# tényleges teszt eredménye

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>További lépések

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

