1. Egy új böngészőablakban, jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Kattintson a **új** > **adatbázisok** > **Azure Cosmos DB**.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **új fiók** lapján adja meg az új Azure Cosmos DB fiók beállításait. 
 
    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet a Azure Cosmos DB fiók azonosításához. Mivel *documents.azure.com* rendszer hozzáfűzi az azonosítója, majd való a kapcsolódási pontot létrehozni, használja a egy egyedi, de azonosítható.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint 3–50 karakter hosszúságúnak kell lennie.
    API|Cassandra|Az API-t határozza meg a fiók létrehozásához. Az Azure Cosmos DB biztosít öt API-k az alkalmazás igényeinek leginkább megfelelő: SQL (a dokumentum-adatbázis), Gremlin (graph adatbázis), a mongodb-Protokolltámogatással (a dokumentum-adatbázis), Azure Table és Cassandra, minden egyes igénylő jelenleg egy külön fiókot. <br><br>Válassza ki **Cassandra** , mert a gyors üzembe helyezés lekérdezhető CQL szintaxis használatával wide-oszlop adatbázist létrehozásához.<br><br>Ha Cassandra (wide-oszlop) csak akkor jelenik meg a listában, akkor kell [csatlakozni alkalmazása](../articles/cosmos-db/cassandra-introduction.md#sign-up-now) a Cassandra API programot.<br><br> [További információ a Cassandra API](../articles/cosmos-db/cassandra-introduction.md)|
    Előfizetés|*Az Ön előfizetése*|Válassza ki a Azure Cosmos DB fiókhoz használni kívánt Azure-előfizetéshez. 
    Erőforráscsoport|*Itt adhatja meg a egyedi névvel fent ismertetett azonosítója*|Adjon meg egy új erőforráscsoport nevet a fiókjához. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*Válassza ki a felhasználók a legközelebb eső régiót*|Válassza ki a földrajzi helyet, ahol Azure Cosmos DB fiókja üzemeltetéséhez. A felhasználók számára, hogy biztosítsa a leggyorsabb hozzáférést az adatok legközelebbi helyet használja.
    Rögzítés az irányítópulton | Válassza ezt: | Válassza ezt a jelölőnégyzetet, hogy az új adatbázis-fiókja hozzáadódik a portál irányítópultján egyszerűen hozzáférhetnek.

    Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure Cosmos DB új fiók panele](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-2.png)

4. A fiók létrehozásának folyamata eltart néhány percig. Számítógépfiók létrehozása közben a portál irányítópultján jeleníti meg a **telepítése Azure Cosmos DB** csempére.

    ![Az Azure portál értesítések csempe](./media/cosmos-db-create-dbaccount-cassandra/deploying-cosmos-db.png)

    Ha a fiók létrejött, a **Gratulálunk! A Azure Cosmos DB-fiók létrejött** lap is megjelenik. 

