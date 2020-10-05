---
title: A hozzáférési hitelesítő adatok biztonságossá tétele az Azure szinapszis Analytics Apache Spark társított szolgáltatásaival
description: Ez a cikk azokat a fogalmakat ismerteti, amelyekkel biztonságosan integrálható a szinapszis-elemzések Apache Spark a társított szolgáltatások és a jogkivonat-függvénytár használatával más szolgáltatásokkal
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 90e7297236994650e0820e883c94a98b29c49fb7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91249416"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>A hitelesítő adatok biztonságossá tétele a társított szolgáltatásokkal a TokenLibrary
A külső forrásokból származó adatokhoz való hozzáférés gyakori minta. Ha a külső adatforrás engedélyezi a névtelen hozzáférést, akkor előfordulhat, hogy a kapcsolatot hitelesítő adatokkal, titkos kulccsal vagy kapcsolati karakterlánccal kell védenie.  

Az Azure szinapszis Analytics társított szolgáltatásokat biztosít az integrációs folyamat egyszerűsítéséhez, ha egy társított szolgáltatásban vagy Azure Key Vaultban tárolja a kapcsolati adatokat. A társított szolgáltatás létrehozása után az Apache Spark hivatkozhat a társított szolgáltatásra, hogy alkalmazza a kapcsolati adatokat a kódban. 

További információ: [társított szolgáltatások](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> Ahhoz, hogy a munkaterületen lévő Azure Data Lake Storage fájljai hozzáférjenek a HRE, ezért a TokenLibrary nem kell használnia. 


## <a name="prerequisite"></a>Előfeltétel
* Társított szolgáltatás – létre kell hoznia egy társított szolgáltatást a külső adatforráshoz, és hivatkoznia kell a társított szolgáltatásra a jogkivonat-tárból. További információ a [társított szolgáltatásokról](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Kapcsolódás a szinapszis munkaterületen kívüli ADLS Gen2hoz

A szinapszis egy integrált társított szolgáltatási élményt biztosít a Azure Data Lake Storage Gen2 számára.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>A jogkivonat-függvénytár használata

Ha más társított szolgáltatásokhoz szeretne csatlakozni, közvetlenül is meghívhatja a TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 A kapcsolati karakterlánc lekéréséhez használja a <b>getConnectionString</b> függvényt, és adja át a <b>társított szolgáltatás nevét</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Adott értékek elemzése a <i>kulcs = érték</i> párokból a kapcsolódási karakterláncban, például: 

<i>DefaultEndpointsProtocol = https; AccountName = \<AccountName> ; AccountKey =\<AccountKey></i>

használja a <b>getConnectionStringAsMap</b> függvényt, és adja át a kulcsot az érték visszaadásához.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>További lépések

- [Írás az SQL-készletbe](./synapse-spark-sql-pool-import-export.md)

