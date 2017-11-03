Az Azure Portal Adatkezelő eszközét mostantól adatbázis és gyűjtemény létrehozására is használhatja. 

1. Kattintson a **adatkezelő** > **új gyűjtemény**. 
    
    A **gyűjtemény hozzáadása** területen jelenik meg a képernyő jobb szélén, görgessen jobbra látható szeretne.

    ![Az Azure-portál Data Explorer, a gyűjtemény hozzáadása panel](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Az a **gyűjtemény hozzáadása** lapján adja meg az új gyűjtemény beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|Feladatok|Adja meg *feladatok* , az új adatbázis nevét. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt.
    Gyűjtemény azonosítója|Elemek|Adja meg *elemek* az új gyűjtemény néven. Gyűjteményazonosítók kell adatbázisnevek megegyező karakter követelmények vonatkoznak.
    Tárkapacitás| Rögzített méretű (10 GB)|Módosítsa a beállítást **rögzített (10 GB-os)**. Ez az érték az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Módosítsa az átviteli sebesség 400 kérelemegység (RU/mp) másodpercenként. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.
    Partíciókulcs|/kategória|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciókulcs kiválasztása fontos a nagy teljesítményű gyűjtemények létrehozásához. Erről bővebben a [Particionálásra tervezés](../articles/cosmos-db/partition-data.md#designing-for-partitioning) részben olvashat.

    Kattintson az **OK** gombra.

    Új adatbázis és gyűjtemény adatkezelő jeleníti meg.

    ![Az Azure-portál Data Explorer, az új adatbázis és gyűjtemény](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)