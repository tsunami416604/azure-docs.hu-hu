---
title: "Az Azure CLI Script – hozzon létre egy Azure-adatbázis PostgreSQL |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - adatbázist hoz létre Azure PostgreSQL-kiszolgáló, és beállítja egy kiszolgálószintű tűzfalszabályt."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 11/03/2017
ms.openlocfilehash: c83c5e86ea99a25ff106e0238f4d294bec100c32
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozása és konfigurálása egy tűzfalszabályt, az Azure parancssori felület használatával
Ez a parancsfájlpélda CLI adatbázist hoz létre Azure PostgreSQL-kiszolgáló, és konfigurálja egy kiszolgálószintű tűzfalszabályt. A parancsfájl sikeres futtatása után a PostgreSQL-kiszolgáló elérhető az összes Azure-szolgáltatások és a konfigurált IP-címet.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk szükséges az, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda szerkeszteni a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az postgres kiszolgáló létrehozása](/cli/azure/postgres/server#create) | Az adatbázisokat üzemeltető PostgreSQL-kiszolgáló létrehozása. |
| [az postgres kiszolgáló tűzfal létrehozása](/cli/azure/postgres/server/firewall-rule#create) | Létrehoz egy tűzfalszabály engedélyezése a megadott IP-címtartomány a kiszolgáló és a tartozó adatbázisok. |
| [az csoport törlése](/cli/azure/group#delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések
- További információt az Azure parancssori felület: [Azure CLI-dokumentáció](/cli/azure/overview)
- Próbálja meg a további parancsfájlok: [PostgreSQL Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
