---
title: Mi az Azure Scheduler?
description: Ütemterv létrehozása és az Azure-on belüli vagy kívüli szolgáltatásokat meghívó automatizált feladatok futtatása
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898537"
---
# <a name="what-is-azure-scheduler"></a>Mi az Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

Az [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) segítségével a műveletek deklaratív leírásával felhőben futó [feladatokat](../scheduler/scheduler-concepts-terms.md) hozhat létre. A szolgáltatás ezt követően ezeknek a műveleteknek az ütemezését és futtatását automatikusan végzi el. Például szolgáltatásokat hívhat az Azure-ban és azon kívül, mint például a HTTP- vagy HTTPS-végpontok hívása, és küldhet is üzeneteket az Azure Storage-üzenetsorokba és az Azure Service Bus-üzenetsorokba vagy -témakörökbe. Futtathatja a feladatokat azonnal vagy egy későbbi időpontban. A Schedulerrel egyszerűen hozhat létre [komplex és speciális ismétlődő ütemezéseket](../scheduler/scheduler-advanced-complexity.md). A Scheduler meghatározza, mikor futtathat feladatokat, megőrzi a feladatok eredményeinek előzményeit, amelyeket megtekinthet, majd előre jelezhetően és megbízhatóan ütemezi a számítási feladatokat a futtatáshoz.

Más Azure-ütemezési szolgáltatások, például az [Azure WebJobs](../app-service/webjobs-create.md), amely az Azure App Service [Web Apps](https://azure.microsoft.com/services/app-service/web/) szolgáltatása, szintén használják a Schedulert a háttérben. Ezekhez a műveletekhez a [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)segítségével kezelheti a kommunikációt, ami segít a műveletek kommunikációjának kezelésében.

Az alábbiakban néhány forgatókönyv következik, ahol a Scheduler a segítségére lehet:

* Ismétlődő alkalmazás-műveletek futtatása: például rendszeres időközönként adatokat gyűjthet a Twitterről egy hírcsatornába.

* Napi karbantartást hajthat végre: például a napi metszési naplókat, a biztonsági mentéseket és egyéb karbantartási feladatokat.

  Rendszergazdaként például érdemes lehet biztonsági másolatot készíteni az adatbázisról minden nap 1:00 órakor a következő kilenc hónapban.

Bár a Schedulerrel létrehozhat, kezelhet, karban tarthat, ütemezhet és futtathat számítási feladatokat, a Scheduler nem üzemelteti a számítási feladatokat, és nem futtatja a kódot. A szolgáltatás csupán a máshol, például az Azure-ban, helyileg vagy egy másik szolgáltatónál üzemeltetett szolgáltatást vagy kódot *hívja meg*. A Scheduler meghívható a következőkön keresztül: HTTP, HTTPS, Storage-üzenetsor, Service Bus-üzenetsor vagy Service Bus-témakör.

Feladatok és [webhelycsoportok](../scheduler/scheduler-concepts-terms.md)létrehozásához, ütemezéséhez, kezeléséhez, frissítéséhez vagy törléséhez használhat programkódot, a [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)vagy az [Azure Scheduler PowerShell-parancsmagokat](scheduler-powershell-reference.md).

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
* [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)
* [Összetett ütemezések és speciális ismétlődések létrehozása az Azure Scheduler szolgáltatással](scheduler-advanced-complexity.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
