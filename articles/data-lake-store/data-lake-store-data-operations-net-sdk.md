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
ms.date: 11/02/2017
ms.author: nitinme
ms.openlocfilehash: a5d446986f810993d65c7e73eb95eeb2283c39a3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
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
A [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

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

      * `Microsoft.Azure.DataLake.Store` – Ez az oktatóanyag az 1.0.0-s verziót használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Ez az oktatóanyag a 2.3.1-es verziót használja.
    
    Zárja be a **NuGet-csomagkezelőt**.

6. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Deklarálja a változókat az alább látható módon, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy az itt megadott helyi elérési út és fájlnév létezik a számítógépen.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; //Replace this value with the name of your existing Data Lake Store account.        
            }
        }

A cikk fennmaradó részéből megtudhatja, hogyan használhatja az elérhető .NET-metódusokat az olyan műveletek elvégzésére, mint a hitelesítés, a fájlok feltöltése stb.

## <a name="authentication"></a>Authentication

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Ügyfélobjektum létrehozása
A következő kódrészlet létrehozza a Data Lake Store fájlrendszeri ügyfélobjektumát, amely a szolgáltatásnak küldött kérések kiadására használatos.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Fájl és könyvtár létrehozása
Adja hozzá a következő kódrészletet az alkalmazásához. Ez a kódrészlet hozzáad egy fájlt, valamint minden még nem létező szülőkönyvtárat.

    // Create a file - automatically creates any parent directories that don't exist
    
    string fileName = "/Test/testFilename1.txt";
    using (var streamWriter = new StreamWriter(client.CreateFile(fileName, IfExists.Overwrite)))
    {
        streamWriter.WriteLine("This is test data to write");
        streamWriter.WriteLine("This is line 2");
    }

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz
Az alábbi kódrészlet adatokat fűz hozzá egy létező fájlhoz a Data Lake Store-fiókban.

    // Append to existing file
    using (var streamWriter = new StreamWriter(client.GetAppendStream(fileName)))
    {
        streamWriter.WriteLine("This is the added line");
    }

## <a name="read-a-file"></a>Fájl beolvasása
Az alábbi kódrészlet egy, a Data Lake Store-ban lévő fájl tartalmát olvassa be.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Fájltulajdonságok lekérdezése
Az alábbi kódrészlet egy fájlhoz vagy könyvtárhoz társított tulajdonságokat adja vissza.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

A `PrintDirectoryEntry` metódus meghatározása a [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) található minta részeként érhető el. 

## <a name="rename-a-file"></a>Fájl átnevezése
Az alábbi kódrészlet egy létező fájlt nevez át a Data Lake Store-fiókban.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Könyvtárak enumerálása
Az alábbi kódrészlet egy Data Lake Store-fiókban található könyvtárakat enumerál.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

A `PrintDirectoryEntry` metódus meghatározása a [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) található minta részeként érhető el.

## <a name="delete-directories-recursively"></a>Könyvtárak rekurzív törlése
Az alábbi kódrészlet rekurzív módon töröl egy könyvtárat és annak összes alkönyvtárát.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Példák
Íme néhány példa a Data Lake Store-fájlrendszer SDK-jának használatára.
* [Alapszintű példa a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Speciális példa a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Lásd még:
* [Fiókkezelési műveletek a Data Lake Store-ban a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Store .NET SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
