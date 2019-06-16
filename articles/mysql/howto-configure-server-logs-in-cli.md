---
title: Lassú lekérdezések hozzáférési naplók az Azure Database MySQL-hez készült Azure CLI-vel
description: Ez a cikk ismerteti, hogyan lehet elérni a lassú lekérdezések naplóinak, az Azure Database MySQL-hez az Azure parancssori felület használatával.
author: andrela
ms.author: ajlam
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/219
ms.openlocfilehash: 740dbce579fba6347b1a7f2cfc6bcae40d3503ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052707"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurálja és a lassú lekérdezések naplóinak elérése az Azure CLI-vel
Azure CLI, az Azure parancssori segédprogram használatával az Azure Database for MySQL lassú lekérdezések naplóinak töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [Azure CLI-vel](/cli/azure/install-azure-cli) vagy az Azure Cloud Shellt a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgálóhoz a MySQL lassú lekérdezések naplója a következő lépések végrehajtásával konfigurálhatja:
1. Lassú lekérdezések naplózásának bekapcsolása beállításával a **lassú\_lekérdezés\_log** paraméter ON értékre állítása.
2. Állítsa be például a többi paraméter **hosszú\_lekérdezés\_idő** és **log\_lassú\_rendszergazdai\_utasítások**.

Ezeket a paramétereket, az Azure CLI-n keresztül értékének beállításával kapcsolatban lásd: [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-using-cli.md).

Például a következő CLI-parancsot a lassú lekérdezések naplója bekapcsolja, állítja be a hosszú lekérdezés idő 10 másodperc és majd kikapcsolja a naplózást, a lassú rendszergazdai utasítás. Végül a tekintse át a konfigurációs beállítások listáját.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista naplók az Azure Database for MySQL-kiszolgáló
A kiszolgáló elérhető a lassú lekérdezések naplófájlok felsorolása, futtassa a [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) parancsot.

A naplófájlokban található kiszolgáló listázhatja **mydemoserver.mysql.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A lista a naplófájlok nevű szövegfájlba majd közvetlen **log\_fájlok\_lista.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az a [az mysql server-naplók letöltése](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) parancsot, az egyes naplófájlok töltheti le a kiszolgáló számára. 

Az alábbi példát követve töltse le a kiszolgáló a megadott naplófájlt **mydemoserver.mysql.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezetben.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- Ismerje meg [lassú lekérdezések naplók az Azure Database for MySQL-hez](concepts-server-logs.md).
