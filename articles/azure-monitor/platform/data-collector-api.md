---
title: Azure Monitor HTTP-adatgyűjtő API | Microsoft dokumentumok
description: Az Azure Monitor HTTP-adatgyűjtő API-t, hogy post JSON-adatokat a Log Analytics munkaterület bármely ügyfél, amely megtudja hívni a REST API-t. Ez a cikk ismerteti, hogyan kell használni az API-t, és példákat, hogyan lehet közzétenni az adatokat a különböző programozási nyelvek használatával.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666752"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Naplóadatok küldése az Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)
Ez a cikk bemutatja, hogyan használhatja a HTTP-adatgyűjtő API-t az Azure Monitor nak egy REST API-ügyfélről küldött naplóadatok.  Ismerteti, hogyan formázhatja a parancsfájl vagy alkalmazás által gyűjtött adatokat, azok felvétele egy kérelembe, és ezt a kérést az Azure Monitor engedélyezte.  Példák a PowerShell, C#és Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Az Azure Monitor HTTP-adatgyűjtő API nyilvános előzetes verzióban érhető el.

## <a name="concepts"></a>Alapelvek
A HTTP-adatgyűjtő API segítségével naplóadatokat küldhet az Azure Monitor naplóelemzési munkaterületére bármely olyan ügyfélről, amely meghívhat egy REST API-t.  Ez lehet egy runbook az Azure Automationben, amely felügyeleti adatokat gyűjt az Azure-ból vagy egy másik felhőből, vagy egy alternatív felügyeleti rendszer, amely az Azure Monitort használja a naplóadatok konszolidálására és elemzésére.

A Log Analytics-munkaterületen lévő összes adat egy adott bejegyzéstípussal rendelkező rekordként van tárolva.  Formázza az adatokat, hogy a Http Data Collector API-ba több rekordot küldjön a JSON-ban.  Az adatok elküldésekor a rendszer egy külön rekordot hoz létre a tárházban a kérelem hasznos adatában lévő minden rekordhoz.


![HTTP-adatgyűjtő – áttekintés](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Kérelem létrehozása
A HTTP-adatgyűjtő API használatához hozzon létre egy POST-kérelmet, amely tartalmazza a JavaScript-objektumnotúc (JSON) küldendő adatokat.  A következő három tábla felsorolja az egyes kérelmekhez szükséges attribútumokat. Az egyes attribútumokat részletesebben ismertetjük a cikk későbbi részében.

### <a name="request-uri"></a>Kérés URI-ja
| Attribútum | Tulajdonság |
|:--- |:--- |
| Módszer |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tartalomtípus |application/json |

### <a name="request-uri-parameters"></a>URI-paraméterek kérése
| Paraméter | Leírás |
|:--- |:--- |
| CustomerID |A Log Analytics munkaterület egyedi azonosítója. |
| Erőforrás |Az API-erőforrás neve: /api/logs. |
| API-verzió |Az API-t használni ezzel a kéréssel. Jelenleg 2016-04-01. |

### <a name="request-headers"></a>Kérésfejlécek
| Fejléc | Leírás |
|:--- |:--- |
| Engedélyezés |Az engedélyezési aláírás. A cikk későbbi részében elolvashatja, hogyan hozhat létre HMAC-SHA256 fejlécet. |
| Napló típusa |Adja meg a beküldött adatok bejegyzéstípusát. Csak betűket, számokat és aláhúzást (_) tartalmazhat, és nem haladhatja meg a 100 karaktert. |
| x-ms-dátum |A kérelem feldolgozásának dátuma 1123-as RFC formátumban. |
| x-ms-AzureResourceId | Az adatokhoz társítandó Azure-erőforrás-azonosító. Ez feltölti a [_ResourceId](log-standard-properties.md#_resourceid) tulajdonságot, és lehetővé teszi, hogy az adatok szerepeljenek az [erőforrás-környezet](design-logs-deployment.md#access-mode) lekérdezésekben. Ha ez a mező nincs megadva, az adatok nem fognak szerepelni az erőforrás-környezet lekérdezésekben. |
| időáltal generált mező | Az adatelem időbélyegét tartalmazó adatok ban lévő mező neve. Ha megad egy mezőt, akkor annak tartalmát a **TimeGenerated**használja. Ha ez a mező nincs megadva, a **TimeGenerated** alapértelmezett beállítása az üzenet betöltésének időpontja. Az üzenetmező tartalmának az ISO 8601 formátumban YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Engedélyezés
Az Azure Monitor HTTP-adatgyűjtő API-hoz intézett kérelmeknek tartalmazniuk kell egy engedélyezési fejlécet. A kérelem hitelesítéséhez alá kell írnia a kérelmet az elsődleges vagy a másodlagos kulcs a kérelmet küldő munkaterület. Ezután adja át az aláírást a kérés részeként.   

Az engedélyezési fejléc formátuma:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*A WorkspaceID* a Log Analytics-munkaterület egyedi azonosítója. *Az aláírás* egy [kivonatoló alapú üzenethitelesítési kód (HMAC),](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) amely a kérelemből épül fel, majd az [SHA256 algoritmus](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx)sal számítja ki. Ezután a Base64 kódolással kódolja.

Ezzel a formátummal kódolja a **SharedKey** aláírási karakterláncot:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Íme egy példa egy aláírási karakterláncra:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Ha rendelkezik az aláírási karakterláncmal, kódolja azt az UTF-8 kódolású karakterlánc HMAC-SHA256 algoritmusával, majd az eredményt Base64-ként kódolja. Használja ezt a formátumot:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

A következő szakaszokban szereplő minták mintakóddal rendelkeznek az engedélyezési fejléc létrehozásához.

## <a name="request-body"></a>A kérés törzse
Az üzenet törzsének jsonban kell lennie. Egy vagy több rekordot kell tartalmaznia a tulajdonság névvel és értékpárokkal a következő formátumban. A tulajdonság neve csak betűket, számokat és aláhúzást (_) tartalmazhat.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Egyetlen kérelemben több rekordot is kötegelhet egy következő formátumban. Minden rekordnak azonos bejegyzéstípusnak kell lennie.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Rekord típusa és tulajdonságai
Egyéni rekordtípust határoz meg, amikor adatokat küld az Azure Monitor HTTP-adatgyűjtő API-n keresztül. Jelenleg nem írhat adatokat olyan meglévő bejegyzéstípusokba, amelyeket más adattípusok és megoldások hoztak létre. Az Azure Monitor beolvassa a bejövő adatokat, majd olyan tulajdonságokat hoz létre, amelyek megfelelnek a megadott értékek adattípusainak.

Az adatgyűjtő API-hoz minden kérelemnek tartalmaznia kell egy **naplótípus-fejlécet** a bejegyzéstípus nevével. A **_CL** utótag automatikusan hozzáfűzi a megadott nevet, hogy megkülönböztesse a többi naplótípustól egyéni naplóként. Ha például a **MyNewRecordType**nevet adja meg, az Azure Monitor létrehoz egy **MyNewRecordType_CL**típusú rekordot. Ez segít annak biztosításában, hogy ne legyenek ütközések a felhasználó által létrehozott típusnevek és a jelenlegi vagy jövőbeli Microsoft-megoldásokban szállított típusok között.

Egy tulajdonság adattípusának azonosításához az Azure Monitor hozzáad egy utótagot a tulajdonság nevéhez. Ha egy tulajdonság null értéket tartalmaz, a tulajdonság nem szerepel a rekordban. Ez a táblázat a tulajdonság adattípusát és a megfelelő utótagot sorolja fel:

| Tulajdonság adattípusa | Utótag |
|:--- |:--- |
| Sztring |_s |
| Logikai |_b |
| Double |_d |
| Dátum/idő |_t |
| GUID (karakterláncként tárolva) |_g |

Az egyes tulajdonokhoz az Azure Monitor által használt adattípus attól függ, hogy az új rekord bejegyzéstípusa már létezik-e.

* Ha a rekordtípus nem létezik, az Azure Monitor létrehoz egy újat a JSON-típus következtetés használatával az új rekord egyes tulajdonságai adattípusának meghatározásához.
* Ha a rekordtípus létezik, az Azure Monitor megkísérli létrehozni egy új rekordot a meglévő tulajdonságok alapján. Ha az új rekord egyik tulajdonságának adattípusa nem egyezik, és nem konvertálható a meglévő típusra, vagy ha a rekord nem létezik, az Azure Monitor létrehoz egy új tulajdonságot, amely rendelkezik a megfelelő utótaggal.

Ez a beküldési tétel például három tulajdonsággal rendelkező rekordot hoz létre: **number_d**, **boolean_b**és **string_s:**

![1. mintarekord](media/data-collector-api/record-01.png)

Ha ezután beküldte ezt a következő bejegyzést, és az összes értéket karakterláncként formázta, a tulajdonságok nem változnak. Ezek az értékek meglévő adattípusokra konvertálhatók:

![2. mintarekord](media/data-collector-api/record-02.png)

De ha ezt követően a következő beküldést, az Azure Monitor hozza létre az új tulajdonságok **at boolean_d** és **string_d.** Ezek az értékek nem konvertálhatók:

![3. mintarekord](media/data-collector-api/record-03.png)

Ha ezután a következő bejegyzést küldte el a bejegyzéstípus létrehozása előtt, az Azure Monitor három tulajdonsággal rendelkező rekordot hoz létre, **number_s**, **boolean_s**és **string_s.** Ebben a bejegyzésben a kezdeti értékek mindegyike karakterláncként van formázva:

![4. mintarekord](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Fenntartott tulajdonságok
A következő tulajdonságok le vannak foglalva, és nem használhatók egyéni bejegyzéstípusban. Hibaüzenetet fog kapni, ha a hasznos adat tartalmazza a tulajdonságnevek bármelyikét.

- Bérlő

## <a name="data-limits"></a>Adatkorlátok
Az Azure Monitor adatgyűjtési API-ba küldött adatok körül vannak bizonyos korlátozások.

* Legfeljebb 30 MB/post az Azure Monitor adatgyűjtő API-ba. Ez egy méretkorlát egyetlen bejegyzésre. Ha egyetlen, 30 MB-ot meghaladó bejegyzésből származó adatokat kell felosztani, kisebb méretű adattömbökre kell osztani, és egyidejűleg el kell küldeni.
* Mezőértékeklegfeljebb 32 KB-os korlát. Ha a mező értéke nagyobb, mint 32 KB, az adatok csonkolva lesznek.
* Egy adott típushoz ajánlott mezők maximális száma 50. Ez egy gyakorlati korlát a használhatóság és a keresési élmény szempontjából.  
* A Log Analytics-munkaterület egy táblája csak legfeljebb 500 oszlopot támogat (a cikkben mezőként). 
* Az oszlopnév legfeljebb 500 karakterből állhat.

## <a name="return-codes"></a>Visszárukódok
A 200-as HTTP-állapotkód azt jelenti, hogy a kérelem feldolgozásra érkezett. Ez azt jelzi, hogy a művelet sikeresen befejeződött.

Ez a táblázat a szolgáltatás által visszaadott állapotkódok teljes készletét sorolja fel:

| Kód | status | Hibakód | Leírás |
|:--- |:--- |:--- |:--- |
| 200 |OK | |A kérelem sikeresen elfogadásra került. |
| 400 |Rossz kérés |Inaktív ügyfél |A munkaterület le van zárva. |
| 400 |Rossz kérés |InvalidApiVersion |A megadott API-verziót a szolgáltatás nem ismerte fel. |
| 400 |Rossz kérés |Érvénytelen ügyfélazonosító |A megadott munkaterület-azonosító érvénytelen. |
| 400 |Rossz kérés |Érvénytelen adatok formátuma |Érvénytelen JSON lett elküldve. Előfordulhat, hogy a választörzs további információt tartalmaz a hiba megoldásáról. |
| 400 |Rossz kérés |InvalidLogType |A megadott naplótípus speciális karaktereket vagy numerikus karaktereket tartalmazott. |
| 400 |Rossz kérés |MissingApiVersion (MissingApiVersion) |Az API-verzió nincs megadva. |
| 400 |Rossz kérés |MissingContentType típus |A tartalomtípus nincs megadva. |
| 400 |Rossz kérés |MissingLogType típus |A szükséges értéknapló-típus nincs megadva. |
| 400 |Rossz kérés |Nem támogatott tartalomtípus |A tartalomtípus nem **lett alkalmazásra/jsonra**állítva. |
| 403 |Forbidden |InvalidAuthorization (Érvénytelen engedélyezés) |A szolgáltatás nem tudta hitelesíteni a kérelmet. Ellenőrizze, hogy a munkaterület-azonosító és a kapcsolatkulcs érvényes-e. |
| 404 |Nem található | | A megadott URL-cím helytelen, vagy a kérés túl nagy. |
| 429 |Túl sok kérelem | | A szolgáltatás nagy mennyiségű adatot tapasztal a fiókjából. Próbálkozzon később újra a kéréssel. |
| 500 |Belső kiszolgálóhiba |Meghatározatlan hiba |A szolgáltatás belső hibát észlelt. Próbálkozzon újra a kéréssel. |
| 503 |A szolgáltatás nem érhető el |Szolgáltatásnem érhető el |A szolgáltatás jelenleg nem érhető el a kérelmek fogadására. Próbálkozzon újra a kéréssel. |

## <a name="query-data"></a>Adatok lekérdezése
Az Azure Monitor HTTP-adatgyűjtő API által küldött adatok lekérdezéséhez keressen olyan rekordokat, amelyek **típusa** megegyezik a megadott **LogType** értékkel, **és hozzáfűzi a _CL.** Ha például **a MyCustomLog-t**használta, akkor `MyCustomLog_CL`az összes rekordot vissza adja.

## <a name="sample-requests"></a>Mintakérelmek
A következő szakaszokban mintákat talál arról, hogyan küldhet adatokat az Azure Monitor HTTP-adatgyűjtő API-ba különböző programozási nyelvek használatával.

Az egyes minták esetében az alábbi lépésekkel állíthatja be az engedélyezési fejléc változóit:

1. Az Azure Portalon keresse meg a Log Analytics-munkaterületet.
2. Válassza **a Speciális beállítások,** majd **a Csatlakoztatott források lehetőséget.**
2. A **Munkaterület-azonosítótól**jobbra jelölje ki a másolásikont, majd illessze be az azonosítót a **Customer ID** változó értékeként.
3. Az elsődleges **kulcstól**jobbra jelölje ki a másolásikont, majd illessze be az azonosítót a **megosztott kulcs** változó értékeként.

Másik lehetőségként módosíthatja a naplótípus és a JSON-adatok változóit.

### <a name="powershell-sample"></a>PowerShell-minta
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-minta
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 minta
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternatívák és megfontolások
Míg a Data Collector API-t kell fedeznie a legtöbb az Azure-naplók ban gyűjtött szabad formátumú adatok, vannak olyan esetek, ahol egy alternatív a api egyes korlátainak leküzdéséhez. Minden lehetőség a következő, fő szempontok at tartalmazza:

| Alternatív | Leírás | A legalkalmasabb |
|---|---|---|
| [Egyéni események:](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)Natív SDK-alapú betöltés az Application Insightsban | Application Insights, általában az alkalmazáson belüli SDK-n keresztül műszerezve, lehetővé teszi, hogy egyéni adatokat küldjön egyéni eseményeken keresztül. | <ul><li> Az alkalmazáson belül létrehozott, de az SDK által nem felvett adatok az alapértelmezett adattípusok (kérések, függőségek, kivételek és így tovább) segítségével.</li><li> Az Application Insights ban található más alkalmazásadatokkal leggyakrabban korrelált adatok </li></ul> |
| Adatgyűjtő API az Azure figyelőnaplóiban | Az Azure Monitor Naplók adatgyűjtő API-ja teljesen nyitott módja az adatok betöltésének. A JSON-objektumban formázott adatok itt küldhetők el. Az elküldést követően a rendszer feldolgozza, és elérhetőak a Naplókban, hogy korreláljanak a Naplókban vagy más Application Insights-adatokkal. <br/><br/> Az adatok feltöltése fájlként egy Azure Blob blob, ahonnan ezeket a fájlokat feldolgozzák, és feltölti a Log Analytics. Tekintse meg [ezt a](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) cikket egy ilyen folyamat minta megvalósításához. | <ul><li> Olyan adatok, amelyek nem feltétlenül az Application Insights-on belül egy alkalmazáson belül jönnek létre.</li><li> Ilyenek például a keresési és ténytáblák, a referenciaadatok, az előre összesített statisztikák és így tovább. </li><li> Olyan adatokhoz készült, amelyek összevethetők más Azure-figyelő adatokkal (Application Insights, egyéb naplók adattípusai, Biztonsági központ, Azure-figyelő tárolókhoz/virtuális gépekhez és így tovább). </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Az Azure Data Explorer (ADX) az az adatplatform, amely az Application Insights Analytics és az Azure Monitor-naplók at működteti. Most már általánosan elérhető ("GA"), az adatplatform nyers formában teljes rugalmasságot biztosít (de a felügyeleti terhelést igénylő) a fürt (RBAC, megőrzési arány, séma, és így tovább). Az ADX számos [betöltési lehetőséget kínál,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) beleértve a [CSV, A TSV és jSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) fájlokat. | <ul><li> Olyan adatok, amelyek nem kapcsolódnak az Application Insights vagy a Naplók bármely más adatához. </li><li> Speciális betöltési vagy feldolgozási képességeket igénylő adatok, amelyek ma nem érhetők el az Azure Monitor naplókban. </li></ul> |


## <a name="next-steps"></a>További lépések
- A [Log Search API segítségével](../log-query/log-query-overview.md) adatokat kérhet le a Log Analytics-munkaterületről.

- További információ arról, hogyan [hozhat létre adatfolyamatot az adatgyűjtő API-val a](create-pipeline-datacollector-api.md) Logic Apps munkafolyamat használatával az Azure Monitor hoz létre.
