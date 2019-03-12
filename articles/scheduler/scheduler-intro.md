---
title: Mi az Azure Scheduler? | Microsoft Docs
description: Elsajátíthatja, hogyan hozhat létre, ütemezhet és futtathat automatizált feladatokat, amelyek szolgáltatásokat hívnak meg az Azure-ban vagy azon kívül
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 21204e85adf1c68264ea448360c9e1120567ef3f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764574"
---
# <a name="what-is-azure-scheduler"></a>Mi az Azure Scheduler?

> [!IMPORTANT]
> A kivezetésre kerülő Azure Scheduler helyébe az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lép. Feladatok ütemezéséhez [próbálja ki inkább az Azure Logic Apps szolgáltatást](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Az [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) segítségével a műveletek deklaratív leírásával felhőben futó [feladatokat](../scheduler/scheduler-concepts-terms.md) hozhat létre. A szolgáltatás ezt követően ezeknek a műveleteknek az ütemezését és futtatását automatikusan végzi el. Például szolgáltatásokat hívhat az Azure-ban és azon kívül, mint például a HTTP- vagy HTTPS-végpontok hívása, és küldhet is üzeneteket az Azure Storage-üzenetsorokba és az Azure Service Bus-üzenetsorokba vagy -témakörökbe. Futtathatja a feladatokat azonnal vagy egy későbbi időpontban. A Schedulerrel egyszerűen hozhat létre [komplex és speciális ismétlődő ütemezéseket](../scheduler/scheduler-advanced-complexity.md). A Scheduler meghatározza, mikor futtathat feladatokat, megőrzi a feladatok eredményeinek előzményeit, amelyeket megtekinthet, majd előre jelezhetően és megbízhatóan ütemezi a számítási feladatokat a futtatáshoz.

Bár a Schedulerrel létrehozhat, kezelhet, karban tarthat, ütemezhet és futtathat számítási feladatokat, a Scheduler nem üzemelteti a számítási feladatokat, és nem futtatja a kódot. A szolgáltatás csupán a máshol, például az Azure-ban, helyileg vagy egy másik szolgáltatónál üzemeltetett szolgáltatást vagy kódot *hívja meg*. A Scheduler meghívható a következőkön keresztül: HTTP, HTTPS, Storage-üzenetsor, Service Bus-üzenetsor vagy Service Bus-témakör. Feladatok létrehozására, kezelésére és ütemezésére használható az [Azure Portal](../scheduler/scheduler-get-started-portal.md), kód, a [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/) vagy [Azure Scheduler PowerShell-parancsmaghivatkozás](scheduler-powershell-reference.md). Programozott módon létrehozhat, megtekinthet, frissíthet, kezelhet vagy törölhet például feladatokat és [feladatgyűjteményeket](../scheduler/scheduler-concepts-terms.md) parancsfájlok használatával és az Azure Portalon.

Más Azure-ütemezési szolgáltatások, például az [Azure WebJobs](../app-service/webjobs-create.md), amely az Azure App Service [Web Apps](https://azure.microsoft.com/services/app-service/web/) szolgáltatása, szintén használják a Schedulert a háttérben. Az ezekkel a műveletekkel való kommunikációt a [Scheduler REST API-val](https://docs.microsoft.com/rest/api/scheduler/) kezelheti. megkönnyíti a műveletekkel való kommunikációt.

Az alábbiakban néhány forgatókönyv következik, ahol a Scheduler a segítségére lehet:

* **Ismétlődő app-műveletek futtatása**: Ha például rendszeres időközönként adatokat gyűjteni Twitter hírcsatorna.

* **Napi karbantartás**: Például a naplók naponta csatlakozási, biztonsági másolatok és egyéb karbantartási feladatok végrehajtása. 

  Rendszergazdaként például érdemes lehet biztonsági másolatot készíteni az adatbázisról minden nap 1:00 órakor a következő kilenc hónapban.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Scheduler szolgáltatással az Azure Portalon](scheduler-get-started-portal.md)
* Az [Azure Scheduler csomagjaira és számlázására](scheduler-plans-billing.md) vonatkozó információk
* [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)