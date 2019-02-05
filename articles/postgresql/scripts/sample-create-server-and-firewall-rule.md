---
title: Azure CLI-szkript – Azure Database for PostgreSQL létrehozása
description: Azure CLI-példaszkript – Létrehoz egy Azure Database for PostgreSQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 14bb46d8917aedb3ea5a625db2489c97a4fd0ba3
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727109"
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása és tűzfalszabály konfigurálása az Azure CLI használatával
Ez a CLI-példaszkript létrehoz egy Azure Database for PostgreSQL-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt. Miután a szkript sikeresen futott, a PostgreSQL-kiszolgáló elérhető lesz az összes Azure-szolgáltatásból és a konfigurált IP-címről.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule) | Létrehoz egy tűzfalszabályt, amely engedélyezi a kiszolgáló és az azon lévő adatbázisok elérését a megadott IP-címtartományból. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációja](/cli/azure)
- További szkripteket: [Azure CLI-minták az Azure Database for PostgreSQL szolgáltatáshoz](../sample-scripts-azure-cli.md)
