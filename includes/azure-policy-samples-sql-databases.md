---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318243"
---
### <a name="sql-databases"></a>SQL Database-adatbázisok

|  |  |
|---------|---------|
| [Engedélyezett termékváltozatok SQL-adatbázisokhoz](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Ehhez az SQL-adatbázisoknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozat-azonosítókat tartalmazó tömböt vagy egy engedélyezett termékváltozat-neveket tartalmazó tömböt. |
| [Adatbázisszintű fenyegetésészlelési beállítás naplózása](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL Database-adatbázis titkosításának naplózása](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás. |
| [SQL-adatbázisszintű naplózási beállítás naplózása](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Naplózza az SQL-adatbázis naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie.  |