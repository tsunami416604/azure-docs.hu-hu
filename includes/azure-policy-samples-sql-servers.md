---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003735"
---
### <a name="sql-servers"></a>SQL Server-kiszolgálók

|  |  |
|---------|---------|
| [Azure Active Directory-rendszergazda hiányának naplózása](../articles/governance/policy/samples/audit-no-aad-admin.md) | Naplózza, ha nincs Azure Active Directory-rendszergazda hozzárendelve az SQL-kiszolgálóhoz. |
| [Kiszolgálószintű fenyegetésészlelési beállítás naplózása](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL-kiszolgáló naplózási beállításainak naplózása](../articles/governance/policy/samples/sql-server-audit.md) | Az alapján naplózza az SQL-kiszolgálót, hogy engedélyezve vannak-e rajta a naplózási beállítások. |
| [SQL-kiszolgálói szintű naplózási beállítások naplózása](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Naplózza az SQL-kiszolgáló naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie. |
| [Az SQL Server 12.0-s verziójának megkövetelése](../articles/governance/policy/samples/req-sql-12.md) | Megköveteli, hogy az SQL-kiszolgálók a 12.0-s verziót használják.  |