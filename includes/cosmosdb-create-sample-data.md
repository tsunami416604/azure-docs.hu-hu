Az Adatkezelő segítségével adatokat adhat hozzá az új gyűjteményhez.

1. Az új adatbázis az Adatkezelőben a Gyűjtemények ablaktáblán jelenik meg. Bontsa ki az **Elemek** adatbázist, majd a **Teendőlista** gyűjteményt, végül kattintson a **Dokumentumok**, majd pedig az **Új dokumentumok** lehetőségre. 

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Adjon hozzá néhány dokumentumot a gyűjteményhez az alábbi struktúra használatával. Az egyes dokumentumok azonosítóinak egyedinek kell lenniük, a többi tulajdonság azonban tetszés szerint adható meg. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Az Adatkezelővel így már lekérdezések használatával lekérheti adatait. Az Adatkezelő alapértelmezés szerint a SELECT * FROM c paranccsal kéri le a gyűjteményhez tartozó összes dokumentumot, de a SELECT * FROM c ORDER BY c.name ASC parancs használatával a Név tulajdonság szerint betűrendi sorrendbe rendezve is lekérheti a dokumentumokat. 
 
     Az Adatkezelővel létrehozhat tárolt eljárásokat, felhasználói függvényeket és a kiszolgálóoldali üzleti logikát végrehajtó triggereket is. Az Adatkezelő hozzáférhetővé teszi az API-k összes beépített, programozható adatelérési funkcióját, és az Azure Portalon tárolt adatokhoz is egyszerű hozzáférést biztosít.