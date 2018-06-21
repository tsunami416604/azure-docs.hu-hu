---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664595"
---
### <a name="sql-servers"></a>SQL Server-kiszolgálók

|  |  |
|---------|---------|
| [Azure Active Directory-rendszergazda hiányának naplózása](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Naplózza, ha nincs Azure Active Directory-rendszergazda hozzárendelve az SQL-kiszolgálóhoz. |
| [Kiszolgálószintű fenyegetésészlelési beállítás naplózása](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL-kiszolgáló naplózási beállításainak naplózása](../articles/azure-policy/scripts/sql-server-audit.md) | Az alapján naplózza az SQL-kiszolgálót, hogy engedélyezve vannak-e rajta a naplózási beállítások. |
| [SQL-kiszolgálói szintű naplózási beállítások naplózása](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Naplózza az SQL-kiszolgáló naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie. |
| [Az SQL Server 12.0-s verziójának megkövetelése](../articles/azure-policy/scripts/req-sql-12.md) | Megköveteli, hogy az SQL-kiszolgálók a 12.0-s verziót használják.  |