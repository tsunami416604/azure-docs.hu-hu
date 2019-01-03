---
title: A Stretch Database – Azure transzparens adattitkosítás engedélyezése |} A Microsoft Docs
description: Az SQL Server Stretch Database az Azure-ban transzparens adattitkosításának (TDE) engedélyezése
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: 1e40e3d9eb1231666acda89c752ebc8f517e8fc6
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741538"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Transzparens adattitkosítás (TDE) engedélyezze a Stretch Database az Azure-ban
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transzparens adattitkosítás (TDE) segítségével elleni védelemhez kártevő szándékú tevékenységek valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív elvégzésével az alkalmazás módosítása nélkül.

TDE titkosítja a tárolót a teljes adatbázisra az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. Az adatbázis-titkosítási kulcs egy beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány egy egyedülálló megoldás minden egyes Azure-kiszolgáló. A Microsoft legalább 90 naponként automatikusan elforgatja ezeket a tanúsítványokat. TDE általános ismertetését lásd: [Transzparens adattitkosítás (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
TDE engedélyezése az Azure-adatbázis, amely tárolja az adatokat át a Stretch engedélyezve van az SQL Server-adatbázisból, tegye a következőket:

1. Nyissa meg az adatbázishoz a [Azure Portalon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **transzparens adattitkosítás** lehetőség ![][1]
4. Válassza ki a **a** beállítást, és válassza ki **mentése**
   ![][2]

## <a name="disabling-encryption"></a>Letiltja a titkosítást
TDE letiltása az Azure-adatbázis, amely tárolja az adatokat át a Stretch engedélyezve van az SQL Server-adatbázisból, tegye a következőket:

1. Nyissa meg az adatbázishoz a [Azure Portalon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **transzparens adattitkosítás** lehetőség
4. Válassza ki a **ki** beállítást, és válassza ki **mentése**

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
