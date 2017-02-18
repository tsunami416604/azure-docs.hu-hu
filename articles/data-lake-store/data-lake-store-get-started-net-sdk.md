---
title: "A .NET SDK használata Azure Data Lake Store-alkalmazások fejlesztéséhez | Microsoft Docs"
description: "Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása a Data Lake Store-ban az Azure Data Lake Store .NET SDK használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8ec76c597dfb59860b456e42a78239c67d289f13


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Az Azure Data Lake Store használatának első lépései a .NET SDK-val
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

A cikkből megtudhatja, hogyan végezhet el olyan alapvető műveleteket az [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) segítségével, mint például a mappák létrehozása, adatfájlok le- és feltöltése stb. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013 vagy 2015**. Az alábbi utasítások a Visual Studio 2015-öt használják.
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Store-fiók**. A fióklétrehozás módjáról [Az Azure Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md) című cikk nyújt tájékoztatást.
* **Egy Azure Active Directory-alkalmazás létrehozása**. A Data Lake Store alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása
1. Nyissa meg a Visual Studiót, és hozzon létre egy konzolalkalmazást.
2. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
3. Az **Új projekt** területen írja be vagy válassza ki az alábbi értékeket:
   
   | Tulajdonság | Érték |
   | --- | --- |
   | Kategória |Sablonok/Visual C#/Windows |
   | Sablon |Konzolalkalmazás |
   | Név |CreateADLApplication |
4. A projekt létrehozásához kattintson az **OK** gombra.
5. Adja hozzá a NuGet-csomagokat a projekthez.
   
   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   2. Győződjön meg arról, hogy a **Nuget Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:
      
      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a v0.12.5-preview jelű verziót használja.
      * `Microsoft.Azure.Management.DataLake.StoreUploader` – Ez az oktatóanyag a v0.10.6-preview jelű verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a v2.2.8-preview jelű verziót használja.
        
        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.
6. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.
   
        using System;
        using System.Threading;
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
7. Deklarálja a változókat az alább látható módon, és adja meg a meglévő Data Lake Store és erőforráscsoport nevének értékét. Továbbá győződjön meg arról, hogy a megadott helyi elérési út és fájlnév létezik a számítógépen. Vegye fel a következő kódrészletet a névtér-deklarációk után.
   
        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
   
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Authentication
### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Végfelhasználói hitelesítés használata esetén (ehhez az oktatóanyaghoz ajánlott)
Meglévő „natív” Azure AD ügyfélalkalmazással használja, amelyre lent találhat egy példát. Ha segítségre van szüksége az oktatóanyag gyorsabb teljesítéséhez, ennek a módszernek a használatát javasoljuk.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Néhány tudnivaló a fenti kódrészlettel kapcsolatban.

* Az oktatóanyag gyorsabb teljesítése érdekében ez a kódrészlet olyan Azure AD-tartományt és ügyfél-azonosítót használ, amely minden Azure-előfizetés számára alapértelmezés szerint elérhető. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-tartományt, az ügyfél-azonosítót és az átirányítási URI-t. Útmutatás: [Egy Active Directory-alkalmazás létrehozása](data-lake-store-end-user-authenticate-using-active-directory.md).

> [!NOTE]
> A fenti hivatkozások követésével elérhető utasítások egy Azure AD-webalkalmazásra vonatkoznak. A lépések azonban pontosan ugyanazok akkor is, ha úgy döntött, hogy inkább egy natív ügyfélalkalmazást hoz létre. 
> 
> 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés használata esetén
A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez az alkalmazás/szolgáltatás titkos ügyfélkódjával/kulcsával történő hitelesítésére. Meglévő [„webes” Azure AD-alkalmazással](../azure-resource-manager/resource-group-create-service-principal-portal.md) használhatja.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Szolgáltatások közötti, tanúsítvánnyal történő hitelesítés használata esetén
Harmadik lehetőségként a következő kódrészlet is használható az alkalmazás nem interaktív, az alkalmazás tanúsítványával/szolgáltatásnévvel történő hitelesítésére. Meglévő [„webes” Azure AD-alkalmazással](../azure-resource-manager/resource-group-create-service-principal-portal.md) használhatja.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Ügyfélobjektumok létrehozása
A következő kódrészlet létrehozza a Data Lake Store-fiókot és a fájlrendszeri ügyfélobjektumokat. Ezek a szolgáltatásnak küldött kérések kiadására használatosak.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Az összes Data Lake Store-fiók listázása egy előfizetésen belül
A következő kódrészlet listázza az összes Data Lake Store-fiókot egy Azure-előfizetésen belül.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Könyvtár létrehozása
Az alábbi részlet egy `CreateDirectory` metódust mutat be, amely egy könyvtár létrehozására használható egy Data Lake Store-fiókon belül.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Fájl feltöltése
Az alábbi részlet egy `UploadFile` metódust mutat be, amely fájlok egy Data Lake Store-fiókba való feltöltésére használható.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

A `DataLakeStoreUploader` támogatja a rekurzív fel- és letöltést egy helyi fájlelérési út és egy Data Lake Store-fájl elérési útja között.    

## <a name="get-file-or-directory-info"></a>Fájl vagy könyvtár adatainak lekérése
Az alábbi részlet egy `GetItemInfo` metódust mutat be, amely a Data Lake Store-ban elérhető fájlok vagy könyvtárak adatainak lekérésére használható. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Fájlok vagy könyvtárak listázása
Az alábbi részlet egy `ListItem` metódust mutat be, amely egy Data Lake Store-fiókban található fájlok és könyvtárak listázására használható.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Fájlok összefűzése
Az alábbi részlet egy `ConcatenateFiles` metódust mutat be, amely fájlok összefűzésére használható. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz
Az alábbi részlet egy `AppendToFile` metódust mutat be, amely az adatok egy Data Lake Store-fiókban tárolt fájlhoz történő hozzáfűzésére használható.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Fájl letöltése
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

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [A Data Lake Store .NET SDK dokumentációja](https://msdn.microsoft.com/library/mt581387.aspx)
* [A Data Lake Store REST dokumentációja](https://msdn.microsoft.com/library/mt693424.aspx)




<!--HONumber=Jan17_HO4-->


