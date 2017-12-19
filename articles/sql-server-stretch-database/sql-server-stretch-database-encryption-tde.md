---
title: "Átlátható adattitkosítás a Stretch Database – Azure engedélyezése |} Microsoft Docs"
description: "Az SQL Server a Stretch Database az Azure-on átlátható adattitkosítás (TDE) engedélyezése"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
ms.openlocfilehash: ceb355d2ba872ed5d3886c6dc82ca75b1854db0a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>A Stretch Database az Azure-on átlátható adattitkosítás (TDE) engedélyezése
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transzparens Data Encryption (TDE) abban a kártékony tevékenység fenyegetés valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi elvégzésével anélkül, hogy az alkalmazást módosítani kellene.

TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. Az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítvány egyedi minden Azure-kiszolgálóval rendelkezik. Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. TDE általános ismertetését lásd: [átlátszó Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
TDE engedélyezése az Azure adatbázist, az adattárolás átemelt Stretch-kompatibilis SQL Server-adatbázisok, tegye a következőket:

1. Nyissa meg az adatbázishoz a [Azure-portálon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **átlátható adattitkosítás** beállítás![][1]
4. Válassza ki a **a** beállításával, és válassza ki **mentése**
   ![][2]

## <a name="disabling-encryption"></a>Titkosításának letiltása
TDE letiltása az Azure adatbázist, az adattárolás átemelt Stretch-kompatibilis SQL Server-adatbázisok, tegye a következőket:

1. Nyissa meg az adatbázishoz a [Azure-portálon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **átlátható adattitkosítás** beállítás
4. Válassza ki a **ki** beállításával, és válassza ki **mentése**

<!--Anchors-->
[átlátszó Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
