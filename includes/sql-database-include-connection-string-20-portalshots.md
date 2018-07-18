---
title: A kapcsolati karakterlánc lekérését az Azure Portalról
description: A kapcsolati karakterlánc lekérését az Azure Portalról
keywords: SQL-kapcsolatot, a kapcsolati karakterlánc
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: dab7623c86bea4e562313e618f238b9b33c0fdc5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117290"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>A kapcsolati karakterlánc lekérését az Azure Portalról
Használja a [az Azure portal](https://portal.azure.com/) beszerzése a kapcsolati karakterláncot, amely az ügyfélprogram és az Azure SQL Database használatához szükség van.

1. Válassza ki **minden szolgáltatás** > **SQL-adatbázisok**.

2. Adja meg az adatbázis nevét a szűrő szövegmezőbe közel bal felső sarkában a **SQL-adatbázisok** panelen.

3. Válassza ki az adatbázis a sort.

4. Után a panel jelenik meg az adatbázis visual kényelmi válassza ki a **kis méret** gombok a paneleket tallózásával és szűrésével adatbázis használt összecsukásához.

5. Az adatbázis paneljén válassza ki a **adatbázis kapcsolati karakterláncainak megjelenítése**.

6. Másolja a megfelelő kapcsolati karakterláncot. vagyis ha szeretne használni az ADO.NET kapcsolati könyvtár, másolja a megfelelő karakterláncot, a **ADO.NET** fülre.

    ![Másolja a ADO kapcsolati karakterláncot az adatbázishoz][20-CopyAdoConnectionString]

7. Szükség szerint szerkessze a kapcsolati karakterláncot. például helyezze be a jelszó a kapcsolati karakterláncot, vagy távolítsa el "@&lt;servername&gt;" az a felhasználónév, ha a felhasználónév vagy a kiszolgáló neve túl hosszú.

8. Az vagy egy másik formátumba illessze be a kapcsolati sztring adatait az Ügyfélkód program.

További információkért lásd: [kapcsolati karakterláncok és a konfigurációs fájlok](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
