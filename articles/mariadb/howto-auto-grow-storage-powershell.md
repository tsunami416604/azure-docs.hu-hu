---
title: Tárterület automatikus növekedése – Azure PowerShell – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a PowerShell használatával Azure Database for MariaDBban.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497103"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Tároló automatikus növekedése Azure Database for MariaDB-kiszolgálón a PowerShell használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló [elérje a tárolási korlátot](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit) , és csak olvasható legyen. A 100 GB vagy kevesebb kiépített tárterülettel rendelkező kiszolgálók esetében a méret 5 GB-kal nő, ha a szabad terület 10% alá esik. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a méret 5%-kal nő, ha a szabad terület 10 GB alatti. A maximális tárolási korlátok a [Azure Database for MariaDB díjszabási szintjeinek](/azure/mariadb/concepts-pricing-tiers#storage)tárolási szakaszában megadott módon érvényesek.

> [!IMPORTANT]
> Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

## <a name="enable-mariadb-server-storage-auto-grow"></a>A MariaDB-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növelésének engedélyezése a meglévő kiszolgálókon a következő paranccsal:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB a PowerShell használatával](howto-read-replicas-powershell.md).
