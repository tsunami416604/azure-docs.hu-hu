---
title: Hibák és kivételek kezelése a munkafolyamatokban
description: Ismerje meg, hogyan kezelhetők az Azure Logic Apps alkalmazásokkal létrehozott automatizált feladatokban és munkafolyamatokban előforduló hibák és kivételek
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284030"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibák és kivételek kezelése az Azure Logic Apps alkalmazásban

Az a mód, ahogyan bármely integrációs architektúra megfelelően kezeli az állásidőt vagy a függő rendszerek által okozott problémákat, kihívást jelenthet. A problémák és hibák kecses kezelését szolgáló robusztus és rugalmas integrációk létrehozása érdekében a Logic Apps első osztályú élményt nyújt a hibák és kivételek kezeléséhez.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Házirendek újrapróbálkozása

A legalapvetőbb kivételés hibakezelés, használhatja *az újrapróbálkozási házirend* minden olyan műveletvagy eseményindító, ahol támogatott, például lásd http [művelet.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) Az újrapróbálkozási házirend határozza meg, hogy a művelet vagy az eseményindító újrapróbálkozik-e egy kéréssel, amikor az eredeti kérelem idővel vagy sikertelenül működik, azaz minden olyan kérés, amely 408, 429 vagy 5xx választ eredményez. Ha más újrapróbálkozási házirend nincs használva, a rendszer az alapértelmezett házirendet használja.

Az újrapróbálkozási házirend típusai a következők:

| Típus | Leírás |
|------|-------------|
| **Alapértelmezett** | Ez a házirend legfeljebb négy újrapróbálkozást küld *exponenciálisan növekvő* időközönként, amelyek 7,5 másodperccel skáláznak, de 5 és 45 másodperc között vannak korlátozva. |
| **Exponenciális időköz**  | Ez a házirend egy exponenciálisan növekvő tartományból kiválasztott véletlenszerű intervallumot vár a következő kérelem elküldése előtt. |
| **Rögzített időköz**  | Ez a házirend megvárja a megadott időközt a következő kérelem elküldése előtt. |
| **Nincs**  | Ne küldje el újra a kérést. |
|||

A házirend-korlátozások újrapróbálkozásáról a [Logic Apps korlátai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md#request-limits)című témakörben talál további információt.

### <a name="change-retry-policy"></a>Az újrapróbálkozási házirend módosítása

Másik újrapróbálkozási házirend kiválasztásához kövesse az alábbi lépéseket:

1. Nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Nyissa **meg** egy művelet vagy eseményindító beállításait.

1. Ha a művelet vagy az eseményindító támogatja az újrapróbálkozási házirendeket, az **Újrapróbálkozási házirend**csoportban válassza ki a kívánt típust.

Vagy manuálisan is megadhatja az `inputs` újrapróbálkozási házirendet a szakaszban egy művelethez vagy eseményindítóhoz, amely támogatja az újrapróbálkozási házirendeket. Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Szükséges*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*újrapróbálkozás-házirend-típus*> | Sztring | A használni kívánt újrapróbálkozási `default`házirendtípus: , `none`, `fixed`vagy`exponential` |
| <*újrapróbálkozási időköz*> | Sztring | Az újrapróbálkozási időköz, ahol az értéknek [ISO 8601 formátumot kell használnia.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) Az alapértelmezett minimális `PT5S` időköz, `PT1D`a maximális időköz pedig a . A exponenciális intervallumházirend használatakor különböző minimális és maximális értékeket adhat meg. |
| <*újrapróbálkozások*> | Egész szám | Az újrapróbálkozások száma, amelynek 1 és 90 között kell lennie |
||||

*Választható*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*minimális intervallum*> | Sztring | Az exponenciális intervallumházirend esetében a véletlenszerűen kiválasztott intervallum legkisebb intervalluma [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*maximális intervallum*> | Sztring | Az exponenciális intervallumházirend esetében a véletlenszerűen kiválasztott intervallum legnagyobb intervalluma [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Az alábbiakban további információkat talál a különböző házirendtípusokról.

<a name="default-retry"></a>

### <a name="default"></a>Alapértelmezett

Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja, amely valójában egy [exponenciális időköz-házirend,](#exponential-interval) amely legfeljebb négy újrapróbálkozást küld exponenciálisan növekvő időközönként, amelyek 7,5 másodperccel vannak méretezve. Az intervallum ot 5 és 45 másodperc között kell lekorlátozni.

Bár a műveletben vagy az eseményindítóban nincs kifejezetten definiálva, az alapértelmezett házirend hogyan viselkedik egy példa HTTP-műveletben:

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

Ha azt szeretné, hogy a művelet vagy az eseményindító ne próbálkozzon újra `none`a sikertelen kérelmeket, állítsa a <*újrapróbálkozási-házirend-típusú*>.To specify that the action or trigger ed the action or trigger ed the action or trigger d't retry failed requests, set the <retry-policy-type> to.

### <a name="fixed-interval"></a>Rögzített időköz

Ha meg szeretné adni, hogy a művelet vagy az eseményindító a következő kérelem elküldése `fixed`előtt megvárja a megadott időtartamot, állítsa a <*újrapróbálkozási-házirend-típusú*> értékre.

*Példa*

Ez az újrapróbálkozási házirend az első sikertelen kérelem után még kétszer próbálja meg beszerezni a legfrissebb híreket, minden kísérlet között 30 másodperces késleltetéssel:

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

Ha meg szeretné adni, hogy a művelet vagy az eseményindító véletlenszerű időközt várjon a következő kérelem elküldése előtt, állítsa a <*újrapróbálkozási-házirend-típusú*> értékre. `exponential` A véletlenszerű intervallum ot exponenciálisan növekvő tartományból választják ki. Szükség esetén az alapértelmezett minimális és maximális intervallumokat is felülbírálhatja a saját minimális és maximális intervallumok megadásával.

**Véletlen változótartományok**

Ez a táblázat azt mutatja be, hogy a Logic Apps hogyan hoz létre egy egységes véletlen változót a megadott tartományban az egyes újrapróbálkozásokhoz, beleértve az újrapróbálkozások számát is:

| Újrapróbálkozási szám | Minimális intervallum | Maximális időköz |
|--------------|------------------|------------------|
| 1 | max(0, <*minimális intervallum->)* | min(intervallum, <*maximális intervallum*>) |
| 2 | max(intervallum, <*minimális intervallum->)* | min(2 * intervallum, <*maximális időköz*>) |
| 3 | max(2 * intervallum, <*minimális intervallum*>) | min(4 * intervallum, <*maximális intervallum*>) |
| 4 | max(4 * intervallum, <*minimális időtartam->)* | min(8 * intervallum, <*maximális időköz*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>A hibák elfogása és kezelése a "futtatás után" viselkedés módosításával

Amikor műveleteket ad hozzá a Logic App Designer, implicit módon deklarálja a műveletek futtatásához használandó sorrendet. A művelet futásának befejezése után a művelet állapota `Succeeded` `Failed`például , , , `Skipped`vagy `TimedOut`. A `runAfter` tulajdonság minden műveletdefinícióban meghatározza azt a megelőző műveletet, amelynek először be kell fejeződnie, valamint az adott megelőző művelet futtatásához engedélyezett állapotokat. Alapértelmezés szerint a tervezőben hozzáadott művelet csak akkor fut, `Succeeded` ha a megelőző állapota befejeződik.

Amikor egy művelet kezeletlen hibát vagy kivételt okoz, a művelet meg van jelölve, `Failed`és minden követő művelet meg van jelölve. `Skipped` Ha ez a viselkedés történik egy művelet, amely párhuzamos ágak, a Logic Apps motor követi a többi ágai azok befejezésének állapotának meghatározásához. Ha például egy ág `Skipped` művelettel végződik, az ág befejezési állapota a kihagyott művelet elődállapotán alapul. A logikai alkalmazás futása befejezése után a motor az összes elágazási állapot kiértékelésével határozza meg a teljes futtatás állapotát. Ha bármelyik ág sikertelenül végződik, a `Failed`teljes logikai alkalmazás futtatása meg van jelölve.

![Példák a futtatási állapotok kiértékelésére](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Annak érdekében, hogy egy művelet az elődje állapota ellenére is futtatható legyen, [testreszabhatja a művelet "futtatás után" viselkedését](#customize-run-after) az előd sikertelen állapotainak kezeléséhez.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>A "futtatás után" viselkedés testreszabása

Testre szabhatja a művelet "futtatás után" viselkedését, így a művelet `Succeeded`akkor `Failed` `Skipped`fut, amikor a megelőző állapota , , , `TimedOut`vagy ezen állapotok bármelyike. Ha például az Excel Online `Add_a_row_into_a_table` elődműveletének `Failed`megjelölése után szeretne e-mailt küldeni, a helyett `Succeeded`módosítsa a "futtatás után" viselkedést a következő lépéssel:

* A tervező nézetben jelölje ki a három pontot (**...**) gombot, majd válassza **a Futtatás beállítása után**lehetőséget.

  !["Futtatás után" viselkedés konfigurálása művelethez](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A műveletalakzat a megelőző művelethez szükséges alapértelmezett állapotot jeleníti meg, amely ebben a példában **egy sor hozzáadása egy táblához:**

  ![Művelet alapértelmezett "futtatásután" viselkedése](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Módosítsa a "futtatás után" viselkedést a kívánt állapotra, ami ebben a példában **nem sikerült:**

  ![Módosítsa a "futtatás után" viselkedést "nem sikerült"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Annak megadásához, hogy a művelet akkor `Failed` `Skipped` futjon, hogy a megelőző művelet meg legyen-e jelölve a ban, vagy `TimedOut`jelölje ki a többi állapotot:

  ![A "futtatás után" viselkedés módosítása más állapotra](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Kódnézetben a művelet JSON-definíciójában szerkesztheti `runAfter` a tulajdonságot, amely ezt a szintaxist követi:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Ebben a példában `runAfter` módosítsa `Succeeded` `Failed`a tulajdonságot a következőre:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Annak megadása, hogy a művelet akkor `Failed`futjon, hogy a megelőző művelet meg legyen-e jelölve , `Skipped` vagy `TimedOut`adja hozzá a többi állapotot:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Műveletek értékelése hatókörökkel és azok eredményeivel

A tulajdonsággal végrehajtott egyéni `runAfter` műveletek utáni lépésekhez hasonlóan a műveleteket is csoportosíthatja egy [hatókörön](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)belül. A hatóköröket akkor használhatja, ha logikailag csoportosítani szeretné a műveleteket, fel szeretné mérni a hatókör összesített állapotát, és az adott állapot alapján műveleteket hajthat végre. Miután a hatókör összes művelete befejeződik, maga a hatókör saját állapotot kap.

A hatókör állapotának ellenőrzéséhez ugyanazokat a feltételeket használhatja, amelyek segítségével ellenőrizheti `Succeeded`a `Failed`logikai alkalmazás futási állapotát, például a , és így tovább.

Alapértelmezés szerint, ha a hatókör összes művelete sikeres, `Succeeded`a hatókör állapota meg van jelölve. Ha a hatókör ben szereplő `Failed` `Aborted`végső művelet a vagy `Failed`vagy, a hatókör állapota van megjelölve.

Ha kivételeket szeretne `Failed` egy hatókörben, és olyan műveleteket `runAfter` szeretne `Failed` futtatni, amelyek kezelik ezeket a hibákat, használhatja a tulajdonságot az adott hatókörhöz. Így ha a hatókör *ben lévő* műveletek sikertelenek, és a tulajdonságot használja a `runAfter` hatókörhöz, létrehozhat egyetlen műveletet a hibák elfogásához.

A hatókörök korlátait lásd: [Korlátok és konfiguráció.](../logic-apps/logic-apps-limits-and-config.md)

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Környezet és hibák eredményeinek beírása

Bár a hibák befogása egy hatókörből hasznos, érdemes környezetben is, hogy segítsen megérteni, hogy pontosan mely műveletek sikertelen, valamint a hibák vagy állapotkódok, amelyek visszaadott.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) függvény kontextust biztosít a hatókör összes műveletének eredményeiről. A `result()` függvény egyetlen paramétert fogad el, amely a hatókör neve, és egy tömböt ad vissza, amely az adott hatókörön belül ről származó összes műveleteredményt tartalmazza. Ezek a műveletobjektumok ugyanazokat `actions()` az attribútumokat tartalmazzák, mint az objektum, például a művelet kezdési időpontja, befejezési időpontja, állapota, bemenetei, korrelációs azonosítók és kimenetek. Ha a hatókörön belül sikertelen műveletek környezetét szeretné elküldeni, egyszerűen párosíthat egy `@result()` kifejezést a `runAfter` tulajdonsággal.

`Failed` Ha egy adott hatókörminden egyes műveletéhez futtatni szeretne egy műveletet, és az eredmények tömbjét `@result()` a sikertelen műveletekig szeretné szűrni, párosíthat egy kifejezést egy [**szűrőtömb**](logic-apps-perform-data-operations.md#filter-array-action) művelettel és egy [**minden ciklushoz.**](../logic-apps/logic-apps-control-flow-loops.md) A szűrt eredménytömböt végrehajthatja, és minden `For_each` egyes hiba esetén végrehajthatja a műveletet a ciklus használatával.

Íme egy példa, amelyet egy részletes magyarázat követ, amely http POST-kérelmet küld a választörzsnek minden olyan műveletre vonatkozóan, amely nem sikerült a "My_Scope" hatókörön belül:

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

Az alábbiakban részletes forgatókönyvként olvashat, amely leírja, hogy mi történik ebben a példában:

1. A "My_Scope" összes műveletének eredményének lekért, a **Tömbszűrő** művelet ezt a szűrőkifejezést használja:`@result('My_Scope')`

1. A **Tömb szűrőfeltétele** minden olyan `@result()` elem, `Failed`amelynek állapota megegyezik a. Ez a feltétel szűri a tömböt, amely az összes művelet eredményeit a "My_Scope" le egy tömb, csak a sikertelen művelet eredményeit.

1. Hurkonyművelet `For_each` végrehajtása a *szűrt* tömbkimeneteken. Ez a lépés minden korábban szűrt sikertelen művelet eredményéhez műveletet hajt végre.

   Ha a hatókör egyetlen művelete sikertelen, `For_each` a ciklusban lévő műveletek csak egyszer futnak. Több sikertelen művelet hibaenként egy műveletet okoz.

1. HTTP-bejegyzés küldése `For_each` az elemválasz törzsén, amely a `@item()['outputs']['body']` kifejezés.

   Az `@result()` elemalakzat megegyezik `@actions()` az alakzattal, és ugyanúgy elemezhető.

1. Két egyéni fejlécet is tartalmaz a`@item()['name']`sikertelen műveletnévvel ( )`@item()['clientTrackingId']`és a sikertelen futtatási ügyfélkövetési azonosítóval ( ).

Referenciaként az itt látható példa `@result()` egyetlen elemre, `name` `body`amely `clientTrackingId` a , a , és az előző példában elemzött tulajdonságokat mutatja. A `For_each` műveleten `@result()` kívül ezeknek az objektumoknak egy tömbjét adja vissza.

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

Különböző kivételkezelési minták végrehajtásához használhatja a cikkben korábban ismertetett kifejezéseket. Dönthet úgy, hogy egyetlen kivételkezelési műveletet hajt végre a hatókörön kívül, amely `For_each` elfogadja a hibák teljes szűrt tömbjét, és eltávolítja a műveletet. A `\@result()` válaszból a korábban leírt egyéb hasznos tulajdonságokat is megadhat.

## <a name="set-up-azure-monitor-logs"></a>Az Azure Monitor-naplók beállítása

A korábbi minták nagyszerű módja annak, hogy a hibák és kivételek egy futtatáson belül, de a futtatástól független hibák azonosítására és válaszolására is kiválóan használható. [Az Azure Monitor](../azure-monitor/overview.md) segítségével egyszerűen elküldheti az összes munkafolyamat-eseményt, beleértve az összes futtatási és műveletállapotot is, egy [Log Analytics-munkaterületre](../azure-monitor/platform/data-platform-logs.md), [Az Azure storage-fiókba](../storage/blobs/storage-blobs-overview.md)vagy [az Azure Event Hubs-ba.](../event-hubs/event-hubs-about.md)

A futtatási állapotok kiértékeléséhez figyelheti a naplókat és a metrikákat, vagy közzéteheti őket bármely figyelési eszköz, amely szeretné. Az egyik lehetséges lehetőség az, hogy az összes eseményt az Event Hubs-on keresztül továbbítja az [Azure Stream Analytics szolgáltatásba.](https://azure.microsoft.com/services/stream-analytics/) A Stream Analytics, írhat élő lekérdezések alapján bármilyen anomáliák, átlagok, vagy a diagnosztikai naplók hibái. A Stream Analytics segítségével információkat küldhet más adatforrásokba, például várólistákba, témakörökbe, SQL-be, Azure Cosmos DB-be vagy Power BI-ba.

## <a name="next-steps"></a>További lépések

* [Tekintse meg, hogyan építi fel az ügyfél a hibakezelést az Azure Logic Apps alkalmazásokkal](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps-példák és forgatókönyvek keresése](../logic-apps/logic-apps-examples-and-scenarios.md)
