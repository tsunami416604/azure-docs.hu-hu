---
title: A kapcsolati karakterlánc beszerzése az Azure Portalról
description: A kapcsolati karakterlánc beszerzése az Azure Portalról
keywords: sql kapcsolat,kapcsolati karakterlánc
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179199"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>A kapcsolati karakterlánc beszerzése az Azure Portalról
Az [Azure Portal](https://portal.azure.com/) használatával szerezze be a kapcsolati karakterláncot, amely szükséges az ügyfélprogram az Azure SQL Database-rel való együttműködéshez.

1. Válassza az **Összes szolgáltatás** > **SQL-adatbázisa lehetőséget.**

2. Írja be az adatbázis nevét az **SQL-adatbázisok** panel bal felső részén lévő szűrőszövegmezőbe.

3. Válassza ki az adatbázis sorát.

4. Miután a panel megjelenik az adatbázisban, a vizuális kényelem érdekében válassza a **Kis méret** gombokat a böngészéshez és az adatbázis-szűréshez használt panelek összecsukásához.

5. Az adatbázis paneljén válassza az **Adatbázis-kapcsolati karakterláncok megjelenítése**lehetőséget.

6. Másolja a megfelelő kapcsolati karakterláncot. Azaz ha a ADO.NET kapcsolattárat kívánja használni, másolja a megfelelő karakterláncot a **ADO.NET** lapról.

    ![Az adatbázis ADO kapcsolati karakterláncának másolása][20-CopyAdoConnectionString]

7. Szükség szerint szerkesztsd a kapcsolati karakterláncot. azaz szúrja be a jelszót a kapcsolati&lt;karakterláncba, vagy távolítsa el a "@ kiszolgálónév&gt;" szót a felhasználónévből, ha a felhasználónév vagy a kiszolgáló neve túl hosszú.

8. Az egyik vagy másik formátumban illessze be a kapcsolati karakterlánc adatait az ügyfél programkódjába.

További információt a [Kapcsolati karakterláncok és konfigurációs fájlok című témakörben talál.](https://msdn.microsoft.com/library/ms254494.aspx)

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
