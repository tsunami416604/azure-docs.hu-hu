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
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318188"
---
### <a name="sql-servers"></a>SQL Server-kiszolgálók

|  |  |
|---------|---------|
| [Azure Active Directory-rendszergazda hiányának naplózása](../articles/governance/policy/samples/audit-no-aad-admin.md) | Naplózza, ha nincs Azure Active Directory-rendszergazda hozzárendelve az SQL-kiszolgálóhoz. |
| [Kiszolgálószintű fenyegetésészlelési beállítás naplózása](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL-kiszolgáló naplózási beállításainak naplózása](../articles/governance/policy/samples/sql-server-audit.md) | Az alapján naplózza az SQL-kiszolgálót, hogy engedélyezve vannak-e rajta a naplózási beállítások. |
| [SQL-kiszolgálói szintű naplózási beállítások naplózása](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Naplózza az SQL-kiszolgáló naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie. |
| [Az SQL Server 12.0-s verziójának megkövetelése](../articles/governance/policy/samples/require-sql-12.md) | Megköveteli, hogy az SQL-kiszolgálók a 12.0-s verziót használják.  |