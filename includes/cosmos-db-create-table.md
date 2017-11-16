Most már használhatja a Data Explorer eszközt az Azure portálon adatbázis és tábla létrehozásához. 

1. Kattintson a **adatkezelő** > **új tábla**. 
    
    A **táblázat hozzáadása** területen jelenik meg a képernyő jobb szélén, görgessen jobbra látható szeretne.

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Az a **táblázat hozzáadása** lapján adja meg az új táblázat beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Táblaazonosító|minta tábla|Az új tábla azonosítója. A táblák nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Tárkapacitás| Rögzített méretű (10 GB)|Módosítsa a beállítást **rögzített (10 GB-os)**. Ez az érték az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Módosítsa az átviteli sebesség 400 kérelemegység (RU/mp) másodpercenként. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.

    Kattintson az **OK** gombra.

    Új adatbázis és tábla adatkezelő jeleníti meg.

    ![Az Azure-portál Data Explorer, az új adatbázis és gyűjtemény](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)