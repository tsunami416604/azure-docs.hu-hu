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
ms.date: 05/09/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: f637b8617c4a9ed3be1eb0932a94b0ffcc7c8f7e
ms.contentlocale: hu-hu
ms.lasthandoff: 06/07/2017


---
<a id="get-started-with-azure-data-lake-store-using-net-sdk" class="xliff"></a>

# Az Azure Data Lake Store használatának első lépései a .NET SDK-val
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

A cikkből megtudhatja, hogyan végezhet el olyan alapvető műveleteket az [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) segítségével, mint például a mappák létrehozása, adatfájlok le- és feltöltése stb. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

<a id="prerequisites" class="xliff"></a>

## Előfeltételek
* **Visual Studio 2013, 2015 vagy 2017** Az alábbi utasítások a Visual Studio 2015 Update 2-t használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-fiók**. A fióklétrehozás módjáról [Az Azure Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md) című cikk nyújt tájékoztatást.

* **Egy Azure Active Directory-alkalmazás létrehozása**. A Data Lake Store alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.

<a id="create-a-net-application" class="xliff"></a>

## .NET-alkalmazás létrehozása
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

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.
6. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

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
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

<a id="authentication" class="xliff"></a>

## Authentication

<a id="if-you-are-using-end-user-authentication-recommended-for-this-tutorial" class="xliff"></a>

### Végfelhasználói hitelesítés használata esetén (ehhez az oktatóanyaghoz ajánlott)

Ezt egy meglévő Azure AD natív alkalmazással használva **interaktív** módon hitelesítheti az alkalmazást, vagyis a rendszer az Azure-beli hitelesítő adatok megadását kéri.

Az egyszerű használat érdekében az alábbi kódrészletben alapértelmezett értékek vannak megadva az ügyfél-azonosítóhoz és az átirányítási URI-hez, amelyek bármely Azure-előfizetéssel működnek. Ha segítségre van szüksége az oktatóanyag gyorsabb teljesítéséhez, ennek a módszernek a használatát javasoljuk. Az alábbi kódrészletben csak adja meg a bérlőazonosító értékét. Ezt az [Active Directory-alkalmazás létrehozásával kapcsolatos](data-lake-store-end-user-authenticate-using-active-directory.md) szakaszban megadott útmutatás szerint kérheti le.

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

Néhány tudnivaló a fenti kódrészlettel kapcsolatban:

* Az oktatóanyag gyorsabb teljesítése érdekében ez a kódrészlet olyan Azure AD-tartományt és ügyfél-azonosítót használ, amely minden Azure-előfizetés számára alapértelmezés szerint elérhető. Így **a kódrészletet változtatás nélkül használhatja az alkalmazásában**.
* Ha azonban a saját Azure AD-tartományát és alkalmazásügyfél-azonosítóját szeretné használni, létre kell hoznia egy natív Azure AD-alkalmazást, majd a létrehozott alkalmazáshoz használnia kell az Azure AD-bérlőazonosítót, az ügyfél-azonosítót és az átirányítási URI-t. Útmutatásért tekintse meg az [Active Directory-alkalmazás a Data Lake Store-ral, végfelhasználói hitelesítéshez való létrehozásával](data-lake-store-end-user-authenticate-using-active-directory.md) kapcsolatos témakört.

<a id="if-you-are-using-service-to-service-authentication-with-client-secret" class="xliff"></a>

### Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés használata esetén
A következő kódrészlet használható az alkalmazás **nem interaktív hitelesítéséhez** az alkalmazás/egyszerű szolgáltatás titkos ügyfélkódja/kulcsa segítségével. Ez a megoldás meglévő „webes” Azure AD-alkalmazásokhoz használható. Az Azure AD-webalkalmazás létrehozásával és az alábbi kódrészlethez szükséges ügyfél-azonosító és titkos ügyfélkód lekérésével kapcsolatos útmutatásért lásd az [Active Directory-alkalmazás szolgáltatások közötti hitelesítéshez, a Data Lake Store-ral való létrehozásáról](data-lake-store-authenticate-using-active-directory.md) szóló témakört.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

<a id="if-you-are-using-service-to-service-authentication-with-certificate" class="xliff"></a>

### Szolgáltatások közötti, tanúsítvánnyal történő hitelesítés használata esetén

Harmadik lehetőségként a következő kódrészlet is használható az alkalmazás **nem interaktív**, az Azure Active Directory-alkalmazás/egyszerű szolgáltatás tanúsítványával történő hitelesítésére. Ez [tanúsítványokkal rendelkező meglévő Azure AD-alkalmazásokhoz](../azure-resource-manager/resource-group-authenticate-service-principal.md) használható.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

<a id="create-client-objects" class="xliff"></a>

## Ügyfélobjektumok létrehozása
A következő kódrészlet létrehozza a Data Lake Store-fiókot és a fájlrendszeri ügyfélobjektumokat. Ezek a szolgáltatásnak küldött kérések kiadására használatosak.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

<a id="list-all-data-lake-store-accounts-within-a-subscription" class="xliff"></a>

## Az összes Data Lake Store-fiók listázása egy előfizetésen belül
A következő kódrészlet listázza az összes Data Lake Store-fiókot egy Azure-előfizetésen belül.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

<a id="create-a-directory" class="xliff"></a>

## Könyvtár létrehozása
Az alábbi részlet egy `CreateDirectory` metódust mutat be, amely egy könyvtár létrehozására használható egy Data Lake Store-fiókon belül.

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

<a id="upload-a-file" class="xliff"></a>

## Fájl feltöltése
Az alábbi részlet egy `UploadFile` metódust mutat be, amely fájlok egy Data Lake Store-fiókba való feltöltésére használható.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Az SDK támogatja a rekurzív fel- és letöltést egy helyi fájlelérési út és egy Data Lake Store-fájl elérési útja között.    

<a id="get-file-or-directory-info" class="xliff"></a>

## Fájl vagy könyvtár adatainak lekérése
Az alábbi részlet egy `GetItemInfo` metódust mutat be, amely a Data Lake Store-ban elérhető fájlok vagy könyvtárak adatainak lekérésére használható.

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

<a id="list-file-or-directories" class="xliff"></a>

## Fájlok vagy könyvtárak listázása
Az alábbi részlet egy `ListItem` metódust mutat be, amely egy Data Lake Store-fiókban található fájlok és könyvtárak listázására használható.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

<a id="concatenate-files" class="xliff"></a>

## Fájlok összefűzése
Az alábbi részlet egy `ConcatenateFiles` metódust mutat be, amely fájlok összefűzésére használható.

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

<a id="append-to-a-file" class="xliff"></a>

## Hozzáfűzés fájlhoz
Az alábbi részlet egy `AppendToFile` metódust mutat be, amely az adatok egy Data Lake Store-fiókban tárolt fájlhoz történő hozzáfűzésére használható.

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

<a id="download-a-file" class="xliff"></a>

## Fájl letöltése
Az alábbi részlet egy `DownloadFile` metódust mutat be, amely egy fájl Data Lake Store-fiókból való letöltésére használható.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

<a id="next-steps" class="xliff"></a>

## Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [A Data Lake Store .NET SDK dokumentációja](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [A Data Lake Store REST dokumentációja](https://msdn.microsoft.com/library/mt693424.aspx)

