---
title: Adatok importálása az Azure Application Insights Analytics |} A Microsoft Docs
description: Az alkalmazás telemetriai adatainak csatlakozni statikus adatok importálása, vagy importálja egy külön stream Analytics lekérdezési.
services: application-insights
keywords: Nyissa meg a séma, az adatok importálása
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: mbullwin
ms.openlocfilehash: cfcdf13f8aa4dfab9b361ccbb82ea4b2c3e2ca0d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054483"
---
# <a name="import-data-into-analytics"></a>Elemzési adatok importálása

Az adatok importálása [Analytics](app-insights-analytics.md), vagy a csatlakoztatáshoz az [Application Insights](app-insights-overview.md) az alkalmazásából származó telemetriai adatok, vagy úgy, hogy egy külön adatfolyamként elemzése. Analytics egy hatékony lekérdezési nyelvet, nagy mennyiségű időbélyegzővel Streamek telemetria elemzése jól alkalmazható tevékenységekről a.
A saját séma használatával analyticsbe is importálhat adatokat. Nem kell a standard Application Insights-sémák például kérelem vagy nyomkövetési használja.

Importálhatja, JSON vagy Adatforrásnézet (elválasztó tagolt - vesszőt, pontosvesszőt vagy lap) fájlokat.

> [!IMPORTANT]
> Ez a cikk **elavult**. Az ajánlott módszer az adatok bevitele a Log Analytics-n keresztül van a [Log Analytics data collector API-t.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

Nincsenek három olyan helyzetekben, ahol Analyticshez való importálásával hasznos:

* **Csatlakozzon az alkalmazás telemetriai adatainak.** Ha például sikerült importálni egy táblát, amely a webhely URL-címek képez olvashatóbb lap címét. Elemzés a webhelyen a tíz legelterjedtebb oldalt megjelenítő irányítópult diagram jelentést is létrehozhat. Most azt jeleníti meg a lap címét az URL-címek helyett.
* **Az alkalmazás telemetriai adatainak** más forrásokból, például a hálózati forgalom, a kiszolgáló adatait, vagy a CDN-naplófájljai.
* **Egy külön stream Analytics vonatkoznak.** Application Insights Analytics egy hatékony eszköz, amely nagyszerűen működik a ritka, időbélyegzővel adatfolyam - sokkal jobb, mint az SQL sok esetben. Ha például egy stream más forrásból, elemezheti az Analytics.

Az adatforrás történő adatküldés ördöngösség. 

1. (Egy idő) Adja meg a séma "adatforrás" adatot.
2. (Rendszeres) Az adatok feltöltése az Azure storage, és a REST API-t értesíteni minket, amely új adatok vár a feldolgozási hívja. Néhány percen belül az adatok elemzési lekérdezés érhető el.

A feltöltési gyakoriságának definiálva van, és milyen gyors szeretné, hogy a lekérdezésekhez elérhető legyen az adatok. Legyen hatékonyabb feltölteni az adatokat a nagyobb méretű adattömböket, de nem lehet nagyobb, mint 1GB.

> [!NOTE]
> *Nagy mennyiségű elemzéséhez adatforrások van?* [*Fontolja meg* logstash *tehetnek az adatok Application insightsba.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Előkészületek

A következők szükségesek:

1. Egy Application Insights-erőforrást a Microsoft Azure-ban.

 * Ha azt szeretné, külön-külön egyéb telemetriai adatok elemzéséhez [hozzon létre egy új Application Insights-erőforrást](app-insights-create-new-resource.md).
 * Ha való csatlakozás vagy összehasonlítja az alkalmazás már be van állítva az Application Insights telemetriai adatait, majd használhatja az erőforrást az adott alkalmazáshoz.
 * Ennek az erőforrásnak közreműködői vagy tulajdonosi hozzáférés.
 
2. Az Azure storage. Feltöltése az Azure storage, és az Analytics az adatokat olvas be itt. 

 * Azt javasoljuk, hogy a blobok egy dedikált storage-fiókot hoz létre. Ha a blobok osztanak meg más folyamatokkal, hosszabb ideig tart a folyamatok olvasni a blobokat.


## <a name="define-your-schema"></a>A séma meghatározásakor

Adatok importálása előtt meg kell adni egy *adatforrás,* amely megadja, hogy a séma az adatokat.
Használhat egy adott Application Insights-erőforrásban legfeljebb 50 adatforrások

1. Az adatforrás varázsló elindításához. "Új adatforrás hozzáadása" gomb használatával. Másik lehetőségként – kattintson a beállítások gombra a jobb felső sarokban, és a legördülő menüből válassza a "Adatforrások".

    ![Új adatforrás hozzáadása](./media/app-insights-analytics-import/add-new-data-source.png)

    Adjon meg egy nevet az új adatforrás.

2. Határozza meg, hogy a feltölteni kívánt fájlok formátuma.

    A formátum megadása manuálisan, vagy feltölt egy mintafájlt.

    Ha az adatok CSV formátumban, a minta első sorának oszlopfejléceket is lehet. A mezők nevét a következő lépésben módosíthatja.

    A minta tartalmaznia kell legalább 10 sort vagy adatokat rögzíti.

    Oszlop vagy a mező neve alfanumerikus nevét (a tárolóhelyek és írásjelek) nélkül kell rendelkeznie.

    ![Mintafájl feltöltése](./media/app-insights-analytics-import/sample-data-file.png)


3. Tekintse át a sémát, amely a varázsló van itt. A mintából típusok következtetni, ha szüksége lehet a következtetett típusú oszlopok módosítása.

    ![Tekintse át a következtetett séma](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Választható.) Töltse fel a sémadefiníciót. Tekintse meg az alábbi formátumban.

 * Válassza ki a Timestamp. Az összes data Analytics kell rendelkezhetnek időbélyegmezővel. Típusúnak kell lennie `datetime`, de nem kell lennie a "timestamp" nevű. Ha az adatok egy adott napon és időpontban ISO formátumban tartalmazó oszlop, akkor válassza ezt a időbélyeg oszlop. Ellenkező esetben válassza a "adatként a Beérkezett", és a egy időbélyegmezőt hozzáadja az importálási folyamat.

5. Az adatforrás létrehozása.

### <a name="schema-definition-file-format"></a>Séma csomagdefiníciósfájl-formátumról

Helyett szerkesztési felhasználói felületének a sémát, töltse be a sémadefiníciót fájlból. A séma definíció formátum a következőképpen történik: 

Tagolt formátumú 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

JSON-formátumban 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Minden oszlop helye, neve és típusa alapján azonosítja.

* Hely – Format tagolt fájl, akkor azt a leképezett érték. JSON-formátumban célszerű a jpath a leképezett kulcs.
* Név – a oszlopban megjelenített nevét.
* Írja be – ez az oszlop adattípusát.
 
> [!NOTE]
> Abban az esetben példaadatok használta, és a fájl formátuma csak, sémadefiníciója kell minden oszlop leképezése és új oszlopok hozzáadása a végén.
> 
> JSON lehetővé teszi, hogy az adatok részleges leképezést, így a JSON-formátumú sémadefiníciója nem minden kulcs, amely megtalálható a mintaadatok leképezéséhez. Azt is leképezheti oszlopokat, amelyek nem részei a mintaadatokat. 

## <a name="import-data"></a>Adatok importálása

Adatok importálása, töltse fel az Azure storage, hozzon létre egy hozzáférési kulcsot, és végezze el a REST API-hívás.

![Új adatforrás hozzáadása](./media/app-insights-analytics-import/analytics-upload-process.png)

Hajtsa végre a következő folyamat manuálisan, vagy állítsa be az automatikus rendszerek rendszeres időközönként tennie. Kövesse az alábbi lépéseket az importálni kívánt minden adatblokkja esetében van szüksége.

1. Töltse fel az adatokat [az Azure blob storage-bA](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Blobok lehet bármelyik mérete legfeljebb 1GB tömörítetlen. A teljesítmény szempontjából ideálisak a nagy méretű blobok több száz MB-ot.
 * A Gzip ideje és a lekérdezés elérhető legyen az adatok közel valós idejű tömöríthetők. Használja a `.gz` fájlnév-kiterjesztés.
 * Célszerű használni erre a célra egy önálló tárfiókot teljesítménylassulás különböző szolgáltatásokat hívásait elkerülése érdekében.
 * Amikor adatokat küld a nagy gyakoriságú, minden néhány másodpercben, ajánlott egynél több tárfiók használata a teljesítmény szempontjából.

 
2. [A BLOB közös hozzáférésű Jogosultságkód kulcs létrehozása](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). A kulcs kell egy lejárati idővel egy nap, és olvasási hozzáférést nyújtanak.
3. REST-hívások értesíteni, amely az adatokat arra vár, hogy az Application Insights.

 * Végpont: `https://dc.services.visualstudio.com/v2/track`
 * HTTP-metódus: POST
 * Hasznos:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

A helyőrzőket a következők:

* `Blob URI with Shared Access Key`: Kap ezt az eljárást a kulcs létrehozása. Ez csak a blob.
* `Schema ID`: A generált a megadott séma séma azonosító. A blob adatait meg kell felelnie a sémát.
* `DateTime`: Az az időpont, amikor a kérelem elküldésekor, (UTC). Ezek a formátumok fogadunk: ISO8601 (például a "2016-01-01 13:45:01"); RFC822 ("szerda, 16 DEC. 14 14:57:01 + 0000"); RFC850 ("szerda, 16 – 14 közötti DEC. 14:57:00 (UTC)"); RFC1123 ("szerda, 14 DEC. 2016 14:57:00 + 0000").
* `Instrumentation key` az Application Insights-erőforrás.

A data Analytics érhető el néhány perc múlva.

## <a name="error-responses"></a>Hibaválaszok

* **400 Hibás kérés**: azt jelzi, hogy a kérelem tartalma érvénytelen. Ellenőrzés:
 * Megfelelő kialakítási kulcsot.
 * Érvényes idő érték. Most már a időpontja (UTC) kell lennie.
 * Az esemény JSON megfelel a sémának.
* **403 Tiltott**: Miután elküldte a blob nem érhető el. Győződjön meg arról, hogy a közös hozzáférési kulcs érvényes, és nem járt le.
* **404 nem található**:
 * A blob nem létezik.
 * A sourceId nem megfelelő.

További információt a válaszként megjelenített hibaüzenetét érhető el.


## <a name="sample-code"></a>Mintakód

Ez a kód a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet-csomagot.

### <a name="classes"></a>Osztályok

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Adatok betöltése

Ez a kód használata minden egyes blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>További lépések

* [Ismerkedjen meg a Log Analytics lekérdezési nyelv](app-insights-analytics-tour.md)
* Ha Logstash használja, használja a [adatokat küldeni az Application Insights a Logstash beépülő modul](https://github.com/Microsoft/logstash-output-application-insights)
