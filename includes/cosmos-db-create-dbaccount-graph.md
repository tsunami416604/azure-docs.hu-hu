1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali panelen kattintson az **Új**, az **Adatbázisok**, majd az **Azure Cosmos DB** elemre, végül a **Létrehozás** parancsra.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg az Azure Cosmos DB-fiók kívánt beállításait. 

    Az Azure Cosmos DB használata esetén négy programozási modell közül választhat: Gremlin (Graph), MongoDB, SQL (DocumentDB) vagy Tábla (kulcs-érték). Jelenleg mindegyikhez külön fiókra van szükség.
       
    Ebben a rövid útmutatóban a Graph API használatával fogunk programozni, tehát a **Gremlin (gráf)** lehetőséget fogja választani az űrlap kitöltésekor. Ha katalógusalkalmazásból származó dokumentumadatokkal, kulcs/érték (tábla) típusú adatokkal vagy MongoDB-alkalmazásból migrált adatokkal dolgozik, vegye figyelembe, hogy az Azure Cosmos DB magas rendelkezésre állású, globálisan elosztott adatbázis-szolgáltatási platformot tud biztosítani az összes alapvető fontosságú alkalmazáshoz.

    Töltse ki az **Új fiók** panel mezőit. Ehhez használja segítségként az alábbi képernyőképen szereplő információkat – az Ön értékei eltérhetnek a képernyőképen láthatóktól.
 
    ![Az Azure Cosmos DB új fiók panele](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Egyedi érték*|Az Azure Cosmos DB-fiókot azonosító egyedi név. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót. Az azonosító csak kisbetűkből, számokból és a kötőjel (-) karakterből állhat, valamint legalább 3, és legfeljebb 50 karakter hosszú lehet.
    API|Gremlin (gráf)|Ebben a cikkben a [Graph API](../articles/cosmos-db/graph-introduction.md) használatával programozunk.|
    Előfizetés|*Az Ön előfizetése*|Az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetés. 
    Erőforráscsoport|*Megegyezik az azonosítóval*|A fiók új erőforráscsoport-neve. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*A felhasználókhoz legközelebb eső régió*|Az a földrajzi hely, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Válassza ki a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
5. A felső eszköztáron kattintson az **Értesítések** ikonra ![az értesítési ikon](./media/cosmos-db-create-dbaccount-graph/notification-icon.png) az üzembe helyezési folyamat monitorozásához.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-graph/notification.png)

6.  Amikor az Értesítések ablak azt jelzi, hogy az üzembe helyezés sikeresen megtörtént, zárja be az értesítési ablakot, és nyissa meg az új fiókot az Irányítópult **Minden erőforrás** csempéjéről. 

    ![DocumentDB-fiók az Összes erőforrás csempén](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)