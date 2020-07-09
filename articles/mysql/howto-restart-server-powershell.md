---
title: Kiszolgáló újraindítása – Azure PowerShell – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL kiszolgálót a PowerShell használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.openlocfilehash: faefdfb93257521c9c687045c1467c5d2d0f84ab
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107937"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Azure Database for MySQL kiszolgáló újraindítása a PowerShell-lel

Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL-kiszolgálót. Előfordulhat, hogy a kiszolgáló karbantartás miatt újra kell indítania a kiszolgálót, ami rövid kimaradást okoz a művelet során.

A kiszolgáló újraindítása le van tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MySql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Amint az az. MySql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-mysql-server-database-using-azure-powershell.md)
