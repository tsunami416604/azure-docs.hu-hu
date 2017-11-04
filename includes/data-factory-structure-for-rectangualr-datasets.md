## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Adja meg a struktúra definíciójának téglalap alakú adatkészletek
A struktúra a szakasz az adatkészletek JSON egy **választható** téglalap alakú táblákhoz (a sorok és oszlopok) szakaszt, és a tábla oszlopait gyűjteményét tartalmazza. A struktúra szakasz típuskonverziók vagy olyan típussal kapcsolatos információk, vagy ez az oszlop-hozzárendelések fogja használni. A következő szakaszok ismertetik részletesebben ezeket a szolgáltatásokat. 

Mindegyik oszlop tartalmaz a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát. Tekintse meg a típus átalakítások című szakaszt több részletek kapcsolatban, hogy mikor kell hogy típussal kapcsolatos információk megadása |Nem |
| Kulturális környezet |.NET-alapú kulturális környezet, használandó típus van megadva, és Datetime vagy Datetimeoffset .NET-típusa. Alapértelmezett érték "en-us". |Nem |
| Formátumban |Formázó karakterlánc, használandó típus van megadva, és Datetime vagy Datetimeoffset .NET-típusa. |Nem |

A következő példában három oszlopok felhasználói azonosítóját, nevét és lastlogindate táblának a struktúra szakasz JSON.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Használja az alábbi útmutatást, ha "szerkezeti" információval és tartalmának a **struktúra** szakasz.

* **A strukturált adatforrások** , hogy az adatok séma- és típusú információk tárolása az adatokat mozgatná (forrás például SQL Server, Oracle, az Azure tábla stb.), a "structure" szakasz olyan formában adja meg, csak akkor, ha azt szeretné, valamint hajthatja végre oszlopleképezés adott forrás adott oszlop szerepel a fogadó és a nevek oszlopok nem azonosak (lásd az alábbi oszlop leképezése részben). 
  
    Fent említett, a típus adatainak megadása nem kötelező "structure" szakaszában. A strukturált források típusinformációt már elérhető adatkészlet-definícióban az adattárban részeként, akkor nem tartalmazhat típusinformációt elvégzését indokló, hogy a "structure" szakasz.
* **Az olvasási adatforrások (kifejezetten az Azure blob) séma** adatok tárolására nem tárolja az adatokat bármely séma vagy típus információ választhat. Az ilyen típusú adatforrások a "structure" a következő 2 esetekben kell tartalmaznia:
  * Szeretné oszlop leképezése.
  * A másolási tevékenység során a forrás adatkészlet esetén megadhatja a "structure" írja be az adatokat, és adat-előállító fogja használni a típus adatainak való átalakításra a fogadó natív típust. Lásd: [helyezze át az adatokat, és az Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) cikkében találja.

### <a name="supported-net-based-types"></a>Támogatott. A NET-alapú típusok
Adat-előállítót a következő CLS szabványnak megfelelő .NET-alapú típusú értékek a "structure" típusú adatokat ad olvasási adatforrások, például az Azure blob-séma támogatja.

* Int16
* Int32 
* Int64
* Egyetlen
* Dupla
* Decimális
* Byte]
* logikai érték
* Karakterlánc 
* GUID
* Dátum és idő
* datetimeoffset
* Időtartomány 

A dátum és idő & Datetimeoffset opcionálisan kiegészítheti a "nyelv" & "formátum" karakterlánc a egyéni dátum/idő karakterlánc elemzése megkönnyítése érdekében. Tekintse meg a típus átalakítás az alábbi minta.

