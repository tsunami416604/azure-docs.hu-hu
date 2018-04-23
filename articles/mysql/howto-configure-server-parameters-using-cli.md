---
title: A szolgáltatás paraméterei MySQL az Azure-adatbázis konfigurálása
description: Ez a cikk ismerteti az Azure-adatbázis konfigurálása a szolgáltatás paraméterei MySQL az Azure CLI parancssori segédprogram használatával.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9caf6b164f2433ab3c1b701554f562211cc2de80
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Kiszolgáló konfigurációs paraméterek testreszabása az Azure parancssori felület használatával
Listáról, megjelenítése és konfigurációs paraméterek frissítéséhez egy Azure-adatbázis MySQL-kiszolgáló Azure CLI-vel, az Azure parancssori segédprogramot. Motor konfigurációk egy részét a kiszolgáló szintjén van közzétéve, és módosíthatja. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Az Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure-felhő rendszerhéj a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista kiszolgáló konfigurációs paraméterek az Azure Database MySQL-kiszolgáló
Minden módosíthatóvá paraméter a kiszolgáló, az értékek listában, futtassa a [az mysql kiszolgálólista konfigurációs](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) parancsot.

A kiszolgáló konfigurációs paraméterek, a kiszolgáló listázhatja **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
A definíciója a listában szereplő paraméterek, tekintse meg a MySQL útmutató szakaszban [Server rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Kiszolgálókonfiguráció paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részletei láthatók, futtassa a [az mysql kiszolgáló konfigurációs megjelenítése](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) parancsot.

Ez a példa bemutatja részleteit a **lassú\_lekérdezés\_napló** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportbatartozó**myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>A kiszolgáló konfigurációs paraméter értékét módosítani.
Módosíthatja egy bizonyos kiszolgáló konfigurációs paraméter, amely frissíti az alapul szolgáló konfigurációs értéket a MySQL-kiszolgáló motor értékét is. Frissítse a konfigurációt, használja a [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) parancsot. 

Frissítése az **lassú\_lekérdezés\_napló** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mysql.database.azure.com** erőforráscsoportba tartozó  **contoso.com.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha szeretne egy konfigurációs paraméter értékének alaphelyzetbe állítása, hagyja ki ezt az opcionális `--value` paraméter, és a szolgáltatás az alapértelmezett érték vonatkozik. A fenti példában az alábbihoz hasonlóan fog kinézni:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód alaphelyzetbe állítja a **lassú\_lekérdezés\_napló** konfigurációját, és az alapértelmezett érték **OFF**. 

## <a name="next-steps"></a>További lépések
- Konfigurálása [kiszolgáló adatait az Azure portálon](howto-server-parameters.md)