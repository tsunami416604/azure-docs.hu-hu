---
title: A Azure Portal a kapcsolatok karakterláncának beszerzése
description: A Azure Portal a kapcsolatok karakterláncának beszerzése
keywords: SQL-kapcsolatok, kapcsolatok karakterlánca
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179199"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>A Azure Portal a kapcsolatok karakterláncának beszerzése
A [Azure Portal](https://portal.azure.com/) használatával szerezze be azt a kapcsolódási karakterláncot, amely szükséges ahhoz, hogy az ügyfélprogram működjön a Azure SQL Databaseval.

1. Válassza **a minden szolgáltatás** > **SQL-adatbázisok**lehetőséget.

2. Adja meg az adatbázis nevét az **SQL-adatbázisok** panel bal felső sarkában található szűrő szövegmezőbe.

3. Válassza ki az adatbázis sorát.

4. Miután a panel megjelenik az adatbázishoz, a vizuális kényelem beállításnál válassza a **minimalizálás** gombokat a tallózáshoz és az adatbázis-szűréshez használt pengék összecsukásához.

5. Az adatbázis paneljén válassza az adatbázis- **kapcsolati karakterláncok megjelenítése**lehetőséget.

6. Másolja a megfelelő kapcsolattípus-karakterláncot. Ha a ADO.NET-kapcsolódási függvénytárat szeretné használni, másolja a megfelelő karakterláncot a **ADO.net** lapról.

    ![Az adatbázis ADO-kapcsolatok karakterláncának másolása][20-CopyAdoConnectionString]

7. Szükség szerint szerkessze a kapcsolatok karakterláncát. például illessze be a jelszót a kapcsolatok karakterlánccá, vagy távolítsa el a&lt;"&gt;@ servername" nevet a felhasználónévből, ha a Felhasználónév vagy a kiszolgálónév túl hosszú.

8. Az egyik formátumban vagy egy másikban illessze be a kapcsolatok karakterláncának adatait az ügyfélalkalmazás kódjába.

További információ: a [kapcsolatok karakterláncai és konfigurációs fájljai](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
