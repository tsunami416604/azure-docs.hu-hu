---
title: Lassú lekérdezési naplók elérése - Azure CLI - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan érheti el a lassú lekérdezési naplók az Azure Database for MySQL az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 87db1a2af0bfdc854c909ef4221a3d97f9bf10d5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270672"
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
2. Adja meg, hogy hová szeretné kiadni a naplókat **a\_naplókimenet használatával.** Ha naplókat szeretne küldeni a helyi tárolóba és az Azure Monitor diagnosztikai naplóiba, válassza a **Fájl**lehetőséget. Ha csak az Azure Monitor naplókba szeretne naplókat küldeni, válassza a **Nincs** lehetőséget.
3. Módosítsa az egyéb paramétereket, például a **hosszú\_\_lekérdezési időt** és a lassú **\_\_rendszergazdai\_utasítások naplózását.**

A paraméterek azure CLI-n keresztüli értékének beállításáról a [Kiszolgálóparaméterkonfigurálása](howto-configure-server-parameters-using-cli.md)című témakörből megtudhatja.

A következő CLI parancs például bekapcsolja a lassú lekérdezési naplót, a hosszú lekérdezési időt 10 másodpercre állítja be, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az ellenőrzés konfigurációs beállításait.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Az Azure Database for MySQL-kiszolgáló naplóinak listázása
Ha **log_output** "Fájl" értékre van konfigurálva, a naplókat közvetlenül a kiszolgáló helyi tárolójából érheti el. A kiszolgálóhoz rendelkezésre álló lassú lekérdezési naplófájlok listázásához futtassa az [az mysql server-logs list parancsot.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

A kiszolgáló naplófájljait **mydemoserver.mysql.database.azure.com** a **myresourcegroup**erőforráscsoport alatt listázhatja. Ezután irányítsa a naplófájlok listáját egy **list.txt nevű\_szövegfájlba.\_**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Ha **log_output** "Fájl" értékre van konfigurálva, az [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) paranccsal letöltheti az egyes naplófájlokat a kiszolgálóról.

A következő példában letöltheti a kiszolgáló **naplófájlját, mydemoserver.mysql.database.azure.com** a **myresourcegroup** erőforráscsoport erőforráscsoport alatt a helyi környezetbe.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>További lépések
- Ismerje meg a [lassú lekérdezési naplókat az Azure Database for MySQL szolgáltatásban.](concepts-server-logs.md)
