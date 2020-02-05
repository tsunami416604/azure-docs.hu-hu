---
title: Azure-beli megerősített diagnosztikai naplók engedélyezése és használata
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és dolgozhat az Azure Bastion diagnosztikai naplóival.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989450"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>A megerősített diagnosztikai naplók engedélyezése és használata

Ahogy a felhasználók az Azure Bastion használatával csatlakoznak a munkaterhelésekhez, a Bastion naplózhatja a távoli munkamenetek diagnosztikát. Ezután a diagnosztika használatával megtekintheti, hogy mely felhasználók kapcsolódtak a munkaterhelésekhez, hogy milyen időpontban, hol és más hasonló naplózási információkkal. A diagnosztika használatához engedélyeznie kell a diagnosztikai naplókat az Azure Bastion-ben. Ennek a cikknek a segítségével engedélyezheti a diagnosztikai naplókat, majd megtekintheti a naplókat.

## <a name="enable"></a>Diagnosztikai napló engedélyezése

1. A [Azure Portal](https://portal.azure.com)navigáljon az Azure Bastion-erőforráshoz, és válassza a **diagnosztikai beállítások** lehetőséget az Azure Bastion oldalon.

   ![diagnosztikai beállítások](./media/diagnostic-logs/1diagnostics-settings.png)
2. Válassza a **diagnosztikai beállítások**, majd a **+ diagnosztikai beállítás hozzáadása** elemet a naplók célhelyének hozzáadásához.

   ![diagnosztikai beállítás hozzáadása](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. A **diagnosztikai beállítások** lapon válassza ki a diagnosztikai naplók tárolására szolgáló Storage-fiók típusát.

   ![tárolási hely kiválasztása](./media/diagnostic-logs/3add-storage-account.png)
4. A beállítások elvégzése után az alábbi példához hasonlóan fog kinézni:

   ![Példa beállításai](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>Diagnosztikai napló megtekintése

A diagnosztikai naplók eléréséhez közvetlenül használhatja azt a Storage-fiókot, amelyet a diagnosztika beállításainak engedélyezésekor adott meg.

1. Keresse meg a Storage-fiók erőforrását, majd a **tárolók**lehetőséget. Megjelenik a Storage-fiók blob-tárolójában létrehozott elemzések **– naplók – bastionauditlogs** blob.

   ![diagnosztikai beállítások](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Ahogy a tárolón belül navigál, a blogban különféle mappák jelennek meg. Ezek a mappák az Azure-beli megerősített erőforráshoz tartozó erőforrás-hierarchiát jelölik.

   ![diagnosztikai beállítás hozzáadása](./media/diagnostic-logs/2-resource-h.png)
3. Navigáljon az Azure-beli megerősített erőforrás teljes hierarchiához, amelynek diagnosztikai naplóit szeretné elérni/megtekinteni. A diagnosztikai naplók esetében az "y =", a "=", a ", a" h = "és a" = "érték jelzi az évet, hónapot, napot, órát és percet.

   ![tárolási hely kiválasztása](./media/diagnostic-logs/3-resource-location.png)
4. Keresse meg az Azure Bastion által létrehozott JSON-fájlt, amely a diagnosztikai napló adatait tartalmazza az adott időszakra vonatkozóan.

5. Töltse le a JSON-fájlt a Storage blob-tárolóból. A JSON-fájlból például a következő hivatkozás látható:

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

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
