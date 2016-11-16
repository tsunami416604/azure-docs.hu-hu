---
title: "A Scheduler alapfogalmai, terminológiája és entitásai | Microsoft Docs"
description: "Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája, beleértve a feladatokat és a feladatgyűjteményeket.  Egy ütemezett feladat átfogó példáját mutatja be."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8c0d2b57f7a998ae52a08a5ae0cbc57a0a77f002


---
# <a name="scheduler-concepts-terminology-entity-hierarchy"></a>A Scheduler alapfogalmai, entitáshierarchiája és terminológiája
## <a name="scheduler-entity-hierarchy"></a>A Scheduler entitáshierarchiája
A következő táblázat ismerteti a feladatütemezési API által közzétett vagy használt fő erőforrásokat:

| Erőforrás | Leírás |
| --- | --- |
| **Feladatgyűjtemény** |A feladatgyűjtemények feladatok csoportjait tartalmazzák, valamint a gyűjteményben lévő feladatok által közösen használt beállításokat, kvótákat és szabályozásokat tartják karban. A feladatgyűjteményeket az előfizetés tulajdonosa hozza létre, ahol a feladatokat használat vagy alkalmazáshatárok alapján csoportosítja. Ez egyetlen régióra van korlátozva. Lehetővé teszi a kvóták kényszerítését is, az adott gyűjteményben lévő összes feladat használatának korlátozásához. Példák a kvótákra: MaxJobs, MaxRecurrence. |
| **Feladat** |Egyedi, ismétlődő műveletet megadó feladat, egyszerű vagy összetett végrehajtási stratégiákkal. A műveletek HTTP-, tárolásisor-, Service Bus üzenetsor- vagy Service Bus témakörkéréseket tartalmazhatnak. |
| **Feladatelőzmények** |A feladatelőzmény egy feladat végrehajtásának részletes adatait jelenti. Megállapítható belőle a feladat végrehajtásának sikeressége vagy meghiúsulása, illetve bármely részletes válaszadat. |

## <a name="scheduler-entity-management"></a>A Scheduler entitáskezelése
Az Scheduler és a Szolgáltatásfelügyeleti API lényegében a következő műveleteket teszi elérhetővé az erőforrásokon:

| Képesség | Leírás és URI-cím |
| --- | --- |
| **A feladatgyűjtemény kezelése** |GET, PUT és DELETE kérések támogatása a feladatgyűjtemények, valamint a bennük lévő feladatok létrehozásához és módosításához. A feladatgyűjtemények kvótákra és megosztott beállításokra vonatkozó feladatok tárolói. Példák a később részletezendő kvótákra: feladatok maximális száma és legkisebb ismétlődési időköz. <p>PUT és DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Feladatkezelés** |GET, PUT, POST, PATCH és DELETE kérések támogatása a feladatok létrehozásához és módosításához. Az összes feladatnak egy már létező feladatgyűjteményhez kell tartoznia, így nem történhet implicit létrehozás. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Feladatelőzmények kezelése** |GET parancs támogatása a 60 napos feladat-végrehajtási előzménytörténet lekéréséhez, ide értve a végrehajtás során eltelt időt és annak eredményeit is. Az állapot szerinti szűrés érdekében támogatja a lekérdezési karakterláncok paramétereit. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Feladattípusok
Számos feladattípus létezik: HTTP-feladatok (beleértve az SSL-t támogató HTTPS-feladatokat), tárolásisor-feladatok, Service Bus üzenetsor-feladatok és Service Bus témakör-feladatok. A HTTP-feladatok remekül használhatók, ha egy meglévő számítási feladat vagy szolgáltatás egy végponttal rendelkezik. A tárolásisor-feladatok a tárolási sorokon történő üzenet-közzétételre használhatók, így e feladatok ideálisan alkalmazhatók tárolási sorokat használó számítási feladatok esetében. Ehhez hasonlóan a Service Bus feladatok olyan számítási feladatok esetében alkalmazhatók előnyösen, amelyek Service Bus-üzenetsorokat és -témaköröket használnak.

## <a name="the-job-entity-in-detail"></a>Részletes információk a „feladat” entitásról
Alapszinten egy ütemezett feladat számos részből áll:

* A feladat időzítőjének indításakor végrehajtandó művelet  
* (Nem kötelező) A feladat futtatásának időpontja  
* (Nem kötelező) A feladat megismétlésének időpontja és gyakorisága  
* (Nem kötelező) Az elsődleges művelet meghiúsulása esetén elinduló művelet  

Belső használatra az ütemezett feladatok olyan, a rendszer által biztosított adatokat is tartalmaznak, mint a következő ütemezett végrehajtás időpontja.

Az alábbi kód egy ütemezett feladat átfogó példáját mutatja be. Részletes információkat az ezt követő szakaszokban talál.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Ahogy az a fenti ütemezett mintafeladat esetében is látható, a feladatdefiníciók több részből állnak:

* Kezdési idő („startTime”)  
* Művelet („action”), amely hibaműveletet („errorAction”) is tartalmaz
* Ismétlődés („recurrence”)  
* Folyamatállapot („state”)  
* Feladatállapot („status”)  
* Újrapróbálkozási házirend („retryPolicy”)  

Vizsgáljuk meg részletesebben ezeket:

## <a name="starttime"></a>startTime
A „startTime” paraméter a kezdési időpont, és lehetőséget ad a hívó számára hálózati időzóna-eltolódás megadására [ISO-8601 formátumban](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action és errorAction
Az „action” a minden alkalommal meghívott művelet, és egy szolgáltatásmeghívási típust ír le. A művelet a megadott ütemezés szerint végrehajtandó elemet jelenti. A Scheduler támogatja a HTTP-, a tárolásisor-, a Service Bus üzenetsor- és a Service Bus témakörműveleteket.

A fenti példán egy HTTP-művelet látható. Az alábbiakban egy példát láthat egy tárolásisor-műveletre:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Az alábbiakban egy példát láthat egy a Service Bus témakör-műveletre:

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Az alábbiakban egy példát láthat egy Service Bus üzenetsor-műveletre:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

Az „errorAction” hibakezelő művelet az elsődleges művelet meghiúsulása esetén lesz meghívva. Ezen változót egy hibakezelési végpont meghívására vagy felhasználói értesítés küldésére használhatja. Ha az elsődleges végpont nem érhető el (például vészhelyzet esetén a végponti helyen), ez a másodlagos végpont elérésére vagy egy hibakezelési végpont értesítésére használható. Az elsődleges művelethez hasonlóan a hibakezelési művelet is lehet egyszerű vagy összetett (más műveleteken alapuló) logikájú. Az SAS-token létrehozását lásd: [Közös hozzáférésű jogosultságkód létrehozása és használata](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>recurrence
Az ismétlődés több részből áll:

* Gyakoriság: percenként, óránként, naponta, hetente, havonta, évente  
* Időköz: az ismétlődés adott gyakoriságának megfelelő időköz  
* Előírt ütemezés: ismétlődési értékként megadhat perceket, órákat, munkanapokat, hónapokat és a hónap adott napjait  
* Darabszám: Az előfordulások darabszáma  
* Befejezés időpontja: A megadott befejezési időt követően egyetlen feladat sem lesz végrehajtva  

Ismétlődő feladatról akkor beszélünk, ha ismétlődő objektummal rendelkezik a JSON-definíciójában. Ha a count (darabszám) és az endTime (befejezés időpontja) paraméter is meg van adva, az elsőként teljesülő befejezési szabály lesz figyelembe véve.

## <a name="state"></a>state
A feladat folyamatállapota négyféle érték egyikét veheti fel: engedélyezve, letiltva, befejezve vagy meghiúsult. A PUT és a PATCH feladatok engedélyezett vagy letiltott állapotúra frissíthetők. A befejeződött vagy meghiúsult feladatok végállapota nem frissíthető (bár a feladaton keresztül továbbra is törölhetők). Példa a State tulajdonságra:

        "state": "disabled", // enabled, disabled, completed, or faulted
A befejeződött vagy meghiúsult feladatok 60 nap után törlődnek.

## <a name="status"></a>status
Ha elindít egy feladatot a Schedulerben, a rendszer értesíteni fogja annak aktuális állapotáról. Ennek az objektumnak a beállítását a felhasználó nem, kizárólag a rendszer végezheti el. Ezt azonban egy külön hivatkozott erőforrás helyett a feladatobjektum tartalmazza, így a feladat állapota egyszerűen lekérhető.

A feladat állapota magában foglalja az előző végrehajtás időpontját (ha volt ilyen), a következő ütemezett végrehajtás időpontját (a folyamatban lévő feladatok esetében) és a feladat végrehajtásainak számát.

## <a name="retrypolicy"></a>retryPolicy
Ha egy Scheduler-feladat meghiúsul, újrapróbálkozási házirend adható meg a művelet újraindítására, illetve annak módjára vonatkozóan. Ezt a **retryType** objektum határozza meg – ha nem létezik újrapróbálkozási házirend, ennek értéke **none** (nincs), ahogy fent látható. Ha létezik újrapróbálkozási házirendet, állítsa **fixed** (rögzített) értékűre.

Egy újrapróbálkozási házirend beállításához a rendszer két további beállítás megadását kérheti: az újbóli próbálkozási időközt (**retryInterval**) és az újbóli próbálkozások számát (**retryCount**).

A **retryInterval** objektummal megadott újrapróbálkozási időköz az újbóli próbálkozások közötti időtartamot jelenti. Ennek alapértelmezett értéke 30 másodperc; minimum 15 másodperc, maximum 18 hónap állítható be. Az Ingyenes feladatok gyűjteményében szereplő feladatok minimális konfigurálható értéke 1 óra.  Ennek megadása ISO 8601 formátumban történik. Ehhez hasonlóan történik a **retryCount** objektummal megadott újbóli próbálkozási szám értékének megadása is; ez a megkísérelt újbóli próbálkozások száma. Ennek alapértelmezett értéke 4, és legfeljebb 20 lehet.\. A **retryInterval** és a **retryCount** egyike sem kötelező. Ha a **retryType** objektum beállítása **fixed** (rögzített), és explicit módon nem lett megadva érték, az alapértelmezett értékek lesznek érvényesek.

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)




<!--HONumber=Nov16_HO2-->


