---
title: Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításával (előzetes verzió) | Microsoft dokumentumok
description: A lekérdezésgyorsítás (előzetes verzió) használatával adatok egy részhalmazát kérheti le a tárfiókból.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771845"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításával (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a lekérdezésgyorsítás (előzetes verzió) az adatok egy részhalmazának lekéréséhez a tárfiókból. 

A lekérdezésgyorsítás (előzetes verzió) az Azure Data Lake Storage új lehetősége, amely lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak azokat az adatokat kérik le, amelyek egy adott művelet végrehajtásához szükségesk. További információ: [Azure Data Lake Storage Query Acceleration (preview)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> A lekérdezésgyorsítási szolgáltatás nyilvános előzetes verzióban érhető el, és a Kanadai Közép- és Franciaország-középrégiókban érhető el. A korlátozások áttekintéséhez olvassa el az [Ismert problémák](data-lake-storage-known-issues.md) című cikket. Az előnézetbe való regisztráláshoz tekintse meg [ezt az űrlapot.](https://aka.ms/adls/qa-preview-signup)  

## <a name="prerequisites"></a>Előfeltételek

### <a name="net"></a>[.NET](#tab/dotnet)

- Az Azure Storage eléréséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Általános **célú v2-es** tárfiók. lásd: [Tárfiók létrehozása](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Az Azure Storage eléréséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Általános **célú v2-es** tárfiók. lásd: [Tárfiók létrehozása](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verzió.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Ez a cikk feltételezi, hogy az Apache Maven használatával javaprojektet hozott létre. A Project Apache Maven használatával történő létrehozásáról a [Beállítás című témakörben](storage-quickstart-blobs-java.md#setting-up)látható.
  
---

## <a name="install-packages"></a>Csomagok telepítése 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Töltse le a lekérdezésgyorsítási csomagokat. A csomagokat tartalmazó tömörített .zip fájl a következő [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)hivatkozással szerezhető be: . 

2. Bontsa ki a fájl tartalmát a projektkönyvtárba.

3. Nyissa meg a projektfájlt (*.csproj)* egy szövegszerkesztőben, \<és\> adja hozzá ezeket a csomaghivatkozásokat a Project elemhez.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Állítsa vissza az előzetes SDK-csomagokat. Ez a példa parancs a `dotnet restore` parancs használatával visszaállítja az előzetes SDK-csomagokat. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Állítsa vissza az összes többi függőséget a nyilvános NuGet tárházból.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Hozzon létre könyvtárat a projekt gyökerében. A gyökérkönyvtár a **pom.xml** fájlt tartalmazó könyvtár.

   > [!NOTE]
   > A cikkben szereplő példák feltételezik, hogy a könyvtár neve **lib**.

2. Töltse le a lekérdezésgyorsítási csomagokat. A csomagokat tartalmazó tömörített .zip fájl a következő [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)hivatkozással szerezhető be: . 

3. Bontsa ki a .zip fájlban lévő fájlokat a létrehozott könyvtárba. A példánkban ez a könyvtár neve **lib**. 

4. Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Utasítások hozzáadása


### <a name="net"></a>[.NET](#tab/dotnet)

Adja `using` hozzá ezeket az állításokat a kódfájl tetejéhez.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

A lekérdezés gyorsítása CSV- és Json-formátumú adatokat olvas be. Ezért győződjön meg arról, hogy a használni kívánt CSV- vagy Json-elemzési kódtárak hoz hozzá. A cikkben megjelenő példák egy CSV-fájlt elemeznek a NuGet-en elérhető [CsvHelper-könyvtár](https://www.nuget.org/packages/CsvHelper/) használatával. Ezért ezeket `using` az állításokat hozzáadjuk a kódfájl tetejéhez.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

A cikkben bemutatott példák összeállításához ezeket `using` az állításokat is hozzá kell adnia.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Adja `import` hozzá ezeket az állításokat a kódfájl tetejéhez.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Adatok beolvasása szűrő használatával

Az SQL segítségével megadhatja a sorszűrő predikátumok és oszlop vetületek a lekérdezés gyorsítási kérelem. A következő kód lekérdezi a tárolóban lévő CSV-fájlt, és `Hemingway, Ernest`minden olyan adatsort visszaad, amelyben a harmadik oszlop megegyezik az értékkel. 

- Az SQL-lekérdezésben `BlobStorage` a kulcsszó a lekérdezett fájlt jelöli.

- Az oszlophivatkozások az `_N` első oszlop helyeként vannak `_1`megadva. Ha a forrásfájl fejlécsort tartalmaz, akkor a fejlécsorban megadott névvel hivatkozhat az oszlopokra. 

### <a name="net"></a>[.NET](#tab/dotnet)

Az async `BlobQuickQueryClient.QueryAsync` metódus elküldi a lekérdezést a lekérdezés gyorsítása API-t, majd streameli az eredményeket az alkalmazásba [streamobjektumként.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

A `BlobQuickQueryClient.openInputStream()` metódus elküldi a lekérdezést a lekérdezés gyorsítási API-t, `InputStream` majd továbbítja az eredményeket az alkalmazásnak, mint egy objektum, amely lehet olvasni, mint bármely más InputStream objektum.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Adott oszlopok beolvasása

Az eredményeket az oszlopok egy részhalmazára is leveheti. Így csak az adott számítás végrehajtásához szükséges oszlopokat kell beolvasnia. Ez javítja az alkalmazások teljesítményét és csökkenti a költségeket, mivel kevesebb adat kerül átvitelre a hálózaton keresztül. 

Ez a kód `PublicationYear` csak az adatkészlet összes könyvének oszlopát olvassa be. A forrásfájl fejlécsorának adatait is felhasználja a lekérdezés oszlopainak hivatkozásához.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

A következő kód egyesíti a sorszűrést és az oszlopvetületeket ugyanabba a lekérdezésbe. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>További lépések

- [Lekérdezésgyorsítás igénylési képernyő](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure Data Lake Storage lekérdezésgyorsítás (előzetes verzió)](data-lake-storage-query-acceleration.md)
- [Lekérdezésgyorsítás SQL nyelvi hivatkozás (előzetes verzió)](query-acceleration-sql-reference.md)
- Lekérdezésgyorsítás REST API-hivatkozása
