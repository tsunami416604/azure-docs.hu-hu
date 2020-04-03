---
title: Az Azure Bastion diagnosztikai naplóinak engedélyezése és az okkal való kapcsolat
description: Ebben a cikkben megtudhatja, hogyan engedélyezheti és dolgozhat az Azure Bastion diagnosztikai naplók használatával.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619275"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Bástyadiagnosztikai naplók engedélyezése és az okkal való kapcsolat

Ahogy a felhasználók az Azure Bastion használatával csatlakoznak a számítási feladatokhoz, a Bastion naplózhatja a távoli munkamenetek diagnosztikáját. Ezután a diagnosztika segítségével megtekintheti, hogy mely felhasználók mely számítási feladatokhoz, mikor, honnan és más hasonló releváns naplózási információkhoz kapcsolódnak. A diagnosztika használatához engedélyeznie kell a diagnosztikai naplók az Azure Bastion. Ez a cikk segít a diagnosztikai naplók engedélyezésében, majd a naplók megtekintésében.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>A diagnosztikai napló engedélyezése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Azure-bastion erőforrást, és válassza **a Diagnosztikai beállítások** az Azure Bastion lapon.

   ![diagnosztikai beállítások](./media/diagnostic-logs/1diagnostics-settings.png)
2. Válassza **a Diagnosztikai beállítások lehetőséget,** majd a **+Diagnosztikai beállítás hozzáadása** lehetőséget a naplók hoz való cél hozzáadásához.

   ![diagnosztikai beállítás hozzáadása](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. A **Diagnosztikai beállítások** lapon válassza ki a diagnosztikai naplók tárolására használt tárfiók típusát.

   ![tároló helyének kiválasztása](./media/diagnostic-logs/3add-storage-account.png)
4. Amikor elvégzi a beállításokat, a következő példához hasonlóan fog kinézni:

   ![példa beállítások](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Diagnosztikai napló megtekintése

A diagnosztikai naplók eléréséhez közvetlenül használhatja a megadott tárfiókot, miközben engedélyezi a diagnosztikai beállításokat.

1. Keresse meg a tárfiók-erőforrást, majd a **Tárolók .** Láthatja az **insights-logs-bastionauditlogs** blob a tárfiók blob tárolójában létrehozott.

   ![diagnosztikai beállítások](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ahogy navigálasz a tárolóbelsejében, különböző mappákat látsz a blogodban. Ezek a mappák jelzik az Azure Bastion erőforrás erőforrás-hierarchiáját.

   ![diagnosztikai beállítás hozzáadása](./media/diagnostic-logs/2-resource-h.png)
3. Keresse meg az Azure Bastion erőforrás teljes hierarchiáját, amelynek diagnosztikai naplóit szeretné elérni/nézetbe. Az "y=,"m=", "d=", "h=" és "m=" a diagnosztikai naplók évét, hónapját, napját, óráját és percét jelöli.

   ![tároló helyének kiválasztása](./media/diagnostic-logs/3-resource-location.png)
4. Keresse meg az Azure Bastion által létrehozott json-fájlt, amely a diagnosztikai napló adatait tartalmazza a megkeresett időszakhoz.

5. Töltse le a jsonfájlt a storage blob tárolóból. A json-fájlból származó példabejegyzés az alábbiakban található:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK](bastion-faq.md).
