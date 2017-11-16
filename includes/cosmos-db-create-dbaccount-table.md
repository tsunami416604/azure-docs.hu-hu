1. Egy új böngészőablakban, jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. A bal oldali menüben kattintson az **Új**, az **Adatbázisok**, majd az **Azure Cosmos DB** elemre, végül a **Létrehozás** parancsra. 
   
   ![Képernyőfelvétel az Azure portálról, a További szolgáltatások és az Azure Cosmos DB menüpont kiemelve](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Az a **új fiók** lapján adja meg az új Azure Cosmos DB fiók beállításait. 
 
    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    ID (Azonosító)|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet a Azure Cosmos DB fiók azonosításához. A rendszer a *documents.azure.com* utótaggal egészíti ki a megadott azonosítót az URI létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.<br><br>Az azonosító csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, valamint 3–50 karakter hosszúságúnak kell lennie.
    API|Azure-tábla|Az API-t határozza meg a fiók létrehozásához. Az Azure Cosmos DB biztosít öt API-k az alkalmazás igényeinek leginkább megfelelő: SQL (a dokumentum-adatbázis), Gremlin (graph adatbázis), a mongodb-Protokolltámogatással (a dokumentum-adatbázis), Azure Table és Cassandra, minden egyes igénylő jelenleg egy külön fiókot.<br><br>Válassza ki **Azure Table** , mert a gyors üzembe helyezés, amely együttműködik a tábla API a tábla létrehozásához.<br><br>[További információ a tábla API](../articles/cosmos-db/table-introduction.md) |
    Előfizetés|*Itt adhatja meg a egyedi névvel fent ismertetett azonosítója*|Válassza ki a Azure Cosmos DB fiókhoz használni kívánt Azure-előfizetéshez. 
    Erőforráscsoport|*Megegyezik az azonosítóval*|Adjon meg egy új erőforráscsoport nevet a fiókjához. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet. 
    Hely|*Válassza ki a felhasználók a legközelebb eső régiót*|Válassza ki a földrajzi helyet, ahol Azure Cosmos DB fiókja üzemeltetéséhez. A felhasználók számára, hogy biztosítsa a leggyorsabb hozzáférést az adatok legközelebbi helyet használja.
    Georedundancia engedélyezése| Hagyja üresen | Ez az adatbázis egy replikált verziót egy második (párosított) régióban hoz létre. Hagyja üresen a mezőt.  
    Rögzítés az irányítópulton | Válassza ezt: | Válassza ezt a jelölőnégyzetet, hogy az új adatbázis-fiókja hozzáadódik a portál irányítópultján egyszerűen hozzáférhetnek.

    Ezt követően kattintson a **Create** (Létrehozás) gombra.  

    ![Képernyőfelvétel az Új Azure Cosmos DB panelről](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-2.png)

4. A fiók létrehozásának folyamata eltart néhány percig. Során fiók létrehozása a portál megjeleníti a **telepítése Azure Cosmos DB** csempére.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-table/deploying-cosmos-db.png)

    Ha a fiók létrejött, a **Gratulálunk! A Azure Cosmos DB-fiók létrejött** lap is megjelenik.
