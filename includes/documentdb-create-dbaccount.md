1.  Jelentkezzen be az online a [Microsoft Azure portálra](https://portal.azure.com/).
2.  Az ugrósávon kattintson az **Új**, majd az **Adatok + tárolás**, és végül az **Azure DocumentDB** lehetőségre.

    ![Képernyőfelvétel az Azure portálról adatbázisok létrehozásához, kiemelve az Új gombot, a Létrehozás panel Adatok+ tárolás elemét, valamint az Adatok + tárolás panel Azure DocumentDB elemét.](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. Az **Új DocumentDB-fiók** panelen adja meg a DocumentDB-fiók kívánt beállításait.

    ![Képernyőfelvétel az Új DocumentDB panelről](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


    - Az **Azonosító** mezőben adja meg a DocumentDB-fiókot azonosító nevet.  Az **azonosító** érvényesítése után egy zöld pipa jelenik meg az **Azonosító** mezőben. Az **Azonosító** mező értéke lesz az állomásnév az URI-n belül. Az **azonosító** kizárólag kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és legalább 3, de legfeljebb 50 karakter lehet. Figyelje meg, hogy a rendszer hozzáfűzi a *documents.azure.com* címet a választott végpontnévhez, és így létrehozza a DocumentDB-fiók végpontját.

    - Az **Előfizetés** mezőben válassza ki a DocumentDB-fiókhoz használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer alapértelmezés szerint azt választja ki.

    - Az **Erőforráscsoport** területen válasszon ki vagy hozzon létre egy erőforráscsoportot a DocumentDB-fiókhoz.  Alapértelmezés szerint a rendszer egy, az Azure-előfizetéshez tartozó erőforráscsoportot választ ki.  Ön azonban dönthet úgy, hogy létrehoz egy új erőforráscsoportot, amelyhez hozzá szeretné adni a DocumentDB-fiókot. További információk: [Using the Azure portal to manage your Azure resources](resource-group-portal.md) (Az Azure portál használata az Azure-erőforrások kezeléséhez).

    - A **Hely** beállítás segítségével megadhatja a földrajzi helyet, ahol a DocumentDB-fiókot üzemeltetni szeretné.   

4.  A DocumentDB-fiók beállításainak konfigurálása után kattintson a **Létrehozás** gombra.  A DocumentDB-fiók létrehozása eltarthat néhány percig.  A folyamat állapotát a kezdőpult alján ellenőrizheti.  
    ![Képernyőfelvétel a kezdőpult Létrehozás csempéjéről – online adatbázis-készítő](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  

    A folyamat állapotát az értesítésközpontban is ellenőrizheti.  

    ![Adatbázisok gyors létrehozása – képernyőfelvétel az értesítésközpontról, ahol nyomon követhető a DocumentDB-fiók létrehozásának folyamata](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Képernyőfelvétel az értesítésközpontról, amelyben az látszik, hogy a DocumentDB-fiók sikeresen létrejött, és telepítve lett egy erőforráscsoportba – online adatbázis-készítő értesítése](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  A létrehozást követően a DocumentDB-fiók (az alapértelmezett beállításokkal) azonnal készen áll a használatra az online portálon. Vegye figyelembe, hogy a DocumentDB-fiók alapértelmezett konzisztenciája a **Munkamenet**.  Az alapértelmezett konzisztencia beállításának módosításához kattintson a **Beállítások** ikonra a felső parancssávon, majd kattintson az **Alapértelmezett konzisztencia** bejegyzésre az **Összes beállítás** panel **Szolgáltatás** területén.

    ![Képernyőfelvétel az Erőforráscsoport panelről – alkalmazás fejlesztésének megkezdése](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Képernyőfelvétel a Konzisztenciaszint panelről – munkamenet-konzisztencia](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[DocumentDB-fiók létrehozása]: #Howto
[Következő lépések]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md



<!--HONumber=Jun16_HO2-->


