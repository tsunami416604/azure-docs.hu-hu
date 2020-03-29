---
title: Mi az Azure Scheduler?
description: Ütemezés létrehozása és automatizált feladatok futtatása, amelyek az Azure-on belül vagy kívül is szolgáltatásokat hívnak
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898537"
---
# <a name="what-is-azure-scheduler"></a>Mi az Azure Scheduler?

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Az [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) segítségével a műveletek deklaratív leírásával felhőben futó [feladatokat](../scheduler/scheduler-concepts-terms.md) hozhat létre. A szolgáltatás ezt követően ezeknek a műveleteknek az ütemezését és futtatását automatikusan végzi el. Például szolgáltatásokat hívhat az Azure-ban és azon kívül, mint például a HTTP- vagy HTTPS-végpontok hívása, és küldhet is üzeneteket az Azure Storage-üzenetsorokba és az Azure Service Bus-üzenetsorokba vagy -témakörökbe. Futtathatja a feladatokat azonnal vagy egy későbbi időpontban. A Schedulerrel egyszerűen hozhat létre [komplex és speciális ismétlődő ütemezéseket](../scheduler/scheduler-advanced-complexity.md). A Scheduler meghatározza, mikor futtathat feladatokat, megőrzi a feladatok eredményeinek előzményeit, amelyeket megtekinthet, majd előre jelezhetően és megbízhatóan ütemezi a számítási feladatokat a futtatáshoz.

Más Azure-ütemezési szolgáltatások, például az [Azure WebJobs](../app-service/webjobs-create.md), amely az Azure App Service [Web Apps](https://azure.microsoft.com/services/app-service/web/) szolgáltatása, szintén használják a Schedulert a háttérben. Ezeka műveletek kommunikációja a [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)használatával kezelhető, amely segít a műveletek kommunikációjának kezelésében.

Az alábbiakban néhány forgatókönyv következik, ahol a Scheduler a segítségére lehet:

* Ismétlődő alkalmazásműveletek futtatása: például rendszeres időközönként adatok gyűjtése a Twitterből egy hírcsatornába.

* Napi karbantartás végrehajtása: például a naplók törlése naponta, biztonsági másolatok készítése és egyéb karbantartási feladatok.

  Rendszergazdaként például érdemes lehet biztonsági másolatot készíteni az adatbázisról minden nap 1:00 órakor a következő kilenc hónapban.

Bár a Schedulerrel létrehozhat, kezelhet, karban tarthat, ütemezhet és futtathat számítási feladatokat, a Scheduler nem üzemelteti a számítási feladatokat, és nem futtatja a kódot. A szolgáltatás csupán a máshol, például az Azure-ban, helyileg vagy egy másik szolgáltatónál üzemeltetett szolgáltatást vagy kódot *hívja meg*. A Scheduler meghívható a következőkön keresztül: HTTP, HTTPS, Storage-üzenetsor, Service Bus-üzenetsor vagy Service Bus-témakör.

Feladatok és [feladatgyűjtemények](../scheduler/scheduler-concepts-terms.md)létrehozásához, ütemezéséhez, kezeléséhez, frissítéséhez és törléséhez használhatja a kódot, a [Scheduler REST API-t](https://docs.microsoft.com/rest/api/scheduler/)vagy az [Azure Scheduler PowerShell-parancsmagokat.](scheduler-powershell-reference.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Csomagok és számlázás az Azure Scheduler számára](scheduler-plans-billing.md)
* [Összetett ütemezések és speciális ismétlődések létrehozása az Azure Scheduler rel](scheduler-advanced-complexity.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
