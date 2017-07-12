1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali panelen kattintson az **Új**, az **Adatbázisok**, majd az **Azure Cosmos DB** elemre.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg az Azure Cosmos DB-fiók kívánt beállításait. 

    Az Azure Cosmos DB használata esetén négy programozási modell közül választhat: Gremlin (gráf), MongoDB, SQL (DocumentDB) és a tábla (kulcs-érték). 
    
    Ebben a rövid útmutatóban a DocumentDB API használatával fogunk programozni, tehát az űrlap kitöltésekor az **SQL (DocumentDB)** lehetőséget kell választania. Ha azonban közösségi média alkalmazás gráfadataival, kulcs-érték (tábla) típusú adatokkal vagy MongoDB-alkalmazásból áttelepített adatokkal dolgozik, vegye figyelembe, hogy az Azure Cosmos DB magas rendelkezésre állású, globálisan elosztott adatbázis-szolgáltatási platformot tud biztosítani az összes alapvető fontosságú alkalmazáshoz.

    Töltse ki az **Új fiók** panel mezőit a következő képernyőfelvételen látható információk alapján. A fiók beállításakor használjon egyedi értékeket, amelyek eltérnek a képernyőképen láthatóaktól. 
 
    ![Az Új Azure Cosmos DB-fiók panel](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Egyedi érték*|Az Azure Cosmos DB-fiók azonosításához választott egyedi név. A rendszer a *documents.azure.com* karakterlánccal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót. Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint legalább 3, és legfeljebb 50 karakter hosszú lehet.
    API|SQL (DocumentDB)|Ebben a cikkben a [DocumentDB API](../articles/documentdb/documentdb-introduction.md) használatával fogunk programozni.|
    Előfizetés|*Az Ön előfizetése*|Az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetés. 
    Erőforráscsoport|*Megegyezik az azonosítóval*|A fiók új erőforráscsoport-neve. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*A felhasználókhoz legközelebb eső régió*|Az a földrajzi hely, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Válassza ki a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
5. A felső eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Ha befejeződött a telepítés, nyissa meg az új fiókot az **Összes erőforrás** csempén. 

    ![A DocumentDB-fiók az Összes erőforrás csempén](./media/cosmos-db-create-dbaccount/all-resources.png)
 
