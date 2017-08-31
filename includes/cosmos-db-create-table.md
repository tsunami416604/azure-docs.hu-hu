Az Adatkezelővel táblákat hozhat létre és adatokat adhat hozzá az adatbázishoz. 

1. Az Azure Portalon kattintson a navigációs menü **Adatkezelő (előzetes verzió)** elemére. 
2. Az Adatkezelő panelen kattintson az **Új tábla** elemre, majd töltse ki a lapot az alábbi információk alapján.

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Táblaazonosító|minta tábla|Az új tábla azonosítója. A táblák nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Tárkapacitás| 10 GB|Ne módosítsa az alapértelmezett értéket. Ez az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Ne módosítsa az alapértelmezett értéket. Később lehetősége lesz növelni az [átviteli sebességet](../articles/cosmos-db/request-units.md) a késés csökkentése érdekében.

3. Miután kitöltötte az űrlapot, kattintson az **OK** elemre.