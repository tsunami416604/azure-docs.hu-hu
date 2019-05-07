---
title: Indítsa újra az Azure Database for postgresql-hez - Azure CLI használatával egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan újraindíthatja az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure CLI használatával
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 0a7cd815724fcebd6311860576e620eb9273523b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068980"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Indítsa újra az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure CLI használatával
Ez a témakör ismerteti, hogyan újraindítható egy Azure Database for PostgreSQL-kiszolgálóhoz. Indítsa újra a kiszolgálót, a kiszolgáló a műveletet hajt végre egy rövid kimaradás okozó karbantartási okokból szükségessé.

A kiszolgáló újraindítására, ha a szolgáltatás nem foglalt le lesz tiltva. Ha például a szolgáltatás feldolgozás lehet egy korábban kért művelet, például a virtuális magok méretezés.
 
Befejezéséhez újraindítás szükséges idő attól függ, hogy a PostgreSQL-helyreállítási folyamatot. Az újraindítás idő csökkentéséhez, javasoljuk, hogy a kiszolgálón, az újraindítás előtt előforduló tevékenység minimalizálására.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató az Azure CLI 2.0-s vagy újabb verzió használatát igényli. Adja meg, hogy ellenőrizze a verziót, az Azure CLI-vel parancssorban `az --version`. Telepítéshez vagy frissítéshez olvassa [Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Indítsa újra a kiszolgálót

Indítsa újra a kiszolgálót, a következő paranccsal:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

Ismerje meg [hogyan paraméterek beállítása az Azure Database for postgresql-hez](howto-configure-server-parameters-using-cli.md)