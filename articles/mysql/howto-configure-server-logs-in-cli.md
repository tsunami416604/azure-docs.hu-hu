---
title: Lassú lekérdezési naplók elérése Azure Database for MySQL az Azure CLI használatával
description: Ez a cikk azt ismerteti, hogyan érheti el a lassú lekérdezési naplókat Azure Database for MySQL az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030634"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Az Azure CLI-vel, az Azure parancssori segédprogrammal töltheti le az Azure Database for MySQL lassú lekérdezési naplókat.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MySQL lassú lekérdezési naplóhoz:
1. A lassú lekérdezés-naplózás bekapcsolásához állítsa be a **lassú @ no__t-1query @ no__t-2log** PARAMÉTERt a következőre:.
2. Módosítsa a többi paramétert, például a **Long @ no__t-1query @ no__t-2time** és a **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Ha meg szeretné tudni, hogyan állíthatja be a paraméterek értékét az Azure CLI-n keresztül, tekintse meg a [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-using-cli.md)című témakört.

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, beállítja a hosszú lekérdezési időt 10 másodpercre, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az Áttekintés konfigurációs beállításait.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló naplófájljainak listázása
A kiszolgáló rendelkezésre álló lassú lekérdezési naplófájljainak listázásához futtassa az az [MySQL Server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját egy **log @ no__t-1files\_list.txt**nevű szövegfájlba.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az az [MySQL Server-logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) paranccsal letöltheti a kiszolgáló egyes naplófájljait. 

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.mysql.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- További információ [a Azure Database for MySQL lassú lekérdezési naplóiról](concepts-server-logs.md).
