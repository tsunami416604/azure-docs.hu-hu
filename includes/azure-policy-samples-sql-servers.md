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
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590851"
---
### <a name="sql-servers"></a>SQL Server-kiszolgálók

|  |  |
|---------|---------|
| [Azure Active Directory-rendszergazda hiányának naplózása](../articles/governance/policy/samples/audit-no-aad-admin.md) | Naplózza, ha nincs Azure Active Directory-rendszergazda hozzárendelve az SQL-kiszolgálóhoz. |
| [Kiszolgálószintű fenyegetésészlelési beállítás naplózása](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL-kiszolgáló naplózási beállításainak naplózása](../articles/governance/policy/samples/sql-server-audit.md) | Az alapján naplózza az SQL-kiszolgálót, hogy engedélyezve vannak-e rajta a naplózási beállítások. |
| [SQL-kiszolgálói szintű naplózási beállítások naplózása](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Naplózza az SQL-kiszolgáló naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie. |