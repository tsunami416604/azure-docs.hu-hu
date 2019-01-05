---
title: Használja az Azure Cosmos DB módosításcsatornáját valós idejű adatelemzés megjelenítése
description: Ez a cikk bemutatja, hogyan módosítási hírcsatorna segítségével olyan kereskedelmi cég felhasználói mintákról, hajtsa végre a valós idejű adatok elemzését és megjelenítését.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: b04a31ec46194d68dbbc5e5a4eb2b600968d7be5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037106"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Használja az Azure Cosmos DB módosításcsatornáját valós idejű adatelemzés megjelenítése

Az Azure Cosmos DB-módosítási hírcsatorna egy olyan mechanizmus, a rekordok és a folyamatos növekményes hírcsatorna lekérése egy Azure Cosmos DB-tárolók, azokat a rekordokat a rendszer éppen létrehozott vagy módosított. Módosítási hírcsatorna támogatása működését úgy tároló nem változott. Majd megjeleníti a dokumentumok a sorrendben, amelyben a módosítás módosult a listán. Módosítási hírcsatorna kapcsolatos további információkért lásd: [dolgozik a változáscsatorna](change-feed.md) cikk. 

Ez a cikk bemutatja, hogyan módosítási hírcsatorna használhatja egy e-kereskedelmi cég felhasználói mintákról, hajtsa végre a valós idejű adatok elemzését és megjelenítését. Esemény, például egy felhasználó egy elem megtekintése, felvesz egy elemet a bevásárlókocsihoz vagy elemeire vásárlási elemzi. Esetén ezek az események közül egy új bejegyzést hoznak létre, és a módosítási hírcsatorna rögzítő. Módosítási hírcsatorna majd eseményindítók lépések metrikák elemzéséhez, a vállalati teljesítmény és a tevékenység Vizualizáció eredményez. Mintametrikák, amelyek segítségével megjelenítheti például bevétel, a látogató egyedi, népszerű cikkek, és tekinthetők meg és és a egy bevásárlókocsihoz hozzáadott elem átlagár vásárolt. Minta metrikák segítségével egy e-kereskedelmi cég kiértékelése a hely népszerűsége, a reklám- és árképzési stratégiák kidolgozásában és milyen támogatásán készletre vonatkozó döntéseket.

Figyelése érdekli az első lépések előtt a megoldást ismertető videó lásd a következő videót:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Megoldás-összetevők
Az alábbi ábrán az adatfolyam és a következő összetevők kapnak szerepet a megoldás jelöli:

![Projekt Vizualizáció](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Adatok generálása:** Adatszimuláló esemény, például egy felhasználó egy elem megtekintése, felvesz egy elemet a bevásárlókocsihoz és elemeire vásárlási kereskedelmi adatok létrehozására szolgál. Az adatgenerátor használatával nagy mintaadatkészletet is létrehozhat. A létrehozott mintaadatokat tartalmaz dokumentumokat a következő formátumban:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **A cosmos DB:** A létrehozott adatok egy Azure Cosmos DB-gyűjteményben tárolja el.  

3. **Módosítási hírcsatorna:** Az Azure Cosmos DB-gyűjtemények változásait a módosítási hírcsatorna figyeli. Minden alkalommal, amikor a gyűjtemény (esemény következik be, például egy felhasználó megtekintése egy elemet, amikor felvesz egy elemet a bevásárlókocsihoz, vagy elemeire vásárlási) adnak hozzá egy új dokumentumot, a módosítási hírcsatorna fogja elindítani egy [Azure-függvény](../azure-functions/functions-overview.md).  

4. **Azure-függvény:** Az Azure-függvény feldolgozza az új adatokat, és elküldi azt egy [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Event Hubs:** Az Azure Event Hubs tárolja ezeket az eseményeket, és elküldi azokat [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) részletes elemzéseket végezhet.  

6. **Az Azure Stream Analytics:** Az Azure Stream Analytics az események feldolgozását, és valós idejű adatelemzés végrehajtása a lekérdezések határozza meg. Ezeket az adatokat elküldi a [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **A Power bi-ban:** A Power BI segítségével jelenítheti meg az Azure Stream Analytics által küldött adatokat. Tekintse meg, hogyan módosíthatja a a metrikák valós idejű irányítópultot hozhat létre.  

## <a name="prerequisites"></a>Előfeltételek

* A Microsoft .NET-keretrendszer 4.7.1 vagy újabb

* A Microsoft .NET Core 2.1-es (vagy újabb)

* Visual Studio és az univerzális Windows Platform fejlesztési, .NET asztali fejlesztés és az ASP.NET- és fejlesztési számítási feladatokhoz

* Microsoft Azure-előfizetés

* A Microsoft Power BI-fiókkal

* Töltse le a [Azure Cosmos DB-módosítási hírcsatorna labor](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) a Githubról. 

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása 

Hozzon létre az Azure-erőforrások – Azure Cosmos DB, Storage-fiókot, Event Hub, a megoldás által igényelt, a Stream Analytics. Központilag telepíti ezeket az erőforrásokat az Azure Resource Manager-sablon használatával. A következő lépések segítségével telepíteni ezeket az erőforrásokat: 

1. A Windows PowerShell végrehajtási házirendjének beállítása **Unrestricted**. Ehhez nyissa meg a **Windows Powershellt rendszergazdaként** , és futtassa a következő parancsokat:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. A GitHub-adattárból az előző lépésben letöltött, keresse meg a **Azure Resource Manager** mappát, és nyissa meg a fájlt nevű **parameters.json** fájlt.  

3. Adjon meg értékeket cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, paraméterek a **parameters.json** fájlt. Szüksége lesz később adnak az egyes az erőforrások neveit használja.  

4. A **Windows PowerShell**, keresse meg a **Azure Resource Manager** mappát, és futtassa a következő parancsot:

   ```powershell
   .\deploy.ps1
   ```
5. Amikor a rendszer kéri, adja meg az Azure **előfizetés-azonosító**, **changefeedlab** az erőforráscsoport nevéhez, és **run1** az üzemelőpéldány-név. Az erőforrások üzembe helyezéséhez megkezdése, miután rá az akár 10 percet igénybe vehet.

## <a name="create-a-database-and-the-collection"></a>Egy adatbázis és a gyűjtemény létrehozása

Most már létrehozhat egy gyűjteményt, amely tárolja az e-kereskedelmi webhely eseményeket. Amikor egy felhasználó megtekinti egy elem, egy elemet ad hozzá a kosárhoz vagy cikket vásárol, a gyűjtemény fog kapni egy rekordot, amely tartalmazza a művelet ("megtekintett", "hozzáadott" vagy "vásárolt"), érintett elem nevét, az ár, az érintett elem és a felhasználó a bevásárlókocsi i azonosító száma nvolved.

1. Lépjen a [az Azure Portal](https://portal.azure.com/) , és keresse meg a **Azure Cosmos DB-fiók** a sablon üzembe helyezéséhez létrehozott.  

2. Az a **adatkezelő** ablaktáblán válassza **új gyűjtemény** , és töltse ki az űrlapot a következő adatokat:  

   * Az a **adatbázis-azonosító** mezőben válassza **új létrehozása**, majd adja meg **changefeedlabdatabase**. Hagyja a **kiépítése adatbázis átviteli** mező nincs bejelölve.  
   * Az a **gyűjtemény** azonosító mezőben adja meg **changefeedlabcollection**.  
   * Az a **partíciókulcs** írja be a következőt **/cikk**. Ez a kis-és nagybetűket, ezért győződjön meg arról, hogy írja be azt megfelelően.  
   * Az a **átviteli** írja be a következőt **10000**.  
   * Kattintson az **OK** gombra.  

3. Ezután hozzon létre egy másik gyűjteményt **bérletek** a módosítási hírcsatorna feldolgozásra. A bérletek gyűjteménye koordinálja a változáscsatorna beolvasását feldolgozó módosítás feldolgozása. Egy külön gyűjteményt a partíciónként egy bérlet a bérletek tárolásához használni kívánt szolgál.  

4.  Lépjen vissza a **adatkezelő** ablaktáblán, és válassza ki **új gyűjtemény** , és töltse ki az űrlapot a következő adatokat:

   * Az a **adatbázis-azonosító** mezőben válassza **meglévő**, majd adja meg **changefeedlabdatabase**.  
   * Az a **gyűjteményazonosító** írja be a következőt **bérletek**.  
   * A **tárolókapacitás**válassza **rögzített méretű**.  
   * Hagyja a **átviteli** mezőben állítsa be az alapértelmezett értékére.  
   * Kattintson az **OK** gombra.

## <a name="get-the-connection-string-and-keys"></a>A kapcsolati karakterláncot, és a kulcsok beolvasása

### <a name="get-the-azure-cosmos-db-connection-string"></a>Az Azure Cosmos DB kapcsolati sztring lekérése

1. Lépjen a [az Azure Portal](https://portal.azure.com/) , és keresse meg a **Azure Cosmos DB-fiók** a sablon üzembe helyezéséhez létrehozott.  

2. Keresse meg a **kulcsok** panelen másolja az elsődleges KAPCSOLATI KARAKTERLÁNCOT, és másolja a Jegyzettömbbe vagy egy másik dokumentumba, hogy a labor teljes hozzáférést kap. Címkével kell **Cosmos DB kapcsolati sztring**. Szüksége lesz később másolja a karakterláncot a kódban, ezért jegyezze fel, és ne felejtse el, ahol tárolja azt.

### <a name="get-the-storage-account-key-and-connection-string"></a>A tárolási fiók kulcs és a kapcsolati karakterlánc beolvasása

Az Azure Storage-fiókok engedélyezése a felhasználók számára az adatok tárolásához. A tesztkörnyezetben az Azure-függvény által használt adatok tárolására egy storage-fiókot fogja használni. Az Azure-függvény akkor aktiválódik, ha bármilyen módosítás a gyűjteményhez.

1. Térjen vissza az erőforráscsoportot, és nyissa meg a korábban létrehozott tárfiók  

2. Válassza ki **hozzáférési kulcsok** a bal oldali menüből.  

3. Másolja le az értékeket a **kulcs 1** Jegyzettömbbe vagy egy másik dokumentumba, hogy a labor teljes hozzáférést kap. Érdemes címkézését a **kulcs** , **Tárkulcs** és a **kapcsolati karakterlánc** , **tárolási kapcsolati karakterlánc**. Szüksége lesz, ezek a karakterláncok később másolja be a kódot, ezért jegyezze fel, és ne felejtse el, ahol tárolja őket.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Az event hub névtér kapcsolati sztring lekérése

Az Azure Event Hub kap az eseményadatokat, a tárolók, a folyamatokat, és továbbítja az adatokat. A tesztkörnyezetben, az Azure Event Hubs fog kapni a dokumentum minden alkalommal, amikor új esemény történik (vagyis egy elem van a felhasználó megtekinti, a felhasználó bevásárlókocsihoz hozzáadott vagy felhasználó által megvásárolt), és ezután továbbítja a dokumentum az Azure Stream Analytics számára.

1. Térjen vissza az erőforráscsoportot, és nyissa meg a **Event Hub-Namespace** létrehozott és a prelab a neve.  

2. Válassza ki **megosztott elérési házirendek** a bal oldali menüből.  

3. Válassza ki **RootManageSharedAccessKey**. Másolás a **kapcsolati karakterlánc – elsődleges kulcs** Jegyzettömbbe vagy egy másik dokumentumba, hogy a labor teljes hozzáférést kap. Címkével kell **Event Hub-Namespace** kapcsolati karakterláncot. Szüksége lesz később másolja a karakterláncot a kódban, ezért jegyezze fel, és ne felejtse el, ahol tárolja azt.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Olvassa el a módosítási hírcsatorna beállítása Azure-függvény

Amikor létrejön egy új dokumentumot, vagy egy dokumentumot egy Cosmos DB-gyűjtemények módosul, a módosítási hírcsatorna automatikusan ad hozzá a módosított dokumentumok gyűjtemény módosításait annak előzményeit. Rendszer most felépíti és futtatja az Azure-függvény, amely feldolgozza a változáscsatorna. Ha egy dokumentum létrehozásakor vagy módosításakor a létrehozott gyűjtemény, az Azure-függvény a változáscsatorna aktiválódik. Ezután az Azure-függvény a módosított dokumentumok küld az Event Hubs.

1. Térjen vissza a tárház, amely klónozta az eszközön.  

2. Kattintson a jobb gombbal a fájlt nevű **ChangeFeedLabSolution.sln** válassza **nyissa meg a Visual Studio**.  

3. Navigáljon a **local.settings.json** a Visual Studióban. A megszámlálandó üres értékeket adja meg a korábban rögzített értéket használja majd.  

4. Navigáljon a **ChangeFeedProcessor.cs**. A paraméterek számára a **futtatása** működik, hajtsa végre a következő műveleteket:  

   * Cserélje le a szöveget **itt NEVET a GYŰJTEMÉNY** a gyűjtemény nevét. Ha követte az útmutató korábbi, a gyűjtemény neve changefeedlabcollection.  
   * Cserélje le a szöveget **a BÉRLETEK GYŰJTEMÉNY neve itt** a bérletek gyűjteményének neve. Ha követte az útmutató korábbi, a bérletek gyűjteményének neve nem **bérletek**.  
   * Felső részén a Visual Studióban, győződjön meg arról, hogy a Kezdőprojekt mező a bal oldalon, a zöld nyíl értéke **ChangeFeedFunction**.  
   * Válassza ki **Start** futtatni a programot a lap tetején  
   * Ellenőrizheti, hogy a függvény fut, amikor a konzolalkalmazást szerint a "feladat gazdagép lépések".

## <a name="insert-data-into-azure-cosmos-db"></a>Adatok beszúrása az Azure Cosmos DB-be 

Megtekintéséhez hogyan módosítási hírcsatorna új műveletek egy e-kereskedelmi webhelyen feldolgozza, adatok, amelyek a termék katalógusból származó elemeket, azok az elemek hozzáadása a kocsik és azok kocsik található elemek vásárlási felhasználók szimulálására rendelkezik. Ezeket az adatokat tetszőleges és a egy e-kereskedelmi adatok replikálása céljából helyet néznek.

1. Lépjen vissza a tárházban, a Fájlkezelőben, és kattintson a jobb gombbal **ChangeFeedFunction.sln** újra megnyitni egy új Visual Studio-ablakban.  

2. Keresse meg a **App.config** fájlt. Belül a <appSettings> letiltja, adja hozzá az URI-t, és egyedi **elsődleges kulcs** , amely az Azure Cosmos DB-fiók, amely a korábban kapott.  

3. Adja hozzá a **gyűjtemény** és **adatbázis** nevét. (Ezeket a neveket kell **changefeedlabcollection** és **changefeedlabdatabase** , kivéve, ha úgy dönt, hogy eltérő nevet.)

   ![Kapcsolati sztringek frissítése](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Mentse a módosításokat az összes szerkesztett fájlt.  

5. A Visual Studio felső, ügyeljen arra, hogy a **Kezdőprojekt** , a zöld nyílra a bal oldali mező szerint **DataGenerator**. Válassza ki **Start** futtatni a programot a lap tetején.  
 
6. Várjon, amíg a futtatni kívánt programot. A csillag jelenti azt, hogy adatok várható! Hagyja futni a programot – fontos, hogy nagy mennyiségű adatot gyűjt.  

7. Ha manuálisan lép [az Azure Portal](https://portal.azure.com/) , majd az a Cosmos DB-fiókot az erőforráscsoportban, majd **adatkezelő**, látni fogja a véletlenszerű importált adatokat a  **changefeedlabcollection** .
 
   ![A portál létrehozott adatok](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Állítsa be a stream analytics-feladat

Az Azure Stream Analytics egy teljes körűen felügyelt felhőszolgáltatás, a streamelési adatok valós idejű feldolgozásra. A tesztkörnyezetben használhatjuk a stream analytics az Eseményközpontból érkező új események feldolgozni (azaz ha elem tekinthetők meg, a bevásárlókocsihoz hozzáadott, vagy megvásárolható), események építhet be valós idejű adatok elemzése és elküldheti a vizualizációt Power BI-bA.

1. Az a [az Azure Portal](https://portal.azure.com/), keresse meg az erőforráscsoportot, majd a **streamjob1** (a stream analytics-feladat, amelyet a prelab).  

2. Válassza ki **bemenetek** ahogyan alább is látható.  

   ![Hozzon létre bemeneti](./media/changefeed-ecommerce-solution/create-input.png)

3. Válassza ki **+ streambemenet hozzáadása**. Válassza ki **Eseményközpont** a legördülő menüből.  

4. Töltse ki az új beviteli űrlap a következő adatokkal:

   * Az a **bemeneti** alias mezőben adja meg **bemeneti**.  
   * Válassza ki a kívánt beállítást **Event Hubs kiválasztása az előfizetések közül**.  
   * Állítsa be a **előfizetés** mezőt az előfizetéshez.  
   * Az a **Event Hub-névtér** mezőben adja meg az Event Hub-Namespace a prelab során létrehozott nevét.  
   * Az a **Eseményközpont neve** mezőben válassza ki a kívánt beállítást **meglévő használata** válassza **esemény-hub1** a legördülő menüből.  
   * Hagyja **Eseményközpontba szabályzat** neve mezőben állítsa be az alapértelmezett értékére.  
   * Hagyja **eseményszerializációs formátum** , **JSON**.  
   * Hagyja **Encoding mező** beállítása **UTF-8**.  
   * Hagyja **esemény tömörítési típusa** mező értéke **None**.  
   * Válassza ki a **Mentés** gombot.

5. Lépjen vissza a stream analytics-feladat oldalát, és válassza ki **kimenetek**.  

6. Válassza a **+ Hozzáadás** lehetőséget. Válassza ki **Power BI** a legördülő menüből.  

7. Hozzon létre egy új Power BI-kimenet megjelenítéséhez átlagár, hajtsa végre a következő műveleteket:

   * Az a **kimeneti alias** írja be a következőt **averagePriceOutput**.  
   * Hagyja a **csoportos munkaterület** mező értéke **engedélyezze a kapcsolatot a munkaterületek betöltése**.  
   * Az a **adatkészlet neve** írja be a következőt **averagePrice**.  
   * Az a **táblanév** írja be a következőt **averagePrice**.  
   * Válassza ki a **engedélyezés** gombra, majd kövesse az utasításokat a Power bi-bA a kapcsolat engedélyezéséhez.  
   * Válassza ki a **Mentés** gombot.  

8. Ezután lépjen vissza a **streamjob1** válassza **lekérdezés szerkesztése**.

   ![Lekérdezés szerkesztése](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Illessze be a következő lekérdezést a lekérdezési ablakban. A **ÁTLAGÁR** lekérdezés kiszámítja az átlagos ára az összes elem, amely a felhasználók, az összes, a felhasználók kocsik hozzáadott elemek átlagos ára és átlagos ára az összes elem felhasználó által beszerzett tekinthetők meg. Ez a metrika segítségével döntse el, milyen díjszabás található elemek és milyen készlet a fektethet be, hogy e-kereskedelmi cégek. Például ha megtekintett cikkek átlagos ára sokkal nagyobb vásárolt cikkek átlagos ára, majd egy vállalati döntése alapján kevésbé költséges elemek a leltárhoz adásához.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Válassza ki **mentése** a bal felső sarkában.  

11. Most lépjen vissza **streamjob1** , és válassza ki a **Start** gombra a lap tetején. Az Azure Stream Analytics indítása pár percet is igénybe vehet, de végül azt látja, módosítsa "Indítás" értékről "Fut".

## <a name="connect-to-power-bi"></a>Power bi-hoz

Power BI egy üzleti elemzési eszközök az adatok elemzése és elemzéseket oszthat meg. Egy remek példa hogyan stratégiai jelenítheti meg az elemzett adatok.

1. Jelentkezzen be a Power bi-ba, és navigáljon a **saját munkaterület** nyissa meg a menüben a lap bal oldalán.  

2. Válassza ki **+ létrehozás** a jobb felső sarokban, és válassza ki a **irányítópult** irányítópult létrehozásához.  

3. Válassza ki **+ csempe hozzáadása** a jobb felső sarokban.  

4. Válassza ki **egyedi Streamelési adatok**, majd válassza ki a **tovább** gombra.  
 
5. Válassza ki **averagePrice** a **az ADATKÉSZLETEK**, majd **tovább**.  

6. Az a **Vizualizáció típusának** mezőben válassza ki **fürtözött sávdiagram** a legördülő menüből. A **tengely**, adja hozzá a műveletet. Kihagyás **jelmagyarázat** bármit hozzáadása nélkül. Ezután a következő szakaszban nevű **érték**, adjon hozzá **átlagos**. Válassza ki **tovább**, majd a diagram cím, és válassza ki **alkalmaz**. Az irányítópulton megjelenik egy új diagram!  

7. Most, ha szeretné vizualizálni a további metrikákat, akkor is lépjen vissza a **streamjob1** , és három további kimeneteket hozzon létre a következő mezőket.

   a. **Kimeneti alias:** incomingRevenueOutput, adatkészlet neve: incomingRevenue, tábla neve: incomingRevenue  
   b. **Kimeneti alias:** top5Output, adatkészlet neve: top5, tábla neve: top5  
   c. **Kimeneti alias:** uniqueVisitorCountOutput, adatkészlet neve: uniqueVisitorCount, tábla neve: uniqueVisitorCount

   Válassza ki **lekérdezés szerkesztése** , és illessze be a következő lekérdezéseket **fent** egy már megírt.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   Az első 5 lekérdezés alapján számítja ki, akik megvásárolták száma szerint rangsorolva, amelyekről felső 5 elemének. Ez a metrika segíthet kiértékelése e-kereskedelmi cég mely elemek legnépszerűbbek és is befolyásolják a cég hirdetési, díjszabás, és leltár döntéseket hozhat.

   A BEVÉTEL lekérdezés bevétel összesítjük, az összes elem percenként vásárolt árak számítja ki. Ez a metrika segíthetnek e-kereskedelmi cég, a pénzügyi teljesítmény kiértékelése és is megismerheti, mit napszakokban hozzájárulnak a legtöbb bevételt. Az általános vállalati stratégia, különösen marketing befolyásolhatja.

   Az egyedi LÁTOGATÓINAK lekérdezés alapján számítja ki, hány egyedi látogatóinak a helyen vannak 5 másodpercentként által észlelését egyedi bevásárlókocsi-azonosítók Ez a metrika segíthet a hely forgalmáról értékelje ki és hogyan lehet beszerezni a további ügyfelek annak e-kereskedelmi cégek.

8. Most már hozzáadhat csempéket, valamint ezen adatkészletek esetében.

   * Top 5 azt lenne értelme az értéket használjuk tengelyként elemeket és a egy fürtözött oszlopdiagram tennie.  
   * Bevétel azt lenne értelme ehhez idejét tengely és díjak összege értékeként egy vonaldiagramot. A megjelenítendő időtartomány a legnagyobb lehetséges kell ahhoz, hogy a lehető legtöbb információt biztosít.  
   * Egyedi látogatóinak azt lenne értelme értékeként az egyedi látogatóinak száma kártyavizualizáció tennie.

   Ez a minta-irányítópult beírja a diagramokat:

   ![Vizualizációk](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Nem kötelező: Egy elektronikus kereskedelmi webhellyel megjelenítése

Mostantól megfigyelheti hogyan használhatja az új adatok eszköz valódi e-kereskedelmi webhely kapcsolódni. Az e-kereskedelmi webhely készítéséhez használni egy Azure Cosmos DB-adatbázis (nők, férfi, Uniszex) termékkategóriák listája, a termékkatalógus és a legnépszerűbb elemek listáját.

1. Lépjen vissza a [az Azure Portal](https://portal.azure.com/), majd a **Cosmos DB-fiók**, majd a **adatkezelő**.  

   A két gyűjteményt felvehet **changefeedlabdatabase** - **termékek** és **kategóriák** rögzített tárolási kapacitással.

   Adjon hozzá egy másik gyűjteményt az **changefeedlabdatabase** nevű **topItems** és **/cikk** partíciókulcsként.

2. Válassza ki a **topItems** gyűjteményt, majd a **méretezés és beállítások** állítsa be a **élettartama** kell **30 másodperc** úgy, hogy topItems frissítése Ez lehet 30 másodperc.

   ![Élettartam](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Annak érdekében, hogy feltölti a **topItems** adatgyűjtés és a leggyakrabban vásárolt cikkek, lépjen vissza a **streamjob1** , és vegyen fel egy új **kimeneti**. Válassza ki **Cosmos DB**.

4. Adja meg a kötelező mezőket, címsávnál alatt.

   ![Cosmos-kimenet](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Ha a labor előző részében az első 5 opcionális lekérdezési hozzáadott, folytassa a rész 5a. Ha nem, lépjen tovább a rész 5b.

   5a. A **streamjob1**válassza **lekérdezés szerkesztése** , és illessze be a következő lekérdezés az első 5 lekérdezés alá, de a fenti lekérdezések a többi az Azure Stream Analytics query-szerkesztő.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. A **streamjob1**válassza **lekérdezés szerkesztése** , és illessze be a következő lekérdezést az Azure Stream Analytics query-szerkesztő felett más lekérdezések.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Nyissa meg **EcommerceWebApp.sln** , és keresse meg a **Web.config** fájlt a **Megoldáskezelőben**.  

7. Belül a `<appSettings>` letiltása, adja hozzá a **URI** és **elsődleges kulcs** korábban mentett, ugyanakkor **itt az URI** és **elsődleges kulcs itt**. Majd adja hozzá a az **adatbázisnév** és **gyűjteménynév** jelöli. (Ezeket a neveket kell **changefeedlabdatabase** és **changefeedlabcollection** , kivéve, ha úgy döntött, hogy eltérő nevet.)

   Töltse ki a **termékek gyűjteménynév**, **kategóriák gyűjteménynév**, és **legelső elemek gyűjtemény neve** jelöli. (Ezeket a neveket kell **termékeket, kategóriák és topItems** , kivéve, ha úgy döntött, hogy eltérő nevet.)  

8. Keresse meg és nyissa meg a **kivétele mappa** belül **EcommerceWebApp.sln.** Nyissa meg a **Web.config** fájlra a mappában.  

9. Belül a `<appSettings>` letiltása, adja hozzá a **URI** és **elsődleges kulcs** , hogy korábban mentett felsoroltak közül. Majd adja hozzá a az **adatbázis neve** és **gyűjteménynév** jelöli. (Ezeket a neveket kell **changefeedlabdatabase** és **changefeedlabcollection** , kivéve, ha úgy döntött, hogy eltérő nevet.)  

10. Nyomja meg **Start** futtatni a programot a lap tetején.  

11. Most már Ön is módosításával az e-kereskedelmi webhelyen. Elem megtekintése, vegyen fel egy elemet a bevásárlókocsihoz, módosíthatja a egy elemet a bevásárlókocsihoz a, vagy vásároljon egy elem, ezek az események átadni a Cosmos DB változáscsatorna Event Hub, ASA és majd a Power bi-ban. Javasoljuk, hogy továbbra is fut a DataGenerator jelentős webes forgalmi adatok létrehozásához, és a "Gyors elérésű termékek" valósághű tárházát biztosítja az e-kereskedelmi webhelyen.

## <a name="delete-the-resources"></a>Az erőforrások törlése

A laborgyakorlat során létrehozott erőforrások törléséhez keresse meg az erőforráscsoportot a [az Azure Portal](https://portal.azure.com/), majd **erőforráscsoport törlése** az oldal felső részén látható menüben, és kövesse az utasításokat a megadott.

## <a name="next-steps"></a>További lépések 
  
* Módosítási hírcsatorna kapcsolatos további információkért lásd: [módosítása használatának hírcsatorna támogatása az Azure Cosmos DB-ben](change-feed.md) 
* [Módosítási hírcsatorna értesítés megoldás](change-feed-hl7-fhir-logic-apps.md) egészségügyi szervezet az Azure Cosmos DB használatával.
