## <a name="repeatability-during-copy"></a>Ismételhetőség másolása során
Amikor az adatok másolását az Azure SQL/SQL-kiszolgálón más adatokból tárolja egy kell ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. 

Adat másolása az Azure SQL-/ SQL Server-adatbázis, ha a másolási tevékenység során lesz alapértelmezés szerint a fogadó tábla adatkészlet APPEND alapértelmezés szerint. Például ha az adatok másolása CSV (vesszővel tagolt értékek) fájl adatforrás az Azure SQL-/ SQL Server Database két rekordokat tartalmazó, ez az a táblázat néz:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hiba található a forrásfájl és a mennyiséget le cső 2 4 a forrásfájl frissítése. Az adatszelet az adott időszakra újra futtatni, ha talál fűzött Azure SQL-/ SQL Server-adatbázis két új bejegyzést. Az alábbiakban azt feltételezi, hogy az oszlopok a tábla egyik sincs elsődleges kulcs megszorítását.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ennek elkerülése érdekében meg kell adnia UPSERT szemantikáját egyikét, ami az alábbiakban említett 2 mechanizmusok alatt.

> [!NOTE]
> A szelet futtatható újra automatikusan az Azure Data Factory megadott újrapróbálkozási házirend szerint.
> 
> 

### <a name="mechanism-1"></a>1 mechanizmus
Kihasználhatja **sqlWriterCleanupScript** tulajdonság először törlési művelet végrehajtásához a szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A karbantartási parancsprogramot volna hajtható végre első példányát egy adott szelet, amely az adatokat törölni, hogy a szelet megfelelő SQL-táblából során. A tevékenység ezt követően szúrja be az adatokat a SQL táblázatba. 

Ha a szelet most újra futtatni, akkor megkeresi a mennyiség frissíteni, mivel a szükséges.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, a strukturálatlan szélvédőmosó rekordot a rendszer eltávolítja az eredeti csv a. Futtassa újból a szeletet volna eredményt a következő: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nincs új kellett elvégezni. A másolási tevékenység futott a karbantartási parancsprogramot a megfelelő adatokat, hogy a szelet törlése. Ezután a bemeneti olvasni a fürt megosztott kötetei szolgáltatás (amelyeket majd mindössze 1 bejegyzés) és a táblába beszúrt. 

### <a name="mechanism-2"></a>2 mechanizmus
> [!IMPORTANT]
> sliceIdentifierColumnName jelenleg nem támogatott az Azure SQL Data Warehouse. 

Azzal, hogy a kijelölt oszlop van egy másik mechanizmust ismételhetőség eléréséhez (**sliceIdentifierColumnName**) a cél tábla. Ebben az oszlopban szeretne győződjön meg arról, a forrás és cél maradnak szinkronizált Azure Data Factory által használni. Ezt a módszert akkor működik, ha módosításával vagy a cél SQL tábla sémáját definiáló rugalmasságot. 

Ebben az oszlopban által az Azure Data Factory ismételhetőség célból használják, és a folyamat Azure Data Factory nem teszik séma módosításait a táblába. Ez a megközelítés használatának módja:

1. Adja meg a cél SQL táblázat típusú oszlop bináris (32). Ebben az oszlopban megkötés kell lennie. Ehhez a példához tegyük nevezze ebben az oszlopban "ColumnForADFuseOnly".
2. Használhatja a másolási tevékenység az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Az Azure Data Factory feltölti az oszlop szerint annak érdekében, hogy a forrás és cél maradnak szinkronizált igényét. Az oszlop értékeinek nem használható ebben a környezetben kívül a felhasználó által. 

1 mechanizmus hasonlóan másolási tevékenység során automatikusan az először törölje az adatokat az SQL-tábla érkező adott szeletek, és futtassa a a másolási tevékenység során általában illesztendő adatok forrásból, hogy a szelet célját. 

