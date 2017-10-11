---
title: "Mindig titkosítja: SQL-adatbázishoz – az Azure Key Vault |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan bizalmas adatok védelmének SQL-adatbázisban a varázslóval mindig titkosítja az SQL Server Management Studio adatok titkosításával. Bemutatja, hogyan egyes titkosítási kulcsok tárolására az Azure Key Vault utasításokat is tartalmaz."
keywords: "adatok titkosítását, a titkosítási kulcs, a felhő titkosítás"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 6ca16644-5969-497b-a413-d28c3b835c9b
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: sstein
ms.openlocfilehash: 61bfd420425b4740f6d4ebc01a403a88ff351382
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Mindig titkosítja: Az SQL-adatbázis bizalmas adatok védelmét, és a titkosítási kulcsok tárolása az Azure Key Vault

A cikkből megtudhatja, mennyire biztonságos adatok titkosítás használata az SQL-adatbázisban lévő bizalmas adatokat a [varázsló mindig titkosítja](https://msdn.microsoft.com/library/mt459280.aspx) a [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Bemutatja, hogyan egyes titkosítási kulcsok tárolására az Azure Key Vault utasításokat is tartalmaz.

Mindig titkosított egy olyan új adatok titkosítás technológia az Azure SQL Database és SQL Server, amely segít megvédeni a bizalmas adatok aktívan a kiszolgálón, ügyfél és kiszolgáló között, és miközben használatban van az adatok mozgása során. Mindig titkosított biztosítja, hogy bizalmas adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután konfigurálta az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók férhet hozzá egyszerű szöveges adatokhoz. Részletes információkért lásd: [(adatbázismotor) mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx).

Miután konfigurálta az adatbázist mindig titkosítja, a C# a Visual Studio, a titkosított adatok hoz létre egy ügyfélalkalmazást.

Kövesse a cikkben leírt lépéseket, és megtudhatja, hogyan állíthat be mindig titkosítja az Azure SQL-adatbázis. Ebben a cikkben megtudhatja, hogyan a következő feladatok végezhetők el:

* A varázslóval mindig titkosítja az SSMS létrehozásához [mindig titkosított kulcsok](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Hozzon létre egy [oszlop főkulcs (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Hozzon létre egy [oszlop titkosítási kulcsának (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Hozzon létre egy adatbázis tábláinak és oszlopok titkosításához.
* Hozzon létre egy alkalmazást, amely beszúrja, kiválasztása és a titkosított oszlopokban adatait jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 verzió vagy újabb.
* [.NET-keretrendszer 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (az ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Az Azure PowerShell](/powershell/azure/overview), 1.0-ás vagy újabb verziója. Típus **(Get-Module azure - ListAvailable). Verzió** futnak PowerShell verziójának megtekintéséhez.

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Az ügyfélalkalmazás számára az SQL Database szolgáltatás engedélyezése
Engedélyeznie kell az ügyfélalkalmazás az SQL Database szolgáltatás elérését a szükséges hitelesítés és az beszerzése a *ClientId* és *titkos* hitelesítéséhez szükséges a a következő kódot az alkalmazás.

1. Nyissa meg a [a klasszikus Azure portálon](http://manage.windowsazure.com).
2. Válassza ki **Active Directory** , és kattintson az alkalmazás által használt Active Directory-példányban.
3. Kattintson a **alkalmazások**, és kattintson a **hozzáadása**.
4. Adjon meg egy nevet az alkalmazáshoz (például: *myClientApp*) elemre, jelölje be **WEBALKALMAZÁS**, és kattintson a nyílra, a folytatáshoz.
5. Az a **SIGN-ON URL** és **APP ID URI** adhatja meg egy érvényes URL-címet (például *http://myClientApp*) és a folytatáshoz.
6. Kattintson a **KONFIGURÁLÁSA**.
7. Másolás a **ügyfél-azonosító**. (Később szüksége lesz ezt az értéket a kódban.)
8. A a **kulcsok** szakaszban jelölje be **1 év** a a **válassza ki a duration** legördülő listából. (Fogja másolni a kulcsot, miután menti a 13.)
9. Görgessen le, majd kattintson a **alkalmazás hozzáadása**.
10. Hagyja **megjelenítése** beállítása **Microsoft Apps** válassza **Microsoft Azure szolgáltatásfelügyeleti API**. Kattintson a pipa ikonra, a folytatáshoz.
11. Válassza ki **Azure szolgáltatásfelügyelet eléréséhez...**  a a **delegált engedélyek** legördülő listából.
12. Kattintson a **SAVE** (Mentés) gombra.
13. A mentés befejezése után másolja a kulcs értékét a **kulcsok** szakasz. (Később szüksége lesz ezt az értéket a kódban.)

## <a name="create-a-key-vault-to-store-your-keys"></a>Hozzon létre egy kulcstartót a kulcsok tárolására
Most, hogy az ügyfél alkalmazás van konfigurálva, és az ügyfél-Azonosítóval rendelkezik, akkor hozzon létre egy kulcstartót és a hozzáférési házirend konfigurálása a szolgáltatást, és az alkalmazás számára a tároló kulcsait (mindig titkosított kulcsok). A *létrehozása*, *beolvasása*, *lista*, *bejelentkezési*, *ellenőrizze*, *wrapKey*, és *unwrapKey* engedélyekre szükség, egy új oszlop főkulcsának létrehozásához és az SQL Server Management Studio titkosítási beállításának.

A következő parancsfájl futtatásával gyorsan létrehozhat egy kulcstartót. Ezek a parancsmagok és további információ a létrehozásáról és konfigurálásáról a kulcstároló részletes ismertetése [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md).

    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).Id
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ugrás a **új** > **adatok + tárolás** > **SQL-adatbázis**.
3. Hozzon létre egy **üres** nevű adatbázis **klinikán** egy új vagy meglévő kiszolgálóra. Az Azure portálon adatbázis létrehozásával kapcsolatos részletes utasításokat lásd: [az első Azure SQL-adatbázis](sql-database-get-started-portal.md).
   
    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Szüksége lesz a kapcsolati karakterlánc az oktatóanyag későbbi részében, ezért az adatbázis létrehozása után keresse meg az új klinikán adatbázis, és másolja a kapcsolati karakterláncot. A kapcsolati karakterlánc kaphat bármikor, de könnyen másolja az Azure portálon.

1. Ugrás a **SQL-adatbázisok** > **klinikán** > **adatbázis-kapcsolati karakterláncok megjelenítése**.
2. Másolja a kapcsolati karakterláncot a **ADO.NET**.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódni az adatbázishoz ssms alkalmazásával
Nyissa meg a szolgáltatáshoz az SSMS, és csatlakozzon a kiszolgálóhoz, az egészségügyi ellátó intézmény adatbázissal.

1. Nyissa meg a szolgáltatáshoz az SSMS. (Ugrás **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, ha nem, akkor a megnyitott.)
2. Adja meg a kiszolgáló nevét és hitelesítő adatait. Az SQL-adatbázis paneljén található a kiszolgáló nevét, és a kapcsolati karakterláncban korábban kimásolt. Adja meg a teljes kiszolgálónevet, beleértve a *database.windows.net*.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Ha a **Új tűzfalszabály** ablakban nyitja meg, jelentkezzen be Azure, és lehetővé teszik az SSMS, hozzon létre egy új tűzfalszabályt.

## <a name="create-a-table"></a>Tábla létrehozása
Ebben a szakaszban egy beteg adatokat tároló tábla hoz létre. Nincs kezdetben titkosított – titkosítás fog konfigurálása a következő szakaszban.

1. Bontsa ki a **adatbázisok**.
2. Kattintson a jobb gombbal a **klinikán** adatbázis, és kattintson a **új lekérdezés**.
3. Illessze be a következő Transact-SQL (T-SQL) az új lekérdezési ablakba és **Execute** azt.

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


## <a name="encrypt-columns-configure-always-encrypted"></a>Titkosítani az oszlopok (mindig titkosítja konfigurálása)
SSMS egy varázsló, amellyel könnyen beállítható mindig titkosítja az oszlop, az oszlop titkosítási kulcsának és a titkosított oszlopok beállítása az Ön által itt.

1. Bontsa ki a **adatbázisok** > **klinikán** > **táblák**.
2. Kattintson a jobb gombbal a **betegek** tábla, és válassza ki **titkosítása oszlopok** mindig titkosítja varázsló megnyitásához:
   
    ![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

A mindig titkosítja varázsló az alábbi szakaszokat tartalmazza: **Oszlopválasztás**, **főkulcs konfigurációs**, **érvényesítési**, és **összegzése**.

### <a name="column-selection"></a>Oszlop kiválasztása
Kattintson a **tovább** a a **bemutatása** lapon nyissa meg a **Oszlopválasztás** lap. Ezen a lapon kiválaszthatja titkosítására, oszlopok [a típusú titkosítást, és milyen oszlop titkosítási kulcsának (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) használatára.

Titkosítani **SSN** és **születési dátumot** minden türelmet adatait. A társadalombiztosítási szám oszlop determinisztikus titkosítás, mely támogatja a egyenlőség keresések, társítások és csoportosítás fogja használni. A születési dátumot oszlop véletlenszerű titkosítás, mely nem támogatja a műveleteket fogja használni.

Állítsa be a **titkosítási típus** SSN oszlop **Deterministic** és a születési dátumot oszlop **Randomized**. Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>A főkulcs konfiguráció
A **főkulcs konfigurációs** lap ahol állítsa be a CMK, és válassza ki a kulcstároló-szolgáltatóval a CMK tárolásához. Jelenleg egy CMK tárolhatja a Windows tanúsítványtárolóban, az Azure Key Vault vagy egy hardveres biztonsági modul (HSM).

Ez az oktatóanyag bemutatja, hogyan tárolja a kulcsokat az Azure Key Vault.

1. Válassza ki **az Azure Key Vault**.
2. A legördülő listából válassza ki a kívánt kulcstároló.
3. Kattintson a **Tovább** gombra.

![A főkulcs konfiguráció](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés
Most titkosítani az oszlopokat, vagy mentse később futtatni egy PowerShell-parancsfájlt. A jelen oktatóanyag esetében válassza ki a **Befejezés most már továbbléphet** kattintson **következő**.

### <a name="summary"></a>Összefoglalás
Ellenőrizze, hogy a beállítások helyességét, és kattintson a **Befejezés** mindig titkosítja az a telepítés befejezéséhez.

![Összefoglalás](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Ellenőrizze a varázsló műveletek
A varázsló befejezése után az adatbázis be van állítva mindig titkosítja. A varázsló a következő műveletek végre:

* Egy oszlop főkulcs létrehozva és tárolva az Azure Key Vault.
* Egy oszlop titkosítási kulcsának létrehozva és tárolva az Azure Key Vault.
* A kijelölt oszlopokat a titkosításhoz konfigurálva. A betegek tábla jelenleg nem tartalmaz adatokat, de most a kijelölt oszlopokban szereplő adatokat titkosított.

Ellenőrizheti a szolgáltatáshoz az ssms kulcsok létrehozásának kibontásával **klinikán** > **biztonsági** > **mindig a titkosított kulcsok**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Hozzon létre egy ügyfélalkalmazást, amely kompatibilis a titkosított adatok
Most, hogy mindig titkosítja be van állítva, egy alkalmazás, amely elvégzi hozhat létre *beszúrása* és *kiválasztja* a titkosított oszlopok.  

> [!IMPORTANT]
> Az alkalmazást kell használnia [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objektumok számára történő átadásakor adatokat egyszerű szöveges oszlopok mindig titkosítja a kiszolgálót. Értékek szövegkonstansban SqlParameter objektumok használata nélkül átadja azt eredményezi, hogy kivételt.
> 
> 

1. Nyissa meg a Visual Studio, és hozzon létre egy új C# **Konzolalkalmazás** (Visual Studio 2015-ös vagy korábbi) vagy **Konzolalkalmazás (.NET-keretrendszer)** (Visual Studio 2017 és újabb). Győződjön meg arról, hogy a projekt értéke **.NET-keretrendszer 4.6** vagy újabb.
2. Nevet a projektnek **AlwaysEncryptedConsoleAKVApp** kattintson **OK**.
3. A következő NuGet-csomagok telepítése a **eszközök** > **NuGet-Csomagkezelő** > **Csomagkezelő konzol**.

A Csomagkezelő konzol futtatása a kód két sort.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ahhoz, hogy mindig titkosítja a kapcsolati karakterlánc módosítása
Ez a szakasz azt ismerteti, hogyan is engedélyezhető az mindig titkosítja az adatbázis-kapcsolati karakterlánc.

Mindig titkosítja engedélyezéséhez kell hozzáadnia a **Oszloptitkosítási beállítás** kulcsszót a kapcsolati karakterláncot, és állítsa az értékét **engedélyezve**.

Közvetlenül a kapcsolódási karakterláncban állíthat, vagy beállíthatja a [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A mintaalkalmazást a következő szakasz bemutatja, hogyan használható **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Engedélyezze a mindig titkosítja a kapcsolódási karakterláncban
Adja hozzá a következő kulcsszó a kapcsolati karakterláncot.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Mindig titkosítja a SqlConnectionStringBuilder engedélyezése
A következő kód bemutatja, hogyan lehet engedélyezni a úgy, hogy mindig titkosítja [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) való [engedélyezve](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Az Azure Key Vault-szolgáltató regisztrálása
A következő kód bemutatja, hogyan regisztrálja az Azure Key Vault-szolgáltató az ADO.NET illesztőprogram.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Mindig titkosított minta-Konzolalkalmazás
Ez a példa bemutatja, hogyan:

* Módosítsa a kapcsolati karakterlánc ahhoz, hogy mindig titkosítja.
* Az Azure Key Vault regisztrálni az alkalmazás kulcstároló-szolgáltatóként.  
* Adatok beszúrása a titkosított oszlopokat.
* Válasszon ki egy olyan rekordot szűrést használ a megadott titkosított oszlop az értéket.

Cserélje le a tartalmát **Program.cs** a következő kóddal. Cserélje le a kapcsolati karakterláncot a globális connectionString változó a közvetlenül megelőző a fő metódus az Azure-portálon a érvényes kapcsolati karakterlánccal sorban. Ez akkor tegye ezt a kódot kell mindössze annyi a változás.

Futtassa az alkalmazást, hogy mindig titkosítja a művelet.

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
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


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

            _clientCredential = new ClientCredential(clientId, clientSecret);

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
Gyorsan ellenőrizheti, hogy a tényleges adatokat a kiszolgáló titkosítja az ssms alkalmazásával betegek adatok lekérdezése (az aktuális keresztül ahol **Oszloptitkosítási beállítás** még nincs engedélyezve).

Futtassa a következő lekérdezést a klinikán adatbázison.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Láthatja, hogy a titkosított oszlop nem tartalmaz az egyszerű szöveges adatokat.

   ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Az egyszerű szöveges adatok eléréséhez használja az SSMS, adja hozzá a *Oszloptitkosítási beállítás = engedélyezve* paraméter a kapcsolatra.

1. Az SSMS, kattintson a jobb gombbal a kiszolgáló **Object Explorer** válassza **Disconnect**.
2. Kattintson a **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, és kattintson **beállítások**.
3. Kattintson a **további kapcsolódási paraméterek** és típus **Oszloptitkosítási beállítás = engedélyezve**.
   
    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Futtassa a következő lekérdezést a klinikán adatbázison.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Most már megtekintheti a titkosított oszlopokban az egyszerű szöveges adatokat.

    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Következő lépések
Miután létrehozott egy adatbázist, amely mindig titkosítja használ, érdemes lehet tegye a következőket:

* [Forgassa el, és a kulcsok tisztítása](https://msdn.microsoft.com/library/mt607048.aspx).
* [Már mindig titkosítja a titkosított adatok áttelepítése](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Kapcsolódó információk
* [Mindig titkosítja (ügyféloldali fejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server-titkosítás](https://msdn.microsoft.com/library/bb510663.aspx)
* [Mindig titkosított varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Mindig titkosított blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

