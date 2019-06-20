---
title: Azure CLI-példaszkript – méretezési csoport egy Azure Database for MariaDB-kiszolgáló
description: A CLI-példaszkript méretezi az Azure Database for MariaDB-kiszolgálót méretez más teljesítményszintre a mérőszámok lekérdezése után.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 3fed332846c835106cbd2b8876d93aff1a97b0a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275006"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorozása és méretezése egy Azure Database for MariaDB-kiszolgálóhoz Azure CLI használatával
A CLI-példaszkript egyetlen Azure Database for MariaDB-kiszolgálót méretez más teljesítményszintre a mérőszámok lekérdezése után méretezhető.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. A használt előfizetés-Azonosítót cserélje le a `az monitor` parancsok a saját előfizetés-azonosítójára.  
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb-kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amelyen az adatbázisok futnak. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Azure CLI-minták az Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Méretezéssel kapcsolatos további információkért lásd: [díjcsomagokat.] (.. /Concepts-pricing-tiers.md.
