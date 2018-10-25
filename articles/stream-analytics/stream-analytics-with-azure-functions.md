---
title: 'Oktatóanyag: Az Azure Functions futtatása Azure Stream Analytics-feladatokkal | Microsoft Docs'
description: Ebben az oktatóanyagban megtanulhatja, hogy hogyan konfigurálhatja az Azure Functionst a Stream Analytics-feladatok kimeneti fogadójaként.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.workload: data-services
ms.date: 04/09/2018
ms.author: jasonh
ms.reviewer: jasonh
ms.openlocfilehash: 50ea4dafe7edfdeb851ad6d9cc42a7bca262e970
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985811"
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Az Azure Functions futtatása Azure Stream Analytics-feladatokból 

Az Azure Functions az Azure Stream Analytics szolgáltatásból való futtatásához konfigurálja a Functionst a Stream Analytics-feladat kimeneti fogadóinak egyikeként. A Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amellyel az Azure-ban vagy külső szolgáltatásokban bekövetkező események által aktivált kódok implementálhatók. Az eseményindítókra való reagálás képessége a Stream Analytics-feladatok természetes kimenetévé teszi a Functionst.

A Stream Analytics HTTP-eseményindítókkal hívja meg a Functionst. A Functions kimeneti adaptere segítségével a felhasználók a Stream Analyticshez csatlakoztathatják a Functionst, és így az események a Stream Analytics-lekérdezések alapján aktiválhatók. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Stream Analytics-feladat létrehozása
> * Azure-függvény létrehozása
> * Azure-függvény konfigurálása a feladat kimeneteként

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Stream Analytics-feladat konfigurálása függvény futtatására 

Ez a szakasz bemutatja, hogyan konfigurálható egy Stream Analytics-feladat az Azure Redis Cache-be adatokat író függvény futtatására. A Stream Analytics-feladat eseményeket olvas be az Azure Event Hubsból, és futtat egy lekérdezést, amely meghívja a függvényt. Ez a függvény beolvassa az adatokat a Stream Analytics-feladatból, majd az Azure Redis Cache-be írja azokat.

![Az Azure-szolgáltatások közötti kapcsolatokat bemutató ábra](./media/stream-analytics-with-azure-functions/image1.png)

A feladat megvalósításához az alábbi lépéseket kell végrehajtani:
* [Stream Analytics-feladat létrehozása az Event Hubs szolgáltatást megadva bemenetként](#create-a-stream-analytics-job-with-event-hubs-as-input)  
* [Azure Redis Cache-példány létrehozása](#create-an-azure-redis-cache-instance)  
* [Adatokat az Azure Redis Cache-be írni képes függvény létrehozása az Azure Functions szolgáltatásban](#create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache)    
* [A Stream Analytics-feladat frissítése a kimenetként megadott függvénnyel](#update-the-stream-analytics-job-with-the-function-as-output)  
* [Az eredmények ellenőrzése az Azure Redis Cache-ben](#check-azure-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Stream Analytics-feladat létrehozása az Event Hubs szolgáltatást megadva bemenetként

[Az adathamisítások azonnali felismerése](stream-analytics-real-time-fraud-detection.md) oktatóanyag lépéseit követve hozzon létre egy eseményközpontot, indítsa el az eseménylétrehozó alkalmazást, és hozzon létre egy Stream Analytics-feladatot. (Hagyja ki a lekérdezés és a kimenet létrehozására szolgáló lépéseket. Helyette tekintse meg a Functions-kimenet beállítására vonatkozó alábbi szakaszokat.)

## <a name="create-an-azure-redis-cache-instance"></a>Azure Redis Cache-példány létrehozása

1. Hozzon létre egy gyorsítótárat az Azure Redis Cache-ben a [Gyorsítótár létrehozása](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) című szakaszban ismertetett lépéseket követve.  

2. Miután létrehozta a gyorsítótárat, a **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Jegyezze fel az **Elsődleges kapcsolati sztring** értékét.

   ![Az Azure Redis Cache kapcsolati sztringjének képernyőképe](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Adatokat az Azure Redis Cache-be írni képes függvény létrehozása az Azure Functions szolgáltatásban

1. Tekintse meg a Functions dokumentációjának [Függvényalkalmazás létrehozása](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) című szakaszát. Ebből megtudhatja, hogyan hozhat létre függvényalkalmazást és egy [HTTP-eseményindítóval aktivált függvényt az Azure Functionsben](../azure-functions/functions-create-first-azure-function.md#create-function) CSharp nyelven.  

2. Keresse meg a **run.csx** függvényt. Frissítse az alábbi kóddal. (A „\<your redis cache connection string goes here\>” (a redis cache kapcsolati karakterlánca helye) karakterláncot cserélje le az Azure Redis Cache előző szakaszban lekért elsődleges kapcsolati karakterláncára.)  

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
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
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

   Ha a Stream Analytics a „HTTP Request Entity Too Large” (HTTP-kérelemegység túl nagy) kivételt kapja vissza a függvénytől, csökkenti a Functionsbe küldött kötegek méretét. A függvényben az alábbi kód használatával ellenőrizheti, hogy a Stream Analytics nem küld-e túl nagy kötegeket. Ellenőrizze, hogy a függvényben használt maximális kötegszám- és kötegméretértékek egyeznek a Stream Analytics-portálon megadott értékekkel.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Hozzon létre a **project.json** nevű JSON-fájlt egy tetszőleges szövegszerkesztőben. Használja az alábbi kódot, és mentse a helyi számítógépen. Ez a fájl tartalmazza a C#-függvény által igényelt NuGet-csomagfüggőségeket.  
   
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

2. Keresse meg a függvényt, és válassza az **Áttekintés** > **Kimenetek** > **Hozzáadás** elemet. Új kimenet hozzáadásához válassza az **Azure-függvény** lehetőséget a fogadó beállításánál. Rendelkezésre áll az alábbi tulajdonságokkal rendelkező új Functions kimeneti adapter:  

   |**Tulajdonság neve**|**Leírás**|
   |---|---|
   |Kimeneti alias| A feladat lekérdezésében a kimenetre való hivatkozáshoz használt rövid név. |
   |Importálási beállítás| A függvényt használhatja az aktuális előfizetésből, vagy megadhatja manuálisan a beállításokat, ha a függvény egy másik előfizetésben található. |
   |Függvényalkalmazás| A Functions-alkalmazás neve. |
   |Függvény| A függvény neve a Functions-alkalmazásban (a run.csx függvény neve).|
   |Maximális kötegméret|A függvénybe küldött egyes kimeneti kötegek maximális méretének beállítására szolgál. Alapértelmezés szerint ez az érték 256 KB.|
   |Maximális kötegszám|A függvénybe küldött egyes kötegekben lévő események maximális számának megadására szolgál. Az alapértelmezett érték 100. Ez a tulajdonság nem kötelező.|
   |Kulcs|Más előfizetésből származó függvény használatát teszi lehetővé. A függvény eléréséhez adja meg a kulcs értékét. Ez a tulajdonság nem kötelező.|

3. Adja meg a kimeneti alias nevét. Ebben az oktatóanyagban a **saop1** nevet használjuk (bármilyen tetszőleges nevet használhat). Adja meg az egyéb adatokat.  

4. Nyissa meg a Stream Analytics-feladatot, és frissítse a lekérdezést az alábbiakra. (Amennyiben más nevet adott a kimeneti fogadónak, ügyeljen arra, hogy azt a nevet használja a „saop1” helyett.)  

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

5. Indítsa el a telcodatagen.exe alkalmazást az alábbi parancs futtatásával a parancssorban (használja a `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` formátumot):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Indítsa el a Stream Analytics-feladatot.

## <a name="check-azure-redis-cache-for-results"></a>Az eredmények ellenőrzése az Azure Redis Cache-ben

1. Lépjen az Azure Portalra, majd keresse meg az Azure Redis Cache-t. Válassza a **Konzol** lehetőséget.  

2. A [Redis Cache parancsainak](https://redis.io/commands) használatával ellenőrizze, hogy az adatok megtalálhatók-e a Redis Cache-ben. (A parancs formátuma: Get {kulcs}.) Például:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Ennek a parancsnak a megadott kulcshoz tartozó értéket kell kiírnia:

   ![Az Azure Redis Cache-kimenet képernyőképe](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Ismert problémák

Amikor az Azure Portalon megpróbálja üres (alapértelmezett) értékre visszaállítani a Maximális kötegméret/Maximális kötegszám értékét, mentéskor az érték visszavált a korábban megadott értékre. Ebben az esetben manuálisan adja meg az alapértelmezett értékeket ezekben a mezőkben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megelőzheti a feladat által használt streamelési egységek kiszámlázását. Ha a jövőben szeretné még használni a feladatot, leállíthatja, és később újraindíthatja, amikor szüksége lesz rá. Ha többé már nem kívánja használni ezt a feladatot, az alábbi lépéseket követve törölheti a gyors útmutató által létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Azure-függvényt futtató egyszerű Stream Analytics-feladatot. A Stream Analytics-feladatokkal kapcsolatos további információkért folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Felhasználói JavaScript-függvények futtatása Stream Analytics-feladatokban](stream-analytics-javascript-user-defined-functions.md)