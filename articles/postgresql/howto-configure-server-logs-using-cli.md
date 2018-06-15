---
title: Konfigurálja, és hozzáférést kiszolgálónaplókban PostgreSQL Azure parancssori felület használatával
description: A cikkből megtudhatja, hogyan lehet konfigurálni és a kiszolgálói naplók az Azure-adatbázis hozzáférését PostgreSQL az Azure CLI parancssor használatával.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 951dcca562c08698b4ce4528d005fc91152ea337
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30235527"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban Azure parancssori felület használatával
A parancssori felület (CLI) használatával lehet letölteni a PostgreSQL server hibanaplójában talál. Azonban tranzakciós naplók elérése nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [Azure-adatbázis PostgreSQL-kiszolgáló](quickstart-create-server-database-azure-cli.md)
- A [Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori segédprogram vagy Azure Cloud rendszerhéj a böngészőben

## <a name="configure-logging-for-azure-database-for-postgresql"></a>PostgreSQL az Azure-adatbázis naplózásának konfigurálása
A lekérdezés naplókat, valamint a hibanaplókat eléréséhez kiszolgálót úgy is konfigurálhatja. Hibanaplók automatikus vákuumszivattyú, a kapcsolat és az ellenőrzőpont információkat lehet.
1. Bekapcsolja a naplózást.
2. Ahhoz, hogy a lekérdezések naplózása, frissítése **napló\_utasítás** és **napló\_min\_időtartam\_utasítás**.
3. Frissítés megőrzési időtartama.

További információkért lásd: [kiszolgáló konfigurációs paraméterek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista naplók az Azure Database PostgreSQL-kiszolgáló
A kiszolgáló elérhető naplófájlokat sorolják fel, futtassa a [az postgres server-naplók lista](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) parancsot.

A kiszolgáló a naplófájlokban listázhatja **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup**. A listában, a naplófájlok nevű szövegfájlba majd közvetlen **napló\_fájlok\_lista.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
Az a [az postgres server-naplók letöltése](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) parancs, a kiszolgáló letöltheti a külön naplófájlba. 

Az alábbi példát követve töltse le a kiszolgáló az adott naplófájlban **mydemoserver.postgres.database.azure.com** az erőforráscsoportba tartozó **myresourcegroup** a helyi környezet.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>További lépések
- A kiszolgáló naplóiban kapcsolatos további információkért lásd: [-kiszolgáló naplóit Azure-adatbázis PostgreSQL](concepts-server-logs.md).
- Kiszolgáló paraméterekkel kapcsolatos további információkért lásd: [testre szabhatja a kiszolgáló konfigurációs paraméterek Azure parancssori felület használatával](howto-configure-server-parameters-using-cli.md).
