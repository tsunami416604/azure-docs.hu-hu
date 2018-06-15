---
title: fájl belefoglalása
description: fájl belefoglalása
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678276"
---
### <a name="set-up-pagination"></a>Tördelési beállítása

Az összekötők és a lépések, amelyek több elem beolvasása az eredményeket meghaladhatja az összekötő alapértelmezett oldal méretét. A művelet ebben az esetben az eredmények csak az első oldal adja vissza. Például az alapértelmezett lapmérete a **SQL Server - Get sorok** művelet 2048, de más beállítások alapján eltérőek lehetnek. Győződjön meg arról, hogy a rekordok kap, kapcsolja be a **tördelési** beállítása az adott művelethez. Ezzel a beállítással rendelkezik a Logic Apps alkalmazást kérje meg az összekötő a hátralévő rekordok, de a művelet befejezése után vissza az eredményeket egyetlen üzenetben. 

Az alábbiakban néhány összekötők, ahol a konkrét műveletek tördelési bekapcsolása: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

A példában a **sorok beolvasása** művelet:

1. Ha szeretné tudni, hogy támogatja-e a művelet tördelési, nyissa meg a művelet **beállítások**. 

   ![Nyissa meg a műveletet, a "Beállítások"](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Ha tördelési által támogatott műveletet, módosítsa a **tördelési** beállítása a **ki** való **a**. Győződjön meg arról, hogy a művelet eredménye minimális készletét adja vissza, adjon meg egy értéket **korlát**.

   ![Adja meg, hogy a művelet visszatérési eredmények minimális száma](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Ha elkészült, válassza ki a **végzett**.