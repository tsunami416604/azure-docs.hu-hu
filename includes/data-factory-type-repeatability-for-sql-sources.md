## <a name="repeatability-during-copy"></a>Másolás során az ismételhetőség
Amikor adatmásolásra más adatokat az Azure SQL és SQL Server tárolja egy kell ismételhetőség nem kívánt eredmények elkerülése érdekében szem előtt tartani. 

Adat másolása az Azure SQL és SQL Server-adatbázist, amikor a másolási tevékenység lesz alapértelmezés szerint a fogadó tábla adatkészlet HOZZÁFŰZÉS alapértelmezés szerint. Például ha másol adatokat egy adatforrásból CSV (vesszővel tagolt értékek) fájl két rekordot az Azure SQL és SQL Server-adatbázist tartalmazó, ez az a tábla néz ki:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Tegyük fel, hogy hibákat talált a forrásfájl, és a le cső mennyiség 2 4 a forrásfájl frissítve. Ha újra futtatja az adatszelet az adott időszak, látni fogja a két új rekordot hozzáfűzi az Azure SQL és SQL Server-adatbázist. Az alábbi feltételezi, hogy egyetlen sem az oszlopok a tábla rendelkezik-e az elsődleges kulcshoz tartozó korlátozás.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Ennek elkerülése érdekében meg kell adnia UPSERT szemantika egyik kihasználva az alábbiakban említett 2 mechanizmusok alatt.

> [!NOTE]
> A szelet újból futtatható automatikusan az Azure Data Factoryban a megadott újrapróbálkozási szabályzatának megfelelően.
> 
> 

### <a name="mechanism-1"></a>1 mechanizmus
Kihasználhatja **sqlWriterCleanupScript** tulajdonság először karbantartási művelet végrehajtásához a szelet futtatásakor. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

A karbantartási parancsprogramot lenne hajtható végre első egy adott szelet, amely lenne törölheti az adatait, hogy a szelet megfelelő SQL-táblát másolása során. A tevékenység később szúrja be az adatokat az SQL-táblába. 

Ha a szelet most újra futtatni, akkor megtalálja a mennyiség frissül, mint a keresett.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Tegyük fel, a strukturálatlan ablakmosó rekordot akkor távolítja el az eredeti csv-fájlból. Majd ismét futtatni a szeletet előállítására a következő eredményt: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Jelenleg nincs semmi kellett elvégezni. A másolási tevékenység lefutott a karbantartási parancsprogramot, törölheti az adott szeletre vonatkozó megfelelő adatait. Majd a bemeneti olvasni (amely majd tárolt rekordok csak 1) a CSV-t és a táblába beszúrt. 

### <a name="mechanism-2"></a>2 mechanizmus
> [!IMPORTANT]
> sliceIdentifierColumnName jelenleg nem támogatott az Azure SQL Data warehouse-hoz. 

Egy másik mechanizmust ismételhetőség érdekében egy dedikált oszlop egyik (**sliceIdentifierColumnName**) a cél táblában. Ebben az oszlopban annak érdekében, hogy a forrás- és legyen naprakész szinkronizált Azure Data Factory által volna használható. Ez a módszer rugalmasságot biztosít a cél SQL-tábla sémájának definiálásához vagy módosítása esetén működik. 

Ez az oszlop használni kívánt Azure Data Factory által ismételhetőség célokra, és a folyamat során az Azure Data Factory nem hajt végre a séma módosítása a táblához. Ez a módszer használatának módja:

1. Adja meg egy oszlop bináris típusú (32) a cél SQL-táblát. Ez az oszlop a korlátozások nélkül kell lennie. Nevet Ez az oszlop, "ColumnForADFuseOnly" Ebben a példában.
2. Használja az a másolási tevékenység az alábbiak szerint:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Az Azure Data Factory fel ezt az oszlopot a szükséges forrás és cél maradjon szinkronizált megfelelően. Ez az oszlop értékeit kell nem használható az adott környezeten kívül a felhasználó által. 

1 mechanizmus hasonlóan, a másolási tevékenység automatikusan az először törölje az adatokat a célhelyről az SQL-táblát az adott szeletre vonatkozó, és futtassa a másolási tevékenység általában az adatok beszúrásához forrásból a célra, hogy a szelet. 

