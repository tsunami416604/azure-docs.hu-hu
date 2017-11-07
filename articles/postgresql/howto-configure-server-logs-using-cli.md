---
title: "Konfigurálja, és el a kiszolgáló naplóiban PostgreSQL Azure parancssori felületével |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan konfigurálja, és a kiszolgálói naplók az Azure-adatbázis el az Azure CLI parancssorból PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 5e10ddd8c850d457b4ad77cd5ea4d92edc07017e
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban olvashatók Azure parancssori felület használatával
Letöltheti a PostgreSQL hibanaplókat a parancssori felület (Azure CLI) használatával. Tranzakciós naplók elérése azonban nem támogatott. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy [PostgreSQL-kiszolgáló Azure-adatbázis](quickstart-create-server-database-azure-cli.md)
- Telepítés [Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure-felhő rendszerhéj a böngészőben.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>PostgreSQL az Azure-adatbázis naplózásának konfigurálása
A lekérdezés naplókat, valamint a hibanaplókat eléréséhez kiszolgálót úgy is konfigurálhatja. A hibanaplókban automatikus vákuumszivattyú és a kapcsolat és az ellenőrzőpontok információkat is tartalmazhat.
1. Naplózás bekapcsolása
2. Update naplóját\_utasítás és a naplófájlok\_min\_időtartam\_utasításban lekérdezés naplózásának engedélyezése
3. Frissítse a megőrzési időtartam

További információkért lásd: [kiszolgáló konfigurációs paraméterek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista naplók az Azure Database PostgreSQL-kiszolgáló
A kiszolgáló elérhető naplófájlokat sorolják fel, futtassa a [az postgres server-naplók lista](/cli/azure/postgres/server-logs#list) parancsot.

A kiszolgáló a naplófájlokban listázhatja **mypgserver-20170401.postgres.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**, és közvetlen a nevű szövegfájlba **napló\_fájlok\_lista.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Naplók helyi letöltése a kiszolgálóról
A [az postgres server-naplók letöltése](/cli/azure/postgres/server-logs#download) parancs lehetővé teszi, hogy töltse le a kiszolgáló külön naplófájlba. 

Ez a példa letölti az adott naplófájlban a kiszolgáló **mypgserver-20170401.postgres.database.azure.com** erőforráscsoportba tartozó **myresourcegroup** a helyi környezet.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Következő lépések
- A kiszolgáló naplóiban kapcsolatos további információkért lásd: [Server PostgreSQL az Azure Database-naplók](concepts-server-logs.md)
- A kiszolgáló paraméterek további információkért lásd: [testre szabhatja a kiszolgáló konfigurációs paraméterek Azure parancssori felület használatával](howto-configure-server-parameters-using-cli.md)
