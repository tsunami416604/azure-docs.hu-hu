---
title: "Hozzáférést kiszolgálónaplókban Azure adatbázis MySQL az Azure parancssori felület használatával"
description: "A cikkből megtudhatja, hogyan férhet hozzá a kiszolgálói naplók az Azure-adatbázisban a MySQL az Azure CLI parancssori segédprogram használatával."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8cd83722569eef503030b7e7438a73209cb812d6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban olvashatók Azure parancssori felület használatával
Az Azure-adatbázishoz a MySQL-kiszolgáló naplók az Azure parancssori felület, Azure parancssori segédprogram használatával töltheti le.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [MySQL-kiszolgálóhoz tartozó Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [Azure CLI 2.0](/cli/azure/install-azure-cli) , vagy használja az Azure-felhő rendszerhéj a böngészőben.

## <a name="configure-logging-for-azure-database-for-mysql"></a>MySQL az Azure-adatbázis naplózásának konfigurálása
A MySQL lassú lekérdezés napló elérésére a kiszolgálót úgy is konfigurálhatja.
1. Úgy, hogy bekapcsolja a naplózást **lassú\_lekérdezés\_napló** paraméter ON értékre állítása.
2. Állítsa be például a többi paraméter **hosszú\_lekérdezés\_idő** és **napló\_lassú\_admin\_utasítások**.

Lásd: [Server paraméterek konfigurálása hogyan](howto-configure-server-parameters-using-cli.md) hogyan állítsa be a következő paraméterek az Azure parancssori felületen keresztül.

Például a következő parancssori parancsot a lassú lekérdezés napló bekapcsolása, állítja be a hosszú lekérdezési idő 10 másodperc, és a naplózást, a lassú admin utasítás kikapcsolása. Végezetül felsorolja a konfigurációs beállításokat a jóváhagyásra.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista naplók az Azure Database MySQL-kiszolgáló
A kiszolgáló elérhető naplófájlokat sorolják fel, futtassa a [az mysql-naplói lista](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) parancsot.

A kiszolgáló a naplófájlokban listázhatja **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**, és közvetlen a nevű szövegfájlba **napló\_fájlok \_lista.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
A [az mysql server-naplók letöltése](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) parancs lehetővé teszi, hogy töltse le a kiszolgáló külön naplófájlba. 

Ez a példa letölti az adott naplófájlban a kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup** a helyi környezet.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- További tudnivalók [-kiszolgáló naplóit Azure-adatbázis MySQL](concepts-server-logs.md)
