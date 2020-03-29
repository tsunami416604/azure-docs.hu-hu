---
title: Kiszolgáló újraindítása - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ez a cikk azt ismerteti, hogyan indíthatja újra a PostgreSQL-alapú Azure-adatbázist – Egyetlen kiszolgáló az Azure CLI használatával
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13b26b545f9e95ce2457e4f8d9cf32da59cd91e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770152"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Az Azure Database for PostgreSQL újraindítása – Egyetlen kiszolgáló az Azure CLI használatával
Ez a témakör azt ismerteti, hogyan indíthatja újra az Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy karbantartási okokból újra kell indítania a kiszolgálót, ami rövid leállást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Például a szolgáltatás lehet, hogy egy korábban kért műveletet dolgoz fel, például a virtuális magok méretezése.
 
Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamatátattól függ. Az újraindítási idő csökkentése érdekében azt javasoljuk, hogy minimalizálja a kiszolgálón az újraindítás előtt előforduló tevékenység mennyiségét.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Egy Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját használja. A verzió megerősítéséhez írja be az Azure `az --version`CLI parancssorába a . A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.


## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

További információ [a paraméterek beállításáról az Azure Database for PostgreSQL-ben](howto-configure-server-parameters-using-cli.md)