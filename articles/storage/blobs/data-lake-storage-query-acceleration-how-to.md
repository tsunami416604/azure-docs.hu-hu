---
title: Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítással (előzetes verzió) | Microsoft Docs
description: A lekérdezési gyorsítás (előzetes verzió) használatával lekérheti az adatok egy részhalmazát a Storage-fiókból.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465864"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítással (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a lekérdezési gyorsítást (előzetes verzió) a Storage-fiókból származó adatok egy részhalmazának lekérésére. 

A lekérdezési gyorsítás (előzetes verzió) egy új képesség a Azure Data Lake Storage számára, amely lehetővé teszi az alkalmazások és az elemzési keretrendszerek számára, hogy az adatok feldolgozását az adott művelet végrehajtásához szükséges adatok beolvasásával jelentősen optimalizálja. További információ: [Azure Data Lake Storage lekérdezési gyorsítás (előzetes verzió)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> A lekérdezési gyorsítási funkció nyilvános előzetes verzióban érhető el, és a közép-Kanada középső régiójában és Közép-Franciaországban található. A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt az űrlapot](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Előfeltételek

### <a name="net"></a>[.NET](#tab/dotnet)

- Az Azure Storage eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- **Általános célú v2-** es Storage-fiók. Lásd: [Storage-fiók létrehozása](../common/storage-quickstart-create-account.md).

- [.net SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Az Azure Storage eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- **Általános célú v2-** es Storage-fiók. Lásd: [Storage-fiók létrehozása](../common/storage-quickstart-create-account.md).

- A [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verziója.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Ez a cikk azt feltételezi, hogy az Apache Maven használatával létrehozott egy Java-projektet. Az Apache Maven használatával történő projekt létrehozásával kapcsolatos példát itt talál: [beállítás](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Csomagok telepítése 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Töltse le a lekérdezési gyorsítási csomagokat. A következő hivatkozással lehet beolvasni a csomagokat tartalmazó tömörített. zip fájlt: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Bontsa ki a fájl tartalmát a projekt könyvtárába.

3. Nyissa meg a projektfájlt (*. csproj*) egy szövegszerkesztőben, és adja hozzá a csomag hivatkozásait az \<Project\> elemhez.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Állítsa vissza az előzetes verziójú SDK-csomagokat. Ez a példa parancs visszaállítja az előzetes verziójú SDK-csomagokat a `dotnet restore` paranccsal. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Az összes többi függőség visszaállítása a nyilvános NuGet adattárból.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Hozzon létre egy könyvtárat a projekt gyökerében. A gyökérkönyvtár a **Pom. XML** fájlt tartalmazó könyvtár.

   > [!NOTE]
   > A cikkben szereplő példák azt feltételezik, hogy a könyvtár neve **lib**.

2. Töltse le a lekérdezési gyorsítási csomagokat. A következő hivatkozással lehet beolvasni a csomagokat tartalmazó tömörített. zip fájlt: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Bontsa ki a. zip fájlban található fájlokat a létrehozott könyvtárba. A példánkban a könyvtár neve **lib**. 

4. Nyissa meg a *Pom. XML* fájlt a szövegszerkesztőben. Adja hozzá az alábbi függőségi elemeket a függőségek csoportjához. 

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

Adja hozzá ezeket `using` az utasításokat a fájl elejéhez.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

A lekérdezési gyorsítás lekéri a CSV-és a JSON-formátumú adatait. Ezért ügyeljen arra, hogy a használni kívánt CSV-vagy JSON-elemzési kódtárak használatával adjon hozzá utasításokat. A cikkben megjelenő példák egy CSV-fájlt elemeznek a NuGet-on elérhető [CsvHelper](https://www.nuget.org/packages/CsvHelper/) könyvtár használatával. Ezért ezeket az `using` utasításokat a fájl elejéhez adja.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

A cikkben bemutatott példák fordításához is hozzá kell adnia ezeket az `using` utasításokat is.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Adja hozzá ezeket `import` az utasításokat a fájl elejéhez.

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

## <a name="retrieve-data-by-using-a-filter"></a>Adatlekérdezés szűrő használatával

Az SQL segítségével megadhatja a sorcsoport-predikátumokat és az oszlopok kivetítéseit egy lekérdezési gyorsítási kérelemben. Az alábbi kód egy CSV-fájlt kérdez le a Storage szolgáltatásban, és visszaadja az összes olyan adatsort, amelyben a harmadik oszlop megfelel az értéknek `Hemingway, Ernest` . 

- Az SQL-lekérdezésben a kulcsszó a `BlobStorage` lekérdezni kívánt fájl jelölésére szolgál.

- Az oszlopok hivatkozásai az `_N` első oszlop helyeként vannak megadva `_1` . Ha a forrásfájl tartalmaz egy fejlécet, akkor az oszlopokat a fejlécsorban megadott név alapján lehet megtekinteni. 

### <a name="net"></a>[.NET](#tab/dotnet)

Az aszinkron metódus `BlobQuickQueryClient.QueryAsync` elküldi a lekérdezést a lekérdezési gyorsítási API-nak, majd [stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) -objektumként továbbítja az eredményeket az alkalmazásnak.

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
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
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

A metódus `BlobQuickQueryClient.openInputStream()` elküldi a lekérdezést a lekérdezési gyorsítási API-nak, majd az eredményeket visszaküldi az alkalmazásnak olyan `InputStream` objektumként, amely más InputStream-objektumhoz hasonlóan olvasható.

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

Az eredményeket az oszlopok egy részhalmazára is szűkítheti. Így csak az adott számítás végrehajtásához szükséges oszlopokat kéri le. Ez javítja az alkalmazások teljesítményét, és csökkenti a költségeket, mivel kevesebb adat kerül át a hálózaton keresztül. 

Ez a kód csak az `PublicationYear` adatkészletben lévő összes könyv oszlopát kérdezi le. Emellett a forrásfájl fejléc sorában található információkat is használja a lekérdezésben lévő oszlopokra.


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

A következő kód kombinálja a sorok szűrését és az oszlopok kivetítését ugyanabba a lekérdezésbe. 

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

## <a name="next-steps"></a>Következő lépések

- [Lekérdezési gyorsítás beléptetésének űrlapja](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage lekérdezési gyorsítás (előzetes verzió)](data-lake-storage-query-acceleration.md)
- [A lekérdezés gyorsításának SQL nyelvi referenciája (előzetes verzió)](query-acceleration-sql-reference.md)
