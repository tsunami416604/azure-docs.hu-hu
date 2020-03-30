---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272846"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Adattípus|Leírás|
|----|----|----|
|**AlertDisplayName**|Sztring|A riasztás megjelenítendő neve.|
|**Riasztástípusa**|Sztring|A riasztás típusa. Az azonos típusú riasztások értékének azonos értékűnek kell lennie. Ez a mező egy kulcsos karakterlánc, amely a riasztás típusát, és nem egy riasztási példányt jelöli. Az azonos észlelési logikából/analitikusból származó összes riasztási példánynak azonos értékkel kell rendelkeznie a riasztástípushoz.|
|**Feltört entitás**|Sztring|A riasztáshoz leginkább kapcsolódó erőforrás megjelenítendő neve.|
|**Leírás**|Sztring|A riasztás leírása.|
|**EndTimeUtc**|DateTime|A riasztásban szereplő utolsó esemény vagy tevékenység időpontja.  A mezőnek olyan karakterláncnak kell lennie, amely megfelel az ISO8601 formátumnak, beleértve az UTC időzóna adatait is.|
|**Entitások**|IEnumerable (IEntity)|A riasztáshoz kapcsolódó entitások listája. Ez a lista különböző típusú entitások keverékét képes tárolni. Az entitások típusa az Entitások szakaszban definiált típusok bármelyike lehet. Az alábbi listában nem szereplő entitások is elküldhetők, de nem garantált, hogy fel lesznek dolgozva (a riasztás nem fog meghiúsulni az új típusú entitások ellenőrzésén).|
|**Bővített tulajdonságok**|Szótár (karakterlánc,karakterlánc)|A szolgáltatók (tetsben) egyéni mezőket is tartalmazhatnak itt.|
|**Szándék**|Felsorolás|A gyilkos lánchoz kapcsolódó szándék a riasztás mögött. A támogatott értékek listáját és az Azure Security Center által támogatott leölési láncleképezések magyarázatait [a Szándékok című témakörben található.](../articles/security-center/alerts-reference.md#intentions)<br/>Ebben a mezőben több érték is lehet (vesszővel elválasztva).|
|**IsIncident (Véletlen)**|Logikai|Ez a mező határozza meg, hogy a riasztás egy incidens (több riasztás összetett csoportosítása) vagy egyetlen riasztás. A mező alapértelmezett értéke "hamis" (azaz egyetlen riasztás).|
|**FeldolgozásEndTime**|DateTime|Az az időpont, amikor a riasztás elérhető volt a figyelmeztető jelzést tároló eredeti termék végfelhasználója számára.|
|**Productname**|Sztring|A riasztást közzétő termék neve (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS és így tovább).|
|**Szervizeléslépései**|Lista<String>|Kézi műveletelemek a riasztás kiújítása érdekében.|
|**Erőforrás-azonosítók**|Lista (erőforrás-azonosítók)|A riasztás erőforrás-azonosítói, amelyek a riasztást a megfelelő termékexpozíciós csoportba (bérlő, munkaterület, előfizetés stb.) irányíthatják. Riasztásonként több különböző típusú azonosító is lehet.|
|**Súlyosság**|Felsorolás|A riasztás súlyossága a szolgáltató által jelentett. Lehetséges értékek: Információs, Alacsony, Közepes és Magas.|
|**StartTimeUtc**|DateTime|A riasztásban szereplő első esemény vagy tevékenység időpontja. A mezőnek olyan karakterláncnak kell lennie, amely megfelel az ISO8601 formátumnak, beleértve az UTC időzóna adatait is.|
|**Állapot**|Felsorolás|A riasztás életciklus-állapota.<br/>A támogatott állapotok a következők: Új, Megoldott, Elutasítva, Ismeretlen.<br/>A támogatott beállításoktól eltérő értéket megadva riasztás "Ismeretlen" állapotú.<br/>Egy olyan riasztás, amely nem ad meg értéket, az "Új" állapothoz van rendelve.|
|**SystemAlertId azonosító**|Sztring|A riasztásazonosító.|
|**TimeGenerated**|DateTime|A riasztásszolgáltató által generált riasztás időpontja. Ha a belső riasztásszolgáltatók nem jelentik, a termék választhatja azt az időt, amelyet a termék feldolgozásra kapott.  A mezőnek olyan karakterláncnak kell lennie, amely megfelel az ISO8601 formátumnak, beleértve az UTC időzóna adatait is.|
|**Szállítónév**|Sztring|A riasztást kivető szállító neve.|
|||
