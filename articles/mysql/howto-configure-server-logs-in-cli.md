---
title: Kiszolgálói naplók az Azure Database MySQL-hez készült Azure CLI-vel
description: Ez a cikk azt ismerteti, hogyan férhetnek hozzá a kiszolgálónaplók, Azure database for MySQL-hez, az Azure CLI parancssori segédprogram használatával.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423457"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja és kiszolgálói naplók elérése az Azure CLI-vel
Azure CLI, az Azure parancssori segédprogram használatával töltheti le az Azure Database for MySQL-kiszolgáló naplóit.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [Azure CLI 2.0](/cli/azure/install-azure-cli) vagy az Azure Cloud Shellt a böngészőben

## <a name="configure-logging-for-azure-database-for-mysql"></a>MySQL-hez készült Azure-adatbázis naplózásának konfigurálása
A kiszolgálóhoz a MySQL lassú lekérdezések naplója a következő lépések végrehajtásával konfigurálhatja:
1. Kapcsolja be a naplózást beállításával a **lassú\_lekérdezés\_log** paraméter ON értékre állítása.
2. Állítsa be például a többi paraméter **hosszú\_lekérdezés\_idő** és **log\_lassú\_rendszergazdai\_utasítások**.

Ezeket a paramétereket, az Azure CLI-n keresztül értékének beállításával kapcsolatban lásd: [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-using-cli.md). 

Például a következő CLI-parancsot a lassú lekérdezések naplója bekapcsolja, állítja be a hosszú lekérdezés idő 10 másodperc és majd kikapcsolja a naplózást, a lassú rendszergazdai utasítás. Végül a tekintse át a konfigurációs beállítások listáját.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista naplók az Azure Database for MySQL-kiszolgáló
A kiszolgáló elérhető naplófájlok felsorolása, futtassa a [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) parancsot.

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
- Ismerje meg [kiszolgálói naplók az Azure Database for MySQL](concepts-server-logs.md).
