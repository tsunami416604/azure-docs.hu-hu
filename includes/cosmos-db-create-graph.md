Az Azure Portal Adatkezelő eszközét mostantól gráfadatbázisok létrehozására is használhatja. 

1. Az Azure portálon a bal oldali menüben válassza ki a **adatok kezelővel (előzetes verzió)**.

2. A **adatok kezelővel (előzetes verzió)**, jelölje be **új diagram**. Az alábbi információk segítségével, majd adja meg a lap:

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|sample-database|Az új adatbázisnak adja a *sample-database* nevet. Adatbázis nevének 1 és 255 karakter közé kell esnie, és nem tartalmazhat `/ \ # ?` vagy záró szóköz.
    Gráfazonosító|sample-graph|Az új gyűjteménynek adja a *sample-graph* nevet. A gráfok nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázis-azonosítókra.
    Tárkapacitás| 10 GB|Ne módosítsa az alapértelmezett értéket. Ez az adatbázis tárkapacitása.
    Teljesítmény|400 kérelemegység|Ne módosítsa az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.

3. Miután a rendszer kitölti az űrlapot, jelölje ki a **OK**.
