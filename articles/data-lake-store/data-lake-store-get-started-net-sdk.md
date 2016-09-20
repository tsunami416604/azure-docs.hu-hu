<properties
   pageTitle="A Data Lake Store .NET SDK használata az alkalmazások fejlesztéséhez | Azure"
   description="Az Azure Data Lake Store .NET SDK használata alkalmazások fejlesztéséhez"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Az Azure Data Lake Store használatának első lépései a .NET SDK-val

> [AZURE.SELECTOR]
- [Portál](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Ismerje meg, hogyan hozhat létre Azure Data Lake-fiókot az Azure Data Lake Store .NET SDK használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

## Előfeltételek

* Visual Studio 2013 vagy 2015 Az alábbi utasítások a Visual Studio 2015-öt használják.
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure-előfizetés engedélyezése** a Data Lake Store nyilvános előzetes verziójához. Lásd az [utasításokat](data-lake-store-get-started-portal.md#signup).
* **Egy Azure Active Directory-alkalmazás létrehozása**. Az Azure Active Directory használatával történő hitelesítésnek két módja van – **interaktív** és **nem interaktív**. A hitelesítés kívánt módjának megfelelően eltérő előfeltételek érvényesek.
    * **Interaktív hitelesítés** (a cikk ezt használja) – Az Azure Active Directoryban létre kell hoznia egy **webalkalmazást**. Miután létrehozta az alkalmazást, kérje le az alkalmazáshoz kapcsolódó alábbi értékeket.
        - Az **ügyfél-azonosító** és az **átirányítási URI** lekérése az alkalmazáshoz
        - Delegált engedélyek beállítása

    * **Nem interaktív hitelesítés** – Az Azure Active Directoryban létre kell hoznia egy **webalkalmazást**. Miután létrehozta az alkalmazást, kérje le az alkalmazáshoz kapcsolódó alábbi értékeket.
        - Az **ügyfél-azonosító**, az **ügyfél titkos kulcsa** és az **átirányítási URI** lekérése az alkalmazáshoz
        - Delegált engedélyek beállítása
        - Rendelje hozzá az Azure Active Directory-alkalmazást egy szerepkörhöz. A szerepkör lehet annak a hatókörnek a szintjén, amelyen engedélyt kíván biztosítani az Azure Active Directory-alkalmazásnak. Az alkalmazás például hozzárendelhető az előfizetés szintjén vagy egy erőforráscsoport szintjén is. 

    Az értékek lekérésére, az engedélyek beállítására és a szerepkörök hozzárendelésére vonatkozó utasítások: [Create Active Directory application and service principal using portal](../resource-group-create-service-principal-portal.md) (Active Directory-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával).

## .NET-alkalmazás létrehozása

1. Nyissa meg a Visual Studiót, és hozzon létre egy konzolalkalmazást.

2. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.

3. Az **Új projekt** területen írja be vagy válassza ki az alábbi értékeket:

  	| Tulajdonság | Érték                       |
  	|----------|-----------------------------|
  	| Kategória | Sablonok/Visual C#/Windows |
  	| Sablon | Konzolalkalmazás         |
  	| Név     | CreateADLApplication        |

4. A projekt létrehozásához kattintson az **OK** gombra.

5. Adja hozzá a NuGet-csomagokat a projekthez.

    1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
    2. Győződjön meg arról, hogy a **Nuget Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
    3. Keresse meg és telepítse az alábbi Data Lake Store-csomagokat:

        * `Microsoft.Azure.Management.DataLake.Store`
        * `Microsoft.Azure.Management.DataLake.StoreUploader`

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")

    4. Telepítse a(z) `Microsoft.IdentityModel.Clients.ActiveDirectory` csomagot is az Azure Active Directory-hitelesítéshez. Mindenképpen *törölje* az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet jelölését a csomag stabil verziójának telepítése érdekében.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Create a new Azure Data Lake account")


    5. Zárja be a **NuGet-csomagkezelőt**.

7. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

8. Deklarálja a változókat az alább látható módon, majd adja meg a Data Lake Store és az erőforráscsoport nevének értékét. A megadott Data Lake Store-nevet az alkalmazás fogja létrehozni. Az itt megadott erőforráscsoportnak már léteznie kell. Továbbá győződjön meg arról, hogy a megadott helyi elérési út és fájlnév létezik a számítógépen. Vegye fel a következő kódrészletet a névtér-deklarációk után.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

A cikk fennmaradó részében megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a felhasználók hitelesítése, Data Lake Store-fiók létrehozása, fájlok feltöltése stb. Ha egy teljes mintát szeretne a Data Lake Store használatáról, tekintse meg a cikk alján található [Függeléket](#appendix-sample-code).

## Felhasználó hitelesítése

Az Azure Active Directory használatával történő hitelesítésnek két módja van:

* **Interaktív**, amikor a felhasználó az alkalmazás használatával jelentkezik be. Ezt az alábbi kódrészletben szereplő `AuthenticateUser` metódus valósítja meg.

* **Nem interaktív**, amikor az alkalmazás maga biztosítja saját hitelesítő adatait. Ezt az alábbi kódrészletben szereplő `AuthenticateAppliaction` metódus valósítja meg.

### Interaktív hitelesítés

Az alábbi kódrészlet egy `AuthenticateUser` metódust mutat be, amely az interaktív bejelentkezéshez használható.

    // Authenticate the user with AAD through an interactive popup.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
    
        var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
            PromptBehavior.Auto, UserIdentifier.AnyUser);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }

### Nem interaktív hitelesítés

Az alábbi részlet egy `AuthenticateApplication` metódust mutat be, amely a nem interaktív bejelentkezéshez használható.

    // Authenticate the application with AAD through the application's secret key.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
        var credential = new ClientCredential(appClientId, clientSecret);
    
        var tokenAuthResult = authContext.AcquireToken(resource, credential);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }
    
## Data Lake Store-fiók létrehozása

Az alábbi részlet egy `CreateAccount` metódust mutat be, amely egy Data Lake Store-fiók létrehozására használható.

    // Create Data Lake Store account
    public static void CreateAccount()
    {
        var adlsParameters = new DataLakeStoreAccount(location: _location);
        _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
    } 

## Az összes Data Lake Store-fiók listázása egy előfizetésen belül

Az alábbi részlet egy `ListAdlStoreAccounts` metódust mutat be, amely egy adott Azure-előfizetésen belüli összes Data Lake Store-fiók listázására használható.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
    
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
    
        return accounts;
    }

## Könyvtár létrehozása

Az alábbi részlet egy `CreateDirectory` metódust mutat be, amely egy könyvtár létrehozására használható egy Data Lake Store-fiókon belül.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Fájl feltöltése a Data Lake Store-ba

Az alábbi részlet egy `UploadFile` metódust mutat be, amely fájlok egy Data Lake Store-fiókba való feltöltésére használható.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

## Fájl vagy könyvtár adatainak lekérése

Az alábbi részlet egy `GetItemInfo` metódust mutat be, amely a Data Lake Store-ban elérhető fájlok vagy könyvtárak adatainak lekérésére használható. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Fájlok vagy könyvtárak listázása

Az alábbi részlet egy `ListItem` metódust mutat be, amely egy Data Lake Store-fiókban található fájlok és könyvtárak listázására használható.
    
    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Fájlok összefűzése

Az alábbi részlet egy `ConcatenateFiles` metódust mutat be, amely fájlok összefűzésére használható. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Hozzáfűzés fájlhoz

Az alábbi részlet egy `AppendToFile` metódust mutat be, amely az adatok egy Data Lake Store-fiókban tárolt fájlhoz történő hozzáfűzésére használható.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Fájl letöltése

Az alábbi részlet egy `DownloadFile` metódust mutat be, amely egy fájl Data Lake Store-fiókból való letöltésére használható.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Data Lake Store-fiók törlése

Az alábbi részlet egy `DeleteAccount` metódust mutat be, amely egy Data Lake Store-fiók törlésére használható.

    // Delete account
    public static void DeleteAccount()
    {
        _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
    }

## Függelék: Mintakód

Az alábbi részlet egy átfogó kódminta, amelyet átmásolhat az alkalmazásába egy végpontok közötti művelet megtekintéséhez a Data Lake Store-ban. A kódrészlet futtatása előtt győződjön meg arról, hogy megadta a szükséges értékeket, például a Data Lake Store nevét, az erőforráscsoport nevét stb. Az Azure Active Direcctory hitelesítéséhez szükséges értékeket is meg kell adnia, például a következőket: **\<APPLICATION-CLIENT-ID>** , **\<APPLICATION-REPLY-URI>** és **\<SUBSCRIPTION-ID>**.

Bár az alábbi kódrészlet az interaktív és a nem interaktív módszerhez is biztosít metódusokat, a nem interaktív kódblokk megjegyzésként szerepel. Az interaktív metódus az AAD-alkalmazás ügyfél-azonosítójának és átirányítási URI-jának megadását igényli. Az előfeltételekben található hivatkozás tartalmazza a beszerzésükre vonatkozó útmutatást.

>[AZURE.NOTE] Ha módosítaná a kódrészletet, és inkább a nem interaktív (`AuthenticateApplication`) metódust használná, akkor az ügyfél-azonosító és az ügyfél válasz URI-ja mellett az ügyfél hitelesítési kulcsát is meg kell adnia a metódus bemeneteként. A [Create Active Directory application and service principal using portal](../resource-group-create-service-principal-portal.md) (Active Directory-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával) című cikk az ügyfél hitelesítő kulcsának létrehozásáról és lekéréséről is hasznos információkat tartalmaz.
    
Végül győződjön meg arról, hogy a megadott helyi elérési út és fájlnév létezik a számítógépen. Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.



    using System;
    using System.IO;
    using System.Security;
    using System.Text;
    using System.Collections.Generic;
    using System.Linq;

    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;

    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                _location = "East US 2";

                string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                string remoteFolderPath = "/data_lake_path/";
                string remoteFilePath = remoteFolderPath + "file.txt";

                // Authenticate the user
                var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                    "<APPLICATION-CLIENT-ID>", new Uri("<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                // Run sample scenarios
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Creating a directory.");

                // Create a directory in the Data Lake Store
                CreateDirectory(remoteFolderPath);
                WaitForNewline("Directory created.", "Showing directory info.");

                // Get info about the directory in the Data Lake Store
                var itemInfo = GetItemInfo(remoteFolderPath);
                Console.WriteLine("Type: " + itemInfo.Type);
                Console.WriteLine("Last modified (UTC): " +
                                  new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                      itemInfo.ModificationTime.Value));
                WaitForNewline("Directory info shown.", "Uploading a file.");

                // Upload a file to the Data Lake Store
                UploadFile(localFilePath, remoteFilePath);
                WaitForNewline("File uploaded.", "Listing files and directories.");

                // List the files in the Data Lake Store
                var itemList = ListItems(remoteFolderPath);
                var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                Console.WriteLine(String.Join("\r\n", fileMenuItems));
                WaitForNewline("Files and directories listed.", "Appending content to a file.");

                // Append to a file in the Data Lake Store
                AppendToFile(remoteFilePath, "123");
                WaitForNewline("Content appended.", "Downloading a file.");

                // Download a file from the Data Lake Store
                DownloadFile(remoteFilePath, localFilePath);
                WaitForNewline("File downloaded.", "Deleting account.");

                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted. You can now exit.");
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                if (!String.IsNullOrWhiteSpace(nextAction))
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                    Console.WriteLine(nextAction);
                }
                else
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                }
            }

            // Authenticate the user with AAD through an interactive popup.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                    PromptBehavior.Auto, UserIdentifier.AnyUser);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            
            /*
            // Authenticate the application with AAD through the application's secret key.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                var credential = new ClientCredential(appClientId, clientSecret);

                var tokenAuthResult = authContext.AcquireToken(resource, credential);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            */

            //Set up clients
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
                _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                _adlsClient.SubscriptionId = subscriptionId;

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            // Create account
            public static void CreateAccount()
            {
                // Create ADLS account
                var adlsParameters = new DataLakeStoreAccount(location: _location);
                _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }

            // Delete account
            public static void DeleteAccount()
            {
                _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
            }

            // List all ADLS accounts within the subscription
            public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
            {
                var response = _adlsClient.Account.List(_adlsAccountName);
                var accounts = new List<DataLakeStoreAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlsClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }

            // Upload a file
            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            // Concatenate files
            public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
            {
                _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(string path)
            {
                return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(string directoryPath)
            {
                return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
            }

            // Download file
            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Append to file
            public static void AppendToFile(string path, string content)
            {
                var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
            }

            // Create a directory
            public static void CreateDirectory(string path)
            {
                _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
            }
        }
    }


## Következő lépések

- [Secure data in Data Lake Store (Az adatok védelme a Data Lake Store-ban)](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store (Az Azure Data Lake Analytics használata a Data Lake Store-ral)](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Az Azure HDInsight használata a Data Lake Store-ral)](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=sep16_HO1-->


