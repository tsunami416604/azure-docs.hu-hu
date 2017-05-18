Az Adatkezelővel gyűjteményeket hozhat létre és adatokat adhat hozzá az adatbázishoz. 

1. Az Azure Portalon kattintson a navigációs menü **Adatkezelő** elemére. 
2. Az Adatkezelő panelen kattintson az **Új gyűjtemény** elemre, majd töltse ki a lapot az alábbi információk alapján.

    ![Adatkezelő az Azure Portalon](./media/cosmosdb-create-collection/azure-cosmosdb-data-explorer.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|Elemek|Az új adatbázis azonosítója. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Gyűjtemény azonosítója|Teendők|Az új gyűjtemény azonosítója. A gyűjtemények nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra.
    Tárkapacitás| Rögzített méretű (10 GB)|Ne módosítsa az alapértelmezett értéket. Ez az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Ne módosítsa az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.
    Partíciókulcs|/userid|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciókulcs kiválasztása fontos a nagy teljesítményű gyűjtemények létrehozásához. Erről bővebben a [Particionálásra tervezés](../articles/cosmos-db/partition-data.md#designing-for-partitioning) c. részben olvashat.    



3. Miután kitöltötte az űrlapot, kattintson az **OK** elemre.