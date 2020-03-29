---
title: Naplók kezelése - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a kiszolgálónaplókat (.log fájlokat) az Azure Database for PostgreSQL – Single Server az Azure CLI használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763572"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Kiszolgálói naplók konfigurálása és elérése az Azure CLI használatával
A PostgreSQL kiszolgáló hibanaplóit a parancssori felület (Azure CLI) segítségével töltheti le. A tranzakciós naplókhoz való hozzáférés azonban nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram vagy az Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
Beállíthatja, hogy a kiszolgáló hozzáférjen a lekérdezési naplókhoz és a hibanaplókhoz. A hibanaplók automatikus vákuummal, kapcsolattal és ellenőrzőpont-adatokkal rendelkezhetnek.
1. Kapcsolja be a naplózást.
2. A lekérdezésnaplózás engedélyezéséhez frissítse **a naplókimutatást\_** és **a napló\_min\_duration\_utasítását.**
3. Megőrzési időszak frissítése.

További információt a [Kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md)című témakörben talál.

## <a name="list-logs"></a>Naplók listázása
A kiszolgálóhoz rendelkezésre álló naplófájlok listázásához futtassa az [az postgres kiszolgálónaplók lista](/cli/azure/postgres/server-logs) parancsát.

A kiszolgáló naplófájljait **mydemoserver.postgres.database.azure.com** a **myresourcegroup**erőforráscsoport alatt listázhatja. Ezután irányítsa a naplófájlok listáját egy **list.txt nevű\_szövegfájlba.\_**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók letöltése helyileg a kiszolgálóról
Az [az postgres szerver-naplók letöltési](/cli/azure/postgres/server-logs) paranccsal letöltheti az egyes naplófájlokat a kiszolgálóhoz. 

A következő példában letöltheti a kiszolgáló naplófájlját, **mydemoserver.postgres.database.azure.com** a **myresourcegroup** erőforráscsoport erőforráscsoport alatt a helyi környezetbe.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>További lépések
- A kiszolgálónaplókról a [Kiszolgálónaplók az Azure Database for PostgreSQL alkalmazásban című témakörben](concepts-server-logs.md)olvashat bővebben.
- A kiszolgáló paramétereiről a [Kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md)című témakörben talál további információt.
