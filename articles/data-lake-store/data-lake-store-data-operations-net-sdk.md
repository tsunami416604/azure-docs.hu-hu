---
title: '.NET SDK: Fájlrendszer-műveletek az Azure Data Lake Storage Gen1 szolgáltatáson'
description: Használja az Azure Data Lake Storage Gen1 .NET SDK fájlrendszer műveletek Data Lake Storage Gen1, például mappák létrehozása, stb.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638901"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Fájlrendszer-műveletek a Data Lake Storage Gen1 rendszeren a .NET SDK használatával

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Ebben a cikkben megtudhatja, hogyan hajthatja végre a fájlrendszer-műveleteket a Data Lake Storage Gen1-en a .NET SDK használatával. A fájlrendszer műveletei közé tartozik a mappák létrehozása a Data Lake Storage Gen1 fiókban, fájlok feltöltése, fájlok letöltése stb.

A .NET SDK használatával a Data Lake Storage Gen1 fiókkezelési műveleteinek módjáról a [Data Lake Storage Gen1 fiókkezelési műveleteinek .NET SDK használatával című](data-lake-store-get-started-net-sdk.md)témakörben talál útmutatást.

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio 2013-as vagy újabb.** Az ebben a cikkben található utasítások a Visual Studio 2019-et használják.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók.** A fiók létrehozásáról az [Azure Data Lake Storage Gen1 című témakörben talál útmutatást.](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>.NET-alkalmazás létrehozása

A [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

1. A Visual Studio programban válassza a **Fájl** menü **Új**, majd **A Project**parancsot.
1. Válassza **a Console App (.NET Framework)** lehetőséget, majd a **Tovább**gombot.
1. A Project név `CreateADLApplication` **mezőbe**írja be a beírt lehetőséget, majd válassza a **Létrehozás lehetőséget.**
1. Adja hozzá a NuGet-csomagokat a projekthez.

   1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
   1. A **NuGet Csomagkezelő** lapon győződjön meg arról, hogy **a Csomag forrás** a **nuget.org.** Ellenőrizze azt is, hogy a **Kiadás előtti felvétel jelölőnégyzet** be van-e jelölve.
   1. Keresse meg és telepítse az alábbi NuGet-csomagokat:

      * `Microsoft.Azure.DataLake.Store`- Ez a cikk a v1.0.0-t használja.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- Ez a cikk a v2.3.1-et használja.

      Zárja be a **NuGet-csomagkezelőt**.

1. Nyissa meg a **Program.cs** fájlt, törölje a meglévő kódot, majd illessze be az alábbi utasításokat, hogy hivatkozásokat a névterekre való hivatkozásokat tudjon felvenni.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Deklarálja a változókat az alább látható módon, majd adja meg a helyőrzők értékét. Arról is győződjön meg, hogy az itt megadott helyi elérési út és fájlnév létezik a számítógépen.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

A cikk további szakaszaiban láthatja, hogyan használhatja a rendelkezésre álló .NET metódusokat olyan műveletek hez, mint a hitelesítés, a fájlfeltöltés stb.

## <a name="authentication"></a>Hitelesítés

* Az alkalmazás végfelhasználói hitelesítése a [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md)című témakörben található.
* Az alkalmazás szolgáltatás-szolgáltatás hitelesítése a [Szolgáltatás-szolgáltatás hitelesítés e-kiszolgáló s1](data-lake-store-service-to-service-authenticate-net-sdk.md)használatával a Data Lake Storage Gen1 használatával című témakörben található.

## <a name="create-client-object"></a>Ügyfélobjektum létrehozása

A következő kódrészlet létrehozza a Data Lake Storage Gen1 fájlrendszer ügyfélobjektumot, amely a szolgáltatásnak érkező kérelmek kiadására szolgál.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Fájl és könyvtár létrehozása

Adja hozzá a következő kódrészletet az alkalmazásához. Ez a kódrészlet hozzáad egy fájlt és minden olyan szülőkönyvtárat, amely nem létezik.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz

A következő kódrészlet adatokat fűz egy meglévő fájlhoz a Data Lake Storage Gen1 fiókban.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Fájl beolvasása

A következő kódrészlet beolvassa egy fájl tartalmát a Data Lake Storage Gen1-ben.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Fájltulajdonságok lekérdezése

Az alábbi kódrészlet egy fájlhoz vagy könyvtárhoz társított tulajdonságokat adja vissza.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

A módszer `PrintDirectoryEntry` definíciója a [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)található minta részeként érhető el.

## <a name="rename-a-file"></a>Fájl átnevezése

A következő kódrészlet átnevez egy meglévő fájlt egy Data Lake Storage Gen1 fiókban.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Könyvtárak enumerálása

A következő kódrészlet egy Data Lake Storage Gen1 fiók könyvtárait sorolja fel.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

A módszer `PrintDirectoryEntry` definíciója a [GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)található minta részeként érhető el.

## <a name="delete-directories-recursively"></a>Könyvtárak rekurzív törlése

A következő kódrészlet rekurzívmódon töröl egy könyvtárat és annak minden alkönyvtárát.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Példák

Íme néhány minta, amely bemutatja a Data Lake Storage Gen1 fájlrendszer SDK használatát.

* [Alapszintű minta a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Speciális minta a GitHubon](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Lásd még

* [A Data Lake Storage Gen1 számlakezelési műveletei a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK–hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>További lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
