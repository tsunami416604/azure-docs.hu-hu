---
title: Konfigurálja és PostgreSQL - hez-Azure CLI-vel egyetlen kiszolgáló kiszolgálói naplók elérése
description: Ez a cikk ismerteti, hogyan konfigurálhat és eléréséhez a kiszolgálónaplók, Azure database for PostgreSQL – egyetlen kiszolgáló az Azure CLI-vel parancssor használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4702db31ffeb15481584b9638f5be1aa640ff39e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067210"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja és kiszolgálói naplók elérése az Azure CLI-vel
A PostgreSQL-kiszolgáló hibanaplói a parancssori felület (Azure CLI) használatával töltheti le. Azonban a tranzakciós naplókhoz való hozzáférés nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-azure-cli.md)
- A [Azure CLI-vel](/cli/azure/install-azure-cli) parancssori eszközzel vagy az Azure Cloud Shellt a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
Beállíthatja, hogy a kiszolgáló eléréséhez a lekérdezések naplói és a hibanaplók. Hibanaplók automatikus vákuumszivattyú, kapcsolat és ellenőrzőpont adatait is rendelkezhet.
1. Kapcsolja be a naplózást.
2. Lekérdezések naplózásának engedélyezéséhez frissíteni **log\_utasítás** és **log\_min\_időtartama\_utasítás**.
3. A megőrzési időtartam frissítése.

További információkért lásd: [kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Lista naplók
A kiszolgáló elérhető naplófájlok felsorolása, futtassa a [az postgres server-logs list](/cli/azure/postgres/server-logs) parancsot.

A naplófájlokban található kiszolgáló listázhatja **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A lista a naplófájlok nevű szövegfájlba majd közvetlen **log\_fájlok\_lista.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
Az a [az postgres server-naplók letöltése](/cli/azure/postgres/server-logs) parancsot, az egyes naplófájlok töltheti le a kiszolgáló számára. 

Az alábbi példát követve töltse le a kiszolgáló a megadott naplófájlt **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezetben.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>További lépések
- Webkiszolgáló-naplókkal kapcsolatos további információkért lásd: [kiszolgálói naplók az Azure Database for PostgreSQL](concepts-server-logs.md).
- A kiszolgálóparaméterekkel kapcsolatos további információkért lásd: [Azure CLI-vel a kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).
