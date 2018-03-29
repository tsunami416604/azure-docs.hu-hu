---
title: Hozzáférést kiszolgálónaplókban Azure-adatbázis MySQL az Azure parancssori felület használatával
description: A cikkből megtudhatja, hogyan férhet hozzá a kiszolgálói naplók az Azure Database MySQL az Azure CLI parancssori segédprogram segítségével.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 037f631b3b23e66f1ed54106cc5be14a809b2e22
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban Azure parancssori felület használatával
Az Azure-adatbázishoz a MySQL-kiszolgáló naplók Azure CLI-vel, az Azure parancssori segédprogram töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [MySQL-kiszolgálóhoz tartozó Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [Azure CLI 2.0](/cli/azure/install-azure-cli) vagy Azure Cloud rendszerhéj a böngészőben

## <a name="configure-logging-for-azure-database-for-mysql"></a>MySQL az Azure-adatbázis naplózásának konfigurálása
Az alábbi lépések megtételével a MySQL lassú lekérdezés napló elérésére a kiszolgálót úgy is konfigurálhatja:
1. Naplózás bekapcsolása úgy, hogy a **lassú\_lekérdezés\_napló** paraméter ON értékre állítása.
2. Más paraméterek, például beállítása **hosszú\_lekérdezés\_idő** és **napló\_lassú\_admin\_utasítások**.

Azure parancssori felületen keresztül e paraméterek értékének beállítására, lásd: [server paraméterek konfigurálása](howto-configure-server-parameters-using-cli.md). 

Például a következő parancssori parancsot a lassú lekérdezés napló bekapcsolása állítja be a hosszú lekérdezési idő 10 másodperc és majd kikapcsolja a naplózást, a lassú admin utasítás. Végezetül felsorolja a konfigurációs beállításokat a jóváhagyásra.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista naplók az Azure Database MySQL-kiszolgáló
A kiszolgáló elérhető naplófájlokat sorolják fel, futtassa a [az mysql-naplói lista](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) parancsot.

A kiszolgáló a naplófájlokban listázhatja **mydemoserver.mysql.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A listában, a naplófájlok nevű szövegfájlba majd közvetlen **napló\_fájlok\_lista.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az a [az mysql server-naplók letöltése](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) parancs, a kiszolgáló letöltheti a külön naplófájlba. 

Az alábbi példát követve töltse le a kiszolgáló az adott naplófájlban **mydemoserver.mysql.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezet.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- További tudnivalók [-kiszolgáló naplóit Azure-adatbázis MySQL](concepts-server-logs.md).
