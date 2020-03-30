---
title: Mindig titkosított - Azure Key Vault
description: Ez a cikk bemutatja, hogyan biztosíthatja a bizalmas adatokat egy SQL-adatbázisban adattitkosítással az SQL Server Management Studio Mindig titkosított varázslójával.
keywords: adattitkosítás, titkosítási kulcs, felhőtitkosítás
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
ms.openlocfilehash: 006c780aeb3db813c8fdfb5da0b5c13fc4dcfebc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067435"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Mindig titkosított: Bizalmas adatok védelme és titkosítási kulcsok tárolása az Azure Key Vaultban

Ez a cikk bemutatja, hogyan biztosíthatja a bizalmas adatokat egy SQL-adatbázisban adattitkosítással az SQL Server Management Studio [Mindig titkosított varázslójával](https://msdn.microsoft.com/library/mt459280.aspx) [.](https://msdn.microsoft.com/library/hh213248.aspx) Emellett utasításokat is tartalmaz, amelyek bemutatják, hogyan tárolhatja az egyes titkosítási kulcsot az Azure Key Vaultban.

A Mindig titkosított egy új adattitkosítási technológia az Azure SQL Database és az SQL Server rendszerben, amely segít megvédeni a kiszolgálón lévő bizalmas adatokat az ügyfél és a kiszolgáló közötti mozgás során, valamint az adatok használata közben. Mindig titkosított biztosítja, hogy a bizalmas adatok soha nem jelenik meg egyszerű szövegként az adatbázis-rendszerben. Az adattitkosítás konfigurálása után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy alkalmazáskiszolgálók férhetnek hozzá a egyszerű szöveges adatokhoz. További információt a [Mindig titkosított (adatbázis-kezelő motor) című témakörben talál.](https://msdn.microsoft.com/library/mt163865.aspx)

Miután konfigurálta az adatbázist a Mindig titkosítva használatára, létrehoz egy ügyfélalkalmazást c# nyelven a Visual Studióval a titkosított adatokkal való együttműködésre.

Kövesse a cikkben leírt lépéseket, és megtudhatja, hogyan állíthatja be a Mindig titkosítva azure-beli SQL-adatbázisokhoz. Ebben a cikkben megtudhatja, hogyan hajthatja végre a következő feladatokat:

- Az SSMS mindig titkosított varázslójával hozzon létre [mindig titkosított kulcsokat.](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
  - [Oszlopfőkulcs (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)létrehozása .
  - [Oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)létrehozása.
- Hozzon létre egy adatbázistáblát, és titkosítsa az oszlopokat.
- Hozzon létre egy alkalmazást, amely beszúrja, kijelöli és megjeleníti a titkosított oszlopok adatait.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

- Azure-fiók és -előfizetés. Ha még nem rendelkezik ilyen, iratkozzon fel egy [ingyenes próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/)
- [AZ SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242-es vagy újabb verziója.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (ügyfélszámítógépen).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/overview) vagy [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Az ügyfélalkalmazás hozzáférésének engedélyezése az SQL Database szolgáltatáshoz

Lehetővé kell tennie, hogy az ügyfélalkalmazás hozzáférjen az SQL Database szolgáltatáshoz egy Azure Active Directory (AAD) alkalmazás beállításával, és másolja az *alkalmazásazonosítót* és *a kulcsot,* amelyet az alkalmazás hitelesítéséhez kell.

Az *alkalmazásazonosító* és a *kulcs*lekért eleméhez kövesse az [erőforrásokhoz hozzáférő Azure Active Directory-alkalmazások és egyszerű szolgáltatás létrehozásának lépéseit.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="create-a-key-vault-to-store-your-keys"></a>Kulcstartó létrehozása a kulcsok tárolásához

Most, hogy az ügyfélalkalmazás konfigurálva van, és az alkalmazás azonosítója, itt az ideje, hogy hozzon létre egy key vault és konfigurálja a hozzáférési szabályzatot, így Ön és az alkalmazás hozzáférhet a tároló titkos kulcsait (a mindig titkosított kulcsok). A *create*, *get*, *list*, *sign*, *verify*, *wrapKey*és *unwrapKey* engedélyek szükségesek új oszlopfőkulcs létrehozásához és az SQL Server Management Studio titkosításának beállításához.

A következő parancsfájl futtatásával gyorsan létrehozhat egy kulcstartót. Ezeknek a parancsoknak a részletes magyarázatát, valamint a key vault létrehozásáról és konfigurálásáról a [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2.  > Nyissa meg az **Erőforrás-adatbázisok** > **Databases****sql-adatbázis létrehozása című**területet.
3. **Hozzon** létre egy **Klinika** nevű üres adatbázist egy új vagy meglévő kiszolgálón. Az Azure Portalon az adatbázis létrehozásának részletes útmutatásaaz [Első Azure SQL-adatbázis.](sql-database-single-database-get-started.md)

    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Szüksége lesz a kapcsolat string később az oktatóanyag, így miután létrehozta az adatbázist, keresse meg az új klinika adatbázis és másolja a kapcsolat ihúr. A kapcsolati karakterlánc ot bármikor beszerezheti, de az Azure Portalon egyszerűen másolhatja.

1. Nyissa meg az **SQL adatbázisok** > **Klinika** > **Adatbázis-kapcsolati karakterláncainak megjelenítése című e-mail-fájlt.**
2. Másolja a **ADO.NET**kapcsolati karakterláncát.

    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével

Nyissa meg az SSMS-t, és csatlakozzon a kiszolgálóhoz a klinika adatbázisával.

1. Nyissa meg az SSMS-t. (Ha nincs megnyitva, nyissa meg **az** **Adatbázis-kezelő motor** **csatlakoztatása** > ablakot.)

2. Adja meg a kiszolgáló nevét és hitelesítő adatait. A kiszolgálónév megtalálható az SQL adatbázis panelen és a korábban másolt kapcsolati karakterláncban. Írja be a teljes kiszolgálónevet, beleértve *a database.windows.net.*

    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Ha megnyílik az **Új tűzfalszabály** ablak, jelentkezzen be az Azure-ba, és hagyja, hogy az SSMS hozzon létre egy új tűzfalszabályt.

## <a name="create-a-table"></a>Tábla létrehozása

Ebben a szakaszban létrehoz egy táblázatot a betegek adatainak tárolására. Kezdetben nincs titkosítva - a titkosítást a következő szakaszban fogja konfigurálni.

1. Bontsa ki **az Adatbázisok csomópontot.**
2. Kattintson a jobb gombbal a **Klinika adatbázisára,** és válassza az Új **lekérdezés (Új lekérdezés) parancsra.**
3. Illessze be a következő Transact-SQL (T-SQL) az új lekérdezési ablakba, és **hajtsa végre.**

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Oszlopok titkosítása (a Mindig titkosított beállítás konfigurálása)

Az SSMS egy varázslót biztosít, amely segít a Mindig titkosított konfigurálásban az oszlopfőkulcs, az oszloptitkosítási kulcs és a titkosított oszlopok beállításával.

1. Bontsa ki **az Adatbázisok** > **klinikatábláinak****Clinic** > csomópontot.
2. Kattintson a jobb gombbal a **Betegek** táblára, és válassza az **Oszlopok titkosítása parancsot** a Mindig titkosított varázsló megnyitásához:

    ![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

A Mindig titkosított varázsló a következő szakaszokat tartalmazza: **Oszlopválasztás**, **Főkulcs-konfiguráció**, **Érvényesítés**és **Összegzés**.

### <a name="column-selection"></a>Oszlopkijelölés

Kattintson a **Tovább** gombra a **Bevezetés** lapon az **Oszlopkijelölés** lap megnyitásához. Ezen a lapon kiválaszthatja, hogy mely oszlopokat szeretné titkosítani, [milyen típusú titkosítást és milyen oszloptitkosítási kulcsot (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) kíván használni.

Titkosítsa **az SSN** és **birthdate** információkat minden beteg számára. Az SSN oszlop determinisztikus titkosítást fog használni, amely támogatja az egyenlőségi keresőket, az illesztéseket és a csoportosítást. A BirthDate oszlop véletlenszerű titkosítást fog használni, amely nem támogatja a műveleteket.

Állítsa az SSN-oszlop **titkosítási típusát** **determinisztikusra,** a BirthDate oszlopot **pedig randomizáltra.** Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Főkulcs konfigurációja

A **Főkulcs konfigurációja** lapon állíthatja be a CMK-t, és kiválaszthatja azt a kulcstároló-szolgáltatót, amelyen a CMK tárolója tárolóba kerül. Jelenleg tárolhat egy CMK-t a Windows tanúsítványtárolóban, az Azure Key Vaultban vagy egy hardveres biztonsági modulban (HSM).

Ez az oktatóanyag bemutatja, hogyan tárolhatja a kulcsokat az Azure Key Vaultban.

1. Válassza az **Azure Key Vault**lehetőséget.
2. Válassza ki a kívánt kulcstartót a legördülő listából.
3. Kattintson a **Tovább** gombra.

![Főkulcs konfigurációja](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés

Titkosíthatja az oszlopokat most, vagy mentse a PowerShell-parancsfájlt, hogy később fusson. Ehhez az oktatóanyaghoz válassza a **Folytatás gombra a befejezéshez,** majd kattintson a **Tovább**gombra.

### <a name="summary"></a>Összefoglalás

Ellenőrizze, hogy a beállítások helyesek-e, és kattintson a **Befejezés** gombra a Mindig titkosított beállítás befejezéséhez.

![Összefoglalás](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>A varázsló műveletének ellenőrzése

A varázsló befejezése után az adatbázis mindig titkosítva. A varázsló a következő műveleteket hajtotta végre:

- Létrehozott egy oszlopfőkulcsot, és tárolta az Azure Key Vaultban.
- Létrehozott egy oszloptitkosítási kulcsot, és tárolta az Azure Key Vaultban.
- A kijelölt oszlopoktitkosításkonfigurálása. A Betegek tábla jelenleg nem tartalmaz adatokat, de a kijelölt oszlopokban lévő adatok titkosítva vannak.

A kulcsok létrehozását az SSMS-ben ellenőrizheti a **Klinika** > **biztonságának** > **mindig titkosított kulcsainak**bővítésével.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>A titkosított adatokkal működő ügyfélalkalmazás létrehozása

Most, hogy a Mindig titkosított beállítás be van állítva, létrehozhat egy olyan alkalmazást, amely *beszúrásokat* és *kijelöléseket* hajt végre a titkosított oszlopokon.  

> [!IMPORTANT]
> Az alkalmazásnak [SqlParameter objektumokat kell használnia,](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) amikor egyszerű szöveges adatokat ad át a kiszolgálónak mindig titkosított oszlopokkal. A literális értékek sqlparameter objektumok használata nélkül történő átadása kivételt eredményez.

1. Nyissa meg a Visual Studio alkalmazást, és hozzon létre egy új C# **konzolalkalmazást** (Visual Studio 2015-ös és korábbi verzióiban) vagy **konzolalkalmazást (.NET keretrendszer)** (Visual Studio 2017-es és újabb verziók). Győződjön meg arról, hogy a projekt **.NET Framework 4.6** vagy újabb.
2. Nevezze el a **projektet AlwaysEncryptedConsoleAKVApp** és kattintson **az OK**gombra.
3. Telepítse a következő NuGet csomagokat az **Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolra.**

Futtassa ezt a két kódsort a Csomagkezelő konzolon:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>A kapcsolati karakterlánc módosítása a Mindig titkosított engedélyezéshez

Ez a szakasz azt ismerteti, hogy miként engedélyezheti a Mindig titkosítva az adatbázis-kapcsolati karakterláncban.

A Mindig titkosított engedélyezése érdekében hozzá kell adnia az **Oszloptitkosítási beállítás** kulcsszót a kapcsolati karakterlánchoz, és engedélyeznie kell **az Engedélyezve beállítást.**

Ezt közvetlenül a kapcsolati karakterláncban állíthatja be, vagy az [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)használatával is beállíthatja. A mintaalkalmazás a következő szakaszban bemutatja, hogyan kell használni **az SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Mindig titkosítva engedélyezése a kapcsolati karakterláncban

Adja hozzá a következő kulcsszót a kapcsolati karakterlánchoz.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Mindig titkosítva engedélyezése az SqlConnectionStringBuilder segítségével

A következő kód bemutatja, hogyan engedélyezhető a Always Encrypted az [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) [beállításával engedélyezve.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Az Azure Key Vault-szolgáltató regisztrálása
A következő kód bemutatja, hogyan regisztrálhatja az Azure Key Vault-szolgáltatót a ADO.NET illesztőprogrammal.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Mindig titkosított mintakonzolalkalmazás

Ez a minta bemutatja, hogyan:

- Módosítsa a kapcsolati karakterláncot a Mindig titkosított engedélyezéséhez.
- Regisztrálja az Azure Key Vaultot az alkalmazás kulcstároló-szolgáltatójaként.  
- Adatok beszúrása a titkosított oszlopokba.
- Jelöljön ki egy rekordot egy titkosított oszlop adott értékének szűrésével.

Cserélje le *Program.cs* tartalmát a következő kódra. Cserélje le a kapcsolati karakterláncot a globális connectionString változó a sorban, amely közvetlenül megelőzi a fő metódus az érvényes kapcsolati karakterlánc az Azure Portalon. Ez az egyetlen változás, amit meg kell tennie, hogy ezt a kódot.

Futtassa az alkalmazást a Mindig titkosítva működés közben i.Run the app to see Always Encrypted in action.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Az adatok titkosításának ellenőrzése

Gyorsan ellenőrizheti, hogy a kiszolgálón lévő tényleges adatok titkosítva vannak-e a Betegek adatainak SSMS-sel történő lekérdezésével (az aktuális kapcsolat használatával, ahol az **oszloptitkosítási beállítás** még nincs engedélyezve).

Futtassa a következő lekérdezést a klinika adatbázisában.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Láthatja, hogy a titkosított oszlopok nem tartalmaznak egyszerű szöveges adatokat.

   ![Új konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Az SSMS használatával az egyszerű szöveges adatok eléréséhez először meg kell győződnie arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel az Azure Key Vaulthoz: *get*, *unwrapKey*, és *ellenőrizze.* További információt az [Oszlopfőkulcsok létrehozása és tárolása (mindig titkosított) című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)

Ezután adja hozzá az *Oszloptitkosítási beállítás=engedélyezett* paramétert a kapcsolat során.

1. Az SSMS-ben kattintson a jobb gombbal a kiszolgálóra az **Objektumkezelőben,** és válassza a **Kapcsolat bontása parancsot.**
2. Kattintson az**Adatbázis-motor** **csatlakoztatása** > gombra a **Csatlakozás kiszolgálóhoz** ablak megnyitásához, majd a **Beállítások**gombra.
3. Kattintson **a További kapcsolatparaméterek gombra,** és írja **be az Oszloptitkosítási beállítás=engedélyezve.**

    ![Új konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Futtassa a következő lekérdezést a klinika adatbázisában.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Most már láthatja az egyszerű szöveges adatokat a titkosított oszlopokban.
     ![Új konzolalkalmazás](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)

## <a name="next-steps"></a>További lépések

A Mindig titkosított adatbázis létrehozása után a következőket teheti:

- [Forgassa el és tisztítsa meg a billentyűket](https://msdn.microsoft.com/library/mt607048.aspx).
- [A Mindig titkosított a már titkosított adatok áttelepítése.](https://msdn.microsoft.com/library/mt621539.aspx)

## <a name="related-information"></a>Kapcsolódó információk

- [Mindig titkosított (ügyfélfejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server titkosítás](https://msdn.microsoft.com/library/bb510663.aspx)
- [Mindig titkosított varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
- [Mindig titkosított blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
