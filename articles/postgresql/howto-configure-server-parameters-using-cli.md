---
title: "Állítsa be a paramétereket az Azure-adatbázis PostgreSQL |} Microsoft Docs"
description: "Ez a cikk ismerteti a paramétereket az Azure-adatbázis konfigurálása az Azure CLI parancssorból PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 96c5ab5caa4fea178a3108947fa858d395650e08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Testre szabhatja a kiszolgáló konfigurációs paraméterek Azure parancssori felület használatával
Listában, megjelenítése, és frissíti a parancssori felület (Azure CLI) használatával Azure PostgreSQL-kiszolgáló konfigurációs paramétereket. Egy részhalmazát motor konfigurációk kiszolgálói szinten van közzétéve, és módosíthatja. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Hozzon létre egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis és adatbázis [PostgreSQL az Azure-adatbázis létrehozása](quickstart-create-server-database-azure-cli.md)
- Telepítse [Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori felület a gépen, vagy használja a [Azure Cloud rendszerhéj](../cloud-shell/overview.md) az Azure portálon, a böngésző használatával.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista kiszolgáló konfigurációs paraméterek az Azure Database PostgreSQL-kiszolgáló
Minden módosíthatóvá paraméter a kiszolgáló, az értékek listában, futtassa a [az postgres kiszolgálólista konfigurációs](/cli/azure/postgres/server/configuration#list) parancsot.

A kiszolgáló konfigurációs paraméterek, a kiszolgáló listázhatja **mypgserver-20170401.postgres.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Kiszolgálókonfiguráció paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részletei láthatók, futtassa a [az postgres kiszolgáló konfigurációs megjelenítése](/cli/azure/postgres/server/configuration#show) parancsot.

Ez a példa bemutatja részleteit a **napló\_min\_üzenetek** kiszolgáló konfigurációs paraméter kiszolgáló **mypgserver-20170401.postgres.database.azure.com** erőforráscsoportba tartozó **contoso.com.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Módosítsa a kiszolgáló konfigurációs paraméter értéke
Módosíthatja egy bizonyos kiszolgáló konfigurációs paraméter, amely frissíti az alapul szolgáló konfigurációs értéket a PostgreSQL-kiszolgáló motor értékét is. Frissítse a konfigurációt, használja a [az postgres server configuration set](/cli/azure/postgres/server/configuration#set) parancsot. 

Frissítése az **napló\_min\_üzenetek** kiszolgáló konfigurációs paraméter kiszolgáló **mypgserver-20170401.postgres.database.azure.com** erőforráscsoportba tartozó **contoso.com.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Ha szeretne egy konfigurációs paraméter értékének alaphelyzetbe állítása, egyszerűen választja hagyja ki az opcionális `--value` paraméter, és a szolgáltatás az alapértelmezett érték vonatkozik. A fenti példában az alábbihoz hasonlóan fog kinézni:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Visszaállítja a **napló\_min\_üzenetek** konfigurációját, és az alapértelmezett érték **figyelmeztetés**. További információ a kiszolgáló konfigurációját és a megengedett értékek dokumentációja PostgreSQL [kiszolgálókonfiguráció](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Következő lépések
- Konfigurálja, és hozzáférést kiszolgálónaplókban, [Server PostgreSQL az Azure Database-naplók](concepts-server-logs.md)
