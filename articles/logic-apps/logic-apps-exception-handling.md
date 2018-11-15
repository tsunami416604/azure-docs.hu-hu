---
title: Hiba- és kivételkezelés – Azure Logic Apps |} A Microsoft Docs
description: Ismerje meg a hiba- és kivételkezelés Azure Logic Apps a minták
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 19a715812f1250523fd050ac8b80dee9ec664be4
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686262"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibák és az Azure Logic Appsben kivételek kezelése

Minden integrációs architektúra megfelelően kezeli az állásidő és a függő rendszerek által okozott problémák módon kihívást is jelenthet. A Logic Apps segítségével hozhat létre hatékony és rugalmas integrációja, amely a problémák és hibák kezelésére, kiváló élményt nyújt a hibák és kivételek kezelése. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

A legalapvetőbb kivétel és a hibakezelés, használhat egy *újrapróbálkozási szabályzat* minden művelet vagy eseményindítót, ahol támogatott. Ez az újrapróbálkozási szabályzat meghatározza, hogyan művelettel vagy eseményindítóval újrapróbálkozik egy kérelmet, ha az eredeti kérés túllépi az időkorlátot vagy meghiúsul, amely minden olyan kérelmet, amely egy 408, 429-es vagy 5xx válasz eredményez. Ha nincs más újrapróbálkozási szabályzatot használja, az alapértelmezett házirend szolgál. 

Az újrapróbálkozási szabályzat típusok a következők: 

| Típus | Leírás | 
|------|-------------| 
| [**Alapértelmezett**](#default-retry) | Ez a szabályzat legfeljebb négy újrapróbálkozás küld [ *ezzel exponenciálisan növelve* ](#exponential-retry) időközök, méretezhető, 7.5 másodperc, de 5 és 45 másodperc között vannak korlátozzuk. | 
| [**Exponenciális időköz**](#exponential-retry)  | Ez a szabályzat kiválasztott exponenciálisan egyre bővülő tartományból a kérések elküldése előtt egy véletlenszerű időköz vár. | 
| [**Rögzített időköz**](#fixed-retry)  | Ez a szabályzat a megadott időszak vár, a kérések elküldése előtt. | 
| [**Egyik sem**](#no-retry)  | Ne küldje el újra a kérelmet. | 
||| 

Újrapróbálkozási házirend korlát kapcsolatos információkért lásd: [Logic Apps-korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Újrapróbálkozási szabályzat módosítása

Egy eltérő újrapróbálkozási házirendet választ ki, kövesse az alábbi lépéseket: 

1. Nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben. 

2. Nyissa meg a **beállítások** számára egy műveletet vagy eseményindítót.

3. Ha a műveletet vagy eseményindítót támogatja újrapróbálkozási szabályzatot, alatt **ismételje meg a házirend**, válassza ki a kívánt típusát. 

Vagy manuálisan is megadhatja az újrapróbálkozási szabályzat a `inputs` egy műveletet vagy eseményindítót, amely támogatja a következő szakaszban újrapróbálkozási szabályzatok. Ez az újrapróbálkozási szabályzat nem ad meg, ha a művelet használja az alapértelmezett szabályzat.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Szükséges*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*újrapróbálkozási házirendtípus*> | Karakterlánc | A használni kívánt újrapróbálkozási házirendtípus: `default`, `none`, `fixed`, vagy `exponential` | 
| <*újrapróbálkozási-időköz*> | Karakterlánc | Az újrapróbálkozási időköz, ahol az értéket kell használnia [ISO 8601 formátumú](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Az alapértelmezett minimális gyakoriság `PT5S` és a maximális időköz `PT1D`. Az exponenciális időköz szabályzat használatakor különböző minimális és maximális értékeket is megadhat. | 
| <*újrapróbálkozások*> | Egész szám | 1 és 90 között kell lennie újrapróbálkozások száma | 
||||

*Nem kötelező*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*minimális-időköz*> | Karakterlánc | Az exponenciális időköz házirend, a legkisebb időköze a véletlenszerűen kiválasztott időköz [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximális-időköz*> | Karakterlánc | Az exponenciális időköz házirend, a véletlenszerűen kiválasztott időszakban a legnagyobb időközönként [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Itt látható a különböző Házirendtípusok további információt.

<a name="default-retry"></a>

### <a name="default"></a>Alapértelmezett

Ez az újrapróbálkozási szabályzat nem ad meg, ha a művelet használja-e az alapértelmezett házirendet, amely ténylegesen egy [exponenciális időköz házirend](#exponential-interval) , amely legfeljebb négy újrapróbálkozás exponenciálisan növeli a magánkészletekben 7,5 másodperc intervallumok küld. Az időköz 5 és 45 másodperc között van korlátozzuk. 

Abban az esetben, ha nincs kifejezetten definiált a műveletet vagy eseményindítót, Íme egy példa HTTP-művelet az alapértelmezett házirend működését:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>None

Adja meg, hogy a művelet vagy trigger nem újra a sikertelen kérelmek, állítsa be a <*újrapróbálkozási házirendtípus*> való `none`.

### <a name="fixed-interval"></a>Rögzített időköz

Adja meg, hogy a művelet vagy trigger vár, a megadott időszak a kérések elküldése előtt, állítsa be a <*újrapróbálkozási házirendtípus*> való `fixed`.

*Példa*

Az újrapróbálkozási szabályzat megkísérli a legfrissebb két alkalommal egyes kísérletek közötti késleltetés 30 másodperces első sikertelen kérelem után:

```json
"Get_latest_news": {
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

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponenciális időköz

Adja meg, hogy a művelet vagy trigger vár egy véletlenszerűen kiválasztott időszakban a kérések elküldése előtt, állítsa be a <*újrapróbálkozási házirendtípus*> való `exponential`. A véletlenszerű időköz exponenciálisan egyre bővülő tartományból van kiválasztva. Igény szerint felülbírálhatja az alapértelmezett minimális és maximális időköze a saját minimális és maximális intervallumok megadásával.

**Véletlen változó tartományok**

Ez a táblázat bemutatja, hogyan Logic Apps a hoz létre egy egységes véletlen változó megadott egyes újra az újrapróbálkozások számát bezárólag:

| Ismételje meg a számot | Minimális időköz | Maximális időköz |
|--------------|------------------|------------------|
| 1 | Max (0, <*minimális-interval*>) | minimális (időközt <*maximális-interval*>) |
| 2 | Max (időközt <*minimális-interval*>) | perc (2 * időközt <*maximális-interval*>) |
| 3 | Max (2 * időközt <*minimális-interval*>) | minimális (4 * időközt <*maximális-interval*>) |
| 4 | Max (4 * időközt <*minimális-interval*>) | minimális (8 * időközt <*maximális-interval*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>A tényleges és RunAfter tulajdonság hibáinak a kezelése

Minden egyes logikaialkalmazás-művelet deklarálja a műveleteket, amelyeket le kell futnia ahhoz, hogy a művelet elindult, hasonló adhatja meg a lépések sorrendje a munkafolyamatban. Egy művelet definíciójában a **runAfter** tulajdonság határozza meg, a rendezés és a egy olyan objektum, amely leírja, hogy mely műveletek és a művelet állapotát a művelet hajtható végre.

Alapértelmezés szerint az összes olyan műveleteket, adja hozzá a Logic App Designerben vannak beállítva az előző lépés után futtassa, ha az előző lépésben eredmény **sikeres**. Azonban testre szabhatja a **runAfter** értékét úgy, hogy a műveletek értesíti, ha a korábbi műveletek tárigénye **sikertelen**, **kihagyva**, vagy valamilyen kombinációját ezeket az értékeket. Például vegyen fel egy elemet egy adott Service Bus-témakör egy adott után **Insert_Row** művelet meghiúsul, ebben a példában használhatja **runAfter** definíciója:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

A **runAfter** tulajdonsága futtatható, ha a **Insert_Row** műveleti állapota **sikertelen**. A művelet futtatásához, ha a művelet állapota **sikeres**, **sikertelen**, vagy **kihagyva**, a következő szintaxist használja:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Műveletek futtató és a fenti művelet sikertelen volt, miután sikeresen be vannak megjelölve **sikeres**. Ez a viselkedés azt jelenti, hogy ha Ön egy munkafolyamatban, magát a Futtatás sikeresen általános hibák van megjelölve **sikeres**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Műveleteket a hatóköröket és az eredményeket kiértékelése

Hasonló lépéseket futtatása után az egyes műveletek a **runAfter** tulajdonság, csoportosíthatja műveletek belül egy [hatókör](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Hatókörök is használhatja, ha meg szeretné logikailag egy csoportba műveletek, a hatókör összesített állapotának felmérése és állapota alapján műveleteket. A hatókör az összes művelet után a magát a hatókör lekéri a saját állapotát. 

A hatókör állapotának ellenőrzéséhez használhatja ugyanezeket a feltételeket, például ellenőrizze a futtatási állapot egy logikai alkalmazást, amellyel **sikeres**, **sikertelen**, és így tovább. 

Alapértelmezés szerint a hatókör művelet sikeres legyen, a hatókör állapot megjelölve **sikeres**. Ha az utolsó műveletnek a hatókör **sikertelen** vagy **Aborted**, állapota a hatókör meg van jelölve **sikertelen**. 

A kivételeket, egy **sikertelen** hatókör és futtatási műveletek, amelyek kezelik ezeket a hibákat, használhatja a **runAfter** tulajdonsága, amely **sikertelen** hatókör. Így ha *bármely* hatókörében a műveletek meghiúsulnak, és használja a **runAfter** tulajdonság az adott hatókörnél, hozhat létre egyetlen művelettel hibák olvasásra.

Hatókörök korlátozásairól lásd: [határértékek és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Környezet és az eredmények lekérése sikertelen

Bár egy hatókörből hibák megelőzésével akkor hasznos, érdemes lehet a környezeti segítenek megérteni, hogy pontosan milyen műveleteket végezzen ezen kívül az esetleges hibákat vagy állapotkódokkal is sikertelen volt. A `@result()` kifejezése egy hatókör összes művelet eredménye kapcsolatos kontextust biztosít a.

A `@result()` kifejezés (a hatókör neve) egyetlen paramétert fogad, és minden művelet eredményeinek hatókörön belüli tömbjét adja vissza. E művelet objektumok például őket a  **@actions()** objektumot, például a művelet kezdési idő, befejezési időpontja, állapota, bemenetei, korrelációs azonosítók és kimenetek. Hatókörön belüli bármely művelet, amelyet nem sikerült a környezet elküldéséhez, könnyen összepárosíthassa egy  **@result()** függvény és egy **runAfter** tulajdonság.

Minden egyes művelethez művelet futtatása egy hatókör, amely rendelkezik egy **sikertelen** eredményt, és az eredmények a sikertelen műveletek le a tömb szűrése, párosítsa  **@result()** az egy **[Tömb szűrése](../connectors/connectors-native-query.md)** művelet és a egy [ **minden** ](../logic-apps/logic-apps-control-flow-loops.md) ciklus. A szűrt eredmények tömb igénybe és a egy műveletet minden egyes hiba a a **minden** ciklus. 

Íme egy példa, kiegészítve a részletes magyarázatát, amely minden művelettel nem sikerült a válasz törzse a HTTP POST-kérelmet küld a "My_Scope" hatókörön belül:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Részletes útmutató, amely leírja, mi történik, ebben a példában a következő:

1. Minden művelet belül "My_Scope", az eredmény eléréséhez a **tömb szűrése** művelet használja a szűrési kifejezés: `@result('My_Scope')`

2. Feltételét **tömb szűrése** tetszőleges `@result()` egyenlő állapotú elem **sikertelen**. Ez az állapot a tömb, amely rendelkezik az összes művelet eredményeinek "My_Scope" le egy tömb csak a sikertelen művelet eredményeket szűri.

3. Hajtsa végre egy **minden** a hurok a művelet a *szűrt tömb* adja vissza. Ebben a lépésben minden korábban szűrt volt sikertelen művelet eredménye egy műveletet végez.

   Ha a hatókörében egyetlen művelettel sikertelen volt a műveletek a **minden** hurok csak egyszer futtatni. 
   Több sikertelen műveletek egy művelet egy hiba miatt.

4. Küldjön egy HTTP POST az a **minden** konfigurációelem-válasz törzsében, amely a `@item()['outputs']['body']` kifejezés. 

   A `@result()` elem alakzat pedig ugyanaz, mint a `@actions()` formázhatja, és ugyanúgy elemezhető legyen.

5. Sikertelen művelet nevű két egyéni fejlécek belefoglalása (`@item()['name']`) és a sikertelen Futtatás követési azonosító: ügyfél (`@item()['clientTrackingId']`).

Referenciaként, Íme egy példa egy adott `@result()` konfigurációelem, amely a **neve**, **törzs**, és **clientTrackingId** , amely elemzi a korábbi tulajdonságai Példa. Kívül egy **minden** művelet `@result()` ezek az objektumok tömbjét adja vissza.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Különböző kivételkezelési minták végrehajtásához használhatja a jelen cikkben korábban ismertetett kifejezések. Előfordulhat, hogy egyetlen kivételkezelési hatálya alá, amely a teljes szűrt tömb hibák művelet végrehajtásához válassza ki, és távolítsa el a **minden** művelet. Egyéb fontos tulajdonságait is használható a  **@result()** választ az előzőleg ismertetett módon.

## <a name="azure-diagnostics-and-metrics"></a>Azure-diagnosztika és metrikák

Az előző minták remek módja a hibák és kivételek belül futtató kezelése, de is azonosíthatja, és független a Futtatás magát a hibáknak. 
[Az Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) küldése az összes munkafolyamat eseményeket, többek között a minden futtatás és műveleti állapot, egy Azure Storage-fiókot vagy egy eseményközpontba, az Azure Event hubs használatával létrehozott egyszerű módszert kínál. 

Futtatási állapotának értékeléséhez, a naplók és metrikák figyelése, vagy közzéteheti őket bármely előnyben részesített figyelési eszközbe. Egy lehetséges megoldás, ha az Event Hubs használatával az események streamelése [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). A Stream Analytics a rendellenességekre, átlagokat, vagy sikertelen a diagnosztikai naplók alapján élő lekérdezésekre írhat. Stream Analytics segítségével adatokat küld más adatforrások, például várólisták, témakörök, SQL, Azure Cosmos DB vagy Power bi-ban.

## <a name="next-steps"></a>További lépések

* [Tekintse meg, hogyan ügyfél hoz létre az Azure Logic Apps hibakezelés](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps-példák és forgatókönyvek keresése](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
