---
title: "Kötegelt események - Azure diagnosztikai naplózás engedélyezése |} Microsoft Docs"
description: "Jegyezze fel, és elemzi az Azure Batch-fiók erőforrásokhoz, mint a készletek és a feladatok diagnosztikai naplózási eseményeket."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Alkalmazásnapló-események diagnosztikai kipróbálási és kötegelt megoldások monitorozása

Csakúgy, mint a számos Azure-szolgáltatások, a Batch szolgáltatás alkalmazásnapló-események az egyes erőforrások az erőforrás élettartama során bocsát ki. Azure Batch diagnosztikai naplók eseményeket rögzíteni az erőforrásokhoz, mint a készletek és a feladatok engedélyezése, és ezután használja a naplók diagnosztikai kiértékelési és a figyelés. Események például a készlet létrehozása, a készlet törlése, a feladat elindítása, feladat elkészült és mások kötegelt diagnosztikai naplók szerepelnek.

> [!NOTE]
> Ez a cikk ismerteti a naplózási események kötegelt fiók erőforrások magukat, nem feladat, és feladatot, kimeneti adatokat. A kimeneti adatait a feladatok és a feladatok tárolása a részletekért lásd: [Azure Batch megőrizni a feladat- és kimeneti](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* [Azure Batch-fiók](batch-account-create-portal.md)
* [Azure Storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Megőrizni a kötegelt diagnosztikai naplók, létre kell hoznia egy Azure Storage-fiók Azure hol tárolja a naplókat. Ehhez a tárfiókhoz megadott amikor Ön [diagnosztikai naplózás engedélyezése](#enable-diagnostic-logging) a Batch-fiókhoz. A tárfiók, megadhatja, ha engedélyezi a naplógyűjtést megegyezik nem említett kapcsolt tárfiókra a [alkalmazáscsomagok](batch-application-packages.md) és [tevékenység kimeneti adatmegőrzési](batch-task-output.md) cikkeket.
  
  > [!WARNING]
  > Ön **felszámított** az Azure Storage-fiókban tárolt adatok. Ez magában foglalja a cikkben szereplő diagnosztikai naplókat. Ezt tartsa szem előtt tervezésekor a [adatmegőrzési jelentkezzen](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése
Diagnosztikai naplózás nincs engedélyezve alapértelmezés szerint a Batch-fiókhoz. Diagnosztikai naplózás minden Batch-fiók segítségével nyomon követni kívánt explicit módon engedélyeznie kell:

[Diagnosztikai naplók gyűjteménye engedélyezése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Azt javasoljuk, hogy olvassa el a teljes [áttekintés az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) cikk áttekintésével megértéséhez nem csak naplózási, de a napló kategóriák támogatja a különböző Azure-szolgáltatások engedélyezése. Azure Batch például támogatja egy napló kategóriában: **szolgáltatásnaplók**.

## <a name="service-logs"></a>Service naplóit
Azure Batch szolgáltatás naplók tartalmaz, például egy készletet vagy feladat kötegelt erőforrás élettartama során az Azure Batch szolgáltatás által kibocsátott eseményeket. Minden esemény kötegelt által kibocsátott a megadott tárfiók JSON formátumban tárolja. Például ez az a szervezet egy minta **készlet esemény létrehozása**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Minden esemény törzsében található a megadott Azure Storage-fiók egy .JSON kiterjesztésű fájlt. Ha azt szeretné, a naplók elérése közvetlen, Kezdésként tekintse át a [séma diagnosztikai naplók a tárfiókban lévő](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Szolgáltatás bejelentkezési események
A Batch szolgáltatás jelenleg a következő szolgáltatás bejelentkezési események bocsát ki. Ez a lista nem lehet teljes, mivel további események is hozzá vannak adva ez a cikk utolsó frissítése óta.

| **Szolgáltatás bejelentkezési események** |
| --- |
| [Alkalmazáskészlet létrehozása][pool_create] |
| [Készlet törlése indítása][pool_delete_start] |
| [Teljes készlet törlése][pool_delete_complete] |
| [Készlet átméretezési indítása][pool_resize_start] |
| [Teljes készlet átméretezése][pool_resize_complete] |
| [A feladat indítása][task_start] |
| [A feladat befejezése][task_complete] |
| [A feladat sikertelen][task_fail] |

## <a name="next-steps"></a>Következő lépések
Diagnosztikai naplóeseményeket tárolása egy Azure Storage-fiókot, mellett is adatfolyam formájában a Batch szolgáltatás bejelentkezési események egy [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), és küldje el a [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md).

* [Event hubs az Azure diagnosztikai naplók adatfolyam](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Adatfolyam-kötegelt diagnosztikai kiválóan méretezhető adatbefogadási szolgáltatás, az Event Hubs eseményeit. Az Event Hubs fogadására képes több millió esemény / másodperc, amely akkor átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató használatával.
* [Log Analytics használata az Azure diagnosztikai naplók elemzése](../log-analytics/log-analytics-azure-storage.md)
  
  A diagnosztikai naplókat küld Naplóelemzési, amelyen az Operations Management Suite (OMS) portálon elemezheti őket, vagy a Power bi-ban vagy az Excel elemzés céljából exportálhatja őket.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
