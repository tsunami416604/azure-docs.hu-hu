---
title: ActiveDirectoryInteractive csatlakozhat az SQL |} A Microsoft Docs
description: C#-kód például magyarázatok SqlAuthenticationMethod.ActiveDirectoryInteractive mód segítségével kezeli az Azure SQL Database csatlakoztatásához.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 3d6eb70b3ce9072dc2c51220af89549022b5dacf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238268"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Csatlakozás az Azure SQL Database ActiveDirectoryInteractive mód használatával

Ez a cikk ismerteti egy futtatható C# kód példa, amely csatlakozik a Microsoft Azure SQL Database. A C#-program használ hitelesítést, amely támogatja az Azure ad-ben a multi-factor authentication (MFA) az interaktív mód. Például a kapcsolódási kísérlet a mobiltelefonjára küldött ellenőrző kódot tartalmazhatnak.

Többtényezős hitelesítés támogatása az SQL-eszközökkel kapcsolatos további információkért lásd: [Azure Active Directory-támogatás az SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod. ActiveDirectoryInteractive Felsorolásérték

.NET-keretrendszer verziója 4.7.2, a enumerálás kezdődően [ **SqlAuthenticationMethod** ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) új értéke **. ActiveDirectoryInteractive**. Amikor egy ügyfél C#-program által használt, ez Felsorolásérték irányítja a rendszer az Azure SQL Database hitelesítése többtényezős hitelesítés támogatása az Azure ad-ben interaktív mód használatával. A felhasználó, majd futtatja a programot a következő párbeszédpanelek látja:

1. Egy párbeszédpanel, amely megjeleníti az Azure AD felhasználói nevét és, amely kéri a jelszót az Azure AD-felhasználó.
    - Ezen a párbeszédpanelen nem jelenik meg, ha nincs jelszava van szükség. Jelszó nélküli van szükség, ha a felhasználó tartománya össze van vonva az Azure ad-ben.

    Többtényezős hitelesítés az Azure AD-ben beállított szabályzat okozta a felhasználóra, ha ezután megjelenik a következő párbeszédpanelek.

2. Csak a felhasználói élmény a többtényezős hitelesítés a forgatókönyvben az első alkalommal a rendszer egy további párbeszédpanel megjelenítése. A párbeszédpanelen, amelyre SMS-üzeneteket küld mobiltelefonszám kér. Minden üzenetet biztosít a *ellenőrzőkódot* , amely a felhasználó a következő párbeszédpanelen kell kötniük.

3. Egy másik párbeszédpanelt, amely az MFA ellenőrző kód, amely a mobiltelefonjára küldött a rendszer kéri.

Az Azure AD MFA igénylése konfigurálásával kapcsolatos további információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

A párbeszédpanelek pillanatképeiért lásd: [többtényezős hitelesítés beállítása az SQL Server Management Studio és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> .NET-keretrendszer API-k különböző általános keresési oldalunkon érhető el a következő hivatkozásra kattintva a praktikus **.NET API böngésző** eszköz:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Název typu hozzá a választható fűzött **? kifejezés =** paramétert, a lapon lehet az eredménye, készen nekünk:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Felkészülés a C#, az Azure portal használatával

Feltételezzük, hogy már rendelkezik egy [létrehozott Azure SQL Database-kiszolgáló](sql-database-get-started-portal.md) és elérhető.


### <a name="a-create-an-app-registration"></a>A. Hozzon létre egy alkalmazásregisztráció

Az Azure AD-hitelesítés használatára, a C# ügyfélprogram, egy GUID Azonosítót kell megadnia egy *clientId* amikor a program megpróbál kapcsolódni. Az alkalmazásregisztráció befejezését állít elő, és megjeleníti a GUID Azonosítót az Azure Portalon a következő címkét: **Alkalmazásazonosító**. A navigációs lépések a következők:

1. Az Azure portal &gt; **Azure Active Directory** &gt; **alkalmazásregisztráció**

    ![Appok regisztrálása](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. A **Alkalmazásazonosító** érték jön létre és jelennek meg.

    ![Alkalmazás-azonosító megjelenítése](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Regisztrált alkalmazás** &gt; **beállítások** &gt; **szükséges engedélyek** &gt; **hozzáadása**

    ![Regisztrált alkalmazás engedélyek beállításai](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Szükséges engedélyek** &gt; **API-hozzáférés hozzáadása** &gt; **API kiválasztása** &gt; **Azure SQL Database**

    ![API-t az Azure SQL Database-hozzáférés hozzáadása](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **API-hozzáférés** &gt; **engedélyek kiválasztása** &gt; **delegált engedélyek**

    ![API-t az Azure SQL Database-engedélyek delegálása](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Az SQL Database-kiszolgálóhoz az Azure AD-rendszergazda beállítása

Minden Azure SQL Database-kiszolgáló a saját SQL logikai kiszolgáló, az Azure AD rendelkezik. C# esetünkben be kell az Azure AD-rendszergazda az Azure SQL Serverhez.

1. **Az SQL Server** &gt; **Active Directory-rendszergazda** &gt; **rendszergazda beállítása**

    - Az Azure SQL Database az Azure AD-rendszergazdák és felhasználók kapcsolatos további információkért lásd: a képernyőképek a [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), a szakaszban **Azure-beli üzembe helyezése Az Azure SQL Database-kiszolgáló Active Directory-rendszergazda**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Készítse elő az Azure AD-felhasználót egy adott adatbázishoz való csatlakozáshoz

Az Azure AD adott, az Azure SQL Database-kiszolgálóhoz adhat hozzá egy felhasználót, aki rendelkezik hozzáféréssel egy adott adatbázishoz.

További információkért lásd: [használata az Azure Active Directory-hitelesítés az SQL Database felügyelt példányába vagy az SQL Data Warehouse-hitelesítéshez](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Nem rendszergazdai felhasználók hozzáadása az Azure AD

Az Azure AD-rendszergazda az SQL Database-kiszolgáló az SQL Database-kiszolgálóhoz való csatlakozáshoz használható. Viszont egy általános eset, hogy a nem rendszergazda jogosultságú felhasználók hozzáadása az Azure ad-ben. A nem rendszergazda jogosultságú felhasználó csatlakozhat használata esetén a többtényezős hitelesítés során hív, ha a többtényezős hitelesítés a felhasználó a elő az Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Az Azure Active Directory Authentication Library (ADAL)

A névtér támaszkodik a C#-program **Microsoft.IdentityModel.Clients.ActiveDirectory**. Ez a névtér osztályokat a szerelvényben ugyanazzal a névvel.

- NuGet használatával töltse le és telepítse az ADAL szerelvényt.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Vegyen fel egy hivatkozást a szerelvény egy fordítási a C#-program támogatásához.




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod enum

C#-példában támaszkodik egy névtér van **System.Data.SqlClient**. Érdeklik az enum **SqlAuthenticationMethod**. Ez az enumerálás rendelkezik a következő értékeket:

- **SqlAuthenticationMethod.ActiveDirectory*interaktív x:&nbsp; használja ezt az egy Azure AD-felhasználó nevét, a multi-factor authentication, MFA eléréséhez.
    - Ez az érték a fókusz a jelen cikk. A felhasználó jelszavát, majd a többtényezős hitelesítés érvényesítés párbeszédpanelek megjelenítésével, ha a többtényezős hitelesítés a felhasználó a várakozásnak küld egy interaktív élmény.
    - Ezt az értéket a .NET-keretrendszer verziója 4.7.2 kezdődően elérhető.

- **SqlAuthenticationMethod.ActiveDirectory*integrált x:&nbsp; használata esetén ez egy *összevont* fiókot. Összevont fiókok a felhasználó nevét ismert, hogy a Windows-tartományhoz. Ez a módszer nem támogatja a többtényezős hitelesítés.

- **SqlAuthenticationMethod.ActiveDirectory*jelszó x:&nbsp; használja a hitelesítéshez, amely egy Azure AD-felhasználót és a felhasználó jelszót igényel. Az Azure SQL Database végrehajtja a hitelesítést. Ez a módszer nem támogatja a többtényezős hitelesítés.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Készítse elő a C# paraméterértékeket az Azure Portalról

A C#-program sikeres futtatás, a hozzá kell rendelnie a megfelelő értékeket a következő statikus mezők. Ezek a statikus mezők úgy működik, paraméterek a programba. A mezők itt látható pretend értékekkel. Is megjelenik az Azure Portalon a megfelelő értékekre szerezhet a helyek a következők:


| Statikus mező neve | Látszólag érték | Ha az Azure Portalon |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-kedvenc-sqldb-svr.database.windows.net" | **SQL Server-kiszolgálók** &gt; **Szűrés név alapján** |
| AzureAD_UserID | „user9@abc.onmicrosoft.com” | **Az Azure Active Directory** &gt; **felhasználói** &gt; **új vendégfelhasználó** |
| Initial_DatabaseName | "master" | **SQL Server-kiszolgálók** &gt; **SQL-adatbázisok** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Az Azure Active Directory** &gt; **alkalmazásregisztrációk**<br /> &nbsp; &nbsp; &gt; **Keresés név alapján** &gt; **alkalmazás azonosítója** |
| RedirectUri | új Uri ("https://bing.com/") | **Az Azure Active Directory** &gt; **alkalmazásregisztrációk**<br /> &nbsp; &nbsp; &gt; **Keresés név alapján** &gt; *[Your-alkalmazás-Regisz]* &gt;<br /> &nbsp; &nbsp; **Beállítások** &gt; **RedirectURIs**<br /><br />Ebben a cikkben minden olyan érvényes érték nem okoz gondot az RedirectUri. Az érték nem igazán használatos ebben az esetben. |
| &nbsp; | &nbsp; | &nbsp; |


Az adott forgatókönyvtől függően előfordulhat, hogy nem értékekre szüksége lesz az előző táblázatban szereplő összes paramétert.




## <a name="run-ssms-to-verify"></a>Futtassa az SSMS ellenőrzése

Hasznos lehet futtatni az SQL Server Management Studio (SSMS) a C#-program futtatása előtt. Az ssms-ben futtassa ellenőrzi, hogy helyesek-e a különböző konfigurációkban. Ezután a C#-program meghibásodása leszűkül, a forráskód is lehet.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Ellenőrizze az SQL Database-tűzfal IP-címek

Futtassa SSMS ugyanabban az épületben, ugyanazon a számítógépen, hogy a C#-program később futtatni fogja. Használhatja, amelyik **hitelesítési** üzemmód úgy gondolja, hogy az a legegyszerűbb. Ha bármely jelzi, hogy az adatbázis-kiszolgáló tűzfal nem fogadja el az IP-címe, oldható meg, amely, ahogyan [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Ellenőrizze a többtényezős hitelesítés (MFA) az Azure ad-hez

Futtassa újra az ssms-ben, ezúttal az **hitelesítési** beállítása **Active Directory - MFA-támogatással rendelkező univerzális**. Ez a beállítás SSMS 17.5 vagy újabb verzióját kell rendelkeznie.

További információkért lásd: [multi-factor Authentication hitelesítés beállítása az ssms-ben és az Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>C#-kód példa

Ebben a példában a C# fordítása, hozzá kell adnia egy hivatkozást a DLL-szerelvény nevű **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Segédanyagok

- **System.Data.SqlClient** névtér:
    - Keresés:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Közvetlen:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** névtér:
    - Keresés:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Közvetlen:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>C#-forráskódot, két részből áll

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

#### <a name="second-half-of-the-c-program"></a>A C#-program második felében

Jobban képi megjelenítés a C#-program két kódblokkok van felosztva. A program futtatásához, illessze be a két kódblokkok együtt.

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

&nbsp;


## <a name="next-steps"></a>További lépések

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

