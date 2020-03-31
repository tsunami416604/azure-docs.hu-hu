---
title: Adatgyűjtő API használata adatfolyamat létrehozásához
description: Az Azure Monitor HTTP-adatgyűjtő API-t a POST JSON-adatok hozzáadása a Log Analytics munkaterület bármely ügyfél, amely megtudja hívni a REST API-t. Ez a cikk a fájlokban tárolt adatok automatizált módon történő feltöltését ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055114"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Adatfolyamat létrehozása az adatgyűjtő API-val

Az [Azure Monitor adatgyűjtő API lehetővé](data-collector-api.md) teszi, hogy az egyéni naplóadatok importálása egy Log Analytics-munkaterület az Azure Monitor. Az egyetlen követelmény az, hogy az adatok JSON-formátumúak legyenek, és legkisebb szegmensekre legyenek felosztva. Ez egy teljesen rugalmas mechanizmus, amely sokféleképpen csatlakoztatható: a közvetlenül az alkalmazásból küldött adatoktól az egyszeri adhoc feltöltésig. Ez a cikk ismerteti néhány kiindulási pont egy közös forgatókönyv: a fájlokban tárolt adatok rendszeres, automatizált alapon való feltöltésének szükségességét. Bár az itt bemutatott folyamat nem lesz a leginkább teljesítményt, vagy más módon optimalizált, célja, hogy kiindulópontként szolgáljon a saját termelési folyamat ának kiépítése felé.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Példa problémára
A cikk további részében megvizsgáljuk az oldalmegtekintési adatokat az Application Insightsban. Hipotetikus forgatókönyvünkben azt szeretnénk, hogy az Application Insights SDK által alapértelmezés szerint gyűjtött földrajzi adatokat a világ minden országának/régiójának lakosságát tartalmazó egyéni adatokkal korreljesszük, azzal a céllal, hogy meghatározzuk, hol kell a legtöbb marketingdollárt elköltenünk. 

Erre a célra nyilvános adatforrást használunk, például az [ENSZ világnépességi kilátásait.](https://esa.un.org/unpd/wpp/) Az adatok a következő egyszerű sémával rendelkeznek:

![Példa egyszerű sémára](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

A példánkban azt feltételezzük, hogy feltöltünk egy új fájlt a legutóbbi évi adatokkal, amint azok elérhetővé válnak.

## <a name="general-design"></a>Általános tervezés
Egy klasszikus ETL-típusú logikát használunk a folyamat tervezéséhez. Az architektúra a következőképpen fog kinézni:

![Adatgyűjtési folyamat architektúrája](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Ez a cikk nem ismerteti, hogyan hozhat létre adatokat, vagy [töltse fel őket egy Azure Blob Storage-fiókba.](../../storage/blobs/storage-upload-process-images.md) Inkább akkor vesszük fel a folyamatot, amint egy új fájl feltöltése a blobba. Innen:

1. Egy folyamat észleli, hogy új adatokat töltöttek fel.  A példánk egy [Azure Logic Alkalmazást](../../logic-apps/logic-apps-overview.md)használ, amely rendelkezik egy eseményindítóval a blobba feltöltött új adatok észleléséhez.

2. A processzor beolvassa ezeket az új adatokat, és átalakítja azokat JSON- ra, az Azure Monitor által megkövetelt formátumra Ebben a példában egy [Azure-függvényt](../../azure-functions/functions-overview.md) használunk a feldolgozási kód végrehajtásának könnyű, költséghatékony módjaként. A függvényt ugyanaz a Logikai alkalmazás indította el, amelyet az új adatok észleléséhez használtunk.

3. Végül, ha a JSON-objektum elérhetővé válik, az Azure Monitor ra kerül elküldésre. Ugyanaz a logikai alkalmazás elküldi az adatokat az Azure Monitornak a beépített Log Analytics-adatgyűjtő-tevékenység használatával.

Bár a blob storage, a Logic App vagy az Azure-függvény részletes beállítása nem szerepel ebben a cikkben, részletes utasítások érhetők el az adott termékek oldalain.

A folyamat figyeléséhez az Application Insights segítségével figyelheti az Azure-függvény [részleteit itt,](../../azure-functions/functions-monitoring.md)és az Azure Monitor figyelheti a logikai alkalmazás [részleteit itt.](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md) 

## <a name="setting-up-the-pipeline"></a>A csővezeték beállítása
A folyamat beállítása, először győződjön meg arról, hogy a blob tároló létrehozása és konfigurálása. Hasonlóképpen győződjön meg arról, hogy a Log Analytics munkaterület, ahová el szeretné küldeni az adatokat, létre jön.

## <a name="ingesting-json-data"></a>JSON-adatok betöltése
JSON-adatok betöltése triviális a Logic Apps, és mivel nem átalakításra van szükség, a teljes folyamat egyetlen Logic App. Miután mind a blob tároló és a Log Analytics munkaterület konfigurálva, hozzon létre egy új logikai alkalmazást, és konfigurálja azt az alábbiak szerint:

![Példa logikai alkalmazások munkafolyamatára](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Mentse a logikai alkalmazást, és folytassa a tesztelést.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML, CSV vagy más adatformátumok betöltése
A Logic Apps ma nem rendelkezik beépített képességekkel az XML, CSV vagy más típusok JSON formátumba való egyszerű átalakításához. Ezért más eszközöket kell használnunk ennek az átalakulásnak a befejezéséhez. Ebben a cikkben az Azure Functions kiszolgáló nélküli számítási képességeit használjuk, mint egy nagyon könnyű és költségbarát módot erre. 

Ebben a példában egy CSV-fájlt elemezünk, de bármely más fájltípus hasonlóan feldolgozható. Egyszerűen módosítsa az Azure-függvény deszerializáló részét, hogy tükrözze az adott adattípus megfelelő logikáját.

1.  Hozzon létre egy új Azure-függvényt a függvény futásidejű v1 és a felhasználás-alapú, amikor a rendszer kéri.  Válassza ki a C#-ot célzó **HTTP-eseményindító** sablont kiindulási pontként, amely a kötéseket szükség szerint konfigurálja. 
2.  A jobb oldali ablaktábla **Fájlok megtekintése** lapján hozzon létre egy **project.json** nevű új fájlt, és illessze be a következő kódot az általunk használt NuGet csomagokból:

    ![Az Azure Functions példaprojektje](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Váltson a **futtatásra** a jobb oldali ablaktáblából, és cserélje le az alapértelmezett kódot a következőkre. 

    >[!NOTE]
    >A projekthez le kell cserélnie a rekordmodellt (a "PopulationRecord" osztályt) a saját adatsémájára.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Mentse el a funkcióját.
5. Ellenőrizze a függvény működését. Váltson a jobb oldali ablaktábla **Teszt** fülére, és a következőképpen konfigurálja a tesztet. Helyezzen egy hivatkozást egy blobra mintaadatokkal a **Testtörzs kérése** szövegmezőbe. A **Futtatás**gombra kattintás után a JSON-kimenetnek a **Kimenet** mezőben kell lennie:

    ![Függvényalkalmazások tesztkódja](./media/create-pipeline-datacollector-api/functions-test-01.png)

Most vissza kell mennünk, és módosítanunk kell a logic app ot, amelyet korábban kezdtünk el építeni, hogy tartalmazza a bevitt és JSON formátumra konvertált adatokat.  A Designer megtekintése segítségével konfigurálja az alábbiakat, majd mentse a logikai alkalmazást:

![A Logic Apps munkafolyamat teljes példája](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>A csővezeték tesztelése
Most egy új fájlt tölthet fel a blob korábban konfigurált, és a logikai alkalmazás által figyelt. Hamarosan meg kell jelennie egy új példányát a Logic App elindul, hívja fel az Azure-függvényt, majd sikeresen küldje el az adatokat az Azure Monitor. 

>[!NOTE]
>Az adatok megjelenése az Azure Monitorban akár 30 percet is igénybe vehet, amikor először küld új adattípust.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>A Log Analytics és az Application Insights más adataival való korreláció
Az Application Insights-oldalmegtekintési adatok nak az egyéni adatforrásból bevitt népességadatokkal való összevetésére irányuló célunk teljesítéséhez futtassa a következő lekérdezést az Application Insights Analytics-ablakból vagy a Log Analytics-munkaterületből:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A kimenetnek meg kell jelennie a két adatforrás most csatlakozott.  

![A leválasztott adatok korrelációja a keresési eredményben példa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Javasolt fejlesztések egy termelési folyamathoz
Ez a cikk bemutatott egy működő prototípus, a logika, amely mögött lehet alkalmazni egy igazi termelési minőségű megoldás. Egy ilyen gyártási minőségű megoldás esetében a következő fejlesztések ajánlottak:

* Adja hozzá a hibakezelést, és próbálja meg újra logika a logikai alkalmazás és a függvény.
* Adjon hozzá logikát annak érdekében, hogy a 30 MB/egyetlen Log Analytics-betöltési API-hívási korlát ne lépjék túl. Szükség esetén ossza fel az adatokat kisebb szegmensekre.
* Állítsa be a törlési szabályzatot a blob storage.Set up a clean-up policy on your blob storage. Miután sikeresen elküldte a Log Analytics munkaterületre, hacsak nem szeretné a nyers adatokat archiválási célokra elérhetővé tenni, nincs ok a tárolás folytatására. 
* Ellenőrizze, hogy a figyelés engedélyezve van-e a teljes folyamaton keresztül, szükség szerint nyomkövetési pontokat és riasztásokat adva hozzá.
* Használja ki a forrásvezérlést a függvény és a logikai alkalmazás kódjának kezeléséhez.
* Győződjön meg arról, hogy a megfelelő változáskezelési házirendet követi, hogy ha a séma megváltozik, a függvény és a Logic Apps ennek megfelelően módosul.
* Ha több különböző adattípust tölt fel, ossza el őket a blobtárolón belüli egyes mappákba, és hozzon létre logikát a logika az adattípus alapján. 


## <a name="next-steps"></a>További lépések
További információ az [adatgyűjtő API-ról,](data-collector-api.md) amelyekkel adatokat írhat a Log Analytics-munkaterületre bármely REST API-ügyfélről.
