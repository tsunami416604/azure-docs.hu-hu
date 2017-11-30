---
title: "Hozzáférés-kiszolgáló naplóit Azure-adatbázis MySQL az Azure parancssori felületével |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan férhet hozzá a kiszolgálói naplók az Azure-adatbázisban a MySQL az Azure CLI parancssori segédprogram használatával."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 908f28d8bd3d0dcbd03636e69cd47b5c47f3cfde
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
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
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista naplók az Azure Database MySQL-kiszolgáló
A kiszolgáló elérhető naplófájlokat sorolják fel, futtassa a [az mysql-naplói lista](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) parancsot.

A kiszolgáló a naplófájlokban listázhatja **myserver4demo.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**, és közvetlen a nevű szövegfájlba **napló\_fájlok \_lista.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
A [az mysql server-naplók letöltése](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) parancs lehetővé teszi, hogy töltse le a kiszolgáló külön naplófájlba. 

Ez a példa letölti az adott naplófájlban a kiszolgáló **myserver4demo.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup** a helyi környezet.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Következő lépések
- További tudnivalók [-kiszolgáló naplóit Azure-adatbázis MySQL](concepts-server-logs.md)
