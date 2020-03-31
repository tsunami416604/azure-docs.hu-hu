---
title: Kiszolgáló újraindítása - Azure CLI - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL-kiszolgáló az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063505"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Indítsa újra a MySQL-kiszolgáló Azure-adatbázisát az Azure CLI használatával
Ez a témakör azt ismerteti, hogyan indíthatja újra az Azure Database for MySQL-kiszolgálót. Előfordulhat, hogy karbantartási okokból újra kell indítania a kiszolgálót, ami rövid leállást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Például a szolgáltatás lehet, hogy egy korábban kért műveletet dolgoz fel, például a virtuális magok méretezése.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamatátattól függ. Az újraindítási idő csökkentése érdekében azt javasoljuk, hogy minimalizálja a kiszolgálón az újraindítás előtt előforduló tevékenység mennyiségét.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MySQL-kiszolgálóhoz](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját használja. A verzió megerősítéséhez írja be az Azure `az --version`CLI parancssorába a . A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.


## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

További információ [a paraméterek beállításáról az Azure Database for MySQL-ben](howto-configure-server-parameters-using-cli.md)