---
title: Log Analytics HTTP adatgyűjtő API |} A Microsoft Docs
description: A Log Analytics HTTP-adatgyűjtő API segítségével POST JSON-adatok hozzáadása a Log Analytics-adattárban a bármely ügyfélnek, amely az REST API-t. Ez a cikk azt ismerteti, hogyan használható az API, és rendelkezik az adatok közzététele a különböző programozási nyelvekhez példái.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: bwren
ms.openlocfilehash: 9fe25821d5a234326570b1681807c6f9dfd6ffc8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211100"
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Adatokat küldeni a Log Analytics és a HTTP-adatgyűjtő API (nyilvános előzetes verzió)
Ez a cikk bemutatja, hogyan adatokat küldeni a Log Analytics REST API-ügyfél-nak a HTTP-adatgyűjtő API használatával.  Ismerteti, hogyan formázza a parancsfájl vagy az alkalmazások által gyűjtött adatokat, foglalja bele egy kérelmet, és rendelkezik a Log Analytics által engedélyezett kérelmet.  A példák a PowerShell, a C# és Python.

> [!NOTE]
> A Log Analytics HTTP-adatgyűjtő API jelenleg nyilvános előzetes verzióban.

## <a name="concepts"></a>Alapelvek
A HTTP-adatgyűjtő API segítségével adatokat küldeni a Log Analytics minden ügyfélről, amely segítségével meghívhatja a REST API-t.  Ez lehet egy runbook az Azure Automationben, hogy a gyűjtő felügyeleti adatait az Azure vagy egy másik felhőt, vagy lehet egy másik felügyeleti rendszer, amely a konszolidálhatja és elemezheti a Log Analytics segítségével.

A Log Analytics-adattárban lévő összes adatot egy rekord, egy adott rekord típusa van tárolva.  Küldhet a HTTP-adatgyűjtő API több rekord JSON-fájlban, az adatok formázása.  Elküldi az adatokat, amikor egy adott rekord jön létre minden egyes rekord, a kérelem hasznos adatainak a tárházban.


![A HTTP adatgyűjtő áttekintése](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Kérés létrehozása
A HTTP-adatgyűjtő API használatához hozzon létre egy POST-kérelmet, amely tartalmazza az adatok küldése a JavaScript Object Notation (JSON).  A következő három táblázatok sorolják fel az attribútumok, amelyek szükségesek az egyes kérések. A cikk későbbi részében részletesebben minden attribútum ismertetünk.

### <a name="request-uri"></a>Kérés URI-ja
| Attribútum | Tulajdonság |
|:--- |:--- |
| Módszer |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tartalom típusa |application/json |

### <a name="request-uri-parameters"></a>A kérés URI paraméterei
| Paraméter | Leírás |
|:--- |:--- |
| CustomerID |A Log Analytics-munkaterület egyedi azonosítója. |
| Erőforrás |Az API-erőforrás neve: / api/logs. |
| API-verzió |A kérelem használata API-verzió. Jelenleg ez a 2016-04-01 el. |

### <a name="request-headers"></a>Kérelemfejlécek
| Fejléc | Leírás |
|:--- |:--- |
| Engedélyezés |Az engedélyezési aláírást. A cikk későbbi részében olvashat egy HMAC-SHA256-fejlécben létrehozása. |
| Log-Type |Adja meg a rekord típusa adatok küldése folyamatban van. A méretkorlát a paraméter nem 100 karakternél. |
| x-ms-date |A dátum, a kérelem feldolgozott RFC 1123 formátumban. |
| time-generated-field |Az adatok, amely tartalmazza az időbélyeget, az elem egy mező nevét. Ha megad egy mezőt, akkor a tartalmát használt **TimeGenerated**. Ha ez a mező nincs megadva, alapértelmezett **TimeGenerated** az az idő, az üzenet betöltött. A mező tartalma követnie kell az ISO 8601 formátum éééé-hh-DDThh:mm:ssZ. |

## <a name="authorization"></a>Engedélyezés
A Log Analytics HTTP-adatgyűjtő API minden kérelem engedélyeztetési fejléc tartalmaznia kell. Hitelesíteni a kérelmet, be kell jelentkeznie a kérelmet az elsődleges vagy másodlagos kulcsát a munkaterületet, amely a kérés küldője. Ezt követően adja át az aláírást a kérés részeként.   

Az engedélyezési fejléc formátuma a következő:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*Munkaterület azonosítója* a Log Analytics-munkaterület egyedi azonosítója. *Aláírás* van egy [kivonat-alapú üzenethitelesítési kód (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , amelyet a kérés értékekből összeállított és majd a számított a [SHA256 algoritmust](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Ezt követően kódol, Base64 kódolással.

Ezt a formátumot használja kódolása a **SharedKey** aláírás karakterlánc:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Íme egy példa látható egy aláírás karakterláncra:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Ha az aláírás-karakterlánc, kódolja azokat a HMAC-SHA256 algoritmust az UTF-8 kódolású karakterlánc használatával, és majd kódolása Base64 az eredményt. Ezt a formátumot használja:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

A következő szakaszokban a mintákat hozhat létre az engedélyeztetési fejléc mintakód rendelkezik.

## <a name="request-body"></a>A kérés törzse
Az üzenet törzse JSON formátumban kell lennie. A tulajdonság név-érték párok az egy vagy több rekord a következő formátumban kell tartalmaznia:

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

Kötegelt együtt, egyetlen kérelem több rekord a következő formátum használatával. A rekordok az azonos típusú rekorddal kell lennie.

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

## <a name="record-type-and-properties"></a>Typ záznamu és tulajdonságok
Amikor küld adatokat a Log Analytics HTTP-adatgyűjtő API keresztül megadhat egy egyéni rekord típusa. Jelenleg nem ír adatokat a meglévő erőforrásrekord-típusok létrehozott egyéb adattípusok és a megoldások által. Log Analytics beolvassa a bejövő adatokat, és létrehozza a tulajdonságokat, amelyek megfelelnek a megadott értékekben adattípusát.

A Log Analytics API minden kérésnek tartalmaznia kell egy **naplótípus** adattípus nevű fejléc. Az utótag **_CL** automatikusan a rendszer hozzáfűzi a nevét, adja meg, hogy megkülönböztesse a többi napló esetében, egy egyéni naplót. Például, ha a név megadása **MyNewRecordType**, a Log Analytics a típusú rekordot hoz létre **MyNewRecordType_CL**. Ezzel biztosíthatja, hogy nem lesznek ütközések a felhasználó által létrehozott típusnevek és mások által szállított aktuális vagy jövőbeli Microsoft-megoldások között.

A tulajdonság adattípusát azonosításához, a Log Analytics hozzáadja egy utótagot a tulajdonság nevét. Ha egy tulajdonság null értéket tartalmaz, a tulajdonság nem szerepel az adott rekord. Ez a táblázat felsorolja a tulajdonságadat típusa és a megfelelő utótag:

| Tulajdonságadat típusa | Utótag |
|:--- |:--- |
| Karakterlánc |_s |
| Logikai |_b |
| Dupla |_d |
| Dátum és idő |_t |
| GUID |_g |

Az adattípus, amely a Log Analytics alkalmaz minden egyes tulajdonság attól függ, hogy typ záznamu az új rekord már létezik.

* Ha a rekord típusa nem létezik, a Log Analytics egy új hoz létre. A log Analytics segítségével, hogy a JSON típusú következtetésekhez határozza meg, az új rekord minden egyes tulajdonság adattípusát.
* Ha a rekord típusa létezik, a Log Analytics próbál meglévő tulajdonságok alapján új rekord létrehozása. Ha adattípusa nem felel meg egy tulajdonságot az új rekordban, és a meglévő típus nem konvertálható, vagy ha a rekord tartalmaz olyan tulajdonságot, amely nem létezik, a Log Analytics hoz létre egy új tulajdonság, amely rendelkezik a megfelelő utótagot.

Például a beküldés bejegyzés lenne hozzon létre egy rekordot három tulajdonságot **number_d**, **boolean_b**, és **string_s**:

![Minta rekord 1](media/data-collector-api/record-01.png)

Majd küldte el a következő bejegyzés formájában karakterláncok értékekkel, ha a tulajdonságok nem szeretné módosítani. Ezek az értékek konvertálhatók meglévő adattípusok:

![2. példa rekord](media/data-collector-api/record-02.png)

De, ekkor történik a következő beküldése, ha a Log Analytics hozna létre az új tulajdonságok **boolean_d** és **string_d**. Ezeket az értékeket nem lehet konvertálni:

![Minta rekord 3](media/data-collector-api/record-03.png)

A következő bejegyzést, majd a rekord típusa létrehozása előtt elküldve, ha a Log Analytics lenne hozzon létre egy rekordot három tulajdonságot **sikeresek**, **boolean_s**, és **string_s**. Ebbe a bejegyzésbe a kezdeti értékekre vannak formázva, karakterlánc:

![4. példa rekord](media/data-collector-api/record-04.png)

## <a name="data-limits"></a>Adatkorlátok
Vannak bizonyos korlátozások az adatok elküldése az a Log Analytics-adatok gyűjtési API körül.

* Közzététel a Log Analytics Data Collector API legfeljebb 30 MB-ot. Ez az egyedi közzétételek méretkorlátot. Ha az adatokat egyetlen közzététele, amely meghaladja a 30 MB-ot, a kell felosztani az adatokat, akár kisebb méretű adattömböket írnak és küldhet nekik egy időben.
* Legfeljebb 32 KB-os korlátot a mezőértékek. Ha a mező értéke 32 KB-nál nagyobb, az adatok csonkolva lesz.
* Egy adott típusú mezők ajánlott maximális száma érték az 50. Ez a használhatóság és a keresési élmény szempontjából gyakorlati korlátozva.  

## <a name="return-codes"></a>Visszatérési kódok
A HTTP-állapotkód: 200, az azt jelenti, hogy a kérelem érkezett-e a feldolgozási. Ez azt jelzi, hogy a művelet sikeresen befejeződött-e.

Ez a táblázat felsorolja, amely a szolgáltatás előfordulhat, hogy vissza állapotkódok teljes körét:

| Kód | status | Hibakód | Leírás |
|:--- |:--- |:--- |:--- |
| 200 |OK | |A kérelem sikeresen elfogadva. |
| 400 |Hibás kérés |InactiveCustomer |A munkaterület le van zárva. |
| 400 |Hibás kérés |InvalidApiVersion |A megadott API-verzió nem ismerte fel a szolgáltatás. |
| 400 |Hibás kérés |InvalidCustomerId |A megadott munkaterület-azonosító érvénytelen. |
| 400 |Hibás kérés |InvalidDataFormat |Érvénytelen JSON el lett küldve. A választörzs tartalmazhat további információkat a hiba megoldásával kapcsolatban. |
| 400 |Hibás kérés |InvalidLogType |A napló típusa tartalmazott különleges karaktereket vagy numerics adott meg. |
| 400 |Hibás kérés |MissingApiVersion |Az API-verzió nincs megadva. |
| 400 |Hibás kérés |MissingContentType |A tartalom típusa nincs megadva. |
| 400 |Hibás kérés |MissingLogType |A szükséges érték napló típusa nincs megadva. |
| 400 |Hibás kérés |UnsupportedContentType |A tartalomtípus nem állították be **application/json**. |
| 403 |Tiltott |InvalidAuthorization |A szolgáltatás nem tudta hitelesíteni a kérelmet. A munkaterület Azonosítóját és a kapcsolat kulcsa érvényességének ellenőrzése. |
| 404 |Nem található | | A megadott URL-cím helytelen, vagy a kérelem mérete túl nagy. |
| 429 |Túl sok kérelem | | A szolgáltatás nagy mennyiségű adatait a fiókból, tapasztal. Próbálkozzon újra később a kérelmet. |
| 500 |Belső kiszolgálóhiba |UnspecifiedError |Belső szolgáltatáshiba történt. Ismételje meg a kérelmet. |
| 503 |Elérhetetlen szolgáltatás |ServiceUnavailable |A szolgáltatás jelenleg nem érhető el a kérelmek fogadására. Ismételje meg a kérelmet. |

## <a name="query-data"></a>Adatok lekérdezése
A Log Analytics HTTP-adatgyűjtő API, a rekordok keresése által küldött adatokat lekérdezni **típus** , amely megegyezik a **LogType** meghatározott, értékkel kiegészítve **_CL**. Például, ha a használt **MyCustomLog**, akkor adna vissza, akkor az összes rekordot **típus = MyCustomLog_CL**.

>[!NOTE]
> Ha a munkaterülete frissítve lett a [Log Analytics új lekérdezési nyelvre](../../azure-monitor/log-query/log-query-overview.md), akkor a fenti lekérdezés módosulnak az alábbiak.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Minta kérelmek
A következő szakaszokban találja a mintákat, hogyan lehet elküldeni az adatokat a Log Analytics HTTP-adatgyűjtő API különböző programozási nyelv használatával.

Minden mintához tegye állíthatja be a változókat az engedélyezési fejléc a következő lépéseket:

1. Az Azure Portalon keresse meg a Log Analytics-munkaterületre.
2. Válassza ki **speciális beállítások** , majd **csatlakoztatott források**.
2. Jobb oldalán **munkaterület-Azonosítót**, és a másolási ikonra, majd illessze be az azonosító értéket a **ügyfél-azonosító** változó.
3. Jobb oldalán **elsődleges kulcs**, és a másolási ikonra, majd illessze be az azonosító értéket a **megosztott kulcsos** változó.

Azt is megteheti módosíthatja a változókat a napló típusa és a JSON-adatokat.

### <a name="powershell-sample"></a>PowerShell-minta
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
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

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
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

### <a name="python-2-sample"></a>Python 2-minta
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

## <a name="next-steps"></a>További lépések
- Használja a [Log Search API](../../azure-monitor/log-query/log-query-overview.md) adatokat lekérni a Log Analytics-adattárban.

- Tudjon meg többet [adatfolyamat létrehozása a Data Collector API-val](../../azure-monitor/platform/create-pipeline-datacollector-api.md) Logic Apps-munkafolyamatot a Log Analytics használatával.
