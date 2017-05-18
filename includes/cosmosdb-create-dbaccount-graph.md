1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Új**, az **Adatbázisok**, majd az **Azure Cosmos DB** elemre.
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmosdb-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg az Azure Cosmos DB-fiók kívánt beállításait. 

    Az Azure Cosmos DB használata esetén négy programozási modell közül választhat: Gremlin (gráf), MongoDB, SQL (DocumentDB) és tábla (kulcs-érték).  
       
    Ebben a rövid útmutatóban a Graph API használatával fogunk programozni, tehát a **Gremlin (gráf)** lehetőséget fogja választani az űrlap kitöltésekor. Ha azonban katalógusalkalmazásból származó dokumentumadatokkal, kulcs/érték (tábla) típusú adatokkal vagy MongoDB-alkalmazásból áttelepített adatokkal dolgozik, vegye figyelembe, hogy az Azure Cosmos DB magas rendelkezésre állású, globálisan elosztott adatbázis-szolgáltatási platformot tud biztosítani az összes alapvető fontosságú alkalmazáshoz.

    Adja meg az adatokat az Új fiók panelen a képernyőfelvételen látható információk alapján. A fiók beállítása során egyedi értékeket fog választani, vagyis az értékek nem fognak pontosan egyezni a képernyőképen szereplő adatokkal. 
 
    ![Képernyőfelvétel az Új Azure Cosmos DB panelről](./media/cosmosdb-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Egyedi érték*|Az Azure Cosmos DB-fiók azonosításához választott egyedi név. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót. Az azonosító csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet.
    API|Gremlin (gráf)|Ebben a cikkben a [Graph API](../articles/cosmos-db/graph-introduction.md) használatával fogunk programozni.|
    Előfizetés|*Az Ön előfizetése*|Az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetés. 
    Erőforráscsoport|*Megegyezik az azonosítóval*|A fiók új erőforráscsoport-neve. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*A felhasználókhoz legközelebb eső régió*|Az a földrajzi hely, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Válassza ki a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    App Service-csomag a Graph API-hoz|Új létrehozása – alapértelmezett S1|Válassza ki a Gremlin Graph-lekérdezéseket végrehajtó számítási erőforrásokat.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
5. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

    ![A Központi telepítés elindítva értesítés](./media/cosmosdb-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Amikor befejeződött a telepítés, nyissa meg az új fiókot az Összes erőforrás csempén. 

    ![A DocumentDB-fiók az Összes erőforrás csempén](./media/cosmosdb-create-dbaccount-graph/azure-documentdb-all-resources.png)