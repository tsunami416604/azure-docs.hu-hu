---
title: Kiszolgálói naplók az Azure Database for MariaDB Azure CLI-vel
description: Ez a cikk azt ismerteti, hogyan férhetnek hozzá a kiszolgálónaplók, Azure database for MariaDB, az Azure CLI parancssori segédprogram használatával.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9fff9f13e5ce6bf8a7805f7794d3e71eb4030104
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065685"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja és kiszolgálói naplók elérése az Azure CLI-vel
Az Azure Database for MariaDB naplók az Azure CLI, az Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- A [Azure CLI-vel](/cli/azure/install-azure-cli) vagy az Azure Cloud Shellt a böngészőben

## <a name="configure-logging-for-azure-database-for-mariadb"></a>MariaDB-hez készült Azure-adatbázis naplózásának konfigurálása
A kiszolgáló eléréséhez a MariaDB lassú lekérdezések naplója a következő lépések végrehajtásával konfigurálhatja:
1. Kapcsolja be a naplózást beállításával a **lassú\_lekérdezés\_log** paraméter ON értékre állítása.
2. Állítsa be például a többi paraméter **hosszú\_lekérdezés\_idő** és **log\_lassú\_rendszergazdai\_utasítások**.

Ezeket a paramétereket, az Azure CLI-n keresztül értékének beállításával kapcsolatban lásd: [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-cli.md).

Például a következő CLI-parancsot a lassú lekérdezések naplója bekapcsolja, állítja be a hosszú lekérdezés idő 10 másodperc és majd kikapcsolja a naplózást, a lassú rendszergazdai utasítás. Végül a tekintse át a konfigurációs beállítások listáját.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista naplók az Azure Database for MariaDB-kiszolgáló
A kiszolgáló elérhető a lassú lekérdezések naplófájlok felsorolása, futtassa a [az mariadb-server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) parancsot.

A naplófájlokban található kiszolgáló listázhatja **mydemoserver.mariadb.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A lista a naplófájlok nevű szövegfájlba majd közvetlen **log\_fájlok\_lista.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az a [az mariadb-naplók letöltése](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) parancsot, az egyes naplófájlok töltheti le a kiszolgáló számára.

Az alábbi példát követve töltse le a kiszolgáló a megadott naplófájlt **mydemoserver.mariadb.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezetben.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- Ismerje meg [lassú lekérdezések naplók az Azure Database for MariaDB](concepts-server-logs.md).
