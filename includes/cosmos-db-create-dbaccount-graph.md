1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** lehetőségre.
   
   ![Az Azure portál "Adatbázis" ablak](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** oldalon adja meg az új Azure Cosmos DB-fiók beállításait. 

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint 3–50 karakter hosszúságúnak kell lennie.
    API|Gremlin (gráf)|A létrehozni kívánt fiók típusát az API határozza meg. Az adott alkalmazás igényei szerint az Azure Cosmos DB a következő öt API-t biztosítja: SQL (dokumentum-adatbázis), Gremlin (gráfadatbázis), MongoDB (dokumentum-adatbázis), Azure-tábla, Cassandra. Jelenleg mindegyikhez külön fiókra van szükség. <br><br>Válassza ki **Gremlin (diagramhoz)** , mert a gyors üzembe helyezés diagramját, amelyek lekérdezhető Gremlin szintaxis használatával van létrehozásához.<br><br>[További információ a Graph API-val](../articles/cosmos-db/graph-introduction.md)
    Előfizetés|*Az Ön előfizetése*|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|*Adja meg a fenti azonosító esetében használt egyedi nevet*|Adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*Válassza ki a felhasználóihoz legközelebb eső régiót*|Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Georedundancia engedélyezése| Hagyja üresen | Ezzel egy második (párosított) régióban létrehozta az adatbázis replikált verzióját. Hagyja üresen.  
    Rögzítés az irányítópulton | Válassza ezt: | Jelölje be ezt a jelölőnégyzetet, így az új adatbázisfiók a könnyű hozzáférés érdekében megtalálható lesz a portál irányítópultján.

    Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure Cosmos DB új fiók panele](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg a portálon megjelenítendő a **Gratulálunk! A Azure Cosmos DB-fiók létrejött** lap.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)