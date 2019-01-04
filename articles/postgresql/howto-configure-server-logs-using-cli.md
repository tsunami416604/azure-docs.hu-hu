---
title: Konfigurálja és hozzáférés-kiszolgáló naplóit a PostgreSQL-hez készült Azure CLI-vel
description: Ez a cikk ismerteti, hogyan konfigurálhat és PostgreSQL-hez készült Azure Database-ben a kiszolgálónaplók elérése az Azure CLI parancssori.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 938aadaa1d17135808cbeb34adac5139bd61f833
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545022"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja és kiszolgálói naplók elérése az Azure CLI-vel
A PostgreSQL-kiszolgáló hibanaplói a parancssori felület (Azure CLI) használatával töltheti le. Azonban a tranzakciós naplókhoz való hozzáférés nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-azure-cli.md)
- A [Azure CLI-vel](/cli/azure/install-azure-cli) parancssori eszközzel vagy az Azure Cloud Shellt a böngészőben

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurálhatja a naplózás az Azure Database for postgresql-hez
Beállíthatja, hogy a kiszolgáló eléréséhez a lekérdezések naplói és a hibanaplók. Hibanaplók automatikus vákuumszivattyú, kapcsolat és ellenőrzőpont adatait is rendelkezhet.
1. Kapcsolja be a naplózást.
2. Lekérdezések naplózásának engedélyezéséhez frissíteni **log\_utasítás** és **log\_min\_időtartama\_utasítás**.
3. A megőrzési időtartam frissítése.

További információkért lásd: [kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista naplók az Azure Database for PostgreSQL-kiszolgáló
A kiszolgáló elérhető naplófájlok felsorolása, futtassa a [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) parancsot.

A naplófájlokban található kiszolgáló listázhatja **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A lista a naplófájlok nevű szövegfájlba majd közvetlen **log\_fájlok\_lista.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
Az a [az postgres server-naplók letöltése](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) parancsot, az egyes naplófájlok töltheti le a kiszolgáló számára. 

Az alábbi példát követve töltse le a kiszolgáló a megadott naplófájlt **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezetben.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>További lépések
- Webkiszolgáló-naplókkal kapcsolatos további információkért lásd: [kiszolgálói naplók az Azure Database for PostgreSQL](concepts-server-logs.md).
- A kiszolgálóparaméterekkel kapcsolatos további információkért lásd: [Azure CLI-vel a kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).
