---
title: Transzparens adattitkosítás engedélyezése a nyújtási adatbázishoz
description: Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli SQL Server stretch adatbázishoz
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034017"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Átlátszó adattitkosítás (TDE) engedélyezése az Azure-beli stretch-adatbázishoz
> [!div class="op_single_selector"]
> * [Azure-portál](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Az átlátszó adattitkosítás (TDE) az adatbázis, a kapcsolódó biztonsági mentések és az inaktív tranzakciós naplófájlok valós idejű titkosításával és visszafejtésével az alkalmazás módosítása nélkül nyújt védelmet a rosszindulatú tevékenységek fenyegetése ellen.

A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával. Az adatbázis-titkosítási kulcsot beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden Egyes Azure-kiszolgálóesetében egyedi. A Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. A TDE általános leírását az Átlátszó adattitkosítás (TDE) című témakörben [tetszetős en]olvassa el.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Ha engedélyezni szeretné a TDE-t egy olyan Azure-adatbázishoz, amely a rugalmasan kezelhető SQL Server-adatbázisból áttelepített adatokat tárolja, tegye a következőket:

1. Az adatbázis megnyitása az [Azure Portalon](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra
3. Válassza az **Adattitkosítás átlátszóvá tétele** lehetőséget![][1]
4. Válassza a **Be** beállítást, majd a **Mentés** lehetőséget.
   ![][2]

## <a name="disabling-encryption"></a>Titkosítás letiltása
Ha le szeretné tiltani a TDE-t egy olyan Azure-adatbázisesetében, amely a rugalmasan engedélyezett SQL Server-adatbázisból áttelepített adatokat tárolja, tegye a következőket:

1. Az adatbázis megnyitása az [Azure Portalon](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra
3. Válassza az **Adattitkosítás átlátszóvá tétele** lehetőséget
4. Válassza a **Ki** beállítást, majd a **Mentés** gombot.

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
