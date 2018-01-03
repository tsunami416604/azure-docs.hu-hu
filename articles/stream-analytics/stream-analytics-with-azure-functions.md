---
title: Futtassa az Azure Functions az Azure Stream Analytics-feladatok |} Microsoft Docs
description: "Megtudhatja, hogyan konfigurálhatja az Azure-függvény szolgáltatást kimeneti fogadóként az adatfolyam elemzési feladatok."
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
ms.openlocfilehash: adc147fc9f47e78cc0a2fcaf53f064bcfa5eee2c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Az Azure Functions az Azure Stream Analytics-feladatok futtatása 
 
> [!IMPORTANT]
> Ez a funkció előzetes verzió van.

Úgy konfigurálja az Azure Functions számít a kimeneti nyelő a Stream Analytics-feladathoz futtathatja az Azure Functions az Azure Stream Analytics használ. Az Azure függvény egy eseményvezérelt, számítási igény élmény, amely lehetővé teszi, amely az Azure vagy harmadik féltől származó szolgáltatással téve események által kiváltott kód megvalósítását. E képesség Azure függvény eseményindítók révén természetes kimeneti Azure Stream Analytics-feladathoz.

Az Azure Stream Analytics hív meg Azure-függvény HTTP eseményindítók keresztül. Az Azure-függvény konzolkimeneti adapter lehetővé teszi, hogy a felhasználók kapcsolódhatnak az Azure Functions a Stream Analytics, úgy, hogy az eseményeket is elindítható a Stream Analytics-lekérdezés alapján. 

Ez az oktatóanyag bemutatja, hogyan csatlakozzon az Azure Stream Analytics [Azure Redis Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) használatával [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-stream-analytics-job-to-run-an-azure-function"></a>Egy Azure-függvény futtatásához Stream Analytics-feladat konfigurálása 

Ez a szakasz bemutatja, hogyan konfigurálhatja a Stream Analytics-feladat futtatásához egy Azure-függvény, amely az Azure Redis Cache írja az adatokat. A Stream Analytics-feladat olvassa be az eseményeket az Event Hubs, végrehajtja a lekérdezést, amely meghívja az Azure függvényt. Az Azure-függvény olvassa be az adatokat a Stream Analytics-feladat, és az Azure Redis Cache írja azt.

![Az ábra mutatja be az oktatóanyag](./media/stream-analytics-with-azure-functions/image1.png)

A következő lépések szükségesek, ez a feladat eléréséhez:
* [Hozzon létre Stream Analytics-feladat az Event Hubs bemeneti adatként.](#create-stream-analytics-job-with-event-hub-as-input)  
* [Hozzon létre egy Azure Redis gyorsítótár.](#create-an-azure-redis-cache)  
* [Egy Azure-függvény, amely lehet adatokat írni a Redis Cache létrehozása.](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Frissítse a adatfolyam analitikus feldolgozás Azure-függvény output típusúként.](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Redis gyorsítótár ellenőrizze az eredményeket.](#check-redis-cache-for-results)  

## <a name="create-stream-analytics-job-with-event-hub-as-input"></a>Stream Analytics-feladat létrehozása az Eseményközpont bemenetként

Kövesse a [valós idejű csalások felderítéséhez](stream-analytics-real-time-fraud-detection.md) az oktatóanyagban létrehoz egy eseményközpontot, indítsa el az esemény-készítő alkalmazás, és hozzon létre egy Azure Stream Analytics (átugorja a lekérdezés és a kimeneti létrehozásának lépéseit, hanem állítják egy Az Azure Functions kimenete a következő szakaszban.)

## <a name="create-an-azure-redis-cache"></a>Azure Redis Cache létrehozása

1. Hozzon létre egy Azure Redis Cache ismertetett lépések segítségével [gyorsítótár létrehozásához](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) a Redis Cache című szakaszban.  

2. A Redis gyorsítótár létrehozása után nyissa meg a létrehozott gyorsítótár > **hívóbetűk** >, és jegyezze fel a **elsődleges kapcsolódási karakterlánc**.

   ![Redis gyorsítótár kapcsolati karakterlánc](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-an-azure-function-that-can-write-data-to-the-redis-cache"></a>Egy Azure-függvény, amely lehet adatokat írni a Redis Cache létrehozása

1. Használja a [függvény-alkalmazás létrehozása](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) hozzon létre egy Azure-függvény alkalmazást az Azure Functions dokumentáció része, és egy [Azure-függvény HTTP-eseményindítóval aktivált](../azure-functions/functions-create-first-azure-function.md#create-function) (más néven Webhook) használatával **CSharp** nyelv.  

2. Keresse meg a **run.csx** funkciót, és frissíti a következő kódot (Győződjön meg arról, hogy a "\<ide kerül a redis gyorsítótár kapcsolati karakterlánc\>" szöveget a Redis Cache elsődleges kapcsolati karakterlánccal hogy lekért az előző szakaszban):  

   ```c#
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

   Azure Stream Analytics 413 (Http kérelem entitás túl nagy) kivétel megkapja az Azure-függvény, csökkentik az Azure Functions küld a kötegek mérete. Az Azure-függvény az alábbi kód segítségével ellenőrizze, hogy az Azure Stream Analytics nem küld túlméretes kötegek. Győződjön meg arról, hogy a Köteg maximális száma és mérete a függvényben értékei a Stream Analytics-portálon megadott értékek konzisztens.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Egy szövegszerkesztőben, az Ön által választott, hozzon létre egy JSON fájlt **project.json** a következő kódra, és mentse azt a helyi számítógépen. Ez a fájl tartalmazza a c# függvény által igényelt NuGet csomag függőségeit.  
   
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
 
4. Lépjen vissza az Azure portál > és az Azure-függvény lép > a a **Platform funkciói** lapon > kattintson a **App Service-szerkesztő** alatt található **Fejlesztőeszközök**. 
 
   ![App service-szerkesztő képernyő](./media/stream-analytics-with-azure-functions/image3.png)

5. A az App Service-szerkesztőben kattintson jobb gombbal a gyökérkönyvtárban, és töltse fel a **project.json** fájlt. A feltöltés befejezését követően frissítse az oldalt, ekkor megjelenik egy automatikusan létrehozott fájlt **project.lock.json**.  Az automatikusan létrehozott fájl a DLL-fájlok a Project.json fájlban megadott hivatkozást tartalmaz.  

   ![Project.json-fájl feltöltése](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytic-job-with-azure-function-as-output"></a>Frissítés az Azure-függvény adatfolyam elemzési feladatot kimenetként

1. Nyissa meg az Azure Stream Analytics-feladat az Azure portálon.  

2. Keresse meg az Azure-függvény > **áttekintése** > **kimenetek** > **Hozzáadás** egy új kimeneti > Válasszon **Azure-függvény** fogadó beállítás. Az új Azure-függvény kimeneti adapter áll rendelkezésre a következő tulajdonságokkal:  

   |**Tulajdonság neve**|**Leírás**|
   |---|---|
   |A kimeneti alias| Egy rövid nevet használt hivatkozás a kimeneti a feladat-lekérdezést. |
   |A beállítás importálása| A jelenlegi előfizetés azure függvényt, vagy adja meg a beállításokat manuálisan Ha más előfizetés található, a függvény. |
   |Függvényalkalmazás| Az Azure-függvény alkalmazás neve. |
   |Függvény| A függvény alkalmazásban (a run.csx függvény neve) függvény neve.|
   |Maximális kötegméret|Ez a tulajdonság segítségével minden egyes kimeneti kötegelt, az Azure-függvény küldött maximális méretének beállítása. Alapértelmezés szerint a értéke legfeljebb 256 KB.|
   |Maximális kötegek száma|Ez a tulajdonság lehetővé teszi az egyes kötegekben, az Azure-függvény küldött események maximális számának megadásához. Az alapértelmezett maximális száma érték 100. Ez a tulajdonság nem kötelező.|
   |Kulcs|Ez a tulajdonság lehetővé teszi egy másik előfizetésből egy Azure-függvény használatát. Adja meg a kulcs értékét a funkció elérésének. Ez a tulajdonság nem kötelező.|

3. Adja meg a kimeneti alias nevét. Az oktatóanyag azt adjon neki nevet **saop1** (használhatja az Ön által választott bármely más neve) és egyéb adatait adja meg.  

4. Nyissa meg a Stream Analytics-feladat, és a lekérdezés frissítése (feltétlenül cserélje le a "saop1" szöveg, ha a kimeneti fogadóját elnevezett eltérően) a következők:  

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

5. A parancssorban a következő parancs futtatásával a telcodatagen.exe alkalmazás indításához (a parancs felsorolásszerűen a formátum - `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`)  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Indítsa el a Stream Analytics-feladathoz.

## <a name="check-redis-cache-for-results"></a>Ellenőrizze a Redis Cache eredmények

1. Navigáljon az Azure portálra, és keresse a Redis Cache > Válasszon **konzol**.  

2. Használjon [Redis gyorsítótár parancsok](https://redis.io/commands) annak ellenőrzéséhez, hogy az adatok a Redis gyorsítótár. (A parancs felsorolásszerűen a formátum-Get {kulcs}). Példa:

   **Első "12/19/2017 21:32:24-123414732"**

   Ez a parancs a megadott kulcs kell nyomtatása:

   ![Redis gyorsítótár kimenete](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Ismert problémák

* Az Azure portálon, amikor visszaállítani a maximális kötegmérete / maximális kötegek száma value empty(default), a biztonsági mentés esetén a korábban megadott érték változik. Úgy adja meg az alapértelmezett értékeket a mezők ebben az esetben.

## <a name="next-steps"></a>További lépések
