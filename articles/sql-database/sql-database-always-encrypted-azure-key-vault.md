---
title: 'Always Encrypted: SQL Database – Azure Key Vault | Microsoft Docs'
description: Ez a cikk bemutatja, hogyan védheti meg a bizalmas adatokat egy SQL-adatbázisban az adattitkosítással a SQL Server Management Studio Always Encrypted varázslójával.
keywords: adattitkosítás, titkosítási kulcs, Felhőbeli titkosítás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 924ec20b9922d12da7291dc4f44b7413c68728c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569583"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Always Encrypted: A bizalmas adatok védelme és a titkosítási kulcsok tárolása Azure Key Vault

Ez a cikk bemutatja, hogyan védheti meg a bizalmas adatokat egy SQL-adatbázisban adattitkosítással az [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) [Always encrypted varázslójának](https://msdn.microsoft.com/library/mt459280.aspx) használatával. Emellett útmutatást is tartalmaz, amely bemutatja, hogyan tárolhatja az egyes titkosítási kulcsokat a Azure Key Vaultban.

A Always Encrypted egy új adattitkosítási technológia Azure SQL Database és SQL Server, amely segít a kiszolgálón tárolt bizalmas adatok védelme során, az ügyfél és a kiszolgáló közötti mozgás során, és az adatok használatban vannak. Always Encrypted biztosítja, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szövegként az adatbázis-rendszeren belül. Az adattitkosítás konfigurálása után csak a kulcsokhoz hozzáférő ügyfélalkalmazások vagy kiszolgálóalkalmazások férhetnek hozzá az egyszerű szöveges információhoz. Részletes információ: [Always encrypted (adatbázismotor)](https://msdn.microsoft.com/library/mt163865.aspx).

Miután a Always Encrypted használatára konfigurálta az adatbázist, a Visual Studióban létre fog hozni C# egy ügyfélalkalmazás-alkalmazást a titkosított adattal való együttműködéshez.

Kövesse a cikkben ismertetett lépéseket, és Ismerje meg, hogyan állíthat be Always Encrypted Azure SQL Database-adatbázishoz. Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő feladatokat:

* [Always encrypted kulcsok](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)létrehozásához használja a SSMS Always encrypted varázslóját.
  * Hozzon létre egy [oszlop főkulcsát (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Hozzon létre egy [oszlop titkosítási kulcsát (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Adatbázis-tábla létrehozása és oszlopok titkosítása.
* Hozzon létre egy alkalmazást, amely beszúrja, kiválasztja és megjeleníti a titkosított oszlopok adatait.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Ebben az oktatóanyagban a következőkre lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nem rendelkezik ilyennel, regisztráljon az [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 vagy újabb verzió.
* A [.NET-keretrendszer 4,6](https://msdn.microsoft.com/library/w0x726c2.aspx) -es vagy újabb verziója (az ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview).

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>A SQL Database szolgáltatás elérésének engedélyezése az ügyfélalkalmazás számára
Engedélyeznie kell az ügyfélalkalmazás számára a SQL Database szolgáltatás elérését egy Azure Active Directory (HRE) alkalmazás beállításával és az alkalmazás hitelesítéséhez szükséges *alkalmazás-azonosító* és *kulcs* másolásával.

Az *alkalmazás azonosítójának* és *kulcsának*beszerzéséhez kövesse az [erőforrásokhoz hozzáférő Azure Active Directory alkalmazás és egyszerű szolgáltatás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md)című témakör lépéseit.

## <a name="create-a-key-vault-to-store-your-keys"></a>Key Vault létrehozása a kulcsok tárolásához
Most, hogy az ügyfélalkalmazás konfigurálva van, és rendelkezik az alkalmazás-AZONOSÍTÓval, itt az ideje, hogy létrehoz egy kulcstartót, és konfigurálja annak hozzáférési házirendjét, hogy az alkalmazás hozzáférhessen a tár titkos kulcsaihoz (a Always Encrypted kulcsokhoz). Az új oszlop főkulcsának létrehozásához, valamint a SQL Server Management Studiosal történő titkosítás beállításához a *Létrehozás*, a lekérés, a *lista*, a *aláírás*, az *ellenőrzés*, a *wrapKey*és a *unwrapKey* engedélyek szükségesek.

A következő szkript futtatásával gyorsan létrehozhat egy kulcstartót. A parancsmagok részletes ismertetését és a Key Vault létrehozásával és konfigurálásával kapcsolatos további információkat a [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)című témakörben talál.

```powershell
    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    # Use the same resource group name when creating your SQL Database below
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzAccount
    $subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
    Set-AzContext -SubscriptionId $subscriptionId

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```



## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az **erőforrás** > -**adatbázisok** > létrehozása**SQL Database**.
3. Hozzon létre egy **Klinika** nevű **üres** adatbázist egy új vagy meglévő kiszolgálón. Az adatbázisok Azure Portalban való létrehozásával kapcsolatos részletes utasításokért tekintse [meg az első Azure SQL Database-adatbázist](sql-database-single-database-get-started.md).
   
    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Az oktatóanyag későbbi részében szüksége lesz a kapcsolódási karakterláncra, ezért az adatbázis létrehozása után keresse meg az új klinika-adatbázist, és másolja a kapcsolódási karakterláncot. A kapcsolódási karakterláncot bármikor lekérheti, de egyszerűen átmásolhatja a Azure Portalba.

1. Ugrás az **SQL Databases** > **klinikára** > **adatbázis-kapcsolati karakterláncok megjelenítése**.
2. Másolja a **ADO.net**tartozó kapcsolatok karakterláncát.
   
    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével
Nyissa meg a SSMS, és kapcsolódjon a kiszolgálóhoz a klinika adatbázisával.

1. Nyissa meg az SSMS-t. (Nyissameg a Kapcsolódás a kiszolgálóhoz ablakot a **Kapcsolódás a kiszolgálóhoz** ablak megnyitásához.  > 
2. Adja meg a kiszolgáló nevét és a hitelesítő adatait. A kiszolgáló neve megtalálható az SQL Database panelen és a korábban átmásolt kapcsolatok karakterláncban. Írja be a kiszolgáló teljes nevét, beleértve a *Database.Windows.net*is.
   
    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Ha megnyílik az **Új tűzfalszabály** ablak, jelentkezzen be az Azure-ba, és hagyja, hogy a SSMS hozzon létre egy új tűzfalszabály-szabályt.

## <a name="create-a-table"></a>Tábla létrehozása
Ebben a szakaszban létre fog hozni egy táblázatot a páciensek számára. Eredetileg nem titkosított – a következő szakaszban fogja konfigurálni a titkosítást.

1. Bontsa ki az **adatbázisok**csomópontot.
2. Kattintson a jobb gombbal a **Klinika** -adatbázisra, majd kattintson az **Új lekérdezés**elemre.
3. Illessze be a következő Transact-SQL (T-SQL) T az új lekérdezési ablakba, és **hajtsa végre** .

```sql
        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Oszlopok titkosítása (Always Encrypted konfigurálása)
A SSMS olyan varázslót biztosít, amely megkönnyíti a Always Encrypted konfigurálását az oszlop főkulcsának, az oszlop titkosítási kulcsának és a titkosított oszlopok beállításával.

1. Bontsa ki az **adatbázisok** > **Klinika** > **táblái**elemet.
2. Kattintson a jobb gombbal a páciensek táblára, és válassza az **oszlopok titkosítása** lehetőséget a Always encrypted varázsló megnyitásához:
   
    ![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

A Always Encrypted varázsló a következő fejezeteket tartalmazza: **Oszlop kijelölése**, **főkulcs konfigurálása**, **Érvényesítés**és **Összefoglalás**.

### <a name="column-selection"></a>Oszlop kijelölése
A **Bevezetés** lapon a **tovább** gombra kattintva nyissa meg az **Oszlop kijelölése** lapot. Ezen az oldalon kiválaszthatja, hogy mely oszlopokat kívánja titkosítani, [a titkosítás típusát és a használni kívánt CEK-kulcsot](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) .

A **Taj** és a **születési** adatok titkosítása minden betegnél. A SSN oszlop a determinisztikus titkosítást fogja használni, amely támogatja az egyenlőségi kereséseket, az illesztéseket és a csoportosítást. A születési oszlop véletlenszerű titkosítást használ, amely nem támogatja a műveleteket.

Állítsa az SSN oszlop **titkosítási típusát** **determinisztikus** értékre, a születési oszlop pedig **véletlenszerű**értékre. Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Főkulcs konfigurálása
A **főkulcs konfigurálása** lapon beállíthatja a CMK, és kiválaszthatja azt a kulcstároló-szolgáltatót, ahol a CMK tárolni fogja. Jelenleg a Windows tanúsítványtárolóban, Azure Key Vault vagy hardveres biztonsági modulban (HSM) is tárolhat CMK.

Ez az oktatóanyag bemutatja, hogyan tárolhatók a kulcsok a Azure Key Vaultban.

1. Válassza a **Azure Key Vault**lehetőséget.
2. Válassza ki a kívánt kulcstartót a legördülő listából.
3. Kattintson a **Tovább** gombra.

![Főkulcs konfigurálása](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Érvényesítés
Ezután titkosíthatja az oszlopokat, vagy mentheti a PowerShell-parancsfájlt, hogy később fusson. Ebben az oktatóanyagban válassza a **Folytatás a befejezéshez** lehetőséget, és kattintson a **tovább**gombra.

### <a name="summary"></a>Összegzés
Ellenőrizze, hogy a beállítások helyesek-e, majd kattintson a **Befejezés** gombra a Always encrypted telepítésének befejezéséhez.

![Összegzés](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>A varázsló műveleteinek ellenőrzése
A varázsló befejezése után az adatbázis Always Encrypted lesz beállítva. A varázsló a következő műveleteket hajtotta végre:

* Létrehozta az oszlop főkulcsát, és azt Azure Key Vaultban tárolta.
* Létrehozta az oszlop titkosítási kulcsát, és azt Azure Key Vaultban tárolta.
* Konfigurálta a kijelölt oszlopokat a titkosításhoz. A páciensek tábla jelenleg nem rendelkezik adattal, de a kijelölt oszlopokban lévő összes meglévő adattal titkosítva van.

A SSMS kulcsok létrehozását a **Klinika** > **biztonsági** > **Always encrypted kulcsainak**kibővítésével ellenőrizheti.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>A titkosított adattal használható ügyfélalkalmazás létrehozása
Most, hogy a Always Encrypted be van állítva, létrehozhat egy olyan alkalmazást, amely végrehajtja a beszúrásokat, és *kiválasztja* a titkosított oszlopokat.  

> [!IMPORTANT]
> Az alkalmazásnak [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) -objektumokat kell használnia, amikor Always encrypted oszlopokkal továbbítja a szöveges adatait a kiszolgálónak. A literális értékek SqlParameter objektumok használata nélkül való átadása kivételt eredményez.
> 
> 

1. Nyissa meg a Visual studiót C# , és hozzon létre egy új **Console-alkalmazást** (Visual Studio 2015 és korábbi verziók) vagy a **Console app (.NET Framework)** (Visual Studio 2017 és újabb verziók). Győződjön meg arról, hogy a projekt a **.NET-keretrendszer 4,6** -es vagy újabb verziójára van beállítva.
2. Nevezze el a projekt **AlwaysEncryptedConsoleAKVApp** , és kattintson **az OK**gombra.
3. Telepítse a következő NuGet-csomagokat az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzolon**.

Futtassa a következő két sornyi kódot a Package Manager konzolon.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>A Always Encrypted engedélyezéséhez módosítsa a kapcsolódási karakterláncot
Ez a szakasz azt ismerteti, hogyan engedélyezhető a Always Encrypted az adatbázis-kapcsolódási karakterláncban.

A Always Encrypted engedélyezéséhez hozzá kell adnia az **oszlop titkosítási beállításához** tartozó kulcsszót a kapcsolódási karakterlánchoz, és be kell állítania azt az **engedélyezett**értékre.

Megadhatja ezt közvetlenül a kapcsolatok karakterláncában, vagy beállíthatja a [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)használatával. A következő szakaszban a minta alkalmazás a **SqlConnectionStringBuilder**használatát mutatja be.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Always Encrypted engedélyezése a kapcsolatok karakterláncában
Adja hozzá a következő kulcsszót a kapcsolódási karakterlánchoz.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Always Encrypted engedélyezése a SqlConnectionStringBuilder
A következő kód bemutatja, hogyan engedélyezheti a Always Encryptedt a [SqlConnectionStringBuilder. ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) beállítás engedélyezésével [.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>A Azure Key Vault-szolgáltató regisztrálása
A következő kód bemutatja, hogyan regisztrálhat a Azure Key Vault szolgáltatót a ADO.NET-illesztőprogrammal.

```csharp
    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted minta konzolos alkalmazás
Ez a példa a következőket mutatja be:

* A Always Encrypted engedélyezéséhez módosítsa a kapcsolódási karakterláncot.
* Azure Key Vault regisztrálása az alkalmazás kulcstároló-szolgáltatója.  
* Illessze be az adatbevitelt a titkosított oszlopba.
* Válasszon egy rekordot egy titkosított oszlop egy adott értékének szűrésével.

Cserélje le a **program.cs** tartalmát a következő kódra. Cserélje le a globális connectionString változó kapcsolati karakterláncát a sorban, amely közvetlenül megelőzi a Main metódust a Azure Portal érvényes kapcsolati karakterlánccal. Ez az egyetlen módosítás, amelyet ehhez a kódnak kell elvégeznie.

Futtassa az alkalmazást Always Encrypted működés közbeni megtekintéséhez.
```CS
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }
```


## <a name="verify-that-the-data-is-encrypted"></a>Az adattitkosítás ellenőrzése
Gyorsan megtekintheti, hogy a kiszolgálón lévő tényleges adatok titkosítva vannak-e a SSMS (az aktuális, az **oszlop titkosítási beállítását** még nem engedélyező) lekérdezéssel.

Futtassa a következő lekérdezést a klinika adatbázisán.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Láthatja, hogy a titkosított oszlopok nem tartalmaznak egyszerű szöveges információt.

   ![Új konzolos alkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Ahhoz, hogy a SSMS használatával hozzáférhessen az egyszerű szöveges információhoz, először ellenőrizze, hogy a felhasználó rendelkezik-e megfelelő engedélyekkel a Azure Key Vault: *Get*, *unwrapKey*és *ellenőrzés*. Részletes információkért lásd: [oszlopok főkulcsainak létrehozása és tárolása (Always encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Ezután adja hozzá az *oszlop titkosítási beállítás = engedélyezve* paramétert a csatlakozáskor.

1. A SSMS kattintson a jobb gombbal a kiszolgálóra **Object Explorer** és válassza a **Leválasztás**lehetőséget.
2. Kattintson > az**adatbázismotor** kapcsolódása elemre a **Kapcsolódás a kiszolgálóhoz** ablak megnyitásához, majd kattintson a **Beállítások**elemre.
3. Kattintson a **további kapcsolatok paramétereinek** és típus **oszlop titkosítási beállítás = engedélyezve**elemre.
   
    ![Új konzolos alkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Futtassa a következő lekérdezést a klinika adatbázisán.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Most már megtekintheti a titkosított oszlopokban lévő egyszerű szöveges információt.
     ![Új konzolos alkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>További lépések
Miután létrehozta a Always Encryptedt használó adatbázist, a következőket teheti:

* [A kulcsok elforgatása és karbantartása](https://msdn.microsoft.com/library/mt607048.aspx).
* [Always encrypted-mel már titkosított adatáttelepítés](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Kapcsolódó információk
* [Always Encrypted (ügyfél-fejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server encryption](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Always Encrypted blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

