1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Új**, az **Adatbázisok**, majd az **Azure Cosmos DB** elemre, végül a **Létrehozás** parancsra.
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Az **Új fiók** panelen adja meg az Azure Cosmos DB-fiók kívánt beállításait. 

    Az Azure Cosmos DB, akkor válasszon egyet a négy programozási modell: Gremlin (diagramhoz), a MongoDB, az SQL és a tábla (kulcs-érték). 
       
    Ebben a rövid útmutatóban a MongoDB API használatával fogunk programozni, tehát a **MongoDB** lehetőséget fogja választani az űrlap kitöltésekor. Ha azonban közösségi média gráfadataival, a katalógusalkalmazásból származó dokumentumadatokkal vagy kulcs/érték (tábla) típusú adatokkal dolgozik, vegye figyelembe, hogy az Azure Cosmos DB magas rendelkezésre állású, globálisan elosztott adatbázis-szolgáltatási platformot tud biztosítani az összes alapvető fontosságú alkalmazáshoz.

    Adja meg az adatokat az **Új fiók** panelen a táblázatban látható információk alapján.
 
    ![Képernyőfelvétel az Új Azure Cosmos DB panelről](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)
   
    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Egyedi érték*|Az Azure Cosmos DB-fiók azonosításához választott egyedi név. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót. Az azonosító csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet.
    API|MongoDB|Az API-t határozza meg a fiók létrehozásához. Az Azure Cosmos DB biztosít öt API-k az alkalmazás igényeinek leginkább megfelelő: SQL (a dokumentum-adatbázis), Gremlin (graph adatbázis), a mongodb-Protokolltámogatással (a dokumentum-adatbázis), Azure Table és Cassandra, minden egyes igénylő jelenleg egy külön fiókot. <br><br>Válassza ki **MongoDB** , mert a gyors üzembe helyezés lekérdezhető MongoDB használatával dokumentum adatbázist létrehozásához.<br><br>[További információ a MongoDB API](../articles/cosmos-db/mongodb-introduction.md)|
    Előfizetés|*Az Ön előfizetése*|Az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetés. 
    Erőforráscsoport|*Megegyezik az azonosítóval*|A fiók új erőforráscsoport-neve. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*A felhasználókhoz legközelebb eső régió*|Az a földrajzi hely, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Válassza ki a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

4. A fiók létrehozásához kattintson a **Létrehozás** gombra.
5. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

    ![Központi telepítés elindítva értesítés](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Amikor befejeződött a telepítés, nyissa meg az új fiókot az Összes erőforrás csempén. 

    ![Az összes erőforrás csempe az Azure Cosmos DB-fiók](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
