---
title: Futtassa az Azure Functions az Azure Stream Analytics-feladatok |} Microsoft Docs
description: "Megtudhatja, hogyan konfigurálhatja az Azure Functions szolgáltatást kimeneti fogadóként Stream Analytics-feladatok."
keywords: "adatfolyam kimeneti, adatok, Azure-függvény"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: f2f4a8d8cda752dc6ed197b8402119f7cbcaf58f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Az Azure Functions az Azure Stream Analytics-feladatok futtatása 

A kimeneti mosdók a Stream Analytics-feladathoz rendelkezésre álló funkciók konfigurálásával futtathatja az Azure Functions az Azure Stream Analytics használ. Funkciók található egy eseményvezérelt, számítási igény, amely lehetővé teszi, amely az Azure vagy harmadik féltől származó szolgáltatással téve események által kiváltott kód megvalósítását. E képesség funkciók eseményindítók természetes kimeneti teszi Stream Analytics-feladatok.

A Stream Analytics funkciók meghívja a HTTP-eseményindítók keresztül. A funkciók a konzolkimeneti adapter úgy, hogy az eseményeket is elindítható a Stream Analytics lekérdezések alapján lehetővé teszi a felhasználóknak a Stream Analytics funkciók csatlakozni. 

Ez az oktatóanyag bemutatja, hogyan kell csatlakozni a Stream Analytics [Azure Redis Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md), használatával [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>A funkció futtatását a Stream Analytics-feladat konfigurálása 

Ez a szakasz bemutatja, hogyan konfigurálhatja a Stream Analytics-feladat futtatásához egy függvényt, amely az Azure Redis Cache írja az adatokat. A Stream Analytics-feladat olvassa be az eseményeket az Azure Event Hubs, és futtat egy lekérdezést, amely meghívja a függvényt. Ez a funkció olvassa be az adatokat a Stream Analytics-feladat, és Azure Redis Cache írja azt.

![Az Azure-szolgáltatások közötti kapcsolatok bemutató ábra](./media/stream-analytics-with-azure-functions/image1.png)

A következő lépések szükségesek, ez a feladat eléréséhez:
* [A Stream Analytics-feladat létrehozása az Event Hubs bemenetként](#create-stream-analytics-job-with-event-hub-as-input)  
* [Hozzon létre egy Azure Redis Cache példányt](#create-an-azure-redis-cache)  
* [Az Azure Functions, amely lehet adatokat írni az Azure Redis Cache-függvény létrehozása](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [A Stream Analytics-feladat a függvény az output típusúként frissítés](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Azure Redis Cache keressen eredmények](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>A Stream Analytics-feladat létrehozása az Event Hubs bemenetként

Kövesse a [valós idejű csalások felderítéséhez](stream-analytics-real-time-fraud-detection.md) létrehoz egy eseményközpontot, az esemény-készítő alkalmazás, és létrehozok egy Stream Analytics-feladat az oktatóanyagot. (Hagyja ki a lekérdezés és a kimeneti létrehozásához szükséges lépéseket. Ehelyett a következő szakaszban talál funkciók kimeneti beállítása.)

## <a name="create-an-azure-redis-cache-instance"></a>Hozzon létre egy Azure Redis Cache példányt

1. A gyorsítótár létrehozása az Azure Redis Cache ismertetett lépések segítségével [gyorsítótár létrehozásához](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. A gyorsítótár, a létrehozása után **beállítások**, jelölje be **hívóbetűk**. Jegyezze fel a **elsődleges kapcsolódási karakterlánc**.

   ![Képernyőfelvétel az Azure Redis Cache kapcsolati karakterlánc](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Az Azure Functions, amely lehet adatokat írni Azure Redis Cache-függvény létrehozása

1. Tekintse meg a [függvény-alkalmazás létrehozása](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) a funkciók dokumentáció része. Ez bemutatja, hogyan szeretne létrehozni egy függvény és egy [HTTP-eseményindítóval aktivált függvény az Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function), a c Sharp nyelv használatával.  

2. Keresse meg a **run.csx** függvény. Frissítse az alábbi kódra. (Győződjön meg arról, hogy "\<ide kerül a redis gyorsítótár kapcsolati karakterlánc\>" az Azure Redis Cache elsődleges kapcsolati karakterlánccal, amely az előző szakaszban lekért.)  

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

   A Stream Analytics a függvényt a "HTTP kérelem entitás túl nagy" kivétel megkapja, csökkenti a funkciók küld a kötegek mérete. A függvény a következő kód segítségével ellenőrizze, hogy a Stream Analytics nem küld túlméretes kötegek. Győződjön meg arról, hogy a Köteg maximális száma és mérete a függvényben értékei a Stream Analytics-portálon megadott értékek konzisztens.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Egy szövegszerkesztőben, az Ön által választott, hozzon létre egy JSON fájlt **project.json**. A következő kódot használja, és mentse a helyi számítógépen. Ez a fájl tartalmazza a C# függvény által igényelt NuGet csomag függőségeit.  
   
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
 
4. Térjen vissza az Azure-portálon. Az a **Platform funkciói** lapján keresse meg a függvény. A **Fejlesztőeszközök**, jelölje be **App Service-szerkesztő**. 
 
   ![Képernyőkép az alkalmazás-szerkesztő](./media/stream-analytics-with-azure-functions/image3.png)

5. A az App Service-szerkesztőben kattintson a jobb gombbal a gyökérkönyvtár, és töltse fel a **project.json** fájlt. A feltöltés befejezését követően frissítse a lapot. Ekkor megjelenik egy automatikusan létrehozott fájlt **project.lock.json**. A automatikusan elnevezi a project.json fájlban megadott .dll fájlokra mutató hivatkozásokat tartalmaz.  

   ![Képernyőkép az alkalmazás-szerkesztő](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>A Stream Analytics-feladat a függvény az output típusúként frissítés

1. Nyissa meg a Stream Analytics-feladat az Azure portálon.  

2. Keresse meg a függvény, és válassza ki **áttekintése** > **kimenetek** > **Hozzáadás**. Egy új kimeneti hozzáadásához válassza **Azure-függvény** fogadó beállítás. Az új funkciók a konzolkimeneti adapter áll rendelkezésre, a következő tulajdonságokkal:  

   |**Tulajdonság neve**|**Leírás**|
   |---|---|
   |Kimeneti alias| Egy rövid nevet használt hivatkozás a kimeneti a feladat-lekérdezést. |
   |Importálási beállítás| A jelenlegi előfizetés függvényt, vagy adja meg a beállításokat manuálisan, ha a függvény egy másik előfizetésben található. |
   |Függvényalkalmazás| A funkciók alkalmazás nevét. |
   |Függvény| A funkciók alkalmazásban (a run.csx függvény neve) függvény neve.|
   |Maximális kötegméret|Minden kimeneti köteg, a függvény küldött maximális méretének beállítása. Alapértelmezés szerint a értéke legfeljebb 256 KB.|
   |Maximális kötegek száma|Az egyes kötegekben, a függvény küldött események maximális számának megadása Az alapértelmezett érték 100. Ez a tulajdonság nem kötelező.|
   |Kulcs|Lehetővé teszi egy másik előfizetésből függvény használható. Adja meg a kulcs értékét a funkció elérésének. Ez a tulajdonság nem kötelező.|

3. Adja meg a kimeneti alias nevét. Az oktatóanyag azt adjon neki nevet **saop1** (használhatja az Ön által választott bármely neve). Töltse ki az egyéb részleteket.  

4. Nyissa meg a Stream Analytics-feladat, és frissítse a lekérdezést a következő. (Ügyeljen cserélje le a "saop1" szöveg, ha a kimeneti fogadóját másképp elnevezett.)  

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

5. A parancssorban a következő parancs futtatásával a telcodatagen.exe alkalmazás indításához (a formátumot használja `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000.2 2**
    
6.  A Stream Analytics-feladat indítása.

## <a name="check-azure-redis-cache-for-results"></a>Azure Redis Cache keressen eredmények

1. Keresse meg az Azure-portálon, és az Azure Redis Cache található. Válassza ki **konzol**.  

2. Használjon [Redis gyorsítótár parancsok](https://redis.io/commands) annak ellenőrzéséhez, hogy az adatok a Redis gyorsítótár. (A parancs felsorolásszerűen a formátum {kulcs} Get.) Példa:

   **Első "12/19/2017 21:32:24-123414732"**

   Ez a parancs a megadott kulcs kell nyomtatása:

   ![Képernyőfelvétel az Azure Redis Cache kimeneti](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Ismert problémák

Az Azure portálon, amikor visszaállítani a maximális kötegmérete / maximális kötegek száma egy érték üres (alapértelmezett), az érték visszavált mentés után a korábban megadott érték. Manuálisan adja meg az alapértelmezett értékeket a mezők ebben az esetben.

