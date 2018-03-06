---
title: "Munkafolyamat-eseményindítók és műveletek - Azure Logic Apps |} Microsoft Docs"
description: "Az eseményindítók és műveletek automatizált munkafolyamatok és a folyamatok létrehozni a logic apps megismerése"
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
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: af30fd30f389cdc2070c45ae3b6e2cb1165239e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Eseményindítók és műveletek a logic app munkafolyamatok

Minden a logic apps indítsa el az eseményindító műveletek követ. Ez a cikk ismerteti az ilyen, eseményindítók és műveletek rendszer Integrációk létrehozásához, és üzleti munkafolyamatok és a folyamatok automatizálása a logic apps épület használható. 
  
## <a name="triggers-overview"></a>Eseményindítók áttekintése 

Minden a logic apps indítsa el az eseményindító, amely azt adja meg a hívásokat, elindíthatja a logikai alkalmazás futtatásához. Eseményindítók használható típusú itt találhatók:

* A *lekérdezési* eseményindító, amely rendszeres időközönként ellenőrzi az adott szolgáltatás HTTP-végpont
* A *leküldéses* indít, amely a [munkafolyamat szolgáltatás REST API](https://docs.microsoft.com/rest/api/logic/workflows)
  
Indítók a legfelső szintű elemet tartalmaznak:  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Eseményindító típusok és a bemeneti adatok  

Minden egyes indítási típus egy másik felülettel rendelkezik, és különböző *bemenetek* annak viselkedését meghatározó. 

| Indítási típus | Leírás | 
| ------------ | ----------- | 
| **Recurrence** | Akkor következik be, a megadott ütemezés szerint. Beállíthatja, hogy egy jövőbeli dátumot és időt az eseményindító kiváltó. A gyakoriság alapján azt is megadhatja, időpontok és a munkafolyamat futtatási nap. | 
| **Kérés**  | A Logic Apps alkalmazást teszi az olyan végponttal, amely hívása, más néven "manual" eseményindító. | 
| **HTTP** | Ellenőrzi, vagy *szavazások*, webes HTTP-végponttal. A HTTP-végpont meg kell felelnie egy meghatározott eseményindító szerződés "202" aszinkron minta használatával vagy tömböt ad vissza. | 
| **ApiConnection** | Például egy HTTP-eseményindítóval kérdezi le, de használja [Microsoft által felügyelt API-k](../connectors/apis-list.md). | 
| **HTTPWebhook** | Lehetővé teszi a Logic Apps alkalmazást az hívható végpont, például a **kérelem** indítható el, de meghívja a megadott URL-cím regisztrálása és a regisztráció megszüntetését. |
| **ApiConnectionWebhook** | Működik, mint a **HTTPWebhook** eseményindító, de a Microsoft által felügyelt API-kat használ. | 
||| 

További információkért lásd: [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Ismétlődés eseményindító  

Ehhez az eseményindítóhoz a megadott ismétlődési és ütemezés alapján fut, és egyszerű módot nyújt rendszeresen futó munkafolyamat. 

Íme egy egyszerű ismétlődés eseményindító példa, amely naponta fut:

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
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Az eseményindító definícióját a következő:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
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
||||| 

Például az ismétlődési eseményindító meghatározza, hogy fut-e a logikai alkalmazás hetente minden hétfőn: 10:30 AM, 12:30 PM és 2:30 PM csendes-óceáni téli idő, kezdési 2017. szeptember 9., 2:00 PM legkorábban:

``` json
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

Az eseményindító tulajdonsága nem kötelező nevű `schema`:
  
| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| Séma | Nem | Objektum | A JSON-séma, amely megvizsgálja a bejövő kérelem. Hasznos segítséget nyújt a soron következő munkafolyamat lépéseket tudja, mely tulajdonságokat kell hivatkoznia. | 
||||| 

Ehhez az eseményindítóhoz végpontjaként meghívni, meg kell hívnia a `listCallbackUrl` API. Lásd: [munkafolyamat szolgáltatás REST API](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>HTTP eseményindító  

Ehhez az eseményindítóhoz lekérdezi a megadott végpontot, és ellenőrzi a válasz határozza meg, hogy a munkafolyamat futtasson-e vagy sem. Itt a `inputs` objektum e egy HTTP-hívás megalkotásához szükséges paramétereket fogadja: 

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| metódus | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| uri azonosító | Igen| Karakterlánc | A HTTP vagy HTTPs végpont az eseményindító-kereső. Maximális méret: 2 KB | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). <p>Ütemező túl van egy több támogatott tulajdonságot: `authority`. Alapértelmezés szerint ez az érték van `https://login.windows.net` Ha nincs megadva, de használhat például egy másik értéket`https://login.windows\-ppe.net`. | 
||||| 

A *újrapróbálkozási házirendet* 408 429 és 5xx mellett minden csatlakozási kivétel időszakos hibák, mint a HTTP-állapotkódok jellemzőek vonatkozik. Megadhatja a házirend a `retryPolicy` objektum az itt látható módon:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

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

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection eseményindító  

Az alapvető funkciókat ehhez az eseményindítóhoz működik, mint a HTTP-eseményindítóval. A művelet azonosítására szolgáló paraméterek azonban eltérőek. Például:   
  
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
        "category": "myCategory"
    }
}
```

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| gazdagép | Igen | Objektum | Az üzemeltetett átjáró és az API-alkalmazás azonosítója | 
| metódus | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Az a `host` objektumot, az alábbiakban a tulajdonságok:  
  
| Elem neve | Szükséges | Leírás | 
| ------------ | -------- | ----------- | 
| API runtimeUrl | Igen | A felügyelt API-végpont | 
| Kapcsolat neve |  | A munkafolyamat által használt felügyelt API-kapcsolat neve. Hivatkoznia kell nevű paraméter `$connection`. |
|||| 

A *újrapróbálkozási házirendet* 408 429 és 5xx mellett minden csatlakozási kivétel időszakos hibák, mint a HTTP-állapotkódok jellemzőek vonatkozik. Megadhatja a házirend a `retryPolicy` objektum az itt látható módon:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Az alábbiakban egy API-kapcsolat eseményindító kimenetek:
  
| Elem neve | Típus | Leírás |
| ------------ | ---- | ----------- |
| fejlécek | Objektum | A fejléceket a HTTP-válaszok | 
| törzs | Objektum | A HTTP-válasz törzsében | 
|||| 

További információ [hogyan működik az API-kapcsolat díjszabása elindítja](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>HTTPWebhook trigger  

Ehhez az eseményindítóhoz biztosít egy végpontot, hasonlóan a `Request` eseményindító, de a HTTPWebhook eseményindító is meghívja a megadott URL-cím regisztrálása és a regisztráció megszüntetését. Íme egy példa hogyan nézhet ki egy HTTPWebhook eseményindító:

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

Ezek a szakaszok számos nem kötelező, és a HTTPWebhook eseményindító viselkedés attól függ, a következő szakaszok adja meg, vagy hagyja ki. Az alábbiakban a HTTPWebhook eseményindító tulajdonságait:
  
| Elem neve | Szükséges | Leírás | 
| ------------ | -------- | ----------- |  
| előfizetés | Nem | Megadja a kimenő kérelem az eseményindító jön létre, és elvégzi a kezdeti regisztráció hívására. | 
| előfizetés lemondása | Nem | Megadja a kimenő kérelem hívására az eseményindító törlődik. | 
|||| 

Megadhat egy webhook eseményindító korlátok ugyanúgy, mint [HTTP aszinkron korlátok](#asynchronous-limits). Itt az kapcsolatos további információ a `subscribe` és `unsubscribe` műveletek:

* `subscribe` nevezik, hogy az eseményindító elindíthatja események figyel. A kimenő hívás kezdődik-e az ugyanezen paraméterekkel, mint szabványos HTTP-műveleteket. Ez a hívás történik, ha a munkafolyamat-módosításai bármely olyan módon, például a hitelesítő adatok már megkezdődött, vagy az eseményindító bemeneti paraméterek módosításához. 
  
  Ez a hívás támogatásához a `@listCallbackUrl()` függvény a munkafolyamat egy ehhez az eseményindítóhoz egyedi URL-címet adja vissza. Az URL-címet jelenti. a szolgáltatás REST API-t használó végpontokon egyedi azonosítója.
  
* `unsubscribe` Ha egy műveletet ehhez az eseményindítóhoz érvénytelen, beleértve az e műveletek Renderelés automatikusan neve:

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

## <a name="triggers-conditions"></a>Eseményindítók: feltételek

Bármely eseményindító egy vagy több feltételt segítségével határozza meg, hogy a munkafolyamat futtasson-e vagy sem. Ebben a példában a jelentés csak eseményindítók során a munkafolyamat `sendReports` paraméter értéke igaz. 

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

Végezetül a feltételeket az állapotkód: az eseményindító hivatkozhat. Például egy munkafolyamat kezdődnek, csak akkor, ha a webhely egy állapotkódot 500 adja vissza:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> Alapértelmezés szerint egy eseményindító akkor következik be, csak a fogadás egy "200 OK" választ. Egy kifejezés hivatkozik egy eseményindító állapotkód bármely olyan módon, ha az eseményindító alapértelmezett viselkedés váltja fel. Igen ha azt szeretné, hogy az eseményindító több állapotkód esetében, például alapján tűz, állapotkód: 200 és 201-es állapotkód, meg kell adnia a jelen nyilatkozat, a feltétel: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Eseményindítók: Feldolgozni egy tömb több futtatása

Az eseményindító feldolgozni a logikai alkalmazásnak tömböt ad vissza, ha egyes esetekben "az egyes" hurkot minden tömb elem túl sok tarthat. Ehelyett használhatja a **SplitOn** az eseményindító tulajdonsága *debatch* a tömb. 

Debatching felosztja a tömb cikkeket, és elindítja egy új logic app példányát futtató minden tömb elemhez. Ezt a módszert akkor hasznos, például, ha azt szeretné, és kérdezze le a végpont visszaadó előfordulhat, hogy több új elemek közötti lekérdezési időközt.
A tömb maximális számának elemek, amelyek **SplitOn** tud feldolgozni egy logikai alkalmazást futtató című [korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Hozzáadhat **SplitOn** csak az eseményindítók manuálisan meghatározása vagy a logikai alkalmazás JSON-definícióból kódnézetben felülbírálása. Nem használhat **SplitOn** Ha azt szeretné, a szinkron válasz minta végrehajtásához. A munkafolyamat által használt **SplitOn** és a válasz tartalmazza művelet aszinkron fut, és azonnal küld egy `202 ACCEPTED` választ.

Ha az eseményindító Swagger-fájl a hasznos adatok között, amely tömb, ismerteti a **SplitOn** tulajdonság automatikusan hozzáadódik az eseményindító. Ellenkező esetben vegye fel ezt a tulajdonságot a debatch kívánt tömböt válasz-adattartalmában belül. 

Tegyük fel például, hogy ez választ ad vissza az API-k: 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
A Logic Apps alkalmazást csak szüksége van a tartalom `Rows`, hogy létrehozhasson egy eseményindítót, például ebben a példában.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Ha használja a `SplitOn` parancssori tulajdonságok, a tömb kívül nem olvasható be. Így például nem sikerül a `status` az API által visszaadott válaszban tulajdonság.
> 
> Hiba elkerülésére, ha a `Rows` tulajdonság nem létezik, a példában a `?` operátor.

Most már használhatja a munkafolyamat-definíciót `@triggerBody().name` beolvasandó `customer-name-one` az első futni, és `customer-name-two` a második futtatható. Igen, az eseményindító kimenete megjelenését, például a példákat:

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Eseményindítók: Csak az összes aktív futtatása Befejezés tűz

Ismétlődés eseményindítók beállíthatja, hogy azok érvényesítést, csak ha az összes aktív futtatása befejeződött. Ez a beállítás konfigurálásához állítsa a `operationOptions` tulajdonságot `singleInstance`:

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

Ha egy ütemezett ismétlődési történik, a munkafolyamat-példányok futása közben, az eseményindító kihagyja, és megvárja a következő ütemezett ismétlési időköz újra.

## <a name="actions-overview"></a>Műveletek áttekintése

Nincsenek számos különböző típusú műveletek, az egyedi viselkedését. Minden művelet egy művelet viselkedését meghatározó különböző bemenetek rendelkezik. Gyűjtemény műveletek önmagukban sok más műveleteket is tartalmazhat. 

### <a name="standard-actions"></a>Standard műveletek  

| Művelettípus | Leírás | 
| ----------- | ----------- | 
| **HTTP** | Meghívja a HTTP-webalkalmazás végponttal. | 
| **ApiConnection**  | A HTTP-művelet hasonlóan működik, de használja [Microsoft által felügyelt API-k](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | HTTPWebhook hasonlóan működik, de a Microsoft által felügyelt API-kat használ. | 
| **Válasz** | A bejövő válasz meghatározása. | 
| **Összeállítás** | A tevékenység bemenetei tetszőleges objektum hoz létre. | 
| **Függvény** | Egy Azure függvény jelöli. | 
| **Wait** | A rögzített méretű idő vagy egy adott időpontig vár. | 
| **munkafolyamat** | Egy beágyazott munkafolyamat jelöli. | 
| **Összeállítás** | A tevékenység bemenetei tetszőleges objektum hoz létre. | 
| **Lekérdezés** | Egy tömb feltétel alapján szűri. | 
| **Kiválasztás** | A tömb egyes elemei projektek be új értéket. Például átalakíthatja számokból álló tömb egy objektumokból álló tömb. | 
| **Table** | Elemek tömbje alakítja át a CSV és HTML táblát. | 
| **Leáll** | Egy munkafolyamat leáll. | 
| **Wait** | A rögzített méretű idő vagy egy adott időpontig vár. | 
| **munkafolyamat** | Egy beágyazott munkafolyamat jelöli. | 
||| 

### <a name="collection-actions"></a>Gyűjtemény műveletek

| Művelettípus | Leírás | 
| ----------- | ----------- | 
| **If** | Egy kifejezés kiértékelése és eredménye, a megfelelő fiókirodai futtatja. | 
| **Switch** | Egy objektum meghatározott értékek alapján különböző műveleteket hajt végre. | 
| **ForEach** | Ez a ismétlési művelet tömb telepítéseket, és minden tömb elemen belső műveleteket hajtja végre. | 
| **Until** | Az ismétlési művelet belső műveleteket hajtja végre, amíg egy feltétel eredménye igaz. | 
| **Hatókör** | Ezzel az egyéb műveletek logikai csoportosítása. | 
|||  

## <a name="http-action"></a>HTTP-művelet  

HTTP-művelethez meghívja a megadott végpont, és ellenőrzi a válasz határozza meg, hogy a munkafolyamat futtasson-e vagy sem. Példa:
  
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
| metódus | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| uri azonosító | Igen| Karakterlánc | A HTTP vagy HTTPs végpont az eseményindító-kereső. Maximális méret: 2 KB | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). <p>Ütemező túl van egy több támogatott tulajdonságot: `authority`. Alapértelmezés szerint ez az érték van `https://login.windows.net` Ha nincs megadva, de használhat például egy másik értéket`https://login.windows\-ppe.net`. | 
||||| 

HTTP-műveletek és támogatja a APIConnection műveletek *ismételje meg a házirendek*. Újrapróbálkozási házirendje érvényes időszakos hibák, mint a HTTP-állapotkódok jellemzőek 408 429 és 5xx minden csatlakozási kivétel mellett. Megadhatja a házirend a `retryPolicy` objektum az itt látható módon:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Ez a példa HTTP-művelet újrapróbálja hívjon le a legújabb híreket kétszer három végrehajtások és minden kísérlet 30 másodperces késleltetés összesen időszakos hibák esetén:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

Az újrapróbálkozási időköz megadott [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601). Ezt az időközt értéke a minimális és az alapértelmezett 20 másodperc, a maximális érték pedig egy óra. Az alapértelmezett és a maximális újrapróbálkozási számláló négy óra. Ha nem adja meg egy újrapróbálkozási házirend-definíció egy `fixed` stratégia használatos az alapértelmezett értékekkel újrapróbálkozási számát és az időközt. Tiltsa le az újrapróbálkozási házirendet, adja meg a típus `None`.

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

Korlátozhatja az időtartamot a egy aszinkron mintát, hogy egy adott időintervallumban. Ha az időtartam nem érte el a Terminálszolgáltatások állapot eltelte után a művelet állapota jelölése `Cancelled` rendelkező egy `ActionTimedOut` kódot. ISO 8601 formátumban megadott korlát időkorlát. Ez a példa bemutatja, hogyan adhat meg korlátai:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection művelet

Ez a művelet egy Microsoft által felügyelt összekötő, egy hivatkozást egy érvényes kapcsolat és az API és a paraméterek igénylő hivatkozik. Íme egy példa a APIConnection művelet:

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
| gazdagép | Igen | Objektum | Az összekötő adatait jelöli, mint a `runtimeUrl` és a kapcsolat objektum hivatkozását. | 
| metódus | Igen | Karakterlánc | A HTTP-metódus egyikét használja: "GET", "POST", "PUT", "DELETE", "Javítás" vagy "HEAD" | 
| elérési út | Igen | Karakterlánc | Az API-művelet elérési útja | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Újrapróbálkozási házirendje érvényes időszakos hibák, mint a HTTP-állapotkódok jellemzőek 408 429 és 5xx minden csatlakozási kivétel mellett. Megadhatja a házirend a `retryPolicy` objektum az itt látható módon:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

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
| gazdagép | Igen | Objektum | Az összekötő adatait jelöli, mint a `runtimeUrl` és a kapcsolat objektum hivatkozását. | 
| elérési út | Igen | Karakterlánc | Az API-művelet elérési útja | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
| retryPolicy | Nem | Objektum | Ez az objektum használata a újrapróbálkozásra 4xx vagy 5xx hiba testreszabása. További információkért lásd: [ismételje meg a házirendek](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nem | Karakterlánc | A speciális viselkedés felülbírálásához csoportját határozza meg. | 
| hitelesítés | Nem | Objektum | A módszert, amelyet a kérés a hitelesítéshez használandó jelöli. További információkért lásd: [Feladatütemező kimenő hitelesítési](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Válasz művelet  

Ez a művelet a teljes válasz forgalma a HTTP-kérelem tartalmazza, és tartalmazza a `statusCode`, `body`, és `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
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

## <a name="compose-action"></a>A művelet összeállítása

Ez a művelet lehetővé teszi, hogy létrehozni egy tetszőleges objektumot, és a kimeneti Kiértékel a művelet bemeneti adatokat, és az eredményt. 

> [!NOTE]
> Használhatja a `Compose` műveletet hoz létre a kimenetet, többek között az objektumok, tömbök és bármely más típusból XML és bináris hasonlóan a logic apps natívan támogatja.

Használhatja például a `Compose` több művelet kimeneteinek egyesítési művelet:

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

## <a name="function-action"></a>Függvény művelet

Ez a művelet lehetővé teszi, hogy jelentik és hívás egy [Azure függvény](../azure-functions/functions-overview.md), például:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
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
| metódus | Nem | Karakterlánc | A függvény használt HTTP-metódus. Ha nincs megadva, a "POST" az alapértelmezett mód. | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
|||||

A Logic Apps alkalmazást mentésekor a Logic Apps motor ellenőrzi néhány a hivatkozott függvény:

* A függvény hozzáféréssel kell rendelkeznie.
* Csak a szabványos HTTP-eseményindítóval vagy általános JSON Webhook eseményindító is használhatja.
* A függvény a megadott útvonal nem tartalmazhat.
* Csak "függvény" és "névtelen" hitelesítési szintjeinek használható.

> [!NOTE]
> A Logic Apps motor kéri le, és gyorsítótárba helyezi azt a eseményindító URL-címet, amely futásidőben. Így bármilyen műveletet érvényteleníti a gyorsítótárazott URL-címe, ha a művelet futásidőben nem sikerül. A probléma megoldása érdekében mentse a logikai alkalmazást újra, amely azt eredményezi, a logikai alkalmazást az lekérése, valamint az eseményindító URL-cím gyorsítótárazása újra.

## <a name="select-action"></a>Kijelölési művelet

Ez a művelet lehetővé teszi a tömb egyes elemei projekt be új értéket. Ez a példa számokból álló tömb alakít át egy objektumokból álló tömb:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| forrás: | Igen | Tömb | A forrástömb |
| kiválasztás | Igen | Bármelyik | A forrás tömb egyes elemei alkalmazott leképezése |
||||| 

A kimenet a `select` művelet olyan tömb, amely a bemeneti tömb azonos számossága. Minden elem által meghatározott alakította a `select` tulajdonság. Ha a bemeneti érték egy üres tömb, a kimeneti is üres tömb.

## <a name="terminate-action"></a>Leállítási művelet

Ez a művelet leállítja a munkafolyamatot futtató, megszakítani minden folyamatban lévő műveleteket, és kihagyja a fennmaradó műveleteket. A megszakítási művelet nem befolyásolja a már elvégzett műveletek.

Ahhoz például, hogy állítsa le, amely rendelkezik futtató `Failed` állapota:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Igen | Karakterlánc | Futtatási tartozó állapotát, amely vagy `Failed` vagy `Cancelled` |
| runError | Nem | Objektum | A hiba részletes adatait. Támogatott csak akkor, ha `runStatus` értéke `Failed`. |
| runError kód | Nem | Karakterlánc | A Futtatás hibakód: |
| runError üzenet | Nem | Karakterlánc | A Futtatás hibaüzenet | 
||||| 

## <a name="query-action"></a>Lekérdezési művelet

Ez a művelet lehetővé teszi egy tömb feltétel alapján szűrni. 

> [!NOTE]
> Az új művelet kimenetet, többek között az objektumok, tömbök és bármely más típusból XML és bináris hasonlóan a logic apps által natívan támogatott összeállításához nem használható.

Ha például 2-nél nagyobb számot kiválasztásához:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| forrás: | Igen | Tömb | A forrástömb |
| Ha | Igen | Karakterlánc | A feltétellel, hogy a forrás tömb egyes elemei vonatkozik. Ha nincs érték felel meg a `where` , az eredmény feltétele egy üres tömb. |
||||| 

A kimenet a `query` művelete olyan tömb, amely rendelkezik, amelyek megfelelnek a következő feltételt: a bemeneti tömb elemei.

## <a name="table-action"></a>Tábla művelet

Ez a művelet lehetővé teszi egy tömb konvertálása a CSV és HTML táblát. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| forrás: | Igen | Tömb | A forrástömb. Ha a `from` tulajdonság értéke üres tömb, a program üres táblát kimenete. | 
| Formátumban | Igen | Karakterlánc | A tábla kívánt formátum, "CSV" vagy "HTML" | 
| oszlopok | Nem | Tömb | A kívánt tábla oszlopait. Használja az alapértelmezett tábla alakzat felülbírálására. | 
| Oszlopfejléc | Nem | Karakterlánc | Az oszlop fejlécére | 
| oszlop értéke | Igen | Karakterlánc | Az oszlop értéke | 
||||| 

Tegyük fel, hogy adhat meg ebben a példában például egy táblázat műveletet:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

A tömb, és `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Ebben a példában a kimenete a következő:

<table><thead><tr><th>ID (Azonosító)</th><th>Name (Név)</th></tr></thead><tbody><tr><td>0</td><td>almák</td></tr><tr><td>1</td><td>narancs</td></tr></tbody></table>

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

Ebben a példában a kimenete a következő:

<table><thead><tr><th>Készítsen azonosítója</th><th>Leírás</th></tr></thead><tbody><tr><td>0</td><td>friss almák</td></tr><tr><td>1</td><td>friss narancs</td></tr></tbody></table>

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
> Az a várakozási időtartamot is megadhat a `interval` objektum vagy a `until` objektum, de soha sem egyszerre mindkettőre.

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| amíg | Nem | Objektum | A pontok alapján időben várakozási időtartama | 
| amíg időbélyeg | Igen | Karakterlánc | Az az időpont [UTC dátum időformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) várakozási lejáratának | 
| interval | Nem | Objektum | A várakozási időtartama időköze és száma alapján | 
| időköze | Igen | Karakterlánc | Az idő egységét. Csak az egyik ezeket az értékeket használja: "második", "perc", "hour", "day", "hét" vagy "honap" | 
| időköz száma | Igen | Egész szám | A várakozási időtartamot időintervallum-egység szám, amely pozitív egész szám | 
||||| 

## <a name="workflow-action"></a>Munkafolyamat-művelet

Ez a művelet lehetővé teszi a munkafolyamat ágyazhatók be. A Logic Apps motor a gyermek munkafolyamat hozzáférés-ellenőrzést végez pontosabban az eseményindító, így a gyermek munkafolyamat hozzáféréssel kell rendelkeznie. Példa:

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
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
| host triggerName | Igen | Karakterlánc | A meghívni kívánt eseményindító nevét | 
| Lekérdezések | Nem | Objektum | Lekérdezési paramétereket, hogy az URL-címben felvenni kívánt jelöli. <p>Például `"queries": { "api-version": "2015-02-01" }` hozzáadja `?api-version=2015-02-01` URL-címét. | 
| fejlécek | Nem | Objektum | Minden egyes fejlécet tartalmazta, amely a kérelemben küldött jelöli. <p>Ha például a nyelv, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| törzs | Nem | Objektum | A tartalom a végpontnak küldött jelöli. | 
||||| 

Ez a művelet kimenetének alapján a meghatározása a `Response` a gyermek munkafolyamat művelet. Ha nem ad meg az alárendelt munkafolyamat egy `Response` művelet, a kimenetek üresek.

## <a name="collection-actions-overview"></a>Gyűjtemény műveletek áttekintése

Segítségével meghatározhatja a munkafolyamat-végrehajtási, a gyűjtemény műveletek a olyan más műveleteket is felvehet. Közvetlenül hivatkozik egy gyűjteményt a gyűjtemény kívül lévő műveletek hivatkozhat. Például, ha megad egy `Http` művelet hatókörben, majd `@body('http')` a munkafolyamaton belül bárhol továbbra is érvényes. Emellett egy gyűjtemény műveletek "futtatható" más műveletek ugyanaz a gyűjtemény.

## <a name="if-action"></a>Ha a művelet

Ez a művelet, amely feltételes utasítás, lehetővé teszi olyan feltétel értékelése és alapján, hogy a kifejezés igaz ág hajtható végre. Ha a feltétel sikeresen igaz, a következő feltételt: "Sikeres" van megjelölve. A műveletek a `actions` vagy `else` objektumok értékelhetők ezeket az értékeket:

* "Sikeres" Amikor futtatni, és a sikeres
* "Sikertelen" futtatni, és sikertelen
* "Skipped", ha a megfelelő fiókirodai nem fut

További információ [feltételes utasítások a logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása mikor `expression` kiértékelésének eredménye `true` | 
| kifejezés | Igen | Karakterlánc | A kiértékelendő kifejezés |
| más | Nem | Objektum | A belső műveletek futtatása mikor `expression` kiértékelésének eredménye `false` |
||||| 

Példa:

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Feltételek használatát kifejezések a műveletek

Íme néhány példa, amelyek bemutatják, hogyan használható kifejezések feltételek:
  
| JSON-kifejezés | Eredmény | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Bármely érték, amely kiértékeli, igaz okozza ezt az állapotot adja át. Csak a logikai kifejezésen támogatja. Más típusúra átalakítása logikai érték, ezek a funkciók használata: `empty` vagy `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Összehasonlítás funkciókat támogatja. Az ebben a példában a művelet csak akkor, ha action1 eredménye nagyobb, mint a küszöbérték futtatja. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Támogatja a logikai funkciók beágyazott logikai kifejezésen létrehozásához. Ebben a példában a művelet fut, amikor action1 eredménye több, mint a küszöbérték, illetve 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Ellenőrizze, hogy egy tömb van-e azokat a cikkeket, tömb funkciókat is használhat. Ebben a példában a művelet fut. Ha a hibák tömb üres. | 
| `"expression": "parameters('hasSpecialAction')"` | Ebben a kifejezésben hibát okoz, és nem egy érvényes feltételt. Feltételeket kell használni a "@" szimbólummal. | 
||| 

## <a name="switch-action"></a>Kapcsoló művelet

Ez a művelet, amely egy switch utasításban, egy objektum, kifejezés vagy token megadott értékek alapján különböző műveleteket hajtja végre. Ez a művelet az objektum, kifejezés vagy token kiértékeli, kiválasztja azt az esetet, amely megfelel az eredményt, és futtatja a műveleteket csak az adott esethez. Semmilyen esetben sem felel meg az eredményt, ha az alapértelmezett művelet fut. Amikor a switch utasítás fut, csak egyetlen esetet meg kell felelnie az eredményt. További információ [kapcsoló utasítások a logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| kifejezés | Igen | Karakterlánc | Az objektum, a kifejezés vagy a token által kiértékelése | 
| esetek | Igen | Objektum | A kifejezés eredménye alapján futtatnak belső műveleteket tartalmaznak. | 
| Eset | Igen | Karakterlánc | A eredményeképpen teljesüléséhez szükséges értéke | 
| műveletek | Igen | Objektum | A belső végrehajtandó műveleteket, az a kifejezés eredményének megfelelő eset futtatása | 
| alapértelmezett | Nem | Objektum | A belső végrehajtandó futtatását, amikor egyetlen eset megfelelő eredménye | 
||||| 

Példa:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Foreach-művelet

Ez a ismétlési művelet tömb telepítéseket, és minden tömb elemen belső műveleteket hajtja végre. Alapértelmezés szerint a Foreach hurok párhuzamosan futtatja. A maximális száma párhuzamos ciklusok, amely "az egyes" hurkokat is futnak, lásd: [korlátozásai és konfigurációs](../logic-apps/logic-apps-limits-and-config.md). Az egyes ciklusok egymás után futnak, állítsa be a `operationOptions` paramétert `Sequential`. További információ [Foreach fut a hurokban, a logic apps](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása a hurkon belül | 
| foreach | Igen | Karakterlánc | A tömböt az iterációt | 
| operationOptions | Nem | Karakterlánc | Adja meg a műveletet lehetőségeket viselkedés testreszabása. Jelenleg csak `Sequential` ahol párhuzamos-e az alapértelmezett viselkedés egymás után futtatási ismétlési. |
||||| 

Példa:

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
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Amíg a művelet

A ismétlési művelet a futás belső műveletek, amíg a feltétel igaz. További információ ["csak" a logic apps hurkok](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- | 
| műveletek | Igen | Objektum | A belső műveletek futtatása a hurkon belül | 
| kifejezés | Igen | Karakterlánc | A kifejezés kiértékelése mindegyik iteráció után | 
| Korlát | Igen | Objektum | A hurok korlátok. Legalább egy korlátot kell definiálnia. | 
| darab | Nem | Egész szám | Az ismétlések végrehajtásához számára vonatkozó korlátozást | 
| timeout | Nem | Karakterlánc | Az időkorlátot a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601) , amely megadja, hogy mennyi ideig kell futtatnia a hurok |
||||| 

Példa:

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
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Hatókör művelet

Ez a művelet lehetővé teszi a logikailag a munkafolyamat műveleteit. A hatókör is lekéri a saját állapotát. Ha minden a műveletek adott hatókör Befejezés fut a. További információ [hatókörök](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Name (Név) | Szükséges | Típus | Leírás | 
| ---- | -------- | ---- | ----------- |  
| műveletek | Igen | Objektum | A belső műveletek hatókörében |
||||| 

## <a name="next-steps"></a>További lépések

* További információ [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md)
* További információ [munkafolyamat REST API-n](https://docs.microsoft.com/rest/api/logic/workflows)