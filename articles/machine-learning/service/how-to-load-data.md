---
title: Adatok betöltése az az Azure Machine Learning Data Prep SDK – Python
description: Ismerje meg az adatok Azure Machine Learning Data Prep SDK-val. Különböző típusú bemeneti adatok betöltése, adja meg a fájl adattípusok és a paraméterek, vagy az SDK az intelligens olvasó funkciók használatához automatikus észlelése a fájl típusa.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 436ff9d318dc311efe27352a8b2ac91cfb5be618
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221325"
---
#<a name="load-and-read-data-with-azure-machine-learning"></a>Betölteni, és az Azure Machine Learning adatokat olvasni.

Használja a [az Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) különböző típusú bemeneti adatok betöltéséhez. 

Az adatok betöltésének két módszer van:
+ Adja meg az adattípus fájl és a paraméterek
+ Az SDK az intelligens olvasó funkciók használatához automatikus észlelése a fájl típusa

## <a name="use-text-line-data"></a>Szöveg sor adatok használata 
A legegyszerűbb módon betölteni az adatokat egyik szöveg sorokként elolvasni.

A következő mintakód:
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Vonal|
|----|-----|
|0|Dátum \| \| minimális hőmérséklet \| \| maximális hőmérséklet|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|
|3|2015-07-3 \| \| -7.0 \| \| 10,5|
|4|2015-07-4 \| \| -5.5 \| \| 9.3|

Miután az adatok be lett töltve, a teljes adatkészlet pandas DataFrame kérheti le.

A következő mintakód:
```python
df = dataflow.to_pandas_dataframe()
df
```
Példa a kimenetre:
||Vonal|
|----|-----|
|0|Dátum\| \| minimális hőmérséklet\| \| maximális hőmérséklet|
|1|2015-07-1\| \| 4.1\| \| 10.0|
|2|2015-07-2\| \| 0,8\| \| 10.8|
|3|2015-07-3\| \| 7.0\| \| 10,5|
|4|2015-07-4\| \| 5.5-ös\| \| 9.3|

## <a name="use-csv-data"></a>CSV-adatok használata
Tagolt fájlok olvasásakor hagyhatja, hogy az alapul szolgáló modul kikövetkeztetni a elemzési paramétereket (például egy elválasztó, kódolás, e használni a fejlécek, stb.) ahelyett, hogy nekik. Ebben a példában próbálnak meg olvasni a fájlt csak a hely megadásával. 

A következő mintakód:
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

Példa a kimenetre:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale megye|10171002158| |
|2|ALABAMA|1|101710|Hale megye|10171002162| |
|3|ALABAMA|1|101710|Hale megye|10171002156| |
|4|ALABAMA|1|101710|Hale megye|10171000588|2|

Megadhatja a paraméterek egyike egy általában azért olvassák el a fájlokat a kihagyandó sorok száma. A következő kód használatával kiszűrhetők az ismétlődő sort.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

Példa a kimenetre:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale megye|10171002158|29|
|1|ALABAMA|1|101710|Hale megye|10171002162|40 |
|2|ALABAMA|1|101710|Hale megye|10171002156| 43|
|3|ALABAMA|1|101710|Hale megye|10171000588|2|
|4|ALABAMA|1|101710|Hale megye|10171000589|23 |

Következő lépésként, tekintse meg az oszlopok adattípusát.
A következő mintakód:
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Sajnos az oszlopok összes érkezett vissza karakterláncként. Ez azért, mert alapértelmezés szerint az Azure Machine Learning Data Prep SDK nem változtatja meg az adatok típusát. Az adatforráshoz, hogy olvasásakor szöveges fájlt, így az SDK olvassa be az összes értékeket karakterláncként. Ebben a példában azonban szeretnénk elemezni az as-számokat számokat tartalmazó oszlop. Ehhez a inference_arguments paraméter current_culture is beállíthatja.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Az oszlopok számos helyesen észlelt as-számokat és a típusuk float64 értékre van állítva. Az Adatbetöltési kész a teljes adatkészlet pandas DataFrame kérheti le.
A következő mintakód:
```python
df = dataflow.to_pandas_dataframe()
df
```

Példa a kimenetre:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale megye|1.017100e + 10|49.0|
|1|ALABAMA|Hale megye|1.017100e + 10|40.0|
|2|ALABAMA|Hale megye|1.017100e + 10|43.0|
|3|ALABAMA|Hale megye|1.017100e + 10|2.0|
|4|ALABAMA|Hale megye|1.017100e + 10|23.0|

## <a name="use-excel-data"></a>Excel-adatok használata
Az Azure Machine Learning Data Prep SDK tartalmaz egy `read_excel` függvény betöltése Excel-fájlokat. A következő mintakód:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

Példa a kimenetre:
||1. oszlop|Column2|Column3|4. oszlopig|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Vas, IVB|60000000.0|Sikeres keresés|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19.58333|17.91667|
|1|Cabo York|Vas, IIIAB|58200000.0|Sikeres keresés|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76.13333|-64.93333|
|2|Campo del Cielo|Vas, IAB-felügyeleti csoport|50000000.0|Sikeres keresés|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27.46667|-60.58333|
|3|Diablo kanyon|Vas, IAB-felügyeleti csoport|30000000.0|Sikeres keresés|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35.05000|-111.03333|
|4|Armanty|Vas, IIIE|28000000.0|Sikeres keresés|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47.00000|88.00000|

Az első lap, az Excel-fájlt töltött. Ugyanaz az eredmény explicit módon kívánja betölteni a lap nevének megadásával érheti el. Ha azt szeretné, a második lap betöltése helyett, megadhatja annak neve argumentumként. Példa:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

Példa a kimenetre:
||1. oszlop|Column2|Column3|4. oszlopig|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|None|None|None|None|None|None|None|None|None|
|1|None|None|None|None|None|None|None|None|None|
|2|None|None|None|None|None|None|None|None|None|
|3|Rang|Beosztás|Studio|Világszerte|Hazai / %|1. oszlop|Tengerentúli / %|Column2|Év ^|
|4|1|Profilkép|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Amint láthatja, a tábla a második táblázatban kellett fejlécek és három üres sor. A függvény argumentumának módosítani kell. Példa:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

Példa a kimenetre:
||Rang|Beosztás|Studio|Világszerte|Hazai / %|1. oszlop|Tengerentúli / %|Column2|Év ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Profilkép|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|
|2|3|A Avengers Marvel a|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|Harry Potter és a Deathly Hallows 2. rész|VB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Szüneteltetve|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="use-fixed-width-data-files"></a>Rögzített szélességű adatfájlok használata
Rögzített szélességű fájlok esetén eltolások listáját adja meg. Az első oszlop érték például eltolási 0. Kezdőpont:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

Példa a kimenetre:
||010000|99999|HAMIS NORVÉGIA|NEM|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|HAMIS NORVÉGIA|NEM|NEM|ENSO||||
|1|010010|99999|JAN MAYEN|NEM|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NEM|NEM|||||
|3|010014|99999|SOERSTOKKEN|NEM|NEM|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NEM|NEM|ENBL|+61383|+005867|+03270|


Ha a fájlok a fejlécek, érdemes az első sor gyökérkönyvtárral adatokat. Átadásával `PromoteHeadersMode.NONE` a fejléc kulcsszó argumentum fejléc észlelési elkerüléséhez és a megfelelő adatokat. Példa:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

Példa a kimenetre:

||1. oszlop|Column2|Column3|4. oszlopig|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|HAMIS NORVÉGIA|NEM|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|HAMIS NORVÉGIA|NEM|NEM|ENSO||||
|2|010010|99999|JAN MAYEN|NEM|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NEM|NEM|||||
|4|010014|99999|SOERSTOKKEN|NEM|NEM|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NEM|NEM|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>SQL-adatok használata
Az Azure Machine Learning Data Prep SDK is betölthet adatokat az SQL Server-kiszolgálók. Jelenleg csak a Microsoft SQL Server támogatott.
SQL-kiszolgáló adatainak beolvasása, hozzon létre egy adatforrás-objektum, amely tartalmazza a kapcsolatadatokat. Példa:
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Amint láthatja, a-password paramétert `MSSQLDataSource` titkos objektum fogad el. Titkos objektum kétféleképpen kaphat:
-   Regisztrálja a végrehajtó motor a titkos kulcsot és annak értékét. 
-   A titkos kód létrehozása csak olyan azonosítót (hasznos, ha a titkos érték már regisztrálva van a végrehajtási környezetben található).

Miután létrehozott egy adatforrás-objektum, folytathatja az adatok olvasása. Példa:
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

Példa a kimenetre:
||Termékazonosító|Name (Név)|ProductNumber|Szín|StandardCost|ListPrice|Méret|Tömeg|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|ROWGUID|A ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL országúti váz – fekete, 58|FR-R92B-58|Fekete|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL országúti váz – piros, 58|FR-R92R-58|Piros|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport-100 sisak, piros|HL-U509-R|Piros|13.0863|34,99|None|None|35|33|2005-07-01-00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|
|3|708|Sport-100 sisak, fekete|HL-U509|Fekete|13.0863|34,99|None|None|35|33|2005-07-01-00:00:00 + 00:00|None|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000 + 00:00|
|4|709|Mountain Bike-os zokni, M|ÍGY-B909-M|Fehér|3.3963|9.50|M|None|27|18|2005-07-01-00:00:00 + 00:00|2006-06-30 00:00:00 + 00:00|None|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000 + 00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

Példa a kimenetre:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Az Azure Data Lake Storage használata
Nincsenek az SDK-t kétféle módon vásárolhatja meg az Azure Data Lake Storage eléréséhez szükséges OAuth-jogkivonatot:
-   A hozzáférési jogkivonat lekérése a legutóbbi bejelentkezési munkamenet a felhasználó Azure parancssori felület bejelentkezési azonosító
-   Egy egyszerű szolgáltatást (SP) és a egy tanúsítvány titkos kulcsot használja

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>A legfrissebb Azure CLI-munkamenetet a hozzáférési jogkivonat használata
A helyi gépén futtassa a következő parancsot:

> [!NOTE] 
> Ha a felhasználói fiók több Azure-bérlő tagjai, AAD URL-cím állomásnév formában adja meg a bérlő van szükség.


Példa:
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Egyszerű szolgáltatás létrehozása az Azure CLI-vel
Egyszerű szolgáltatás létrehozása az Azure CLI és a megfelelő tanúsítványt is használhatja. Egyszerű adott szolgáltatásnak a hatókör csak az Azure Data Lake Storage-fiók "dpreptestfiles"-ra csökken az olvasó, van konfigurálva.  Példa:
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Ez a parancs bocsát ki a `appId` és az elérési utat a tanúsítványfájlt (általában a home mappában). A .crt fájl tartalmazza a nyilvános tanúsítvány és a titkos kulcs PEM formátumban is.

Bontsa ki az ujjlenyomat:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Az ACL-t az Azure Data Lake Storage fájlrendszer konfigurálásához kövesse az ebben a példában az egyszerű szolgáltatás vagy a felhasználó objectId. Példa:
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Konfigurálása `Read` és `Execute` hozzáférés az Azure Data Lake Storage fájl rendszer, az ACL-t a fájlok és mappák esetében külön-külön konfigurálnia kell. Ez az az oka, hogy az az alapul szolgáló HDFS Hozzáférésvezérlésilista-modell nem támogatja az öröklést. Példa:
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
Használja a `adal` csomag (keresztül: `pip install adal`) hozhat létre a hitelesítési környezetet az MSFT a bérlőhöz, és OAuth hozzáférési jogkivonat beszerzése. ADLS, a jogkivonat a kérelemben szereplő erőforrás kell lennie a "https://datalake.azure.net", amely különbözik a legtöbb más Azure-erőforrások.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Honlap|Utca.|city|Megye|
|----|------|-----|----|----|----|----|
|0|1012063|A piaci társítása – Danville Kaledónia gazdák|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledónia|
|1|1011871|Stearns Homestead gazdálkodók ' piaci|http://Stearnshomestead.com |6975 ridge közúti|Parma|Cuyahoga|
|2|1011878|100 mérföld piaci|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. fő út gazdálkodók piaci|http://thetownofsixmile.wordpress.com/ |106 S. fő utca.|Hat lépést|||
|4|1010691|a 10th Steet közösségi gazdálkodók piac|http://agrimissouri.com/mo-grown/grodetail.php... |10 utca és nyárfa|Lamar|Barton|

## <a name="use-smart-reading"></a>Használja az "intelligens olvasási"

Az SDK az intelligens olvasási funkció használatával automatikus észlelése a fájl típusa.
