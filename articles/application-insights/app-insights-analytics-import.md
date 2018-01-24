---
title: "Adatok importálása az Azure Application Insights Analytics |} Microsoft Docs"
description: "Alkalmazás telemetriai kapcsolódása statikus adatok importálása, illetve importálhat egy külön adatfolyam Analytics lekérdezésre."
services: application-insights
keywords: "Nyissa meg a séma, az adatok importálása"
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 963e5cfd929f57b34dcb045df82b64f870e897e2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="import-data-into-analytics"></a>Adatok importálása elemzés

Az adatok importálása [Analytics](app-insights-analytics.md), vagy a csatlakozás a [Application Insights](app-insights-overview.md) telemetriai az alkalmazásból, vagy úgy, hogy egy külön adatfolyamként elemzése. Elemzés jól alkalmazható nagy mennyiségű időbélyegzővel adatstreamek telemetriai adatok elemzése hatékony lekérdezésnyelvet.

Adatok importálhatja a Analytics saját sémáját használja. Használatához a szabványos Application Insights sémák például kérelem vagy a nyomkövetés nem tartalmaz.

Importálhatja, JSON vagy Adatforrásnézet (elválasztó tagolt - vesszővel, pontosvesszővel válassza el vagy lapon) fájlok.

Nincsenek három olyan helyzetekben, ahol importálása a Analytics hasznos:

* **Alkalmazás telemetriai csatlakozik.** Például a táblázat, amely a webhely URL-címek leképezve olvashatóbb weblapok címét sikerült importálni. Elemzés létrehozhat egy irányítópult diagram jelentés tíz legnépszerűbb oldalak a webhelyen. Most megmutatja a lap címek helyett az URL-címeket.
* **A telemetriai összefüggéseket** más adatforrások, például a hálózati forgalom, a kiszolgáló adatait, vagy a CDN-naplófájljai.
* **Egy külön adatfolyam Analytics vonatkozik.** Application Insights Analytics egy olyan erőteljes eszköz, amely ritka, időbélyegzővel adatfolyamok - SQL-re mint javulás megfelelően működik. Ha ilyen adatfolyam más forrásból, elemezheti az Analytics.

Adatok küldése az adatokat az adatforrásból is könnyen. 

1. (Egy alkalommal) Adja meg az adatokat egy az adatforrás sémája.
2. (Rendszeres) Az adatok feltöltése az Azure storage és a REST API-t részünkre, amelyek új adatokat arra vár, hogy adatfeldolgozást hívja. Néhány percen belül az adatok érhető el elemzés lekérdezést.

A feltöltési gyakoriságának definiálva van, és milyen gyors kívánja, hogy az adatokat, hogy a lekérdezések érhető el. Sokkal hatékonyabban, feltölteni az adatokat a nagyobb méretű adattömböket, de nem lehet nagyobb, mint 1GB.

> [!NOTE]
> *Nagy mennyiségű adatforrást elemzése kapott?* [*Érdemes lehet* logstash *szállítási az adatok az Application Insightsban való.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Előkészületek

A következők szükségesek:

1. Az Application Insights-erőforrás a Microsoft Azure-ban.

 * Ha azt szeretné, hogy az adatok egyéb telemetriai elkülönítve [hozzon létre egy új Application Insights-erőforrást](app-insights-create-new-resource.md).
 * Ha való csatlakozás vagy az alkalmazás már be van állítva az Application insights szolgáltatással telemetriai adatok összehasonlítása, majd használhatja az erőforrást az alkalmazáshoz.
 * Közreműködő vagy tulajdonosa hozzá az adott erőforráshoz.
 
2. Az Azure storage. Az Azure storage-be feltölteni, és elemzés lekérdezi az adatok ott. 

 * Javasoljuk, hogy hozzon létre egy dedikált tárfiókot, a blobok. A BLOB megosztott más folyamatokkal, ha hosszabb ideig tart a folyamatok blobok olvasni.


## <a name="define-your-schema"></a>A séma meghatározása

Adatok importálása előtt meg kell adnia egy *adatforrás,* amely adja meg a séma adatait.
Legfeljebb 50 adatforrások egyetlen Application Insights-erőforrást is

1. Az adatforrás varázsló elindításához. Használja az "Új adatforrás hozzáadása" gombra. Másik lehetőségként - kattintson a jobb felső sarkában található beállítások gombra, és kattintson "Az adatforrások" legördülő menüre.

    ![Új adatforrás hozzáadása](./media/app-insights-analytics-import/add-new-data-source.png)

    Adja meg az új adatforrás nevét.

2. Adja meg, hogy fel kell töltenie fájlok formátuma.

    A formátum megadása manuálisan, vagy egy minta-fájl feltöltése.

    Ha az adatok CSV formátumban van, az első sor a minta lehet oszlopfejléceket. A következő lépésben mező nevét módosíthatja.

    A minta tartalmaznia kell legalább 10 sorok vagy adatokat rögzíti.

    Oszlop vagy mező neve alfanumerikus nevek (nélkül szóköz vagy írásjel) kell rendelkeznie.

    ![A minta-fájl feltöltése](./media/app-insights-analytics-import/sample-data-file.png)


3. Tekintse át a séma, a varázsló azonosítóértékeket rendelkezik. Ha a mintából típusok következtetni, szükség lehet úgy, hogy az oszlopok következtetett típusú.

    ![Tekintse át a következtetett séma](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Választható.) Töltse fel a sémadefiníciót. Tekintse meg az alábbi formátumban.

 * Válassza ki az időbélyegző. Minden adat Analytics kell rendelkezhetnek időbélyegmezővel. Típusúnak kell lennie `datetime`, de nem kell neve "időbélyeg" lehet. Ha az adatok egy dátum és idő ISO formátumú tartalmazó oszlop, akkor válassza ezt a Timestamp típusú oszlop. Ellenkező esetben válassza a "Data a Beérkezett", és az importálási folyamat felveszi időbélyegmezővel.

5. Az adatforrás létrehozása.

### <a name="schema-definition-file-format"></a>Séma csomagdefiníciós fájlformátumról

A séma, a felhasználói felületen szerkesztése, helyett betöltése a sémadefiníciót fájlból. A séma definition formátum a következőképpen történik: 

Tagolt formátumú 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

JSON formátumban 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Minden oszlop azonosítja a helyét, nevét és típusát. 

* Hely – Format tagolt fájl, akkor azt a leképezett érték. A JSON formátum esetén a csatlakoztatott kulcs jpath.
* Név – a megjelenített oszlop neve.
* Írja be – az adott oszlop adattípusát.
 
Abban az esetben a mintaadatok lett megadva, és fájlformátumot csak, a sémadefiníciót kell az összes oszlop leképezése és új oszlopok hozzáadása. 

JSON lehetővé teszi, hogy az adatok részleges leképezést, ezért a sémadefiníciót JSON formátum nem kell minden kulcsot, amely megtalálható a mintaadatok hozzárendelését. Az oszlopokat, amelyek nem részei a mintaadatok is leképezheti. 

## <a name="import-data"></a>Adatok importálása

Adatok importálásához töltse fel az Azure storage, az access-kulcs létrehozása, és végezze el a REST API-hívás.

![Új adatforrás hozzáadása](./media/app-insights-analytics-import/analytics-upload-process.png)

Hajtsa végre a következő folyamat manuálisan, vagy állítsa be az automatikus rendszer rendszeres időközönként szeretne. Kövesse az alábbi lépéseket az importálni kívánt minden adatblokk kell.

1. Az adatok feltöltése [Azure blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Blobok lehet bármely tömörítetlen legfeljebb 1GB méretet. Nagyméretű bináris objektumok MB több száz alkalmasak a teljesítmény szempontjából.
 * A Gzip ideje és a késleltetés a lekérdezés érhető el az adatok javítása érdekében tömöríthetők. Használja a `.gz` fájlnév-kiterjesztés.
 * Célszerű használni erre a célra külön tárfiókot a különböző szolgáltatások lelassulnak, ami teljesítmény hívások elkerülése érdekében.
 * Adatok nagy gyakorisággal küldésekor minden néhány másodpercben, javasoljuk, hogy egynél több tárfiókot használni, a teljesítményre vonatkozó megfontolásból.

 
2. [Hozzon létre egy közös hozzáférésű Jogosultságkód-kulcsot a BLOB](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). A kulcs kell egy olyan lejárati időt egy nap van, és olvasási hozzáférést biztosítanak.
3. Az Application Insights adatok váró értesíteni REST hívás.

 * Végpont:`https://dc.services.visualstudio.com/v2/track`
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

* `Blob URI with Shared Access Key`: Kap ezt az eljárást a kulcs létrehozásához. A blob jellemző.
* `Schema ID`: A megadott séma létrehozott a séma azonosító. A blob adatainak kell megfeleljen a sémának.
* `DateTime`: Az idő, ahol a küldés, UTC. Ezek a formátumok fogadunk: ISO8601 (például "2016-01-01 13:45:01"); RFC822 ("Sze, 14 Dec 16 14:57:01 + 0000"); RFC850 ("szerda, 14-Dec-16 14:57:00 UTC"); RFC1123 ("Sze, 14 Dec 2016 14:57:00 + 0000").
* `Instrumentation key`az Application Insights-erőforrás.

A érhetők el az adatok Analytics néhány perc múlva.

## <a name="error-responses"></a>Hibaüzenetek

* **400 Hibás kérelem**: azt jelzi, hogy a kérések forgalma érvénytelen. Részletek:
 * Megfelelő instrumentation kulcs.
 * Érvényes időérték. Most már a ideje UTC Formátumban kell lennie.
 * Az esemény JSON megfelel-e a séma.
* **403 Tiltott**: az elküldött blob nem érhető el. Győződjön meg arról, hogy a megosztott elérési kulcsával érvényes, és nem járt le.
* **Nem található 404**:
 * A blob nem létezik.
 * A sourceId nem megfelelő.

További részletes információk a válaszüzenetben hiba érhető el.


## <a name="sample-code"></a>Mintakód

Ezt a kódot használja a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet-csomagot.

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

### <a name="ingest-data"></a>Adatok kigyűjtése

Ez a kód használható minden egyes blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>További lépések

* [A Log Analytics lekérdezési nyelv bemutatása](app-insights-analytics-tour.md)
* Ha Logstash használata esetén a [Logstash beépülő modul szeretnék adatokat küldeni a Application insights szolgáltatással](https://github.com/Microsoft/logstash-output-application-insights)
