---
title: "Munkafolyamat-eseményindítók és műveletek - Azure Logic Apps |} Microsoft Docs"
description: "További információ az ilyen, eseményindítók és műveletek létrehozására és a munkafolyamatok és az Azure Logic Apps folyamatok automatizálását használható"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 9f95c0c486401e0d709829ce8d560f030932eea7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Eseményindítók és műveletek a logic app munkafolyamatok

Minden a logic apps indítsa el az eseményindító műveletek követ. Ez a témakör ismerteti az ilyen, eseményindítók és műveletek rendszer Integrációk létrehozásához, és üzleti munkafolyamatok és a folyamatok automatizálása a logic apps épület használható. 
  
## <a name="triggers-overview"></a>Eseményindítók áttekintése 

Minden a logic apps indítsa el az eseményindító, amely azt adja meg a hívásokat, elindíthatja a logikai alkalmazás futtatásához. Itt a két módon indítható kezdeményezhet a munkafolyamatot futtató:  

* A lekérdezési eseményindító  
* A leküldéses eseményindítót, amely meghívja a [munkafolyamat szolgáltatás REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Indítók a legfelső szintű elemet tartalmaznak:  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>Eseményindító típusok és a bemeneti adatok  

Minden egyes indítási típus egy másik felülettel rendelkezik, és különböző *bemenetek* annak viselkedését meghatározó. 

| Indítási típus | Leírás | 
| ------------ | ----------- | 
| **Ismétlődés** | Akkor következik be, a megadott ütemezés szerint. Beállíthatja, hogy egy jövőbeli dátumot és időt az eseményindító kiváltó. A gyakoriság alapján azt is megadhatja, időpontok és a munkafolyamat futtatási nap. | 
| **Kérés**  | A Logic Apps alkalmazást teszi az olyan végponttal, amely hívása, más néven "manual" eseményindító. | 
| **HTTP** | Ellenőrzi, vagy *szavazások*, webes HTTP-végponttal. A HTTP-végpont meg kell felelnie egy meghatározott eseményindító szerződés "202" aszinkron minta használatával vagy tömböt ad vissza. | 
| **ApiConnection** | Például egy HTTP-eseményindítóval kérdezi le, de használja [Microsoft által felügyelt API-k](../connectors/apis-list.md). | 
| **HTTPWebhook** | Lehetővé teszi a Logic Apps alkalmazást hívható végpont, például a kérelem eseményindító be, de meghívja a megadott URL-cím regisztrálása és a regisztráció megszüntetését. |
| **ApiConnectionWebhook** | Működik, mint a **HTTPWebhook** eseményindító, de a Microsoft által felügyelt API-kat használ. | 
||| 

Egyéb részletek kapcsolatos információkért lásd: [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md). 
  
## <a name="recurrence-trigger"></a>Ismétlődés eseményindító  

Ehhez az eseményindítóhoz a megadott ismétlődési és ütemezés alapján fut, és egyszerű módot nyújt rendszeresen futó munkafolyamat. Íme egy egyszerű ismétlődés eseményindító példa, amely naponta fut:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```
A kezdési dátummal és idővel a kiváltó az eseményindító is ütemezhető. Például minden hétfőn heti jelentés elindításához is ütemezheti a logikai alkalmazást ebben a példában például egy adott hétfőn elindításához: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

Az eseményindító definícióját a következő: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Igen | Karakterlánc | A egysége milyen gyakran az eseményindító következik be. Csak az egyik ezeket az értékeket használja: "második", "perc", "hour", "day", "hét" vagy "honap" | 
| interval | Igen | Egész szám | Egy pozitív egész szám, amely leírja, milyen gyakran a munkafolyamat fut, a gyakoriság alapján. <p>Az alábbiakban a minimális és maximális intervallumok: <p>-Hónap: 1-16 hónap </br>-Nap: 1-500 nap </br>– Óra: 1-12 000 üzemideje (óra) </br>-Perc: 1-72,000 perc </br>-Második: 1-9,999,999 másodpercben<p>Például ha az intervallum értéke 6, és a gyakoriság "honap", akkor az ismétlődés értéke minden hatodik hónapban. | 
| timeZone | Nem | Karakterlánc | Vonatkozik, csak ha a kezdési időpontot, mert az eseményindító nem fogadja el [UTC eltolás](https://en.wikipedia.org/wiki/UTC_offset). Adja meg az alkalmazni kívánt időzónáját. | 
| startTime | Nem | Karakterlánc | Adja meg a kezdő dátum és idő formátuma: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha megad egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-SSz, ha nem adja meg a időzóna <p>Így például, ha 2017. szeptember 18., 2:00 PM, majd adja meg "2017-09-18T14:00:00", és adja meg például a "Csendes-óceáni téli idő" időzónát. Másik lehetőségként adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** hajtsa végre a kezdési ideje a [ISO 8601 dátuma a megadott időpont](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC dátum időformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [UTC eltolás](https://en.wikipedia.org/wiki/UTC_offset). Időzóna nem adja meg, ha hozzá kell adnia a levél "Z" szóközt nélkül végén. A "Z" hivatkozik a egyenértékű [navigációs idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezés, a kezdési idő nem az első olyan, az összetett ütemezéseket, az eseményindító a kezdési időpontnál bármilyen hamarabb nem érvényesítést. További információ a kezdő dátum és idő: [létrehozása és rendszeresen futó feladatok ütemezése](../connectors/connectors-native-recurrence.md). | 
| weekDays | Nem | Karakterlánc vagy karakterlánctömb | Ha adja meg a "Hét" `frequency`, megadhat egy vagy több napot, egymástól vesszővel elválasztva, ha azt szeretné, a munkafolyamat futtatásához: "Hétfő", "Kedd", "Szerda", "Csütörtök", "Péntek", "Szombat" és "Vasárnap" | 
| hours | Nem | Egész szám vagy az egész tömb | Ha adja meg a "Day" vagy "Hetente" `frequency`, megadhat egy vagy több egész számok 0 és 23, a nap, ha azt szeretné, a munkafolyamat futtatási órára vesszővel elválasztva. <p>Például ha megadja a "10", "12" és "14", kapott 2 PM, az óra közötti, de 10, 12 PM. | 
| minutes | Nem | Egész szám vagy az egész tömb | Ha adja meg a "Day" vagy "Hetente" `frequency`, megadhat egy vagy több egész számok 0 és 59 elválasztva, ha a munkafolyamatot futtatni szeretné az óra perc. <p>Például megadhatja a perc megjelölés "30", és elérhetővé az előző példa óra, nap, 10:30 AM, 12:30 PM és 2:30 PM. | 
|||||| 

Például az ismétlődési eseményindító meghatározza, hogy fut-e a logikai alkalmazás hetente minden hétfőn: 10:30 AM, 12:30 PM és 2:30 PM csendes-óceáni téli idő, kezdési 2017. szeptember 9., 2:00 PM legkorábban:

``` json
{
    "triggers": {
        "myRecurrenceTrigger": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

Az ismétlődési és a kezdési idő példák az ehhez az eseményindítóhoz további információkért lásd: [létrehozása és rendszeresen futó feladatok ütemezése](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Kérelem eseményindító

Ehhez az eseményindítóhoz olyan végponttal, amely használható a Logic Apps alkalmazást egy HTTP-kérelem keresztül hívása funkcionál. A kérelem eseményindító ebben a példában néz ki:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Az eseményindító tulajdonsága nem kötelező nevű *séma*:
  
| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| Séma | Nem | Objektum | A JSON-séma, amely megvizsgálja a bejövő kérelem. Hasznos segítséget nyújt a soron következő munkafolyamat lépéseket tudja, mely tulajdonságokat kell hivatkoznia. | 
||||| 

Ehhez a végponthoz meghívni, meg kell hívnia a *listCallbackUrl* API. Lásd: [munkafolyamat szolgáltatás REST API](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>HTTP eseményindító  

HTTP-eseményindítók kérdezze le a megadott végpont, és ellenőrizze a válasz határozza meg, hogy a munkafolyamat fusson-e. Itt a `inputs` objektum e egy HTTP-hívás megalkotásához szükséges paramétereket fogadja:  

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| Módszer | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| URI | Igen| Karakterlánc | A HTTP vagy HTTPs végpont az eseményindító-kereső. Maximális méret: 2 KB | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| Hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). <p>Ütemező túl van egy több támogatott tulajdonságot: `authority`. Alapértelmezés szerint ez az érték van `https://login.windows.net` Ha nincs megadva, de használhat például egy másik értéket`https://login.windows\-ppe.net`. | 
||||| 
 
Működnek jól a Logic Apps alkalmazást, a HTTP-eseményindítóval előírja, hogy a HTTP API egy adott minta megfelel. Az eseményindító ismeri fel ezeket a tulajdonságokat:  
  
| Válasz | Szükséges | Leírás | 
| -------- | -------- | ----------- |  
| Állapotkód | Igen | Az állapotkód: 200 ("OK") futtató okoz. Bármely más állapotkód futtató nem okoz. | 
| Újrapróbálkozási után fejléc | Nem | Amíg a logic app kérdezze le a végpont újra másodpercek számát. | 
| Hely fejléc | Nem | Hívja a következő lekérdezési időköz az URL-címe. Ha nincs megadva, az eredeti URL-címet használja. | 
|||| 

Íme néhány példa viselkedés a kérelmek különböző típusú:
  
| Válaszkód | Újrapróbálkozás | Viselkedés | 
| ------------- | ----------- | -------- | 
| 200 | {nincs} | A munkafolyamatot futtatni, majd újra keressen további adatok a megadott ismétlődési után. | 
| 200 | 10 másodperc | A munkafolyamatot futtatni, majd újra keressen további adatok 10 másodperc után. |  
| 202 | 60 másodperc | A munkafolyamat nem indítható el. A következő kísérlet történik, egy perc alatt, a megadott ismétlődési vonatkoznak. Ha a megadott ismétlődési kevesebb mint egy perc, a újrapróbálkozási után fejléc lép érvénybe. Ellenkező esetben a megadott ismétlődési szolgál. | 
| 400 | {nincs} | Hibás kérés, ne futtassa a munkafolyamat. Ha nincs `retryPolicy` van definiálva, akkor az alapértelmezett házirend. Miután a rendszer elérte az újbóli próbálkozások számát, az eseményindító újra-adatokat keresi meg a megadott ismétlődési után. | 
| 500 | {nincs}| Kiszolgálóhiba, ne futtassa a munkafolyamat. Ha nincs `retryPolicy` van definiálva, akkor az alapértelmezett házirend. Miután a rendszer elérte az újbóli próbálkozások számát, az eseményindító újra-adatokat keresi meg a megadott ismétlődési után. | 
|||| 

Az alábbiakban a HTTP-eseményindító kimenetek: 
  
| Elem neve | Típus | Leírás |
| ------------ | ---- | ----------- |
| fejlécek | Objektum | A fejléceket a HTTP-válaszok | 
| törzs | Objektum | A HTTP-válasz törzsében | 
|||| 

## <a name="api-connection-trigger"></a>API-kapcsolat eseményindító  

Az API-kapcsolat eseményindító hasonlít az alapszintű funkciókat, hogy a HTTP-eseményindítóval. A művelet azonosítására szolgáló paraméterek azonban eltérőek. Például:  
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| állomás | Igen | Objektum | Az üzemeltetett átjáró és az API-alkalmazás azonosítója | 
| Módszer | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| Hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Az a `host` objektumot, az alábbiakban a tulajdonságok:  
  
| Elem neve | Szükséges | Leírás | 
| ------------ | -------- | ----------- | 
| API runtimeUrl | Igen | A felügyelt API-végpont | 
| Kapcsolat neve |  | A munkafolyamat által használt felügyelt API-kapcsolat neve. Hivatkoznia kell nevű paraméter `$connection`. |
|||| 

Az alábbiakban egy API-kapcsolat eseményindító kimenetek:
  
| Elem neve | Típus | Leírás |
| ------------ | ---- | ----------- |
| fejlécek | Objektum | A fejléceket a HTTP-válaszok | 
| törzs | Objektum | A HTTP-válasz törzsében | 
|||| 
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook eseményindító  

A HTTPWebhook eseményindító biztosít egy végpontot, a kérelem eseményindító hasonló, de a HTTPWebhook eseményindító is meghívja a megadott URL-cím regisztrálása és a regisztráció megszüntetését. Íme egy példa hogyan nézhet ki egy HTTPWebhook eseményindító:  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

Ezek a szakaszok számos nem kötelező, és a HTTPWebhook eseményindító viselkedés attól függ, a következő szakaszok adja meg, vagy hagyja ki. Az alábbiakban a HTTPWebhook eseményindító tulajdonságait:
  
| Elem neve | Szükséges | Leírás | 
| ------------ | -------- | ----------- |  
| előfizetés | Nem | Megadja a kimenő kérelem az eseményindító jön létre, és elvégzi a kezdeti regisztráció hívására. | 
| előfizetés lemondása | Nem | Megadja a kimenő kérelem hívására az eseményindító törlődik. | 
|||| 

Megadhat egy webhook műveletei korlátok ugyanúgy, mint [HTTP aszinkron korlátok](#asynchronous-limits). Itt az kapcsolatos további információ a `subscribe` és `unsubscribe` műveletek:

* `subscribe`nevezik, hogy az eseményindító elindíthatja események figyel. A kimenő hívás kezdődik-e az ugyanezen paraméterekkel, mint szabványos HTTP-műveleteket. Ez a hívás történik, ha a munkafolyamat-módosításai bármely olyan módon, például a hitelesítő adatok már megkezdődött, vagy az eseményindító bemeneti paraméterek módosításához. 
  
  Ez a hívás támogatásához a `@listCallbackUrl()` függvény a munkafolyamat egy ehhez az eseményindítóhoz egyedi URL-címet adja vissza. Az URL-címet jelenti. a szolgáltatás REST API-t használó végpontokon egyedi azonosítója.
  
* `unsubscribe`Ha egy műveletet ehhez az eseményindítóhoz érvénytelen, beleértve az e műveletek Renderelés automatikusan neve:

  * Törlése, vagy az eseményindító letiltása. 
  * Törlése, vagy a munkafolyamat letiltása. 
  * Törlése, vagy az előfizetés letiltása. 
  
  Ez a függvény paramétereinek ugyanazok, mint a HTTP-eseményindítóval.

Az alábbiakban a HTTPWebhook a kimeneteinek indul el, és a bejövő kérelem:
  
| Elem neve | Típus | Leírás |
| ------------ | ---- | ----------- |
| fejlécek | Objektum | A fejléceket a HTTP-válaszok | 
| törzs | Objektum | A HTTP-válasz törzsében | 
|||| 

## <a name="conditions"></a>Feltételek  

Bármely eseményindító egy vagy több feltételt segítségével határozza meg, hogy a munkafolyamat futtasson-e vagy sem. Példa:  

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Ebben az esetben a jelentés csak eseményindítók során a munkafolyamat `sendReports` paraméter értéke igaz. Végezetül a feltételeket az állapotkód: az eseményindító hivatkozhat. Például egy munkafolyamat kezdődnek, csak akkor, ha a webhely adja vissza egy állapotkód: 500, például:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> Ha bármely kifejezés hivatkozik egy eseményindító állapotkód bármely olyan módon, az alapértelmezett viselkedést, amely eseményindítón csak 200 "OK", váltja fel. Például, ha azt szeretné, állapotkód: 200-as és a 201-es állapotkód is elindítható, fel kell vennie: `@or(equals(triggers().code, 200),equals(triggers().code,201))` a feltételként.
  
## <a name="start-multiple-runs-for-a-request"></a>Indítsa el a kérelmek több futtatása

Az egy kérelemhez több futtatások indítsa `splitOn` akkor hasznos, például, ha azt szeretné, és egy végpontot, amely több új elemek közötti lekérdezési időközök kérdezze le.
  
A `splitOn`, a válasz forgalma, amely tartalmazza a tömb elemek, amelyek az eseményindító futtató indításához használni kívánt belül tulajdonság megadása. Tegyük fel, hogy az API-k, ez választ ad vissza, amely rendelkezik:  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
A Logic Apps alkalmazást csak szüksége van a `rows` tartalom, így, hogyan hozhat létre például ebben a példában az eseményindító:  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> Ha használja a `SplitOn` parancssori tulajdonságok, a tömb kívül nem olvasható be, például nem sikerül a `status` az API által visszaadott válaszban tulajdonság.
> Emellett a jelen példában használjuk a `?` operátor, ezért azt elkerülheti a hiba, ha a `rows` tulajdonság nem létezik. 

Igen, a munkafolyamat-definícióban `@triggerBody().name` adja vissza `myFirstRow` az első alkalommal történő futtatásakor, és `mySecondRow` második futtatáshoz. Az eseményindító kimenetek kinézetét ebben a példában:  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>Egyszeri futtatási példánya

Ismétlődés eseményindítók beállíthatja, hogy azok érvényesítést, csak ha az összes aktív futtatása befejeződött. Ha egy ütemezett ismétlődési munkafolyamat-példány futtatása közben, az eseményindító kihagyja, és megvárja a következő ütemezett ismétlési időköz újra.
Ez a beállítás konfigurálásához állítsa a `operationOptions` tulajdonságot `singleInstance`:

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>Műveletek áttekintése

Nincsenek számos különböző típusú műveletek, az egyedi viselkedését. Minden művelet egy művelet viselkedését meghatározó különböző bemenetek rendelkezik. Gyűjtemény műveletek önmagukban sok más műveleteket is tartalmazhat. 

### <a name="standard-actions"></a>Standard műveletek  

| Művelet típusa | Leírás | 
| ----------- | ----------- | 
| **HTTP** | Meghívja a HTTP-webalkalmazás végponttal. | 
| **ApiConnection**  | A HTTP-művelet hasonlóan működik, de használja [Microsoft által felügyelt API-k](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | HTTPWebhook hasonlóan működik, de a Microsoft által felügyelt API-kat használ. | 
| **Válasz** | A bejövő válasz meghatározása. | 
| **Függvény** | Egy Azure függvény jelöli. | 
| **várj** | A rögzített méretű idő vagy egy adott időpontig vár. | 
| **Munkafolyamat** | Egy beágyazott munkafolyamat jelöli. | 
| **Összeállítás** | A tevékenység bemenetei arbitary objektum hoz létre. | 
| **Lekérdezés** | Egy tömb feltétel alapján szűri. | 
| **Kiválasztás** | A tömb egyes elemei projektek be új értéket. Például átalakíthatja számokból álló tömb egy objektumokból álló tömb. | 
| **Tábla** | Elemek tömbje alakítja át a CSV és HTML táblát. | 
| **Leáll** | Egy munkafolyamat leáll. | 
||| 

### <a name="collection-actions"></a>Gyűjtemény műveletek

| Művelet típusa | Leírás | 
| ----------- | ----------- | 
| **Az állapot** | Egy kifejezés kiértékelése és eredménye, a megfelelő fiókirodai futtatja. | 
| **Hatókör** | Ezzel az egyéb műveletek logikai csoportosítása. | 
| **ForEach** | Ez a ismétlési művelet tömb telepítéseket, és minden tömb elemen belső műveleteket hajtja végre. | 
| **Amíg** | Az ismétlési művelet belső műveleteket hajtja végre, amíg egy feltétel eredménye igaz. | 
||| 

## <a name="http-action"></a>HTTP-művelet  

HTTP-műveletek hívható meg a megadott végpont, és ellenőrizze a válasz határozza meg, hogy a munkafolyamat fusson-e. Példa:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

Itt a `inputs` objektum e egy HTTP-hívás megalkotásához szükséges paramétereket fogadja: 

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| Módszer | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| URI | Igen| Karakterlánc | A HTTP vagy HTTPs végpont az eseményindító-kereső. Maximális méret: 2 KB | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| Hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). <p>Ütemező túl van egy több támogatott tulajdonságot: `authority`. Alapértelmezés szerint ez az érték van `https://login.windows.net` Ha nincs megadva, de használhat például egy másik értéket`https://login.windows\-ppe.net`. | 
||||| 

Ez a példa HTTP-művelet újrapróbálja hívjon le a legújabb híreket kétszer három végrehajtások és minden kísérlet 30 másodperces késleltetés összesen időszakos hibák esetén:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

Az újrapróbálkozási időköz megadott [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601). Ezt az időközt értéke a minimális és az alapértelmezett 20 másodperc, a maximális érték pedig egy óra. Az alapértelmezett és a maximális újrapróbálkozási számláló négy óra. Ha a nem ad meg az újrapróbálkozási házirend-definíció egy `fixed` stratégia használatos az alapértelmezett értékekkel újrapróbálkozási számát és az időközt. Tiltsa le az újrapróbálkozási házirendet, adja meg a típus `None`.

### <a name="asynchronous-patterns"></a>Aszinkron minták

Alapértelmezés szerint az összes HTTP-alapú műveletek támogatja a szabványos aszinkron művelet szabályt. Így a távoli kiszolgáló, az azt jelzi, hogy a kérelem elfogadása "202 elfogadott" választ, feldolgozásra, ha a Logic Apps motor egy terminál állapotát, amely nem 202 válasz eléréséig hely válaszfejléc megadott URL-tartja a lekérdezési.
  
A korábban leírt aszinkron viselkedés letiltásához állítsa `operationOptions` való `DisableAsyncPattern` az a művelet bemeneti adatok. A műveleti kimenet ebben az esetben a kiszolgáló válaszát kezdeti 202 alapul. Példa:
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```
<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Aszinkron korlátok

Korlátozhatja az időtartamot a egy aszinkron mintát, hogy egy adott időintervallumban. Ha az időtartam nem érte el a Terminálszolgáltatások állapot eltelte után a művelet állapota jelölése `Cancelled` rendelkező egy `ActionTimedOut` kódot. ISO 8601 formátumban megadott korlát időkorlát. Korlátok adhat meg, ahogy az itt látható:

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection művelet

A APIConnection művelet a Microsoft által felügyelt összekötők hivatkozik. Ehhez a művelethez szükséges egy hivatkozást egy érvényes kapcsolat és az API és a paraméterek kapcsolatos információkat.
Íme egy példa a APIConnection művelet:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| állomás | Igen | Objektum | Az összekötő adatait jelöli, mint a `runtimeUrl` és a kapcsolat objektum hivatkozását. | 
| Módszer | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| Elérési út | Igen | Karakterlánc | Az API-művelet elérési útja | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| Hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="apiconnection-webhook-action"></a>APIConnection webhook művelet

A APIConnectionWebhook művelet a Microsoft által felügyelt összekötők hivatkozik. Ehhez a művelethez szükséges egy hivatkozást egy érvényes kapcsolat és az API és a paraméterek kapcsolatos információkat. Megadhat egy webhook műveletei korlátok ugyanúgy, mint [HTTP aszinkron korlátok](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| állomás | Igen | Objektum | Az összekötő adatait jelöli, mint a `runtimeUrl` és a kapcsolat objektum hivatkozását. | 
| Elérési út | Igen | Karakterlánc | Az API-művelet elérési útja | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| Hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Válasz művelet  

Ez a művelet a teljes válasz forgalma a HTTP-kérelem tartalmazza, és tartalmazza a `statusCode`, `body`, és `headers`:
  
```json
"myResponseAction": {
    "type": "response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

A válasz műveletnek, amelyek nem vonatkoznak más műveletek, kifejezetten speciális korlátozások:  
  
* A válaszműveleteket nem lehet a párhuzamos ágak belül a logic app-definíciót, mert a bejövő kérelem determinisztikus választ igényel.
  
* Ha a munkafolyamat egy válasz művelet után a bejövő kérelem már választ kapott, a válasz művelet tekinthető, hibás vagy ütköző. Ennek eredményeképpen a logikai alkalmazást futtatni van megjelölve `Failed`.
  
* A válaszműveleteket egy munkafolyamatban nem használható a `splitOn` parancs az eseményindító-definícióban, mivel a hívás több futtatása hoz létre. Ennek eredményeképpen keresése ebben az esetben, amikor kerül, a munkafolyamat-működés, és térjen vissza a "hibás kérelem" választ.

## <a name="function-action"></a>Függvény művelet   

Ez a művelet lehetővé teszi, hogy jelentik és hívás egy [Azure függvény](../azure-functions/functions-overview.md), például:

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```
| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- |  
| függvény azonosítója | Igen | Karakterlánc | Az Azure függvény hívása kívánt erőforrás-azonosító. | 
| Módszer | Nem | Karakterlánc | A függvény használt HTTP-metódus. Ha nincs megadva, a "POST" az alapértelmezett mód. | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
|||||

A Logic Apps alkalmazást mentésekor Azure Logic Apps ellenőrzi a hivatkozott függvény:

* A függvény hozzáféréssel kell rendelkeznie.
* Csak szabványos HTTP-eseményindítók vagy általános JSON webhook eseményindítók is használhatja.
* A függvény a megadott útvonal nem tartalmazhat.
* Csak "függvény" és "névtelen" jogosultsági szint engedélyezett.

Az eseményindító URL-cím lekérése, gyorsítótárazva, és futásidőben használja. Így bármilyen műveletet érvényteleníti a gyorsítótárazott URL-címe, ha a művelet futásidőben nem sikerül. Ez a probléma megoldása érdekében mentse a logikai alkalmazást újra, amely azt eredményezi, a logikai alkalmazást az lekérése, valamint az eseményindító URL-cím gyorsítótárazása újra.

## <a name="wait-action"></a>Várjon, amíg a művelet  

Ez a művelet felfüggeszti a munkafolyamat végrehajtása a megadott intervallumban. Ebben a példában a munkafolyamat 15 percig is várakoznia okai:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Azt is megteheti várakozási idő az adott néhány percet, használhatja a példa:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> A várakozási időtartama vagy megadhatók a `until` objektum vagy `interval` objektum, de soha sem egyszerre mindkettőre.
  
| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| amíg | Nem | Objektum | A pontok alapján időben várakozási időtartama | 
| amíg időbélyeg | Igen | Karakterlánc | Az az időpont [UTC dátum időformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) várakozási lejáratának | 
| interval | Nem | Objektum | A várakozási időtartama időköze és száma alapján | 
| időköze | Igen | Karakterlánc | Az idő egységét. Csak az egyik ezeket az értékeket használja: "második", "perc", "hour", "day", "hét" vagy "honap" | 
| időköz száma | Igen | Egész szám | A várakozási időtartamot időintervallum-egység szám, amely pozitív egész szám | 
||||| 

## <a name="workflow-action"></a>Munkafolyamat-művelet   

Ez a művelet egy másik munkafolyamat jelöli. A Logic Apps a munkafolyamatot, vagy pontosabban az eseményindító, ami azt jelenti, hogy a munkafolyamat hozzáféréssel kell rendelkeznie a hozzáférés-ellenőrzést végez.

Ez a művelet kimenetének alapján a meghatározása a `response` a gyermek munkafolyamat művelet. Ha még nem definiált egy `response` műveletet, majd a kimenetek üresek.

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- |  
| állomás azonosítója | Igen | Karakterlánc| Az erőforrás-azonosítója a hívni kívánt munkafolyamat | 
| állomás Eseményindító_neve | Igen | Karakterlánc | A meghívni kívánt eseményindító nevét | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
|||||   

## <a name="compose-action"></a>A művelet összeállítása

Ez a művelet lehetővé teszi, hogy létrehozni egy tetszőleges objektumot, és a kimeneti Kiértékel a művelet bemeneti adatokat, és az eredményt. 

> [!NOTE]
> Használhatja a `Compose` műveletet hoz létre a kimenetet, többek között az objektumok, tömbök és bármely más típusból XML és bináris hasonlóan a logic apps natívan támogatja.

A compose művelet használható például az egyesítés kimeneteinek több művelet:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="select-action"></a>Kijelölési művelet

Ez a művelet lehetővé teszi a tömb egyes elemei projekt be új értéket.
Például egy tömb számok alakítani egy objektumokból álló tömb, használhatja:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| ettől: | Igen | Tömb | A forrástömb |
| Válassza ki | Igen | Bármelyik | A forrás tömb egyes elemei alkalmazott leképezése |
||||| 

A kimenet a `select` művelet olyan tömb, amely a bemeneti tömb azonos számossága. Minden elem által meghatározott alakította a `select` tulajdonság. Ha a bemeneti érték egy üres tömb, a kimeneti is üres tömb.

## <a name="query-action"></a>Lekérdezési művelet

Ez a művelet lehetővé teszi egy tömb feltétel alapján szűrni. Ez a példa választja ki a 2-nél nagyobb számot:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

A kimenet a `query` művelete olyan tömb, amely rendelkezik, amelyek megfelelnek a következő feltételt: a bemeneti tömb elemei.

> [!NOTE]
> Ha nincs érték felel meg a `where` , az eredmény feltétele egy üres tömb.

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| ettől: | Igen | Tömb | A forrástömb |
| Ha | Igen | Karakterlánc | A feltétellel, hogy a forrás tömb egyes elemei vonatkozik |
||||| 

## <a name="table-action"></a>Tábla művelet

Ez a művelet lehetővé teszi, hogy elemeket tömbje átalakítani egy **CSV** vagy **HTML** tábla. Tegyük fel például, hogy egy `@triggerBody()` a tömbbel rendelkező:

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

És adhat meg ebben a példában például egy táblázat műveletet:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

Ez a példa az eredmény a HTML-táblázat néz ki: 

<table><thead><tr><th>id</th><th>név</th></tr></thead><tbody><tr><td>0</td><td>almák</td></tr><tr><td>1</td><td>narancs</td></tr></tbody></table>

Ezt a táblázatot testre szabhatja, megadhatja az oszlopok explicit módon, például:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Ez a példa az eredmény a HTML-táblázat néz ki: 

<table><thead><tr><th>Készítsen azonosítója</th><th>Leírás</th></tr></thead><tbody><tr><td>0</td><td>friss almák</td></tr><tr><td>1</td><td>friss narancs</td></tr></tbody></table>

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| ettől: | Igen | Tömb | A forrástömb. Ha a `from` tulajdonság értéke üres tömb, a program üres táblát kimenete. | 
| Formátumban | Igen | Karakterlánc | A tábla kívánt formátum, vagy **CSV** vagy **HTML** | 
| oszlopok | Nem | Tömb | A kívánt tábla oszlopait. Használja az alapértelmezett tábla alakzat felülbírálására. | 
| Oszlopfejléc | Nem | Karakterlánc | Az oszlop fejlécére | 
| oszlop értéke | Igen | Karakterlánc | Az oszlop értéke | 
||||| 

## <a name="terminate-action"></a>Leállítási művelet

Ez a művelet leállítja a munkafolyamatot futtató, visszavonja az üzenetsoroktól műveletek és kihagyja a fennmaradó műveleteket. A megszakítási műveletet végzett műveletek nem befolyásolja.

Például, amely rendelkezik "sikertelen" állapota futtató leállításához használhatja ebben a példában:

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Igen | Karakterlánc | Futtatási tartozó állapotát, amely vagy `Failed` vagy`Cancelled` |
| runError | Nem | Objektum | A hiba részletes adatait. Támogatott csak akkor, ha `runStatus` értéke `Failed`. |
| runError kód | Nem | Karakterlánc | A Futtatás hibakód: |
| runError üzenet | Nem | Karakterlánc | A Futtatás hibaüzenet |
||||| 

## <a name="collection-actions-overview"></a>Gyűjtemény műveletek áttekintése

Bizonyos műveleteket olyan önmagukban műveleteket is felvehet. Egy gyűjtemény hivatkozás műveletek közvetlenül a gyűjtemény kívül lehet hivatkozni. Ha megadhatja például `Http` a egy `scope`, majd `@body('http')` továbbra is érvényes bárhol a munkafolyamatban. Műveletek rendelkezhet gyűjteményben `runAfter` csak az ugyanahhoz a gyűjteményekhez lévő egyéb műveletek.

## <a name="condition-if-action"></a>Feltétel: Ha a művelet

Ez a művelet lehetővé teszi, hogy olyan feltétel értékelése, majd hajtsa végre egy ágat, hogy a kifejezés eredménye alapján `true`. 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása mikor `expression` kiértékelésének eredménye`true` | 
| kifejezés | Igen | Karakterlánc | A kiértékelendő kifejezés |
| más | Nem | Objektum | A belső műveletek futtatása mikor `expression` kiértékelésének eredménye`false` |
||||| 

Ha a feltétel sikeresen, a feltétel jelölése `Succeeded`. Vagy a műveletek a `actions` vagy `else` értékelhetők objektumok: 

* `Succeeded`Amikor futtatni, és sikertelen
* `Failed`Amikor futtatni, és sikertelen
* `Skipped`Ha a megfelelő fiókirodai nem fut

Az alábbiakban a feltételek használatát kifejezések egy műveletben példák:
  
| JSON-érték | Eredmény | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | Bármely érték, amely ezt az állapotot adja át a true értékre állítására értékelődik ki. Csak a logikai kifejezésen támogatja. Más típusúra átalakítása logikai érték, ezek a funkciók használata: `empty` és`equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | Összehasonlítás funkciókat támogatja. Az ebben a példában a művelet csak futtatja, ha a kimeneti `act1` meghaladja a küszöbértéket. | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | Támogatja a logikai funkciók beágyazott logikai kifejezésen létrehozásához. Az ebben a példában a művelet a futás esetén kimenete `act1` 100 alatt vagy a küszöbérték fölött van. | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | Ellenőrizze, hogy egy tömb van-e azokat a cikkeket, tömb funkciókat is használhat. Az ebben a példában a művelet a futás esetén a `errors` tömbjének értéke üres. | 
| `"expression": "parameters('hasSpecialAction')"` | Hiba történt, nem egy érvényes, mert @ van szükség a feltétel feltételek. |  
|||

## <a name="scope-action"></a>Hatókör művelet

Ez a művelet lehetővé teszi a logikailag a munkafolyamat műveleteit.

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- |  
| műveletek | Igen | Objektum | A belső műveletek hatókörében |
||||| 

## <a name="foreach-action"></a>ForEach-művelet

Ez a ismétlési művelet tömb telepítéseket, és minden tömb elemen belső műveleteket hajtja végre. Alapértelmezés szerint a `foreach` hurok párhuzamosan fut, és futtathatja egyszerre párhuzamosan 20 végrehajtások. Végrehajtási szabályokat állíthat be, használja a `operationOptions` paraméter.

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása a hurkon belül | 
| foreach | Igen | Karakterlánc | A tömböt az iterációt | 
| operationOptions | Nem | Karakterlánc | Adja meg a műveletet lehetőségeket viselkedés testreszabása. Jelenleg csak `Sequential` ahol párhuzamos-e az alapértelmezett viselkedés egymás után futtatási ismétlési. |
||||| 

## <a name="until-action"></a>Amíg a művelet

A ismétlési művelet a futás belső műveletek csak egy feltétel eredménye igaz.

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Név | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása a hurkon belül | 
| kifejezés | Igen | Karakterlánc | A kifejezés kiértékelése mindegyik iteráció után | 
| Korlát | Igen | Objektum | A hurok korlátok. Legalább egy korlátot kell definiálnia. | 
| darab | Nem | Egész szám | Az ismétlések végrehajtásához számára vonatkozó korlátozást | 
| timeout | Nem | Karakterlánc | Az időkorlátot a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601) , amely megadja, hogy mennyi ideig kell futtatnia a hurok |
||||| 

## <a name="next-steps"></a>Következő lépések

* [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md)
* [Munkafolyamat REST API-n](https://docs.microsoft.com/rest/api/logic/workflows)
