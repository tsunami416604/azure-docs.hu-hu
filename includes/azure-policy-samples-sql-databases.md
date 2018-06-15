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
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664607"
---
### <a name="sql-databases"></a>SQL Database-adatbázisok

|  |  |
|---------|---------|
| [Engedélyezett termékváltozatok SQL-adatbázisokhoz](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Ehhez az SQL-adatbázisoknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozat-azonosítókat tartalmazó tömböt vagy egy engedélyezett termékváltozat-neveket tartalmazó tömböt. |
| [Adatbázisszintű fenyegetésészlelési beállítás naplózása](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL Database-adatbázis titkosításának naplózása](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás. |
| [SQL-adatbázisszintű naplózási beállítás naplózása](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Naplózza az SQL-adatbázis naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [Transzparens adattitkosítás állapotának naplózása](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Naplózza, ha az SQL-adatbázis transzparens adattitkosítása nincs engedélyezve.  |