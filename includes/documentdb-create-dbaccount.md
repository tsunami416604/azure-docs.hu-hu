1. Egy új ablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az ugrósávon kattintson az **Új**, majd az **Adatbázisok**, és végül az **NoSQL (DocumentDB)** elemre.
   
   ![Képernyőfelvétel az Azure Portalról, a További szolgáltatások és a DocumentDB (NoSQL) menüpont kiemelve](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  
3. Az **Új fiók** panelen adja meg a DocumentDB-fiók kívánt beállításait.
   
    ![Képernyőfelvétel az Új DocumentDB panelről](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)
   
   * Az **Azonosító** mezőben adja meg a DocumentDB-fiókot azonosító nevet.  Az **azonosító** érvényesítése után egy zöld pipa jelenik meg az **Azonosító** mezőben. Az **Azonosító** mező értéke lesz az állomásnév az URI-n belül. Az **azonosító** kizárólag kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és legalább 3, de legfeljebb 50 karakter lehet. Figyelje meg, hogy a rendszer hozzáfűzi a *documents.azure.com* címet a választott végpontnévhez, és így létrehozza a DocumentDB-fiók végpontját.
   * A **NoSQL API** mezőben válassza a **DocumentDB** elemet.  
   * Az **Előfizetés** mezőben válassza ki a DocumentDB-fiókhoz használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer alapértelmezés szerint azt választja ki.
   * Az **Erőforráscsoport** területen válasszon ki vagy hozzon létre egy erőforráscsoportot a DocumentDB-fiókhoz.  Alapértelmezés szerint új erőforráscsoport jön létre. További információk: [Az Azure Portal használata az Azure-erőforrások kezeléséhez](../articles/azure-portal/resource-group-portal.md).
   * A **Hely** beállítás segítségével megadhatja a földrajzi helyet, ahol a DocumentDB-fiókot üzemeltetni szeretné. 
4. A DocumentDB-fiók beállításainak konfigurálása után kattintson a **Létrehozás** gombra. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg az értesítési központot.  
   
   ![Adatbázisok gyors létrehozása – képernyőfelvétel az értesítési központról, ahol nyomon követhető a DocumentDB-fiók létrehozásának folyamata](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![Képernyőfelvétel az értesítésközpontról, amelyben az látszik, hogy a DocumentDB-fiók sikeresen létrejött, és telepítve lett egy erőforráscsoportba – online adatbázis-készítő értesítése](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)
5. A létrehozás után a DocumentDB-fiók (az alapértelmezett beállításokkal) azonnal készen áll a használatra. Az alapértelmezett beállítások áttekintéséhez kattintson a **NoSQL (DocumentDB)** ikonra az ugrósávon, majd kattintson az új fiókra, és végül az erőforrásmenü **Alapértelmezett konzisztencia** elemére.

   ![Képernyőfelvétel, amely az Azure DocumentDB-adatbázisfiók megnyitásának módját mutatja az Azure Portalon](./media/documentdb-create-dbaccount/azure-documentdb-database-open-account-portal.png)  

   A DocumentDB-fiók alapértelmezett konzisztenciája a **Munkamenet**.  Egy másik elérhető konzisztenciabeállítás kiválasztásával módosíthatja az alapértelmezett konzisztenciát. További információ a DocumentDB által kínált konzisztenciaszintekről: [Konzisztenciaszintek a DocumentDB-ben](../articles/documentdb/documentdb-consistency-levels.md).

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


<!--HONumber=Feb17_HO2-->


