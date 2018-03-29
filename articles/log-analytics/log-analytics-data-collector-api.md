---
title: Elemzés API HTTP adatgyűjtő naplózása |} Microsoft Docs
description: A napló Analytics HTTP Data Collector API segítségével POST JSON-adatokat hozzáadni a Naplóelemzési tárház minden ügyfélről, amely a REST API meghívása. Ez a cikk ismerteti, hogyan lehet az API-val, és példák közzétegyék az adataikat a különböző programnyelveken rendelkezik.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: 167c36d2fa9bc182b6e37c0f47f838fde1ba01df
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Adatokat küldeni a Log Analyticshez a HTTP adatait gyűjtője API-t (nyilvános előzetes verzió)
Ez a cikk bemutatja, hogyan használja a HTTP adatok adatgyűjtő API REST API-ügyfél Naplóelemzési adatküldéshez.  Bemutatja, hogyan lehet a parancsfájl vagy az alkalmazás által összegyűjtött adatok formázása, adja hozzá a kérelem és a kérésre Naplóelemzési engedélyezve van.  A példák PowerShell, a C# és Python.

> [!NOTE]
> A napló Analytics HTTP Data Collector API nyilvános előzetes verziójában van.

## <a name="concepts"></a>Alapelvek
A HTTP-adatokat gyűjtő API segítségével bármely ügyfélnek, amely a REST API meghívása Naplóelemzési adatküldéshez.  Ez lehet egy runbook az Azure Automationben, hogy a gyűjtő felügyeleti adatokat Azure vagy egy másik felhőben, vagy azt egy másik felügyeleti rendszer összefogása és elemezhetik a Naplóelemzési használó lehet.

A Naplóelemzési tárházban található összes adat egy rekord, egy adott rekordtípus tárolja.  Az adatok több bejegyzést a JSON-ban, a HTTP-adatokat gyűjtő API küldendő formázása.  Elküldeni az adatokat, ha egy egyéni rekord jön létre az egyes rekordokhoz, a kérelem hasznos a tárházban.


![HTTP adatgyűjtő áttekintése](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Kérelem létrehozása
A HTTP-adatokat gyűjtő API használatához hozzon létre egy POST kérést, amely tartalmazza az adatokat küldeni a JavaScript Object Notation (JSON).  A következő három táblázatokban az attribútumok, amelyek szükségesek az egyes kérelmek. Azt írja le a cikk későbbi részében részletesebben összes attribútumot.

### <a name="request-uri"></a>Kérés URI-ja
| Attribútum | Tulajdonság |
|:--- |:--- |
| Módszer |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tartalomtípus |application/json |

### <a name="request-uri-parameters"></a>A kérelem URI-paraméterei
| Paraméter | Leírás |
|:--- |:--- |
| CustomerID |A Naplóelemzési munkaterület egyedi azonosítója. |
| Erőforrás |Az API-erőforrás neve: / api/logs. |
| API-verzió |A kérelem használandó API verziója. Ez jelenleg 2016-04-01. |

### <a name="request-headers"></a>Kérelemfejlécek
| Fejléc | Leírás |
|:--- |:--- |
| Engedélyezés |Az engedélyezési aláírás. A cikk későbbi részében olvashat létrehozása egy HMAC-SHA256-fejlécben. |
| Napló-típusa |Adja meg az adatok küldése folyamatban rekord típusát. A napló típusa jelenleg csak alfanumerikus karaktereket tartalmazhat. Nem támogatja írhatók vagy speciális karaktereket. |
| x-ms-date |A kérelem feldolgozása, RFC 1123 formátumban dátuma. |
| time-generated-field |Az adatok, amely tartalmazza az elem a Timestamp típusú mező neve. Ha a megadott mező, akkor annak tartalmát használt **TimeGenerated**. Ha ez a mező nincs megadva, az alapértelmezett **TimeGenerated** a alkalom, hogy az üzenet van okozhatnak. A mező tartalmának érdemes követnie az ISO 8601 formátum éééé-hh-SSz. |

## <a name="authorization"></a>Engedélyezés
A napló Analytics HTTP adatokat gyűjtő API kérésének tartalmaznia kell egy engedélyezési fejléc. A kérés hitelesítéséhez, be kell jelentkeznie a kérelmet az elsődleges vagy másodlagos kulcsát a munkaterületen, a kérést. Így továbbítsa az adott aláírás a kérelem részeként.   

Az engedélyezési fejléc formátuma a következő:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* a Naplóelemzési munkaterület egyedi azonosítója. *Aláírás* van egy [kivonat-alapú üzenethitelesítési kód (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , a kérelem összeállított és majd számított használatával a [SHA-256 algoritmus](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Majd hogy kódolása Base64 kódolással.

Ezt a formátumot használja kódolására a **SharedKey** aláírás-karakterlánc:

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

Ha az aláírás karakterlánc, a HMAC-SHA-256 algoritmus használatával az UTF-8 kódolású karakterlánc kódolása, és majd a az eredmény Base64 kódolása. Ezt a formátumot használja:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

A mintákat a következő szakaszokban lévő mintakód hozhat létre egy engedélyezési fejléc rendelkezik.

## <a name="request-body"></a>Kérelem törzse
Az üzenet törzsét JSON kell lennie. A tulajdonság név-érték párok egy vagy több rekordot a következő formátumban kell tartalmaznia:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

A köteg egyetlen kérelemmel együtt a több rekordot a következő formátum használatával. A rekordok az azonos rekord típusúnak kell lennie.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Bejegyzéstípus és tulajdonságai
Az egyéni típusát határozza meg, amikor a napló Analytics HTTP adatait gyűjtője API-n keresztül adatokat. Jelenleg, akkor nem lehet adatokat írni rekord típusú létező létrehozott más adattípusok és megoldásokat. A Naplóelemzési a bejövő adatokat olvas, és ezután hoz létre, amelyek megfelelnek a megadott értékek adatok típusú tulajdonságok.

A napló Analytics API kéréseknek tartalmaznia kell egy **hibanapló-típus** a rekordtípus nevű fejléc. A utótag **_CL** automatikusan hozzáfűzi a neve meg egy egyéni napló, a más napló megkülönböztetésére. Például, ha beírja a nevét **MyNewRecordType**, Log Analyticshez azzal a típussal, létrehoz egy rekordot **MyNewRecordType_CL**. Ezzel biztosíthatja, hogy nincsenek-e a felhasználó által létrehozott típusnevek és a jelenlegi vagy jövőbeli Microsoft megoldások szállított közötti ütközések.

A tulajdonság adattípusát kikereséséhez Naplóelemzési hozzáadja egy utótagot a tulajdonságnév. Tulajdonság értéke null, ha a tulajdonság nem szerepel a rekordban. Ez a táblázat felsorolja a tulajdonságadat-típus és a megfelelő utótag:

| Tulajdonságadat-típus | Utótag |
|:--- |:--- |
| Karakterlánc |_s |
| Logikai |_b |
| Dupla |_d |
| Dátum és idő |_t |
| GUID |_g |

Az adattípus, amely Naplóelemzési alkalmaz minden egyes tulajdonság attól függ, az új bejegyzés bejegyzéstípus létezik-e.

* Ha a rekord típusa nem létezik, a Naplóelemzési hoz létre egy új. A Naplóelemzési a JSON-típusok kikövetkeztetése alapján határozza meg az új rekord minden egyes tulajdonság adattípusára.
* Ha a rekord típusa létezik, a Naplóelemzési megkísérli hozzon létre egy új rekordot a meglévő tulajdonságok alapján. Ha az adattípust a egy tulajdonság az új rekordban nem felel meg, és a már meglévő típus nem konvertálható, vagy ha a rekord tartalmaz olyan tulajdonságon, amely nem létezik, Log Analyticshez hoz létre egy új tulajdonság, amely rendelkezik a megfelelő utótag.

Például a küldésének bejegyzés hozna létre egy olyan rekordot három tulajdonságokkal **number_d**, **boolean_b**, és **string_s**:

![A minta rekord 1](media/log-analytics-data-collector-api/record-01.png)

Ha ezután a következő bejegyzés formázott karakterláncok értékekkel, a tulajdonságok nem megváltozna. Ezek az értékek konvertálhatók meglévő adattípusok:

![2. példa rekord](media/log-analytics-data-collector-api/record-02.png)

De ha a következő küldésének majd végrehajtott Naplóelemzési rekordot kell létrehoznia az új tulajdonságok **boolean_d** és **string_d**. Ezeket az értékeket nem lehet konvertálni:

![A minta rekord 3](media/log-analytics-data-collector-api/record-03.png)

A következő bejegyzést, majd a rekordtípus létrehozása előtt elküldve, ha Naplóelemzési három tulajdonságokkal hozna létre egy olyan rekordot **sikeresek**, **boolean_s**, és **string_s**. Ez a bejegyzés a kezdeti értékeit formázott karakterláncként:

![4. példa rekord](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Adatok korlátok
Nincsenek bizonyos korlátozások a a Log Analytics-adatok gyűjtése API közzé adatok körül.

* Legfeljebb 30 MB post napló Analytics adatokat gyűjtő API. Ez az egyetlen post méretkorlátot. Ha az adatok egyetlen utáni, amely 30 MB meghaladja, az adatok akár kisebb méretű adattömböket írnak és elküldheti azokat párhuzamosan kell.
* Legfeljebb 32 KB-os korlát mezők értékét. Ha 32 KB-nál nagyobb értéket, az adatok csonkolva lesz.
* Egy adott típusú mezőket ajánlott maximális száma érték az 50. Ez a használhatóság és keresési élményt szempontból gyakorlati korlátozni.  

## <a name="return-codes"></a>A visszatérési kódok
200-as HTTP-állapotkód: azt jelenti, hogy a kérelem érkezett a feldolgozáshoz. Ez azt jelzi, hogy a művelet sikeresen befejeződött-e.

Ez a táblázat felsorolja a szolgáltatás esetleg vissza állapotkódokat teljes készletének:

| Kód | status | Hibakód | Leírás |
|:--- |:--- |:--- |:--- |
| 200 |OK | |A kérés sikeresen elfogadva. |
| 400 |Hibás kérés |InactiveCustomer |A munkaterület be lett zárva. |
| 400 |Hibás kérés |InvalidApiVersion |A megadott API-verzió nem ismerte fel a szolgáltatás. |
| 400 |Hibás kérés |InvalidCustomerId |A megadott munkaterület-azonosító érvénytelen. |
| 400 |Hibás kérés |InvalidDataFormat |Érvénytelen JSON el lett küldve. Az adott válasz törzsének tartalmazhatnak a hiba megoldásával kapcsolatos további információk. |
| 400 |Hibás kérés |InvalidLogType |A napló típusa megadott tartalmazott különleges karaktereket vagy írhatók. |
| 400 |Hibás kérés |MissingApiVersion |Az API-verzió nincs megadva. |
| 400 |Hibás kérés |MissingContentType |A tartalom típusa nincs megadva. |
| 400 |Hibás kérés |MissingLogType |A szükséges érték napló típusa nincs megadva. |
| 400 |Hibás kérés |UnsupportedContentType |Nincs beállítva a tartalomtípus **az application/json**. |
| 403 |Tiltott |InvalidAuthorization |A szolgáltatás nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy a munkaterület azonosítója és a kapcsolat kulcs érvényesek. |
| 404 |Nem található | | A megadott URL-cím érvénytelen, vagy a kérelem túl nagy. |
| 429 |Túl sok kérelem | | A szolgáltatás problémát nagyszámú fiókja adatait. Próbálkozzon újra később a kérelmet. |
| 500 |Belső kiszolgálóhiba. |UnspecifiedError |A szolgáltatás belső hibát észlelt. Próbálja megismételni a kérelmet. |
| 503 |A szolgáltatás nem érhető el |ServiceUnavailable |A szolgáltatás jelenleg nem érhető el a kérelmek fogadására. Próbálja megismételni a kérést. |

## <a name="query-data"></a>Adatok lekérdezése
A napló Analytics HTTP adatokat gyűjtő API rekordokat keres által küldött lekérdezési adatok **típus** , amely megegyezik a **LogType** , amely a megadott érték hozzáíródik **_CL**. Például, ha a használt **MyCustomLog**, majd az összes rekordot alakítanák vissza **típus = MyCustomLog_CL**.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>A minta-kérelmek
A következő szakaszokban lévő látni fogja, hogy hogyan szeretné elküldeni a napló Analytics HTTP Data Collector API segítségével különböző programnyelveken minták.

Minden egyes minta hajtsa végre ezeket a lépéseket, a változók beállítása hitelesítési fejlécéhez:

1. Az Azure portálon keresse meg a Naplóelemzési munkaterület.
2. Válassza ki **speciális beállítások** , majd **csatlakoztatott adatforrások**.
2. Jobb oldalán **munkaterület azonosítója**, válassza a Másolás ikonra, és illessze be az azonosító az értékeként a **ügyfél azonosítója** változó.
3. Jobb oldalán **elsődleges kulcs**, válassza a Másolás ikonra, és illessze be az azonosító az értékeként a **megosztott kulcsos** változó.

Azt is megteheti módosíthatja a változók a napló típusa és a JSON-adatokat.

### <a name="powershell-sample"></a>PowerShell-példa
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


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
        -fileName $fileName `
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
```
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

### <a name="python-sample"></a>Python-minta
```
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
- Használja a [napló Search API](log-analytics-log-search-api.md) adatok lekérése a Log Analytics-tárházat.
