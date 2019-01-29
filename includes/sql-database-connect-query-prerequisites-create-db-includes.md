---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198061"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Az Azure SQL Database helyezett [logikai kiszolgáló](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) vagy [felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). A következő módszerekkel hozhat létre adatbázist:

| A logikai kiszolgáló | Felügyelt példány |
| --- | --- |
| [Portál](../articles/sql-database/sql-database-get-started-portal.md) | [Portál](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [Parancssori felület](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Csak a logikai kiszolgáló** – egy konfigurált kiszolgálószintű tűzfalszabályt, amely lehetővé teszi a logikai kiszolgálóhoz való csatlakozáshoz. További információkért lásd: [kiszolgálószintű tűzfalszabály létrehozása](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Felügyelt példány csak** -kapcsolat a számítógépről, amely a felügyelt példány fér hozzá beállítva. Használhatja a következő beállításokat:
  - [Az Azure virtuális gép](../articles/sql-database/sql-database-managed-instance-configure-vm.md) az azonos Azure virtuális hálózat, felügyelt példányt, amely hozzáférhet a példányt.
  - [Pont – hely kapcsolat](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) a számítógépen, amely lehetővé teszi, hogy a számítógép csatlakozik a virtuális hálózathoz, ahol a felügyelt példány kerül, és a felügyelt példány és minden más SQL Server, a hálózaton.
