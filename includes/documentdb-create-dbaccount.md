1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  Az ugrósávon kattintson az **Új**, majd az **Adatok + tárolás**, és végül az **Azure DocumentDB (NoSQL)** elemre.

    ![Képernyőfelvétel az Azure Portalról, a További szolgáltatások és a DocumentDB (NoSQL) menüpont kiemelve](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. Az **Új fiók** panelen adja meg a DocumentDB-fiók kívánt beállításait.

    ![Képernyőfelvétel az Új DocumentDB panelről](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


    - Az **Azonosító** mezőben adja meg a DocumentDB-fiókot azonosító nevet.  Az **azonosító** érvényesítése után egy zöld pipa jelenik meg az **Azonosító** mezőben. Az **Azonosító** mező értéke lesz az állomásnév az URI-n belül. Az **azonosító** kizárólag kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és legalább 3, de legfeljebb 50 karakter lehet. Figyelje meg, hogy a rendszer hozzáfűzi a *documents.azure.com* címet a választott végpontnévhez, és így létrehozza a DocumentDB-fiók végpontját.

    - Az **Előfizetés** mezőben válassza ki a DocumentDB-fiókhoz használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer alapértelmezés szerint azt választja ki.

    - Az **Erőforráscsoport** területen válasszon ki vagy hozzon létre egy erőforráscsoportot a DocumentDB-fiókhoz.  Alapértelmezés szerint új erőforráscsoport jön létre. További információk: [Using the Azure portal to manage your Azure resources](../articles/azure-portal/resource-group-portal.md) (Az Azure Portal használata az Azure-erőforrások kezeléséhez).

    - A **Hely** beállítás segítségével megadhatja a földrajzi helyet, ahol a DocumentDB-fiókot üzemeltetni szeretné. 
    
    - Ha kényelmes hozzáférést szeretne a fiókjához és a jövőben létrehozandó erőforrásaihoz, jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.  

4.  A DocumentDB-fiók beállításainak konfigurálása után kattintson a **Létrehozás** gombra. Az üzembe helyezési folyamat állapotát a kezdőpult alján követheti nyomon.  
    ![Képernyőfelvétel a kezdőpult Létrehozás csempéjéről – online adatbázis-készítő](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  

    A folyamat állapotát az értesítésközpontban is ellenőrizheti.  

    ![Adatbázisok gyors létrehozása – képernyőfelvétel az értesítésközpontról, ahol nyomon követhető a DocumentDB-fiók létrehozásának folyamata](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Képernyőfelvétel az értesítésközpontról, amelyben az látszik, hogy a DocumentDB-fiók sikeresen létrejött, és telepítve lett egy erőforráscsoportba – online adatbázis-készítő értesítése](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  A létrehozás után a DocumentDB-fiók (az alapértelmezett beállításokkal) azonnal készen áll a használatra. Vegye figyelembe, hogy a DocumentDB-fiók alapértelmezett konzisztenciája a **Munkamenet**.  Az alapértelmezett konzisztenciát az erőforrásmenü **Alapértelmezett konzisztencia** elemére kattintva módosíthatja. További információ a DocumentDB által kínált konzisztenciaszintekről: [Konzisztenciaszintek a DocumentDB-ben](../articles/azure-portal/resource-group-portal.md).

    ![Képernyőfelvétel az Erőforráscsoport panelről – alkalmazás fejlesztésének megkezdése](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Képernyőfelvétel a Konzisztenciaszint panelről – munkamenet-konzisztencia](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[DocumentDB-fiók létrehozása]: #Howto
[Következő lépések]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md



<!--HONumber=sep16_HO1-->


