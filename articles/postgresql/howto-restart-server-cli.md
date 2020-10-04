---
title: Kiszolgáló újraindítása – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-egy kiszolgálót az Azure CLI használatával
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 30770dd7ed71f4cde68c1a616a256bf60b5acaef
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704718"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL újraindítása – egyetlen kiszolgáló az Azure CLI használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.
 
Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában adja meg a következőt: `az --version` . Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

Útmutató [Paraméterek beállításához a Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
