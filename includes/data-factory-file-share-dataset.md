## <a name="fileshare-dataset-type-properties"></a>A fájlmegosztási dataset típus tulajdonságokat.
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](../articles/data-factory/v1/data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében.

A **typeProperties** szakaszban nem egyezik az adatkészlet egyes típusú. A dataset típusra vonatkozó adatokat tartalmazza. A typeProperties szakasz egy adatkészlet típusú **fájlmegosztási** adatkészlet tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál.<br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter is alkalmazható egy bemeneti fájlmegosztási az adatkészlethez. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Például folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](#specifying-textformat), [Json formátumban](#specifying-jsonformat), [az Avro formátum](#specifying-avroformat), [Orc formátum](#specifying-orcformat), és [Parquet formátum](#specifying-parquetformat) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**; és a támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [tömörítés megadó](#specifying-compression) szakasz. |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli mód használata. A bináris mód és a hamis értéket ASCII igaz. Alapértelmezett érték: igaz. A tulajdonság csak akkor használható, típusú társított kapcsolódószolgáltatás-típus esetén: FTP-kiszolgáló. |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.
>
>

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használatával
Az előző szakaszban említett, megadhat egy dinamikus folderPath idő adatsorozat adatok partitionedBy fájlnevét. A Data Factory makrók és a rendszer változó SliceStart, egy adott adatszelet logikai időtartamnak jelző SliceEnd azt is megteheti.

Idő adatsorozat adatkészleteket, az ütemezés és a szeletek kapcsolatos további tudnivalókért lásd: [létrehozása adatkészletek](../articles/data-factory/v1/data-factory-create-datasets.md), [ütemezés & végrehajtási](../articles/data-factory/v1/data-factory-scheduling-and-execution.md), és [létrehozása folyamatok](../articles/data-factory/v1/data-factory-create-pipelines.md) cikkeket.

#### <a name="sample-1"></a>1. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} adat-előállító rendszer változó SliceStart (YYYYMMDDHH) formátumban megadott érték helyére. A szelet kezdete a SliceStart hivatkozik. A folderPath nem azonos az egyes szeletek. Példa: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Ebben a példában év, hónap, nap és SliceStart idején ki kell olvasni a külön változókat, amelyek folderPath és a fájlnév tulajdonság.
