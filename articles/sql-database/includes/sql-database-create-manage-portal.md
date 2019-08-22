---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/09/2019
ms.author: sstein
ms.openlocfilehash: f7b385f359311217595352be31ba0687ca87bf40
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873720"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure SQL-erőforrások létrehozása és kezelése a Azure Portal

A Azure Portal egyetlen oldalt biztosít, ahol kezelheti az [összes Azure SQL-erőforrást,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) beleértve az SQL-alapú virtuális gépeket is.

Az **Azure SQL-erőforrások** oldal eléréséhez válassza az **Azure sql** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe.

> [!NOTE]
> Az **Azure SQL** gyors és egyszerű módot biztosít az SQL-adatbázisok, a rugalmas készletek, az adatbázis-kiszolgálók, az SQL-felügyelt példányok és az SQL-alapú virtuális gépek eléréséhez. Az Azure SQL nem szolgáltatás vagy erőforrás. 

A meglévő erőforrások kezeléséhez válassza ki a kívánt elemet a listában. Új Azure SQL-erőforrások létrehozásához válassza a **+ Hozzáadás**lehetőséget. 

![Azure SQL-portál lap](../media/sql-database-technical-overview/azure-sql.png)

A **+ Hozzáadás gombra**kattintva további információkat jeleníthet meg a különböző beállításokról a csempék **részleteinek megjelenítése** lehetőség kiválasztásával.

![adatbázisok csempe részletei](../media/sql-database-technical-overview/create-single-database.png)

Részletes információ:

- [Önálló adatbázis létrehozása](../sql-database-single-database-get-started.md)
- [Rugalmas készlet létrehozása](../sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Felügyelt példány létrehozása](../sql-database-managed-instance-get-started.md)
- [SQL-alapú virtuális gép létrehozása](../../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)