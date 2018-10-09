---
title: 'Always Encrypted: SQL Database – az Azure Key Vault |} A Microsoft Docs'
description: Ez a cikk bemutatja, hogyan teheti biztonságossá a bizalmas adatokat egy SQL database-ben az adatok titkosítása a Always Encrypted varázsló használatával az SQL Server Management Studio.
keywords: adattitkosítás, a titkosítási kulcs, a felhő titkosítás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 2d735225782398b4e22a42816586a56cab54b763
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870196"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Always Encrypted: Bizalmas adatok védelme és tárolni a titkosítási kulcsokat az Azure Key Vaultban

Ez a cikk bemutatja, hogyan titkosítás használatával az SQL-adatbázisban tárolt bizalmas adatok védelme a [Always Encrypted varázsló](https://msdn.microsoft.com/library/mt459280.aspx) a [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Emellett olyan utasításokat tartalmaz, amelyek bemutatják, hogyan egyes titkosítási kulcsok tárolására az Azure Key Vaultban.

Always Encrypted az Azure SQL Database és SQL Server, amelynek segítségével a kiszolgálón, az inaktív bizalmas adatok védelme során adatátviteli ügyfél és kiszolgáló közötti, és az adatok használata közben az új adatok titkosítási technológiával. Mindig titkosított biztosítja, hogy bizalmas adatok soha nem jelenik meg, mint belül az adatbázis-rendszer egyszerű szövegként. Ha adattitkosítás konfigurál, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók, a kulcsokhoz hozzáférő hozzáférhet egyszerű szöveges adatokat. Részletes információkért lásd: [Always Encrypted (adatbázismotor)](https://msdn.microsoft.com/library/mt163865.aspx).

Miután konfigurálta az Always Encrypted használni kívánt adatbázis, létrehozhat egy ügyfélalkalmazás a titkosított adatok használatát a Visual studióval C#-ban.

Kövesse az ebben a cikkben, és megtudhatja, hogyan állíthatja be az Always Encrypted Azure SQL-adatbázis. Ebben a cikkben megtudhatja, hogyan hajthat végre a következő feladatokat:

* Az Always Encrypted varázsló segítségével az ssms-ben hozzon létre [Always Encrypted kulcsok](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Hozzon létre egy [oszlopfőkulcs (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Hozzon létre egy [oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Hozzon létre egy adatbázistábla soraihoz, és titkosítani az oszlopok.
* Hozzon létre egy alkalmazást, amely beszúrja, kiválasztása, és a titkosított oszlopokat adatait jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek
A jelen oktatóanyag esetében lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nincs fiókja, regisztráljon egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 verzió vagy újabb.
* [.NET-keretrendszer 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (az ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Az Azure PowerShell](/powershell/azure/overview), 1.0-s vagy újabb verziója. Típus **(Get-Module az azure - ListAvailable). Verzió** megtekintéséhez a PowerShell mely verzióját futtatja.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Az SQL Database szolgáltatáshoz való hozzáféréshez, az ügyfélalkalmazás engedélyezése
Az ügyfélalkalmazás egy Azure Active Directory (AAD) alkalmazás beállításával, és másolja az SQL Database szolgáltatás eléréséhez engedélyeznie kell a *Alkalmazásazonosító* és *kulcs* , amelyeket meg kell az alkalmazás hitelesítéséhez.

Az első a *Alkalmazásazonosító* és *kulcs*, kövesse a [hozzon létre egy Azure Active Directory alkalmazás és -szolgáltatásnév erőforrások eléréséhez](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Hozzon létre egy kulcstartót, hogy a kulcsokat
Most, hogy az ügyfélalkalmazás van konfigurálva, és rendelkezik az alkalmazás azonosítója, hozzon létre egy kulcstartót, és a hozzáférési szabályzat konfigurálása, hogy Ön és az alkalmazás hozzáférhessen a tárolóhoz, titkos kódok (az Always Encrypted kulcsok). A *létrehozása*, *első*, *lista*, *bejelentkezési*, *ellenőrzése*, *wrapKey*, és *unwrapKey* engedélyekre szükség, egy új oszlopfőkulcs létrehozásához és a titkosítás beállítása az SQL Server Management Studio.

Gyorsan létrehozhat egy kulcstartót a következő szkript futtatásával. Ezeket a parancsmagokat és a további információ a létrehozása és konfigurálása a key vault részletes leírását lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md).

    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a **erőforrás létrehozása** > **adatbázisok** > **SQL Database**.
3. Hozzon létre egy **üres** nevű adatbázis **Clinic** egy új vagy meglévő kiszolgálóra. Adatbázis létrehozása az Azure Portalon kapcsolatos részletes utasításokat talál [az első Azure SQL database](sql-database-get-started-portal.md).
   
    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Szüksége lesz a kapcsolati karakterlánc az oktatóanyag későbbi részében, így Miután létrehozta az adatbázist, az új Clinic adatbázis keresse meg és másolja a kapcsolati karakterláncot. A kapcsolati karakterláncot a bármikor megtekintheti, de egyszerűen másolja az Azure Portalon.

1. Lépjen a **SQL-adatbázisok** > **Clinic** > **adatbázis kapcsolati karakterláncainak megjelenítése**.
2. Másolja a kapcsolati karakterláncot a **ADO.NET**.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével
Nyissa meg az ssms-ben, és csatlakozzon a Clinic adatbázist tartalmazó kiszolgálón.

1. Nyissa meg az SSMS-t. (Ugrás a **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, ha még nincs megnyitva.)
2. Adja meg a kiszolgáló nevét és hitelesítő adatokat. Az SQL-adatbázis panelen található a kiszolgáló nevét, és a kapcsolati karakterláncban korábban vágólapra másolt. Írja be a teljes kiszolgálónevet, beleértve a *database.windows.net*.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Ha a **Új tűzfalszabály** ablak megnyílik, jelentkezzen be Azure-ban, és lehetővé teszik az ssms-ben hozzon létre egy új tűzfalszabályt az Ön számára.

## <a name="create-a-table"></a>Tábla létrehozása
Ebben a szakaszban létrehozhat egy táblát, amely a betegek adatokat tárolja. Már nem kezdetben titkosított – titkosítás fogja beállítani a következő szakaszban.

1. Bontsa ki a **adatbázisok**.
2. Kattintson a jobb gombbal a **Clinic** adatbázis, és kattintson a **új lekérdezés**.
3. Az új lekérdezési ablakban illessze be a következő Transact-SQL (T-SQL) és **Execute** azt.

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


## <a name="encrypt-columns-configure-always-encrypted"></a>(Az Always Encrypted konfigurálása) oszlop titkosítása
SSMS biztosít egy varázsló, amellyel könnyen konfigurálhatja az Always Encrypted által az Ön számára a oszlopfőkulcshoz oszloptitkosítási kulcs és a titkosított oszlopokat beállítását.

1. Bontsa ki a **adatbázisok** > **Clinic** > **táblák**.
2. Kattintson a jobb gombbal a **betegek** táblázatot, majd **titkosítása oszlopok** az Always Encrypted varázsló megnyitásához:
   
    ![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Az Always Encrypted varázsló az alábbi szakaszokat tartalmazza: **Oszlopválasztás**, **főkulcs konfigurációs**, **érvényesítési**, és **összegzése**.

### <a name="column-selection"></a>Oszlop kiválasztása
Kattintson a **tovább** a a **bemutatása** megnyitásához a **Oszlopválasztás** lapot. Ezen a lapon kiválaszthatja titkosítására, mely oszlopok [titkosítás, típusa és milyen oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) használatára.

Titkosítása **SSN** és **születési** egyes betegek adatait. A társadalombiztosítási szám oszlop determinisztikus titkosítás, mely támogatja a egyenlőség kereséseket, illesztés és csoportosítás fogja használni. A születési dátum oszlop véletlenszerű titkosítás, amely nem támogatja a műveletek alkalmaznak.

Állítsa be a **titkosítási típus** SSN oszlop **Deterministic** és születési dátum oszlop **Randomized**. Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>A főkulcs konfiguráció
A **főkulcs konfigurációs** lap, amelyen a CMK beállítása és a kulcstároló-szolgáltató kiválasztása a CMK tárolásához. Jelenleg egy CMK tárolhatja a Windows-tárolóba, az Azure Key Vault vagy egy hardveres biztonsági modul (HSM).

Az oktatóanyag bemutatja, hogy a kulcsokat az Azure Key Vaultban.

1. Válassza ki **az Azure Key Vault**.
2. A legördülő listából válassza ki a kívánt key vaultban.
3. Kattintson a **Tovább** gombra.

![A főkulcs konfiguráció](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés
Most titkosítani az oszlopokat, vagy később futtatni egy PowerShell-parancsfájl mentése. A jelen oktatóanyag esetében válassza ki a **most gombra a folytatáshoz** kattintson **tovább**.

### <a name="summary"></a>Összegzés
Győződjön meg arról, hogy a beállítások helyességét, és kattintson a **Befejezés** az Always Encrypted a telepítés befejezéséhez.

![Összegzés](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Ellenőrizze a varázsló műveletek
A varázsló befejezése után az adatbázis beállítása az Always Encrypted. A varázsló a következő műveletek végre:

* Egy oszlopfőkulcs létrehozott és tárolt, az Azure Key Vaultban.
* Létrehozott egy oszloptitkosítási kulcs és az Azure Key Vaultban tárolt azt.
* A kijelölt oszlopokat a titkosításhoz konfigurálva. A betegek tábla jelenleg nem tartalmaz adatokat, de a kijelölt oszlopokban szereplő összes meglévő adat már titkosítva van.

Az ssms-ben a kulcsok létrehozásának ellenőrzéséhez kibontásával **Clinic** > **biztonsági** > **mindig a titkosított kulcsok**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Egy ügyfélalkalmazás, amely együttműködik a titkosított adatok létrehozása
Most, hogy az Always Encrypted be van állítva, hozhat létre olyan alkalmazás, amely végrehajtja *szúr be* és *kiválasztja* meg a titkosított oszlopokat.  

> [!IMPORTANT]
> Az alkalmazás kell használnia [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objektumok egyszerű szöveges adatokat a Always Encrypted oszlopokkal rendelkező kiszolgáló történő átadásakor. Szöveges értékek passing SqlParameter objektumok használata nélkül kivételt eredményez.
> 
> 

1. Nyissa meg a Visual Studiót, és hozzon létre egy új C# **Konzolalkalmazás** (Visual Studio 2015 és korábbi verziók) vagy **Console App (.NET Framework)** (Visual Studio 2017-es és újabb verziók). Ellenőrizze, hogy a projekt értéke **.NET Framework 4.6** vagy újabb.
2. Adja a projektnek **AlwaysEncryptedConsoleAKVApp** kattintson **OK**.
3. A következő NuGet-csomagok telepítése a **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console**.

A Package Manager Console két kódsorok futnak.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Always Encrypted engedélyezéséhez a kapcsolati karakterlánc módosítása
Ez a szakasz ismerteti, hogyan Always Encrypted engedélyezéséhez az adatbázis-kapcsolati karakterláncot.

Always Encrypted engedélyezéséhez hozzá kell a **oszlop titkosítási beállítás** kulcsszó használatával a kapcsolati karakterláncot, és állítsa be **engedélyezve**.

Közvetlenül a kapcsolati karakterláncban állíthat, vagy beállíthatja a [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A következő szakaszban szereplő mintaalkalmazás bemutatja, hogyan **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Engedélyezze az Always Encrypted a kapcsolati karakterlánc
Adja hozzá a következő kulcsszó a kapcsolati karakterláncot.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Mindig titkosított SqlConnectionStringBuilder engedélyezése
A következő kód bemutatja, hogyan lehet engedélyezni az Always Encrypted beállításával [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) való [engedélyezve](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Az Azure Key Vault-szolgáltató regisztrálása
A következő kód bemutatja, hogyan regisztrálja az Azure Key Vault-szolgáltató az ADO.NET-illesztő.

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



## <a name="always-encrypted-sample-console-application"></a>Always Encrypted minta-Konzolalkalmazás
Ez a minta azt ismerteti, hogyan lehet:

* Always Encrypted engedélyezéséhez a kapcsolati karakterlánc módosítása.
* Az Azure Key Vault regisztrálja az alkalmazás kulcstároló-szolgáltatóként.  
* Adatok beszúrása a titkosított oszlopokat.
* Válassza ki egy rekordot egy megadott értéket egy titkosított oszlop szűrésével.

Cserélje le a tartalmát **Program.cs** az alábbi kódra. Cserélje le a kapcsolati karakterláncot a connectionString globális változó a sor közvetlenül megelőző a Main metódushoz, az Azure Portalon érvényes kapcsolati karakterláncra. Ez az az egyetlen változás, győződjön meg arról, hogy ez a kód kell.

Always Encrypted megtekintéséhez működés közben az alkalmazás futtatásához.

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



## <a name="verify-that-the-data-is-encrypted"></a>Győződjön meg arról, hogy az adatok titkosítása
Gyorsan ellenőrizheti, hogy a kiszolgálón a tényleges adatok titkosítja az ssms-ben a betegek adatok lekérdezése (az aktuális kapcsolat használatával ahol **oszlop titkosítási beállítás** még nem áll).

Futtassa a következő lekérdezést a Clinic adatbázison.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Láthatja, hogy a titkosított oszlopokban nem tartalmaz egyszerű szöveges adatokat.

   ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Használható az SSMS az egyszerű szöveges adatok eléréséhez, akkor először győződjön meg arról, hogy a felhasználó rendelkezik-e megfelelő engedélyekkel az Azure Key Vault: *első*, *unwrapKey*, és *ellenőrzése*. Részletes információkért lásd: [létrehozása és Store (Always Encrypted) oszlop főkulcsok](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-2017).

Majd adja hozzá a *oszlop titkosítási beállítás = engedélyezve* paraméter a kapcsolódás során.

1. Az ssms-ben, kattintson a jobb gombbal a kiszolgáló **Object Explorer** válassza **Disconnect**.
2. Kattintson a **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, és kattintson **beállítások**.
3. Kattintson a **további kapcsolódási paraméterek** , és írja be **oszlop titkosítási beállítás = engedélyezve**.
   
    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Futtassa a következő lekérdezést a Clinic adatbázison.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Most már megtekintheti a titkosított oszlopokban az egyszerű szöveges adatokat.

    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>További lépések
Miután létrehozott egy adatbázist, amely használja az Always Encrypted, érdemes a következőket:

* [Elforgatás és karbantartása a kulcsok](https://msdn.microsoft.com/library/mt607048.aspx).
* [Az Always Encrypted használatával titkosított adatok migrálása](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Kapcsolódó információk
* [Always Encrypted (ügyféloldali fejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server encryption](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Mindig titkosított blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

