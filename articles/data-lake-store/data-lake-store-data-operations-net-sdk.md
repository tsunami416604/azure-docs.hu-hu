---
title: ".NET SDK: Fájlrendszerműveletek az Azure Data Lake Store-ban | Microsoft Docs"
description: "Az Azure Data Lake Store .NET SDK használata fájlrendszerműveletek (például mappák létrehozása) végrehajtására a Data Lake Store-ban."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 7f6319dcf1ae66a686dd1c2ea2810b3041183098
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Fájlrendszerműveletek az Azure Data Lake Store-ban a .NET SDK használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Ebből a cikkből megtudhatja, hogyan történik a fájlrendszerműveletek végrehajtása az Azure Data Lake Store-ban a .NET SDK használatával. Fájlrendszerművelet lehet például a mappák létrehozása a Data Lake Store-fiókokban, a fájlok feltöltése, a fájlok letöltése stb.

A Data Lake Store fiókkezelési műveleteinek a .NET SDK használatával való végrehajtásával kapcsolatban lásd: [Fiókkezelési műveletek a Data Lake Store-ban a .NET SDK használatával](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2013, 2015 vagy 2017** Az alábbi utasítások a Visual Studio 2017-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-fiók**. A fióklétrehozás módjáról [Az Azure Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md) című cikk nyújt tájékoztatást.

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
   2. Győződjön meg arról, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) lapon a **Package source** (Csomag forrása) értéke **nuget.org**, és az **Include prerelease** (Előzetes verzió belefoglalása) jelölőnégyzet be van jelölve.
   3. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.Management.DataLake.Store` – Ez az oktatóanyag a 2.1.3-as előzetes verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.2.12-es verziót használja.

        ![NuGet-forrás hozzáadása](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Új Azure Data Lake-fiók létrehozása")
   4. Zárja be a **NuGet-csomagkezelőt**.
6. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Deklarálja a változókat az alább látható módon, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy az itt megadott helyi elérési út és fájlnév létezik a számítógépen.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Authentication

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-objects"></a>Ügyfélobjektumok létrehozása
A következő kódrészlet létrehozza a Data Lake Store-fiókot és a fájlrendszeri ügyfélobjektumokat. Ezek a szolgáltatásnak küldött kérések kiadására használatosak.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

## <a name="create-a-directory"></a>Könyvtár létrehozása
Adja hozzá a következő metódust az osztályhoz. A részlet egy `CreateDirectory()` metódust mutat be, amely egy könyvtár létrehozására használható egy Data Lake Store-fiókon belül.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `CreateDirectory()` metódus meghívásához.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Fájl feltöltése
Adja hozzá a következő metódust az osztályhoz. A kódrészlet egy `UploadFile()` metódust mutat be, amely fájlok egy Data Lake Store-fiókba való feltöltésére használható. Az SDK támogatja a rekurzív fel- és letöltést egy helyi fájlelérési út és egy Data Lake Store-fájl elérési útja között.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `UploadFile()` metódus meghívásához.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Fájl vagy könyvtár adatainak lekérése
Az alábbi részlet egy `GetItemInfo()` metódust mutat be, amely a Data Lake Store-ban elérhető fájlok vagy könyvtárak adatainak lekérésére használható.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `GetItemInfo()` metódus meghívásához.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Fájlok vagy könyvtárak listázása
Az alábbi részlet egy `ListItems()` metódust mutat be, amely egy Data Lake Store-fiókban található fájlok és könyvtárak listázására használható.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `ListItems()` metódus meghívásához.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Fájlok összefűzése
Az alábbi részlet egy `ConcatenateFiles()` metódust mutat be, amely fájlok összefűzésére használható.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `ConcatenateFiles()` metódus meghívásához. Ez a kódrészlet feltételezi, hogy már feltöltött egy másik fájlt a Data Lake Store-fiókba, és az adott fájl útvonala meg van adva az *anotherRemoteFilePath* karakterláncban.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz
Az alábbi részlet egy `AppendToFile()` metódust mutat be, amely az adatok egy Data Lake Store-fiókban tárolt fájlhoz történő hozzáfűzésére használható.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `AppendToFile()` metódus meghívásához.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Fájl letöltése
Az alábbi részlet egy `DownloadFile()` metódust mutat be, amely egy fájl Data Lake Store-fiókból való letöltésére használható.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Adja hozzá a következő kódrészletet a `Main()` metódushoz a `DownloadFile()` metódus meghívásához.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>Lásd még:
* [Fiókkezelési műveletek a Data Lake Store-ban a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Store .NET SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
