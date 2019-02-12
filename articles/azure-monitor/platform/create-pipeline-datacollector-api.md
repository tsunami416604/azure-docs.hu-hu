---
title: Adatfolyamatok létrehozása az Azure az Azure Monitor adatgyűjtő API |} A Microsoft Docs
description: Az Azure Monitor HTTP-adatgyűjtő API segítségével POST JSON-adatok hozzáadása a Log Analytics-munkaterület minden ügyfélről, amely az REST API-t. Ez a cikk ismerteti, hogyan tölthetők fel fájlok automatikus módon átalakítson tárolt adatokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.openlocfilehash: d2736e397827373949da1634a99056420dc13b8a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003856"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Adatfolyamatok létrehozása a Data Collector API-val

A [Azure Monitor adatgyűjtő API](data-collector-api.md) lehetővé teszi, hogy minden olyan egyéni napló adatok importálása az Azure monitorban Log Analytics-munkaterület. A csak követelmények, hogy az adatok JSON-formátumú, és 30 MB vagy kisebb szegmensek felosztása kell-e. Ez egy teljesen rugalmas mechanizmust, amely számos módon lehet csatlakoztatni az: közvetlenül az alkalmazásból küldött adatokból való egyszeri ad hoc ad hoc tölt fel. Ez a cikk néhány gyakori forgatókönyv esetén kiindulási pont lesz szerkezeti: töltse fel az adatok rendszeres, automatizált alapon-fájlban tárolt kell. Amíg a folyamat megjelenik itt nem lesz a legtöbb nagy teljesítményű, vagy ellenkező esetben optimalizált, célja, hogy saját éles folyamat felépítésével bajlódnia felé kiindulási pontként szolgál.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>A példában a probléma
Ez a cikk további részében hogy megvizsgálja a lapmegtekintések adatainak az Application insights szolgáltatásban. A képzeletbeli esetünkben korrelációját, ha a cél az azonosítása, ahol azt kell lennie költségkeret-beállítási a legtöbbet a világ minden ország népesség tartalmazó egyéni adatok alapértelmezés szerint az Application Insights SDK által gyűjtött földrajzi adatok szeretnénk marketing dollár. 

Nyilvános adatforrás például használjuk a [UN világ Population vevőket](https://esa.un.org/unpd/wpp/) erre a célra. Az adatok az alábbi egyszerű sémát fog rendelkezni:

![Példa egyszerű séma](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

Ebben a példában feltételezzük, hogy egy új fájl a legkésőbbi év adatokkal, amint elérhetővé válnak fogja feltölteni.

## <a name="general-design"></a>Általános Tervező
Egy klasszikus ETL-típus logikai folyamat megtervezéséhez használjuk. Az architektúra a következőképpen fog kinézni:

![Adatok gyűjtése folyamat architektúra](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Ez a cikk nem fedezik adatok létrehozása vagy [töltse fel az Azure Blob Storage-fiók](../../storage/blobs/storage-upload-process-images.md). Ahelyett hogy felveszi a folyamatot, amint egy új fájlt töltenek fel a blob. Innen:

1. Egy folyamat észleli, hogy az új adatok fel lett töltve.  A példában egy [Azure Logic Apps](../../logic-apps/logic-apps-overview.md), amely rendelkezik elérhető új adatokat a blob feltöltése folyamatban észlelő eseményindító.

2. Processzor olvassa be az új adatokat, és konvertálja JSON formátumban szükséges Azure figyelő az ebben a példában, használjuk egy [Azure-függvény](../../azure-functions/functions-overview.md) feldolgozási kód végrehajtásának egyszerű, költséghatékony módja. A függvény által ugyanolyan logikai alkalmazást, hogy annak észlelésére használnak, megkezdődik a az új adatokat.

3. Végül Miután érhető el a JSON-objektum, továbbítja az Azure Monitor. Ugyanez a logikai alkalmazás elküldi az adatokat az Azure Monitor használatával, a beépített a Log Analytics Data Collector tevékenység.

A blob storage, a logikai alkalmazásban vagy az Azure-függvény részletes beállítása nem a következő cikkben ismertetett, amíg az adott termékek oldalain részletes utasításokat érhetők el.

Ez a folyamat monitorozásához figyelése az Azure-függvényt használjuk az Application Insights [részletesen Itt](../../azure-functions/functions-monitoring.md), és a logikai alkalmazás figyelése az Azure Monitor [részletesen Itt](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>A folyamat beállítása
Beállítása a csővezetéket, először győződjön meg arról, hogy a blob-tároló létrehozása és konfigurálása. Hasonlóképpen győződjön meg arról, hogy létrejött-e a Log Analytics-munkaterületet, ahol szeretné elküldeni az adatokat.

## <a name="ingesting-json-data"></a>JSON-adatok feldolgozása
JSON-adatok feldolgozására, nagyon egyszerű, a Logic Apps szolgáltatással, és átalakítás nélkül kell történnie, mivel azt is encase a teljes folyamat egyetlen logikai alkalmazásban. A blob-tároló és a Log Analytics-munkaterületen konfigurált, miután hozzon létre egy új logikai alkalmazást, és konfigurálja a következők szerint:

![Logic apps munkafolyamat – példa](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Mentse a logikai alkalmazást, és folytathatja a tesztelés.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML-, CSV, vagy más formátumú adatok feldolgozására
A Logic Apps jelenleg nem rendelkezik beépített funkciókkal JSON formátumba egyszerű átalakítása XML-, CSV, vagy más típusú. Ezért kell használnia egy másik azt jelenti, hogy az átalakítás végrehajtásához. Ebben a cikkben az Azure Functions kiszolgáló nélküli számítási képességekre, így nagyon könnyen használható és költség-barát úgy vesszük. 

Ebben a példában nem elemezni, egy CSV-fájlt, de bármilyen más típusú fájl hasonlóképpen dolgozhassák fel. Az Azure-függvényt, hogy a megfelelő logikai az adott típusú deszerializálása részét egyszerűen módosíthatja.

1.  Hozzon létre egy új Azure-függvény, a függvény futtatókörnyezeti v1 és fogyasztásalapú akkor, ha kéri.  Válassza ki a **HTTP-eseményindító** sablon megcélzó C#, amely a kötések konfigurálja, hogy kiindulópontként. 
2.  Az a **fájlok megtekintése** lapra a jobb oldali ablaktáblán, hozzon létre egy új fájlt **project.json** , és illessze be a következő kódot az általunk NuGet-csomagok:

    ![Az Azure Functions példaprojekt](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Váltson **run.csx** a jobb oldali ablaktáblán, majd cserélje ki a alapértelmezett kódot az alábbira. 

    >[!NOTE]
    >A projekthez akkor a rekord modell ("PopulationRecord" osztály) cserélje le a saját sémát.
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

4. Mentse a függvényt.
5. Győződjön meg arról, hogy a kód megfelelően működik-e, hogy a függvény teszteléséhez. Váltson a **tesztelése** a jobb oldali ablaktáblán, a teszt konfigurálása a következő lapra. Egy blobot mintaadatokkal való mutató hivatkozást elhelyezni a **kérelem törzse** szövegmezőbe. Kattintás után **futtatása**, JSON kell megjelennie a kimenetet a **kimeneti** mezőbe:

    ![A Függvényalkalmazások tesztelje a kódot](./media/create-pipeline-datacollector-api/functions-test-01.png)

Most meg kell visszalép és módosítja a logikai alkalmazást, hogy elkezdhesse az korábban betöltött és a JSON formátumra alakítja át az adatokkal.  Nézettervező használja, az alábbiak szerint konfigurálja, és mentse a logikai alkalmazás:

![Logic Apps munkafolyamat teljes példát](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>A folyamat tesztelése
Most a korábban beállított blob feltöltése egy új fájlt, és azt a logikai alkalmazás által figyelt. Hamarosan meg kell tekintse meg a logikai alkalmazás indító ki egy új példányát, hívja az Azure-függvény, és sikeresen elküldheti az adatokat az Azure monitornak. 

>[!NOTE]
>Az adatok megjelennek az Azure Monitor egy új típusú adatokat küld az első alkalommal akár 30 percet is igénybe vehet.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Egyéb adatokkal, a Log Analytics és az Application Insights használatával történik
Az a célunk az Application Insights Lapmegtekintések adatainak azt az egyéni adatforrásból betöltött feltöltése adatokkal naplókezelője végrehajtásához futtassa a következő lekérdezés az Application Insights-elemzési ablakban vagy a Log Analytics-munkaterület:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A kimenet jelenít meg a két adatokat már csatlakoztatott források.  

![Egy keresési eredmény példában adatok használatával történik a különálló](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Gyártási folyamatok javasolt fejlesztései
Ez a cikk egy működő prototípust, amely mögötti logika is alkalmazható felé egy igaz gyártási minőségű megoldás jelenik meg. Az ilyen gyártási minőségű megoldások a következő fejlesztéseket ajánlott:

* Adja hozzá a hibakezelést és újrapróbálkozási logika, a logikai alkalmazás és a függvény.
* Adja hozzá a logikát, győződjön meg arról, hogy nem haladja-e a 30MB/egyetlen Log Analytics Adatbetöltési API-hívás korlátot. Az adatok ossza fel kisebb szegmensek szükség esetén.
* A blob Storage-karbantartási szabályzat beállítása. Miután sikeresen elküldte a Log Analytics-munkaterületet, kivéve, ha szeretné megtartani a rendelkezésre álló nyers adatok archiválási célokból, nem indokolt tárolva folytatja. 
* Ellenőrizze a figyelés engedélyezve van-e a teljes folyamat, és nyomkövetési pontok hozzáadása és riasztást küld a megfelelő módon.
* Használja ki a verziókövetés kezelése a kódot a függvény és a logikai alkalmazás.
* Győződjön meg arról, hogy a megfelelő módosítás-kezelési házirendek követik, úgy, hogy ha a séma módosul, a függvény és a Logic Apps ennek megfelelően módosítani.
* Több különböző adattípusok tölt fel, ha a blob-tárolóban lévő egyes mappákba elkülönítse azokat, és ki a logikai adattípus alapján ventilátor logika létrehozása. 


## <a name="next-steps"></a>További lépések
Tudjon meg többet a [adatgyűjtő API](data-collector-api.md) adatokat írni a Log Analytics-munkaterület bármely REST API-ügyfélből.
