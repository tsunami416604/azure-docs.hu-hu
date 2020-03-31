---
title: Az Azure Cosmos DB módosítási hírcsatornájának használata a valós idejű adatelemzés megjelenítéséhez
description: Ez a cikk azt ismerteti, hogy a változási hírcsatornát hogyan használhatja egy kiskereskedelmi vállalat a felhasználói minták megértésére, a valós idejű adatelemzés és a vizualizáció végrehajtására
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513492"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Az Azure Cosmos DB módosítási hírcsatornájának használata a valós idejű adatelemzés megjelenítéséhez

Az Azure Cosmos DB változáscsatorna egy olyan mechanizmus, amely egy azure Cosmos-tárolórekordjainak folyamatos és növekményes hírcsatornáját szeretné leadni, miközben ezek a rekordok létrehozása vagy módosítása folyamatban van. A hírcsatorna-támogatás módosítása úgy működik, hogy meghallgatja a tárolót a módosításokra. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. Ha többet szeretne tudni a változási hírfolyamról, olvassa el [a Módosítási hírcsatorna-cikk című](change-feed.md) témakört. 

Ez a cikk azt ismerteti, hogy egy e-kereskedelmi vállalat hogyan használhatja a változási hírcsatornát a felhasználói minták megértésére, a valós idejű adatelemzés és a vizualizáció végrehajtására. Elemezni fogja az olyan eseményeket, mint például egy elem megtekintésére, egy elem kosárba adására vagy egy cikk megvásárlására. Ezen események valamelyikének bekövetkeztekor új rekord jön létre, és a módosítási hírcsatorna naplózza a rekordot. A változáscsatorna ezután egy sor lépést indít el, amelyek a vállalati teljesítményt és tevékenységet elemző mérőszámok vizualizációját eredményezik. A megjeleníthető mintamutatók közé tartozik a bevétel, az egyedi webhelylátogató, a legnépszerűbb elemek, valamint a megtekintett cikkek és a kosárhoz hozzáadott cikkek átlagos ára. Ezek a mintamutatók segíthetnek egy e-kereskedelmi vállalatnak a webhely népszerűségének értékelésében, hirdetési és árképzési stratégiáinak kidolgozásában, valamint a befektetéssel kapcsolatos döntések meghozatalában.

A megoldásról szóló videó megtekintése az első lépések előtt a következő videóban található:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Megoldás-összetevők
A következő ábra a megoldásban részt vevő adatfolyamot és összetevőket mutatja be:

![Projekt vizualizációja](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Adatgenerálás:** Az adatszimulátor olyan kiskereskedelmi adatok létrehozására szolgál, amelyek olyan eseményeket jelölnek, mint például egy elem megtekintésének felhasználója, egy cikk hozzáadása a kosárhoz, valamint egy cikk megvásárlása. Az adatgenerátor használatával nagy mennyiségű mintaadatot hozhat létre. A létrehozott mintaadatok a következő formátumú dokumentumokat tartalmazzák:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** A létrehozott adatok egy Azure Cosmos-tárolóban tárolódnak.  

3. **Hírcsatorna módosítása:** A változáscsatorna figyeli az Azure Cosmos-tároló módosításait. Minden alkalommal, amikor új dokumentumot ad hozzá a gyűjteményhez (azaz amikor egy esemény bekövetkezik, amikor egy ilyen felhasználó megtekint egy elemet, hozzáad egy elemet a kosárhoz, vagy megvásárol egy elemet), a módosítási hírcsatorna [egy Azure-függvényt](../azure-functions/functions-overview.md)indít el.  

4. **Azure függvény:** Az Azure Függvény feldolgozza az új adatokat, és elküldi azokat egy [Azure Event Hub.](../event-hubs/event-hubs-about.md)  

5. **Eseményközpont:** Az Azure Event Hub tárolja ezeket az eseményeket, és elküldi őket az [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) további elemzés elvégzéséhez.  

6. **Azure Stream Analytics:** Az Azure Stream Analytics lekérdezéseket határoz meg az események feldolgozásához és a valós idejű adatelemzés hez. Ezeket az adatokat a rendszer ezután elküldi a [Microsoft Power BI-nak.](https://docs.microsoft.com/power-bi/desktop-what-is-desktop)  

7. **Power BI:** A Power BI az Azure Stream Analytics által küldött adatok megjelenítésére szolgál. Létrehozhat egy irányítópultot, amelyből megtudhatja, hogyan változnak a metrikák valós időben.  

## <a name="prerequisites"></a>Előfeltételek

* Microsoft .

* Microsoft .

* Visual Studio univerzális Windows Platform fejlesztéssel, .NET asztali fejlesztéssel, valamint ASP.NET és webfejlesztési munkaterheléssel

* Microsoft Azure-előfizetés

* Microsoft Power BI-fiók

* Töltse le az [Azure Cosmos DB változáscsatorna-labort](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) a GitHubról. 

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása 

Hozza létre az Azure-erőforrásokat – Azure Cosmos DB, Storage-fiók, Event Hub, Stream Analytics a megoldás által igényelt. Ezeket az erőforrásokat egy Azure Resource Manager-sablonon keresztül fogja üzembe helyezni. Az alábbi lépésekkel telepítheti ezeket az erőforrásokat: 

1. Állítsa a Windows PowerShell végrehajtási házirendet **Korlátlan**ra. Ehhez nyissa meg **a Windows PowerShellt rendszergazdaként,** és futtassa a következő parancsokat:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Az előző lépésben letöltött GitHub-tárházból keresse meg az **Azure Resource Manager** mappát, és nyissa meg a **parameters.json** nevű fájlt.  

3. Adja meg a **paraméterek.json** fájlban jelzett cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name paraméterek értékeit. Az egyes erőforrásoknak később adott neveket kell használnia.  

4. A **Windows PowerShell**rendszerben keresse meg az **Azure Resource Manager** mappát, és futtassa a következő parancsot:

   ```powershell
   .\deploy.ps1
   ```
5. Amikor a rendszer kéri, adja meg az **Azure-előfizetés-azonosítót,** **changefeedlab** az erőforráscsoport nevét, és **fuss1** a központi telepítés nevét. Miután az erőforrások üzembe helyezése, akár 10 percet is igénybe vehet, amíg befejeződik.

## <a name="create-a-database-and-the-collection"></a>Adatbázis és gyűjtemény létrehozása

Most létrehoz egy gyűjteményt az e-kereskedelmi webhelyek eseményeinek tárolására. Amikor a felhasználó megtekint egy elemet, hozzáad egy elemet a kosárhoz, vagy megvásárol egy elemet, a gyűjtemény olyan bejegyzést kap, amely tartalmazza a műveletet ("megtekintett", "hozzáadott" vagy "megvásárolt"), az érintett tétel nevét, az érintett cikk árát és a felhasználói kosár azonosítószámát. Részt.

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a sablon üzembe helyezése által létrehozott **Azure Cosmos DB-fiókot.**  

2. Az **Adatkezelő** ablaktáblán válassza az **Új gyűjtemény** lehetőséget, és töltse ki az űrlapot a következő adatokkal:  

   * Az **Adatbázis azonosító mezőben** válassza az **Új létrehozása**lehetőséget, majd írja be a **changefeedlabdatabase parancsot**. Hagyja bejelölve a **Létesítési adatbázis átviteli átaputát.**  
   * A **Gyűjtemény** azonosító mezőben adja meg a **changefeedlabcollection értéket.**  
   * A **Partíciókulcs** mezőbe írja be a **/Item kapcsolót.** Ez a kis- és nagybetűk et is figyelembe teszi, ezért ügyeljen arra, hogy helyesen adja meg.  
   * Az **Átviteli mezőhöz** írja be az **10000**értéket.  
   * Kattintson az **OK** gombra.  

3. Ezután hozzon létre egy másik gyűjtemény nevű **bérletek** változáscsatorna feldolgozása. A bérletek gyűjteménye koordinátái a módosítási hírcsatorna feldolgozása több dolgozó között. Egy külön gyűjtemény tárolják a bérleti partíciónként egy bérletet.  

4. Térjen vissza az **Adatkezelő** ablaktáblába, és válassza az **Új gyűjtemény lehetőséget,** és töltse ki az űrlapot a következő adatokkal:

   * Az **Adatbázis-azonosító** mezőben válassza a **Meglévő használata**lehetőséget, majd írja be a **changefeedlabdatabase parancsot.**  
   * A **Beszedés id** mezőben adja meg a **címbérleteket.**  
   * A **Tárolókapacitás területen**válassza **a Rögzített**lehetőséget.  
   * Hagyja az **átviteli mezőt** az alapértelmezett értékre állítva.  
   * Kattintson az **OK** gombra.

## <a name="get-the-connection-string-and-keys"></a>A kapcsolati karakterlánc és a kulcsok beszerezni

### <a name="get-the-azure-cosmos-db-connection-string"></a>Az Azure Cosmos DB kapcsolati karakterláncának beszereznie

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a sablon üzembe helyezése által létrehozott **Azure Cosmos DB-fiókot.**  

2. Nyissa meg a **Kulcsok** ablaktáblát, másolja az ELSŐDLEGES KAPCSOLAT KARAKTERLÁNC-t, és másolja egy jegyzettömbbe vagy egy másik dokumentumba, amelyhez a tesztkörnyezetben hozzáférhet. Meg kell **címkézni,** hogy Cosmos DB Connection String . A karakterláncot később kell átmásolnia a kódba, ezért jegyezze fel, és ne feledje, hol tárolja.

### <a name="get-the-storage-account-key-and-connection-string"></a>A tárfiók kulcsának és a kapcsolati karakterláncnak a beszereznie

Az Azure Storage-fiókok lehetővé teszik a felhasználók számára az adatok tárolását. Ebben a laborban egy tárfiókot fog használni az Azure-függvény által használt adatok tárolására. Az Azure-függvény akkor aktiválódik, ha bármilyen módosítást hajt végre a gyűjteményen.

1. Visszatérés az erőforráscsoporthoz, és a korábban létrehozott tárfiók megnyitása  

2. Válassza a bal oldali menü **Hozzáférési gombjait.**  

3. Másolja az **1- es kulcs** alatti értékeket egy jegyzettömbbe vagy egy másik dokumentumba, amelyhez a laborban hozzáférhet. A **kulcsot** **tárolókulcsként,** a **Kapcsolat karakterláncot** **pedig tárolási kapcsolati karakterláncként kell címkézni.** Ezeket a karakterláncokat később be kell másolnia a kódba, ezért jegyezze fel, és ne feledje, hol tárolja őket.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Az eseményközpont névtérbeli kapcsolati karakterláncának beszereznie

Az Azure Event Hub megkapja az eseményadatokat, tárolja, feldolgozza és továbbítja az adatokat. Ebben a laborban az Azure Event Hub minden alkalommal kap egy dokumentumot, amikor új esemény történik (azaz egy elemet egy felhasználó megtekint, hozzáad egy felhasználó kosarajába, vagy megvásárol egy felhasználó), majd továbbítja a dokumentumot az Azure Stream Analytics-nek.

1. Térjen vissza az erőforráscsoporthoz, és nyissa meg az előlapban létrehozott és elnevezett **Event Hub-névteret.**  

2. A bal oldali menüben válassza a **Megosztott hozzáférési házirendek** lehetőséget.  

3. Válassza **a RootManageSharedAccessKey lehetőséget.** Másolja a **Kapcsolat karakterlánc-elsődleges kulcsot** egy jegyzettömbbe vagy egy másik dokumentumba, amelyhez a tesztkörnyezetben hozzáférhet. Meg kell címkézni, hogy **Event Hub Namespace** kapcsolat i. A karakterláncot később kell átmásolnia a kódba, ezért jegyezze fel, és ne feledje, hol tárolja.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Az Azure-függvény beállítása a módosítási hírcsatorna olvasásához

Amikor új dokumentumot hoz létre, vagy egy aktuális dokumentumot módosít a Cosmos-tárolóban, a módosítási hírcsatorna automatikusan hozzáadja a módosított dokumentumot a gyűjteménymódosítási előzményekhez. Most létrehoz és futtat egy Azure-függvényt, amely feldolgozza a változáscsatorna. Amikor egy dokumentumot hoz létre vagy módosít a létrehozott gyűjteményben, az Azure-függvény tág működésbe lép a változáscsatorna. Ezután az Azure-függvény elküldi a módosított dokumentumot az Event Hub.

1. Térjen vissza az eszközén klónozott tárházba.  

2. Kattintson a jobb gombbal a **ChangeFeedLabSolution.sln** nevű fájlra, és válassza **a Megnyitás a Visual Studio programmal**parancsot.  

3. Nyissa meg a **local.settings.json webhelyet** a Visual Studióban. Ezután használja a korábban rögzített értékeket az üres helyek kitöltéséhez.  

4. Keresse meg **a ChangeFeedProcessor.cs.** A **Futtatás** függvény paramétereiben hajtsa végre a következő műveleteket:  

   * Cserélje le a **szöveget a gyűjtemény neve ITT** a nevét a gyűjtemény. Ha a korábbi utasításokat követte, a gyűjtemény neve changefeedlabcollection.  
   * Cserélje le a **szöveget a leases gyűjtemény neve ITT** a nevét a bérleti gyűjtemény. Ha a korábbi utasításokat követte, a bérletgyűjtemény neve **bérletek.**  
   * A Visual Studio tetején győződjön meg arról, hogy a zöld nyíl bal oldalán található Indítási projekt mezőben a **ChangeFeedFunction látható.**  
   * A program futtatásához válassza a lap tetején található **Indítás** lehetőséget.  
   * Ellenőrizheti, hogy a függvény fut-e, amikor a konzolalkalmazás azt mondja: "A feladatgazda-állomás elindult".

## <a name="insert-data-into-azure-cosmos-db"></a>Adatok beszúrása az Azure Cosmos DB-be 

Ha meg szeretné tekinteni, hogy a változási hírcsatorna hogyan dolgozza fel az új műveleteket egy e-kereskedelmi webhelyen, szimulálnia kell azokat az adatokat, amelyek azt jelentik, hogy a felhasználók megtekintik a termékkatalógus elemeit, hozzáadjuk ezeket a cikkeket a kosaraikhoz, és megvásárolják a cikkeket a kosaraikban. Ezek az adatok tetszőlegesek, és az e-kereskedelmi webhelyeken található adatok replikálása céljából.

1. Lépjen vissza a tárházba a Fájlkezelőben, és kattintson a jobb gombbal **a ChangeFeedFunction.sln** fájlra, és nyissa meg újra egy új Visual Studio-ablakban.  

2. Nyissa meg az **App.config** fájlt. A `<appSettings>` blokkon belül adja hozzá a végpontot és az egyedi **elsődleges kulcsot,** amely az Azure Cosmos DB-fiók, amely korábban lekért.  

3. Adja hozzá a **gyűjtemény** és **az adatbázis** nevét. (Ezek a nevek kell **changefeedlabcollection** és **changefeedlabdatabase,** kivéve, ha úgy dönt, hogy nevet tiéd másképp.)

   ![Kapcsolati karakterláncok frissítése](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Mentse a módosításokat az összes szerkesztett fájlra.  

5. A Visual Studio tetején győződjön meg arról, hogy a zöld nyíl bal oldalán található **Indítási projekt** mezőben a **DataGenerator**látható. Ezután a program futtatásához válassza a lap tetején található **Indítás** lehetőséget.  
 
6. Várja meg, amíg a program fut. A csillagok azt jelentik, hogy adatok érkeznek! Tartsa a program fut - fontos, hogy sok adatot gyűjtenek.  

7. Ha az [Azure Portalra](https://portal.azure.com/) lép, majd az erőforráscsoporton belül a Cosmos DB-fiókjába, majd az **Adatkezelőbe,** a **changefeedlabcollection gyűjteményében** importált randomizált adatok jelennek meg.
 
   ![A portálon létrehozott adatok](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Adatfolyam-elemzési feladat beállítása

Az Azure Stream Analytics egy teljes körűen felügyelt felhőszolgáltatás a streamelési adatok valós idejű feldolgozásához. Ebben a tesztkörnyezetben adatfolyam-elemzéssel dolgozhat fel új eseményeket az Event Hubról (azaz amikor egy elemet megtekintenek, egy kosárba adtak vagy megvásároltak), ezeket az eseményeket valós idejű adatelemzésbe kell foglalnia, és el küldheti őket a Power BI-ba vizualizációra.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg az erőforráscsoportot, majd **a streamjob1** (az előlaborban létrehozott stream elemzési feladat).  

2. Válassza **a Bemenetek lehetőséget** az alábbiak szerint.  

   ![Bemenet létrehozása](./media/changefeed-ecommerce-solution/create-input.png)

3. Válassza **a + Stream input hozzáadása**lehetőséget . Ezután válassza az **Event Hub** lehetőséget a legördülő menüből.  

4. Töltse ki az új beviteli űrlapot a következő adatokkal:

   * A **Bemeneti** alias mezőbe írja be a **bemeneti értéket.**  
   * Válassza ki az Event Hub kiválasztása lehetőséget **az előfizetéseiből.**  
   * Állítsa be az **Előfizetés** mezőt az előfizetéshez.  
   * Az **Event Hub névtér** mezőjébe írja be az eseményközpont-névtér nevét, amelyet az előlap során hozott létre.  
   * Az **Event Hub name (Eseményközpont neve)** mezőben válassza a **Meglévő használata** lehetőséget, és válassza az **event hub1** lehetőséget a legördülő menüből.  
   * Hagyja **az Event Hub házirendnév** mezőjét az alapértelmezett értékre állítva.  
   * Hagyja **esemény szerializálási formátumjának** **JSON**.  
   * Hagyja **a Kódolás mezőt** **UTF-8-ra**állítva.  
   * Hagyja, hogy **az Eseménytömörítési típusú** mező **ne**lesz .  
   * Válassza ki a **Mentés** gombot.

5. Lépjen vissza az adatfolyam-elemzési feladat oldalára, és válassza **a Kimenetek**lehetőséget.  

6. Válassza a **+ Hozzáadás** lehetőséget. Ezután válassza a **Power BI** lehetőséget a legördülő menüből.  

7. Ha új Power BI-kimenetet szeretne létrehozni az átlagár megjelenítéséhez, hajtsa végre a következő műveleteket:

   * A **Kimeneti alias** mezőbe írja be az **averagePriceOutput**értéket.  
   * Hagyja a **Csoport munkaterület** mezőt a Kapcsolat engedélyezése **a munkaterületek betöltéséhez beállításra.**  
   * Az **Adatkészlet neve** mezőbe írja be az **átlagos Ár értéket.**  
   * A **Táblanév** mezőbe írja be az **átlagos Ár értéket.**  
   * Válassza az **Engedélyezés** gombot, majd kövesse az utasításokat a Power BI-hoz való csatlakozás engedélyezéséhez.  
   * Válassza ki a **Mentés** gombot.  

8. Ezután lépjen vissza a **streamjob1-hez,** és válassza **a Lekérdezés szerkesztése**lehetőséget.

   ![Lekérdezés szerkesztése](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Illessze be a következő lekérdezést a lekérdezési ablakba. Az **ÁTLAGÁR** lekérdezés kiszámítja a felhasználók által megtekintett összes cikk átlagárát, a felhasználók kosaraihoz hozzáadott összes cikk átlagárát, valamint a felhasználók által megvásárolt cikkek átlagárát. Ez a mérőszám segíthet az e-kereskedelmi vállalatoknak eldönteni, hogy milyen árakon értékesítsék a cikkeket, és milyen készletbe fektessenek be. Ha például a megtekintett cikkek átlagára sokkal magasabb, mint a megvásárolt cikkek átlagára, akkor a vállalat dönthet úgy, hogy kevésbé költséges cikkeket ad hozzá a készletéhez.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Ezután válassza a **Mentés** gombot a bal felső sarokban.  

11. Most térjen vissza **a streamjob1-hez,** és válassza a Lap tetején található **Start** gombot. Az Azure Stream Analytics indítása néhány percet is igénybe vehet, de végül látni fogja, hogy "Indítás" és "Futás" között változik.

## <a name="connect-to-power-bi"></a>Csatlakozás a Power BI-hoz

A Power BI egy üzleti elemzési eszközcsomag, mellyel adatokat elemezhet és megoszthatja a levont következtetéseket. Ez egy nagyszerű példa arra, hogyan lehet stratégiailag vizualizálni az elemzett adatokat.

1. Jelentkezzen be a Power BI-ba, és keresse meg a **Saját munkaterület** menüt a lap bal oldalán található menü megnyitásával.  

2. Válassza a **+ Create lehetőséget** a jobb felső sarokban, majd az **Irányítópult** lehetőséget az irányítópult létrehozásához.  

3. Válassza a **+ Hozzáadás választógombot** a jobb felső sarokban.  

4. Válassza **az Egyéni streamelési adatok**lehetőséget, majd a **Tovább** gombot.  
 
5. Válassza az **átlagár** lehetőséget **az ADATKÉSZLETEK közül,** majd válassza a **Tovább**gombot.  

6. A **Vizualizáció típusa** mezőben válassza a legördülő menü **Csoportosított sávdiagramparancsát.** A **Tengely csoportban**adjon hozzá műveletet. A **jelmagyarázat** kihagyása nélkül. Ezután a **következő, Érték**című szakaszban adja hozzá **az átlagot.** Válassza a **Tovább**lehetőséget, majd adja meg a diagram címét, és válassza **az Alkalmaz**lehetőséget. Az irányítópulton egy új diagramot kell látnia!  

7. Most, ha azt szeretné, hogy több metrikák megjelenítéséhez, visszatérhet **a streamjob1** és hozzon létre három további kimenetek a következő mezőket.

   a. **Kimeneti alias:** incomingRevenueOutput, Dataset name: incomingRevenue, Tábla neve: incomingRevenue  
   b. **Kimeneti alias:** top5Output, Adatkészlet neve: top5, Tábla neve: top5  
   c. **Kimeneti alias:** uniqueVisitorCountOutput, Dataset name: uniqueVisitorCount, Táblanév: uniqueVisitorCount

   Ezután válassza **a Lekérdezés szerkesztése lehetőséget,** és illessze be a következő lekérdezéseket a már írt **lekérdezés fölé.**

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
   
   A TOP 5 lekérdezés kiszámítja az első 5 elemet, rangsorolva a megvásárolt alkalmak száma alapján. Ez a mérőszám segíthet az e-kereskedelmi vállalatoknak annak értékelésében, hogy mely cikkek a legnépszerűbbek, és befolyásolhatják a vállalat hirdetési, árképzési és készletdöntéseit.

   A BEVÉTEL lekérdezés úgy számítja ki a bevételt, hogy összefoglalja az egyes percenként vásárolt cikkek árát. Ez a mérőszám segíthet az e-kereskedelmi vállalatoknak a pénzügyi teljesítmény értékelésében, és annak megértésében, hogy a napszakok milyen mértékben járulnak hozzá a legtöbb bevételhez. Ez hatással lehet az általános vállalati stratégia, különösen a marketing.

   Az EGYEDI LÁTOGATÓK lekérdezés kiszámítja, hogy hány egyedi látogató van az oldalon 5 másodpercenként az egyedi kosárazonosítók észlelésével. Ez a mérőszám segíthet az e-kereskedelmi vállalatoknak a webhelybeli tevékenység értékelésében és a további ügyfelek megszerzésének módjának kialakításában.

8. Mostantól ezeket az adatkészleteket is hozzáadhatja csempékhez.

   * A Top 5 esetében célszerű egy csoportosított oszlopdiagramot készíteni, amelynek tengelye az elemek, a szám pedig az érték.  
   * A bevételek esetében célszerű lenne egy sordiagramot készíteni, amelynek ideje a tengely, az árak összege pedig az érték. A megjelenítendő időablaknak a lehető legnagyobbnak kell lennie ahhoz, hogy a lehető legtöbb információt szolgálhassa.  
   * Az egyedi látogatók, lenne értelme, hogy csinál egy kártya megjelenítés a száma egyedi látogatók, mint az érték.

   Így néz ki egy mintairányítópult a következő diagramokkal:

   ![vizualizációk](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Nem kötelező: Vizualizálás e-kereskedelmi webhelysegítségével

Most megfigyelheti, hogyan használhatja az új adatelemző eszközt egy valódi e-kereskedelmi webhelyhez való csatlakozáshoz. Az e-kereskedelmi webhely létrehozásához használjon Egy Azure Cosmos-adatbázist a termékkategóriák (Nők, Férfiak, Unisex), a termékkatalógus és a legnépszerűbb elemek listájának tárolásához.

1. Lépjen vissza az [Azure Portalra,](https://portal.azure.com/)majd a **Cosmos DB-fiókjába,** majd az **Adatkezelőbe.**  

   Adjon hozzá két gyűjteményt **a changefeedlabdatabase** - **termékekhez** és a rögzített tárolókapacitással rendelkező **kategóriákhoz.**

   Adjon hozzá egy másik gyűjteményt a **topItems** nevű **changefeedlabdatabase** és **a /Item** partíciókulcs alatt.

2. Jelölje ki a **topItems** gyűjteményt, és a **Méretezés és beállítások** csoportban állítsa az Élő idő **30** **másodpercre,** hogy a topItems 30 másodpercenként frissüljön.

   ![Élettartam](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Annak érdekében, hogy feltöltse a **topItems** gyűjteményt a leggyakrabban vásárolt elemekkel, lépjen vissza **a streamjob1-re,** és adjon hozzá egy új **kimenetet**. Válassza a **Cosmos DB lehetőséget.**

4. Töltse ki a szükséges mezőket az alábbi képen látható módon.

   ![Cosmos kimenet](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Ha hozzáadta az opcionális TOP 5 lekérdezést a tesztkörnyezet előző részében, folytassa az 5a. Ha nem, folytassa az 5b.

   5a. A **streamjob1-ben**válassza **a Lekérdezés szerkesztése lehetőséget,** és illessze be a következő lekérdezést az Azure Stream Analytics lekérdezésszerkesztőben a TOP 5 lekérdezés alatt, de a többi lekérdezés felett.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. A **streamjob1,válassza** **a Lekérdezés szerkesztése és** beillesztése a következő lekérdezést az Azure Stream Analytics lekérdezésszerkesztőben az összes többi lekérdezések felett.

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

6. Nyissa **meg az ecommerceWebApp.sln fájlt,** és keresse meg a **Web.config** fájlt a **Solution Explorer**programban.  

7. A `<appSettings>` blokkon belül adja hozzá a korábban mentett **URI-t** és **elsődleges kulcsot,** ahol itt az **URI** és az elsődleges **kulcs itt**található. Ezután adja hozzá az **adatbázis nevét** és a **gyűjtemény nevét** a jelzett módon. (Ezek a nevek kell **changefeedlabdatabase** és **changefeedlabcollection,** kivéve, ha úgy döntött, hogy nevet tiéd másképp.)

   Töltse ki a **termékgyűjtemény nevét,** **a kategóriák gyűjteményének nevét**és a felső cikkek **gyűjteményének nevét** a jelzett módon. (Ezeknek a neveknek **termékeknek, kategóriáknak és topElemeknek** kell lenniük, kivéve, ha ön másként nevezte el a tiédet.)  

8. Keresse meg és nyissa meg a **Pénztár mappát** az **EcommerceWebApp.sln fájlban.** Ezután nyissa meg a mappában lévő **Web.config** fájlt.  

9. A `<appSettings>` blokkon belül adja hozzá a korábban mentett **URI-t** és **elsődleges kulcsot,** ahol azt jelezték. Ezután adja hozzá az **adatbázis nevét** és a **gyűjtemény nevét** a jelzett módon. (Ezek a nevek kell **changefeedlabdatabase** és **changefeedlabcollection,** kivéve, ha úgy döntött, hogy nevet tiéd másképp.)  

10. A program futtatásához nyomja le a lap tetején található **Indítás** gombot.  

11. Most már játszhat az e-kereskedelmi oldalon. Amikor megtekint egy elemet, hozzáad egy elemet a kosárhoz, módosítja a kosárban lévő elem mennyiségét, vagy megvásárol egy cikket, ezek az események a Cosmos DB módosítási hírcsatornáján keresztül az Event Hub, ASA, majd a Power BI számára kerülnek átadásra. Javasoljuk, hogy továbbra is futtassa a DataGenerator-t, hogy jelentős webes forgalmi adatokat generáljon, és reális "Forró termékeket" biztosítson az e-kereskedelmi webhelyen.

## <a name="delete-the-resources"></a>Az erőforrások törlése

A labor során létrehozott erőforrások törléséhez keresse meg az [Azure Portal](https://portal.azure.com/)erőforráscsoportját, majd válassza az **Erőforráscsoport törlése parancsot** a lap tetején található menüből, és kövesse a megadott utasításokat.

## <a name="next-steps"></a>További lépések 
  
* Ha többet szeretne megtudni a változási hírcsatorna, olvassa el [a változáscsatorna-támogatás az Azure Cosmos DB-ben](change-feed.md) 
