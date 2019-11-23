---
title: Kiszolgálói naplók konfigurálása és elérése a PostgreSQL-hez – egyetlen kiszolgáló az Azure CLI használatával
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a kiszolgálói naplókat Azure Database for PostgreSQL – egyetlen kiszolgálón az Azure CLI parancssor használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bb33debaa23ad8625b6ddc1cc63738b13bcd19e1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023623"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>A kiszolgálói naplók konfigurálása és elérése az Azure CLI használatával
A PostgreSQL-kiszolgáló naplófájljait a parancssori felület (Azure CLI) használatával töltheti le. A tranzakciós naplókhoz való hozzáférés azonban nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-database-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
Beállíthatja, hogy a kiszolgáló hozzáférjen a lekérdezési naplókhoz és a hibák naplóihoz. A naplókhoz automatikus vákuum, kapcsolatok és ellenőrzőpont-információk is tartozhatnak.
1. A naplózás bekapcsolása.
2. A lekérdezések naplózásának engedélyezéséhez frissítse a **log\_utasítást** és a **naplót\_perc\_időtartam\_utasítást**.
3. A megőrzési időtartam frissítése.

További információ: a [kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Naplók listázása
A kiszolgálóhoz elérhető naplófájlok listázásához futtassa az az [postgres Server-logs List](/cli/azure/postgres/server-logs) parancsot.

A kiszolgálói **mydemoserver.postgres.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját a **log\_Files nevű szövegfájlba\_Listázás. txt**fájlban.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
Az az [postgres Server-logs Download](/cli/azure/postgres/server-logs) paranccsal letöltheti a kiszolgáló egyes naplófájljait. 

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.postgres.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Következő lépések
- A kiszolgálók naplóival kapcsolatos további tudnivalókért tekintse meg [a Azure Database for PostgreSQL kiszolgáló naplófájljait](concepts-server-logs.md).
- A kiszolgálói paraméterekkel kapcsolatos további információkért lásd: [kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI használatával](howto-configure-server-parameters-using-cli.md).
