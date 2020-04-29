---
title: Azure CLI-szkript – méretezés és monitorozás Azure Database for PostgreSQL
description: Azure CLI-szkriptminta – Azure Database for PostgreSQL-kiszolgáló méretezése más teljesítményszintre a mérőszámok lekérdezése után.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 24aaf461576e6e043979660f9de968358763e003
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882989"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Egyetlen PostgreSQL-kiszolgáló monitorozása és méretezése az Azure CLI-vel
Ez a CLI-parancsfájl a metrikák lekérdezése után egy Azure Database for PostgreSQL kiszolgáló számítási és tárolási kapacitását méretezi. A számítás vertikális fel-vagy leskálázást végez. A tárterület csak vertikális felskálázásra használható. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
Frissítse a parancsfájlt az előfizetés-azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres Server Update](/cli/azure/postgres/server#az-postgres-server-update) | Frissíti a PostgreSQL-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- További információ a [Azure Database for PostgreSQL számítási és tárolási](../concepts-pricing-tiers.md) feladatokról
- További szkripteket [az Azure Database for PostgreSQL-hez készült Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- További információ az [Azure CLI](/cli/azure) -ről
