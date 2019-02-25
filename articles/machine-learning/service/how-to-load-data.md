---
title: 'Load: data prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Ismerje meg az adatok Azure Machine Learning Data Prep SDK-val. Különböző típusú bemeneti adatok betöltése, adja meg a fájl adattípusok és a paraméterek, vagy az SDK az intelligens olvasó funkciók használatához automatikus észlelése a fájl típusa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 2/22/2019
ms.custom: seodec18
ms.openlocfilehash: 0fe77a1093bec52c3786a9ae623a2d63e1ba82ce
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750939"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Betölteni, és az Azure Machine Learning adatokat olvasni.

Ebben a cikkben megismerheti az Azure Machine Learning Data Prep SDK használata az adatok különböző módszert. Az SDK dokumentációja, olvassa el a [áttekintése](https://aka.ms/data-prep-sdk). Az SDK támogatja a több adat adatfeldolgozási szolgáltatások, például:

* Elemzés paraméter következtetésekhez (kódolás, elválasztó, a fejlécek) rendelkező több fájltípus betölthető
* Típus-átalakítás következtetésekhez használatával fájl betöltése közben
* MS SQL Server és az Azure Data Lake Storage kapcsolat támogatása

Az alábbi táblázat egy kijelölt adatok betöltése a gyakori fájltípusokból használt funkciók.

| Fájltípus | Függvény | Hivatkozás |
|-------|-------|-------|
|Bármelyik|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Szöveg|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-lines-path--filepath--header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Rögzített szélességű|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Automatikusan az adatok betöltése

Adatok betöltése automatikusan a fájl típusa megadása nélkül, használja a `auto_read_file()` függvény. A fájl- és a olvasásához szükséges argumentumok típusú automatikusan vannak következtetni.

```python
import azureml.dataprep as dprep

dataflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Ez a függvény akkor hasznos, ha automatikus észlelése a fájl típusa, a kódolási és a egy kényelmes belépési pontot egy elemzési argumentumok. A függvény automatikusan is gyakran hajtanak végre tagolt adatok betöltésekor a következő lépéseket hajtja végre:

* Adatcsatornához és a elválasztó beállítása
* A fájl elején üres rekordokat kihagyása
* Adatcsatornához és a fejlécsor beállítása

Azt is megteheti Ha tudja, hogy a fájlt, írja be a kívánt időben, és explicit módon szabályozhatja az elemzi azt szeretné, használja a fájl-specifikus funkciók.

## <a name="load-text-line-data"></a>Szöveg sor adatok betöltése

Egyszerű szöveges adatot olvas be egy adatfolyamot, használja a `read_lines()` választható paraméterek megadása nélkül.

```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```

||Vonal|
|----|-----|
|0|Dátum \| \| minimális hőmérséklet \| \| maximális hőmérséklet|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|


Miután a betöltött, futtassa a következő kódot az adatfolyamot objektum alakítható Pandas dataframe.

```python
pandas_df = dataflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Adatok betöltése CSV

Tagolt fájlok olvasásakor a az alapul szolgáló modul is kikövetkeztetni a elemzési paraméterek (elválasztó, kódolás, hogy kíván használni, fejlécek stb.). Futtassa a következő kódot próbálnak meg olvasni a CSV-fájl csak a hely megadásával.

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale megye|10171002158| |
|2|ALABAMA|1|101710|Hale megye|10171002162| |


Kizárandó sorok betöltése során, adja meg a `skip_rows` paraméter. Ez a paraméter (egy egy-alapú index használatával) a CSV-fájl csökkenő betöltése sorok kihagyja.

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale megye|10171002158|29|
|1|ALABAMA|1|101710|Hale megye|10171002162|40 |

Futtassa a következő kódot az oszlop adattípusait megjelenítéséhez.

```python
dataflow.head(1).dtypes
```
Kimenet:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Alapértelmezés szerint az Azure Machine Learning Data Prep SDK nem változik az adatok típusát. Az adatforrás is olvassa a szöveges fájlt, így az SDK olvassa be az összes értékeket karakterláncként. Ebben a példában a numerikus oszlopok as-számokat nelze analyzovat. Állítsa be a `inference_arguments` paramétert `InferenceArguments.current_culture()` automatikus kikövetkeztetni és oszloptípusának konvertálja a fájl olvasása közben.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes
```
Kimenet:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Több oszlop numerikus megfelelően rendszer és a típusuk értéke `float64`.

## <a name="use-excel-data"></a>Excel-adatok használata

Az SDK tartalmaz egy `read_excel()` függvény betöltése Excel-fájlokat. Alapértelmezés szerint a függvény betölti a munkafüzet első lapja. Egy adott lap betöltése definiálásához adja meg a `sheet_name` paraméter a munkalap neve karakterlánc értékét.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||1. oszlop|Column2|Column3|4. oszlopig|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|1|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|2|None|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|Nincsenek|None|
|3|Rang|Beosztás|Studio|Világszerte|Hazai / %|1. oszlop|Tengerentúli / %|Column2|Év ^|
|4|1|Profilkép|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

A kimenet mutatja, hogy az adatokat, a második táblázatban volt-e a fejlécek előtt három üres sor. A `read_excel()` függvény kihagyja a sorokat, és a fejlécek használata nem kötelező paramétereket tartalmaz. Futtassa a következő kódot, hagyja ki az első három sort, és a fejlécek a negyedik sor használja.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||Rang|Beosztás|Studio|Világszerte|Hazai / %|1. oszlop|Tengerentúli / %|Column2|Év ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Profilkép|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|

## <a name="load-fixed-width-data-files"></a>Rögzített szélességű adatok fájl betöltése

Loadfixed szélességű fájlok karakter eltolások listáját adja meg. Az első oszlop mindig feltételezi, hogy a nulla eltolás kezdőpont.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

||010000|99999|HAMIS NORVÉGIA|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|HAMIS NORVÉGIA|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


Fejléc észlelési elkerülése és a megfelelő adatok elemzése, át kell adnia `PromoteHeadersMode.NONE` , a `header` paraméter.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||1. oszlop|Column2|Column3|4. oszlopig|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|HAMIS NORVÉGIA|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|HAMIS NORVÉGIA|NO|NO|ENSO||||


## <a name="load-sql-data"></a>SQL-adatok betöltése

Az SDK-t is betölthet adatokat az SQL-forrás. Jelenleg csak a Microsoft SQL Server támogatott. SQL-kiszolgáló adatainak beolvasása, hozzon létre egy [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) objektum, amely tartalmazza a kapcsolat paramétereit. A password paramétert, `MSSQLDataSource` fogad egy [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) objektum. Titkos objektum kétféle módon hozhat létre:

* Regisztrálja a végrehajtó motor a titkos kulcsot és annak értékét.
* Csak a titkos kód létrehozása egy `id` (Ha a titkos érték már regisztrálva van a végrehajtási környezet) használatával `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Miután létrehozott egy adatforrás-objektum, folytassa a lekérdezés kimeneti adatokat olvasni.

```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||Termékazonosító|Name (Név)|ProductNumber|Szín|StandardCost|ListPrice|Méret|Tömeg|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|ROWGUID|A ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL országúti váz – fekete, 58|FR-R92B-58|Fekete|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL országúti váz – piros, 58|FR-R92R-58|Piros|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport-100 sisak, piros|HL-U509-R|Piros|13.0863|34,99|None|None|35|33|2005-07-01-00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|


## <a name="use-azure-data-lake-storage"></a>Az Azure Data Lake Storage használata

Nincsenek az SDK-t kétféle módon vásárolhatja meg az Azure Data Lake Storage eléréséhez szükséges OAuth-jogkivonatot:

* A hozzáférési jogkivonat lekérése a legutóbbi munkamenet a felhasználó Azure parancssori felület bejelentkezési azonosító
* Egy egyszerű szolgáltatást (SP) és a egy tanúsítvány titkos kulcsot használja

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>A legfrissebb Azure CLI-munkamenetet a hozzáférési jogkivonat használata

A helyi gépén futtassa a következő parancsot.

```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```

> [!NOTE]
> Ha a felhasználói fiók több Azure-bérlő tagjai, AAD URL-cím állomásnév formában adja meg a bérlő van szükség.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Egyszerű szolgáltatás létrehozása az Azure CLI-vel

Az Azure CLI használatával hozzon létre egy egyszerű szolgáltatást, és a megfelelő tanúsítványt. Egyszerű adott szolgáltatás konfigurált a `reader` szerepkört, a hatókör csak az Azure Data Lake Storage-fiók "dpreptestfiles"-ra csökken.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Ez a parancs bocsát ki a `appId` és az elérési utat a tanúsítványfájlt (általában a home mappában). A .crt fájl tartalmazza a nyilvános tanúsítvány és a titkos kulcs PEM formátumban is.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Az ACL-t az Azure Data Lake Storage fájlrendszer konfigurálásához használja a felhasználó objectId. Ebben a példában az egyszerű szolgáltatás szolgál.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Konfigurálása `Read` és `Execute` hozzáférés az Azure Data Lake Storage fájlrendszer, konfigurálnia az ACL-t a fájlok és mappák esetében külön-külön. Ez az az oka, hogy az az alapul szolgáló HDFS Hozzáférésvezérlésilista-modell nem támogatja az öröklést.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>OAuth hozzáférési jogkivonat beszerzése

Használja a `adal` csomag (`pip install adal`) hozhat létre a hitelesítési környezetet az MSFT a bérlőhöz, és OAuth hozzáférési jogkivonat beszerzése. ADLS, a jogkivonat a kérelemben szereplő erőforrás kell lennie a "https://datalake.azure.net", amely különbözik a legtöbb más Azure-erőforrások.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Webhely|Utca.|city|Megye|
|----|------|-----|----|----|----|----|
|0|1012063|A piaci társítása – Danville Kaledónia gazdák|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledónia|
|1|1011871|Stearns Homestead gazdálkodók ' piaci|http://Stearnshomestead.com |6975 ridge közúti|Parma|Cuyahoga|
|2|1011878|100 mérföld piaci|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. fő út gazdálkodók piaci|http://thetownofsixmile.wordpress.com/ |106 S. fő utca.|Hat lépést|||
|4|1010691|10 street közösségi gazdálkodók piac|https://agrimissouri.com/... |10 utca és nyárfa|Lamar|Barton|

## <a name="next-steps"></a>További lépések

* Lásd: az SDK [áttekintése](https://aka.ms/data-prep-sdk) tervezési minták és használati példák
* Tekintse meg az Azure Machine Learning Data Prep SDK [oktatóanyag](tutorial-data-prep.md) példa egy adott forgatókönyv megoldása