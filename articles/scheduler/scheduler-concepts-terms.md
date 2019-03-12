---
title: Alapfogalmak, terminológia és entitások – Azure Scheduler  Microsoft Docs
description: Elsajátíthatja az Azure Scheduler alapfogalmait, entitáshierarchiáját és terminológiáját, beleértve a feladatokat és a feladatgyűjteményeket
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: a58b247732125574a067deff1d5b03859cd036fc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782292"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Az Azure Scheduler alapfogalmai, terminológiája és entitásai

> [!IMPORTANT]
> A kivezetésre kerülő Azure Scheduler helyébe az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lép. Feladatok ütemezéséhez [próbálja ki inkább az Azure Logic Apps szolgáltatást](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Entitáshierarchia

Az Azure Scheduler REST API az alábbi fő entitásokat (más néven erőforrásokat) használja:

| Entitás | Leírás |
|--------|-------------|
| **Feladat** | Egyedi, ismétlődő műveletet definiál, egyszerű vagy összetett végrehajtási stratégiákkal. A műveletek HTTP-hez, Storage-üzenetsorhoz, Service Bus-üzenetsorhoz vagy Service Bushoz kapcsolódó témakörkéréseket tartalmazhatnak. | 
| **Feladatgyűjtemény** | Feladatok egy csoportját tartalmazza, valamint a gyűjteményben lévő feladatok által közösen használt beállításokat, kvótákat és szabályozásokat tartja karban. Azure-előfizetés tulajdonosaként létrehozhat feladatgyűjteményeket és csoportfeladatokat használat vagy alkalmazáshatárok alapján. A feladatgyűjtemények a következő attribútumokkal rendelkeznek: <p>– Egyetlen régióra vannak korlátozva. <br>– Lehetővé teszik a kvóták kényszerítését, így a gyűjtemények összes feladatának használatát korlátozhatja. <br>– Példák a kvótákra: MaxJobs, MaxRecurrence. | 
| **Feladatelőzmények** | A feladat-végrehajtás részleteit ismerteti, például az állapotot és a válasz részleteit. |
||| 

## <a name="entity-management"></a>Entitáskezelés

A magasabb szinteken a Scheduler REST API ezeket a műveleteket teszi elérhetővé az entitáskezeléshez.

### <a name="job-management"></a>Feladatkezelés

Feladatok létrehozására és szerkesztésére szolgáló műveleteket támogat. Az összes feladatnak egy létező feladatgyűjteményhez kell tartoznia, így nem történhet implicit létrehozás. További információ: [Scheduler REST API – Feladatok](https://docs.microsoft.com/rest/api/scheduler/jobs). Ezen műveletek URI-címe:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`

### <a name="job-collection-management"></a>A feladatgyűjtemény kezelése

Feladatok és feladatgyűjtemények létrehozására és szerkesztésére szolgáló műveleteket támogat, amelyek kvótákra és megosztott beállításokra végeznek leképezéseket. Például a kvóták szabják meg a feladatok maximális számát és legkisebb ismétlődési időközt. További információ: [Scheduler REST API –- Feladatgyűjtemények](https://docs.microsoft.com/rest/api/scheduler/jobcollections). Ezen műveletek URI-címe:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`

### <a name="job-history-management"></a>Feladatelőzmények kezelése

A 60 napos feladat-végrehajtási előzménytörténetet lekérő GET műveletet támogatja, például a végrehajtás során eltelt időt és annak eredményeit is. Az állapot szerinti szűrés érdekében támogatja a lekérdezési sztringek paramétereit. További információ: [Scheduler REST API – Feladatok – Feladatelőzmények listázása](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory). A művelet URI-címe:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`

## <a name="job-types"></a>Feladattípusok

Az Azure Scheduler több feladattípust támogat: 

* HTTP-feladatok, így az SSL-t támogató HTTPS-feladatok is, amelyekre egy meglévő szolgáltatás vagy alkalmazás végpontjához lehet szükség
* Storage-üzenetsorbeli feladatok a tárolási üzenetsorokat használó alkalmazásokhoz és szolgáltatásokhoz, például a tárolási üzenetsorokba történő üzenetküldés
* Service Bus-üzenetsorok feladatai olyan alkalmazásokhoz és szolgáltatásokhoz, amelyek Service Bus-üzenetsorokat használnak
* Service Bus-témafeladatok olyan alkalmazásokhoz és szolgáltatásokhoz, amelyek Service Bus-témafeladatokat használnak

## <a name="job-definition"></a>Feladat definíciója

Magasabb szinteken a Scheduler-feladatok az alábbi alapszintű összetevőkből állnak:

* A feladat időzítőjének indításakor futó művelet
* Nem kötelező: Az idő a feladat futtatása
* Nem kötelező: Mikor és hogyan a feladat megismétlésének
* Nem kötelező: Egy hiba műveletet, amely akkor fut, ha az elsődleges művelet sikertelen

A feladat olyan, a rendszer által biztosított adatokat is tartalmaz, mint a következő ütemezett futás időpontja. A feladat kóddefiníciója egy JavaScript Object Notation (JSON) formátumú objektum, amely a következő elemeket tartalmazza:

| Elem | Szükséges | Leírás | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Nem | A feladat kezdési ideje időzóna-eltolódással [ISO 8601 formátumban](http://en.wikipedia.org/wiki/ISO_8601) | 
| [**action**](#action) | Igen | Az elsődleges művelet részletei, amelyek **errorAction** objektumot tartalmazhatnak | 
| [**errorAction**](#error-action) | Nem | Az elsődleges művelet meghiúsulása esetén futó másodlagos művelet részletei |
| [**recurrence**](#recurrence) | Nem | Egy ismétlődő feladat részletei, például a gyakoriság és az időköz | 
| [**retryPolicy**](#retry-policy) | Nem | A művelet újrapróbálására vonatkozó szabályok | 
| [**state**](#state) | Igen | A feladat aktuális állapotának részletei |
| [**status**](#status) | Igen | A feladat jelenlegi állapotának részletei, amelyeket a szolgáltatás vezérel |
||||

Íme, egy példa egy HTTP-művelet átfogó feladatdefiníciójára, amelyhez a későbbi szakaszok részletesebb elemleírásokat is tartalmaznak: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

A **startTime** objektumban megadhatja a kezdési időpontot és az időzóna-eltolódást [ISO 8601 formátumban](http://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>action

A Scheduler-feladat egy elsődleges **action** műveletet futtat a megadott ütemezés szerint. A Scheduler a HTTP-, a Storage-üzenetsor-, a Service Bus üzenetsor- és a Service Bus-alapú témakörműveleteket támogatja. Ha az elsődleges **action** művelet meghiúsul, a Scheduler egy másodlagos [**errorAction**](#errorAction) műveletet futtathat, amely kezeli a hibát. Az **action** objektum a következő elemeket ismerteti:

* A művelet szolgáltatástípusa
* A művelet adatai
* Egy alternatív **errorAction** művelet

Az előző példa egy HTTP-műveletet ismertet. Íme, egy példa egy tárolási üzenetsorbeli műveletre:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Íme, egy példa egy Service Bus-üzenetsorbeli műveletre:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Íme, egy példa egy Service Bus-beli témakörműveletre:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

További információ a közös hozzáférésű jogosultságkódok (SAS) tokenjeiről: [Engedélyezés a közös hozzáférésű jogosultságkódokkal](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Ha a feladat elsődleges **action** művelete meghiúsul, a Scheduler egy **errorAction** műveletet futtathat, amely kezeli a hibát. Az elsődleges **action** műveletben megadhat egy **errorAction** objektumot, a Scheduler így meghívhat egy hibakezelő végpontot, vagy felhasználói értesítést küldhet. 

Ha például katasztrófa történik az elsődleges végpontnál, az **errorAction** művelettel meghívhat egy másodlagos végpontot, vagy értesíthet egy hibakezelő végpontot. 

Az elsődleges **action** művelethez hasonlóan a hibakezelési művelet is lehet egyszerű vagy összetett (más műveleteken alapuló) logikájú. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Egy feladat akkor ismétlődik, ha annak JSON-definíciója tartalmazza a **recurrence** objektumot, például:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Tulajdonság | Szükséges | Value | Leírás | 
|----------|----------|-------|-------------| 
| **frequency** | Igen, a **recurrence** használatakor | Percenként, óránként, naponta, hetente, havonta, évente | Az előfordulások közötti időegység | 
| **interval** | Nem | 1 és 1000 között, a szélsőértékeket is beleértve | Pozitív egész szám, amely a **frequency** gyakoriságérték alapján meghatározza az egyes előfordulások közötti időegységek számát | 
| **schedule** | Nem | Változó | Összetettebb és speciális ütemezések részletei. Lásd: **hours**, **minutes**, **weekDays**, **months** és **monthDays** (órák, percek, munkanapok, hónapok és hónap adott napjai) | 
| **hours** | Nem | 1–24 | A feladat futtatásának időpontját meghatározó órajelek | 
| **minutes** | Nem | 1–24 | A feladat futtatásának időpontját meghatározó percjelek | 
| **months** | Nem | 1–12 | A feladat futtatásának időpontját meghatározó hónapok | 
| **monthDays** | Nem | Változó | A feladat futtatásának időpontját meghatározó hónap napjai | 
| **weekDays** | Nem | „Monday”, „Tuesday”, „Wednesday”, „Thursday”, „Friday”, „Saturday”, „Sunday” (Hétfő, Kedd, Szerda, Csütörtök, Péntek, Szombat, Vasárnap) | A feladat futtatásának időpontját meghatározó hét napjai | 
| **count** | Nem | <*none*> | Az ismétlődések száma. Az alapértelmezett beállítás a végtelen ismétlődés. Nem használhatja egyszerre a **count** és az **endTime** elemeket, így ilyen esetekben mindig az elsőként lefutó szabály érvényesül. | 
| **endTime** | Nem | <*none*> | Az ismétlődés befejezésének dátuma és ideje. Az alapértelmezett beállítás a végtelen ismétlődés. Nem használhatja egyszerre a **count** és az **endTime** elemeket, így ilyen esetekben mindig az elsőként lefutó szabály érvényesül. | 
||||

További információ az elemekről: [Komplex ütemezések és speciális ismétlődések létrehozása](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Arra az esetre, ha a Scheduler-feladat hibába ütközik, beállíthat egy újrapróbálkozási szabályzatot, amely megszabja, hogy a Scheduler újra elindítsa-e a műveletet, és ha igen, hogyan. Alapértelmezés szerint a Scheduler négyszer próbálkozik újra, 30 másodperces időközönként. Ezt a szabályzatot szigoríthatja és lazíthatja is, ez a szabályzat például napi kétszer próbálkozik újra egy művelettel:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Tulajdonság | Szükséges | Value | Leírás | 
|----------|----------|-------|-------------| 
| **retryType** | Igen | **Fixed**, **None** | Azt határozza meg, hogy megad-e egy újrapróbálkozási szabályzatot (**fixed** – rögzített) vagy sem (**none** – nincs). | 
| **retryInterval** | Nem | PT30S | Megadja az újrapróbálkozások gyakoriságát [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). A minimális érték 15 másodperc, a maximális érték pedig 18 hónap. | 
| **retryCount** | Nem | 4 | Megszabja az újrapróbálkozási kísérletek számát. A maximális érték 20. | 
||||

További információ: [Magas rendelkezésre állás és megbízhatóság](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

A feladatok állapota a következő lehet: **Enabled** (Engedélyezve), **Disabled** (Letiltva), **Completed** (Befejezve) vagy **Faulted** (Hibás), például: 

`"state": "Disabled"`

Ha **Enabled** vagy **Disabled** állapotra szeretne módosítani egy feladatot, használja a PUT vagy a PATCH műveletet.
Azonban ha egy feladat **Completed** vagy **Faulted** állapotú, azt nem frissítheti, habár a DELETE műveletet így is elvégezheti rajta. A Scheduler 60 nap után törli a befejeződött vagy hibás feladatokat. 

<a name="status"></a>

## <a name="status"></a>status

Miután elindul egy feladat, a Scheduler megjeleníti annak állapotadatait a **status** objektumban, amelyet csak a Scheduler vezérelhet. A **status** objektumot azonban megtalálhatja a **job** objektumban. A feladatok állapota a következő információkat tartalmazza:

* Az előző végrehajtás ideje (ha volt ilyen)
* Folyamatban lévő feladatok esetén a következő ütemezett végrehajtás ideje
* A feladat-végrehajtások száma
* A meghiúsulások száma (ha volt ilyen)
* A hibák száma (ha volt ilyen)

Példa:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Alapfogalmak, terminológia és entitáshierarchia](scheduler-concepts-terms.md)
* [Komplex ütemezések és speciális ismétlődések létrehozása](scheduler-advanced-complexity.md)
* [Korlátok, kvóták, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
