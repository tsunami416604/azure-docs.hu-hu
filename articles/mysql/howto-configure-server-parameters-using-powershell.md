---
title: Kiszolgáló paramétereinek konfigurálása – Azure PowerShell-Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for MySQL a PowerShell használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 4/29/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb17c8631392972c429220b846720447be48cecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495811"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MySQL a PowerShell használatával

Egy Azure Database for MySQL kiszolgáló konfigurációs paramétereit a PowerShell használatával listázhatja, megjelenítheti és frissítheti. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MySql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Amint az az. MySql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása

A kiszolgáló összes módosítható paraméterének és értékének listázásához futtassa a `Get-AzMySqlConfiguration` parancsmagot.

Az alábbi példa felsorolja a kiszolgáló **mydemoserver** tartozó kiszolgálói konfigurációs paramétereket az erőforráscsoport **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Az egyes felsorolt paraméterek definícióját a MySQL-referenciával foglalkozó szakaszban találja a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)című részben.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek

Egy adott konfigurációs paraméter részleteinek megjelenítéséhez futtassa a `Get-AzMySqlConfiguration` parancsmagot, és adja meg a **Name** paramétert.

Ez a példa a **lassú \_ lekérdezési \_ napló** kiszolgálójának konfigurációs paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup**alatt.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása

Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja a `Update-AzMySqlConfiguration` parancsmagot.

A **lassú \_ lekérdezési \_ napló** kiszolgáló-konfigurációs paraméterének frissítése a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup**területen.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tároló automatikus növekedése Azure Database for MySQL-kiszolgálón a PowerShell használatával](howto-auto-grow-storage-powershell.md).
