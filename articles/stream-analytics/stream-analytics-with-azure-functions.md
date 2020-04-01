---
title: Oktatóanyag – Azure-függvények futtatása az Azure Stream Analytics-feladatokban
description: Ebben az oktatóanyagban megtanulhatja, hogy hogyan konfigurálhatja az Azure Functionst a Stream Analytics-feladatok kimeneti fogadójaként.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/27/2020
ms.openlocfilehash: 837174b3ccc08a74583587cb9efd34f8f720aec5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77589453"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Oktatóanyag: Azure-függvények futtatása az Azure Stream Analytics-feladatokból 

Az Azure Functions az Azure Stream Analytics szolgáltatásból való futtatásához konfigurálja a Functionst a Stream Analytics-feladat kimeneti fogadóinak egyikeként. A Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amellyel az Azure-ban vagy külső szolgáltatásokban bekövetkező események által aktivált kódok implementálhatók. Az eseményindítókra való reagálás képessége a Stream Analytics-feladatok természetes kimenetévé teszi a Functionst.

A Stream Analytics HTTP-eseményindítókkal hívja meg a Functionst. A Functions kimeneti adaptere segítségével a felhasználók a Stream Analyticshez csatlakoztathatják a Functionst, és így az események a Stream Analytics-lekérdezések alapján aktiválhatók. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Stream Analytics-feladat létrehozása és futtatása
> * Azure-gyorsítótár létrehozása a Redis-példányhoz
> * Azure-függvény létrehozása
> * Eredmények érti az Azure Cache for Redis-t

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Stream Analytics-feladat konfigurálása függvény futtatására 

Ez a szakasz bemutatja, hogyan konfigurálhat egy Stream Analytics-feladatot egy olyan függvény futtatásához, amely adatokat ír az Azure Cache for Redis számára. A Stream Analytics-feladat eseményeket olvas be az Azure Event Hubsból, és futtat egy lekérdezést, amely meghívja a függvényt. Ez a függvény beolvassa az adatokat a Stream Analytics-feladatból, és írja az okat az Azure Cache for Redis.

![Az Azure-szolgáltatások közötti kapcsolatokat bemutató ábra](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Stream Analytics-feladat létrehozása az Event Hubs szolgáltatást megadva bemenetként

[Az adathamisítások azonnali felismerése](stream-analytics-real-time-fraud-detection.md) oktatóanyag lépéseit követve hozzon létre egy eseményközpontot, indítsa el az eseménylétrehozó alkalmazást, és hozzon létre egy Stream Analytics-feladatot. Hagyja ki a lekérdezés és a kimenet létrehozásához szükséges lépéseket. Ehelyett tekintse meg a következő szakaszokat egy Azure Functions kimenet beállításához.

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure-gyorsítótár létrehozása a Redis-példányhoz

1. Hozzon létre egy gyorsítótárat az Azure Cache for Redis-ben a [Gyorsítótár létrehozása](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)című részben leírt lépésekkel.  

2. Miután létrehozta a gyorsítótárat, a **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Jegyezze fel az **Elsődleges kapcsolati sztring** értékét.

   ![Képernyőkép a Redis-kapcsolati karakterlánchoz készült Azure-gyorsítótárról](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Hozzon létre egy függvényt az Azure Functionsben, amely adatokat írhat a Redis Azure-gyorsítótárába

1. Tekintse meg a Functions dokumentációjának [Függvényalkalmazás létrehozása](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) című szakaszát. Ez a szakasz bemutatja, hogyan hozhat létre egy függvényalkalmazást és egy [HTTP-aktivált függvényt](../azure-functions/functions-create-first-azure-function.md#create-function)az Azure Functionsben a CSharp-nyelv használatával.  

2. Keresse meg a **run.csx** függvényt. Frissítse az alábbi kóddal. Cserélje le **a "\<az Azure\>Cache for Redis kapcsolati karakterlánc megy itt "** az Azure Cache for Redis elsődleges kapcsolati karakterlánc, amely az előző szakaszban lekért. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Ha a Stream Analytics a „HTTP Request Entity Too Large” (HTTP-kérelemegység túl nagy) kivételt kapja vissza a függvénytől, csökkenti a Functionsbe küldött kötegek méretét. A következő kód biztosítja, hogy a Stream Analytics ne küldjön túlméretes kötegeket. Ellenőrizze, hogy a függvényben használt maximális kötegszám- és kötegméretértékek egyeznek a Stream Analytics-portálon megadott értékekkel.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Hozzon létre a **project.json** nevű JSON-fájlt egy tetszőleges szövegszerkesztőben. Illessze be a következő kódot, és mentse a helyi számítógépre. Ez a fájl tartalmazza a C#-függvény által igényelt NuGet-csomagfüggőségeket.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Lépjen vissza az Azure Portalra. A **Platformfunkciók** lapon keresse meg a függvényt. A **Fejlesztési eszközök** részen válassza az **App Service Editor** elemet. 
 
   ![Az App Service Editor képernyőképe](./media/stream-analytics-with-azure-functions/image3.png)

5. Az App Service Editorban kattintson a jobb gombbal a gyökérkönyvtárba, és töltse fel a **project.json** fájlt. Ha a feltöltés sikeres, frissítse az oldalt. Meg kell jelennie egy automatikusan létrehozott fájlnak **project.lock.json** néven. Az automatikusan létrehozott fájl a project.json fájlban megadott .dll fájlokra mutató hivatkozásokat tartalmaz.  

   ![Az App Service Editor képernyőképe](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>A Stream Analytics-feladat frissítése a kimenetként megadott függvénnyel

1. Nyissa meg a Stream Analytics-feladatot az Azure Portalon.  

2. Tallózással keresse meg a funkciót, és válassza **az Áttekintés** > **kimenetek** > **hozzáadása lehetőséget.** Új kimenet hozzáadásához válassza az **Azure-függvény** lehetőséget a fogadó beállításánál. A Functions kimeneti adapter a következő tulajdonságokkal rendelkezik:  

   |**Tulajdonság neve**|**Leírás**|
   |---|---|
   |Kimeneti alias| A feladat lekérdezésében a kimenetre való hivatkozáshoz használt rövid név. |
   |Importálási beállítás| A függvényt használhatja az aktuális előfizetésből, vagy megadhatja manuálisan a beállításokat, ha a függvény egy másik előfizetésben található. |
   |Függvényalkalmazás| A Functions-alkalmazás neve. |
   |Függvény| A függvény neve a Functions-alkalmazásban (a run.csx függvény neve).|
   |Maximális kötegméret|Beállítja az egyes kimeneti kötegek maximális méretét, amelyet a függvény bájtban küld el. Alapértelmezés szerint ez az érték 262 144 bájt (256 KB) értékre van állítva.|
   |Maximális kötegszám|A függvénybe küldött egyes kötegekben lévő események maximális számának megadására szolgál. Az alapértelmezett érték 100. Ez a tulajdonság nem kötelező.|
   |Kulcs|Más előfizetésből származó függvény használatát teszi lehetővé. A függvény eléréséhez adja meg a kulcs értékét. Ez a tulajdonság nem kötelező.|

3. Adja meg a kimeneti alias nevét. Ebben az oktatóanyagban **saop1**névnek nevezik, de bármilyen tetszőleges nevet használhat. Adja meg az egyéb adatokat.

4. Nyissa meg a Stream Analytics-feladatot, és frissítse a lekérdezést az alábbiakra. Ha nem nevezte el a kimeneti fogadó **saop1**nevet, ne felejtse el módosítani a lekérdezésben.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Indítsa el a telcodatagen.exe alkalmazást a következő parancs futtatásával a parancssorban. A parancs a `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`formátumot használja.  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Indítsa el a Stream Analytics-feladatot.

## <a name="check-azure-cache-for-redis-for-results"></a>Eredmények érti az Azure Cache for Redis-t

1. Tallózással keresse meg az Azure Portalt, és keresse meg a Redis Azure-gyorsítótárát. Válassza a **Konzol** lehetőséget.  

2. Az [Azure Cache for Redis parancsokkal](https://redis.io/commands) ellenőrizze, hogy az adatok az Azure Cache for Redis.Use Azure Cache for Redis commands to verify that your data is in Azure Cache for Redis. (A parancs a(z) {key} formátumot veszi fel.) Például:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Ennek a parancsnak a megadott kulcshoz tartozó értéket kell kiírnia:

   ![Képernyőkép az Azure Cache for Redis kimenetről](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Ha hiba történik az Azure Functions események küldése közben, a Stream Analytics újrapróbálkozik a legtöbb művelettel. Az összes http kivételt a rendszer a sikerességig újra megpróbálja, kivéve a http 413-as hibát (az entitás túl nagy). A túl nagy méretű entitáshibákat a rendszer adathibaként kezeli, amelyet az [újrapróbálkozási vagy áthelyezési házirend](stream-analytics-output-error-policy.md)alá tartozó .

> [!NOTE]
> A Stream Analytics és az Azure Functions HTTP-kéréseiidő-megadása 100 másodpercre van állítva. Ha az Azure Functions alkalmazás több mint 100 másodpercet vesz igénybe egy köteg feldolgozásához, a Stream Analytics-hibák ki.

## <a name="known-issues"></a>Ismert problémák

Amikor az Azure Portalon megpróbálja üres (alapértelmezett) értékre visszaállítani a Maximális kötegméret/Maximális kötegszám értékét, mentéskor az érték visszavált a korábban megadott értékre. Ebben az esetben manuálisan adja meg az alapértelmezett értékeket ezekben a mezőkben.

A [HTTP-útválasztás](https://docs.microsoft.com/sandbox/functions-recipes/routes?tabs=csharp) használatát az Azure-függvényekben jelenleg nem támogatja a Stream Analytics.

A virtuális hálózatban üzemeltetett Azure Functionshez való csatlakozás támogatása nincs engedélyezve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyszerű Stream Analytics-feladatot, amely egy Azure-függvényt futtat. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Felhasználói JavaScript-függvények futtatása Stream Analytics-feladatokban](stream-analytics-javascript-user-defined-functions.md)
