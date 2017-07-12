Az Adatkezelővel gyűjteményeket hozhat létre és adatokat adhat hozzá az adatbázishoz. 

1. Az Azure Portalon kattintson a bal oldali navigációs menü **Adatkezelő (előzetes verzió)** elemére. 

2. Az **Adatkezelő (előzetes verzió)** panelen kattintson az **Új gyűjtemény** elemre, majd töltse ki a lapot az alábbi információk alapján:

    ![Az Azure Portal Adatkezelő panelje](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|Feladatok|Az új adatbázis azonosítója. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt.
    Gyűjtemény azonosítója|Elemek|Az új gyűjtemény azonosítója. A gyűjtemények nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra.
    Tárkapacitás| Rögzített méretű (10 GB)|Használja az alapértelmezett értéket. Ez az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Használja az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.
    kérelemegység/m|Ki|Ne módosítsa az alapértelmezett értéket. Ha később spiky munkaterhelések kezelésére van szüksége, bekapcsolhatja a [kérelemegység/m](../articles/cosmos-db/request-units-per-minute.md) funkciót.
    Partíciókulcs|/kategória|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciókulcs kiválasztása fontos a nagy teljesítményű gyűjtemények létrehozásához. Erről bővebben a [Particionálásra tervezés](../articles/cosmos-db/partition-data.md#designing-for-partitioning) részben olvashat.    
3. Miután végzett az űrlappal, kattintson az **OK** gombra.