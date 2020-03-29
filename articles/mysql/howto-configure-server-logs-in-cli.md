---
title: Lassú lekérdezési naplók elérése - Azure CLI - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan érheti el a lassú lekérdezési naplók az Azure Database for MySQL az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062482"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Az Azure Database for MySQL lassú lekérdezési naplók az Azure CLI, az Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure Database for MySQL szerver](quickstart-create-mysql-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy az Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgáló taszthatja be, hogy hozzáférjen a MySQL lassú lekérdezési naplójához az alábbi lépésekkel:
1. A lassú lekérdezésnaplózás bekapcsolásával kapcsolja be a **lassú\_lekérdezési\_napló** paramétert BE-re.
2. Módosítsa az egyéb paramétereket, például a **hosszú\_\_lekérdezési időt** és a lassú **\_\_rendszergazdai\_utasítások naplózását.**

A paraméterek azure CLI-n keresztüli értékének beállításáról a [Kiszolgálóparaméterkonfigurálása](howto-configure-server-parameters-using-cli.md)című témakörből megtudhatja.

A következő CLI parancs például bekapcsolja a lassú lekérdezési naplót, a hosszú lekérdezési időt 10 másodpercre állítja be, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az ellenőrzés konfigurációs beállításait.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Az Azure Database for MySQL-kiszolgáló naplóinak listázása
A kiszolgálóhoz rendelkezésre álló lassú lekérdezési naplófájlok listázásához futtassa az [az mysql server-logs list parancsot.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

A kiszolgáló naplófájljait **mydemoserver.mysql.database.azure.com** a **myresourcegroup**erőforráscsoport alatt listázhatja. Ezután irányítsa a naplófájlok listáját egy **list.txt nevű\_szövegfájlba.\_**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az [az mysql szerver-naplók letöltési](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) parancs, akkor töltse le az egyes naplófájlokat a szerver. 

A következő példában letöltheti a kiszolgáló **naplófájlját, mydemoserver.mysql.database.azure.com** a **myresourcegroup** erőforráscsoport erőforráscsoport alatt a helyi környezetbe.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- Ismerje meg a [lassú lekérdezési naplókat az Azure Database for MySQL szolgáltatásban.](concepts-server-logs.md)
