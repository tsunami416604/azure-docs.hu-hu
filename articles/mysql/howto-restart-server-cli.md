---
title: Indítsa újra az Azure Database for MySQL-kiszolgálóhoz az Azure CLI használatával
description: Ez a cikk bemutatja, hogyan újraindítható egy Azure Database for MySQL-kiszolgálóhoz az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623153"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Indítsa újra az Azure Database for MySQL-kiszolgálóhoz az Azure CLI használatával
Ez a témakör ismerteti, hogyan újraindítható egy Azure Database for MySQL-kiszolgálóhoz. Indítsa újra a kiszolgálót, a kiszolgáló a műveletet hajt végre egy rövid kimaradás okozó karbantartási okokból szükségessé.

A kiszolgáló újraindítására, ha a szolgáltatás nem foglalt le lesz tiltva. Ha például a szolgáltatás feldolgozás lehet egy korábban kért művelet, például a virtuális magok méretezés.

Befejezéséhez újraindítás szükséges idő attól függ, hogy a MySQL-helyreállítási folyamatot. Az újraindítás idő csökkentéséhez, javasoljuk, hogy a kiszolgálón, az újraindítás előtt előforduló tevékenység minimalizálására.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MySQL-kiszolgáló](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató az Azure CLI 2.0-s vagy újabb verzió használatát igényli. Adja meg, hogy ellenőrizze a verziót, az Azure CLI-vel parancssorban `az --version`. Telepítéshez vagy frissítéshez olvassa [Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Indítsa újra a kiszolgálót

Indítsa újra a kiszolgálót, a következő paranccsal:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

Ismerje meg [paraméterek beállítása az Azure Database MySQL-hez](howto-configure-server-parameters-using-cli.md)