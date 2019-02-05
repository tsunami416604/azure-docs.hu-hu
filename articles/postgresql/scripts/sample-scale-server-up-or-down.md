---
title: Azure CLI-szkript – Azure Database for PostgreSQL méretezése
description: Azure CLI-szkriptminta – Azure Database for PostgreSQL-kiszolgáló méretezése más teljesítményszintre a mérőszámok lekérdezése után.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 65958eb1e2d7cc4492e0437b24db77f00ebc7eba
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728333"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Egyetlen PostgreSQL-kiszolgáló monitorozása és méretezése az Azure CLI-vel
Ez a CLI-mintaszkript egyetlen Azure Database for PostgreSQL-kiszolgálót méretez más teljesítményszintre a mérőszámok lekérdezése után. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le az `az monitor` parancsokban használt előfizetés-azonosítót a saját előfizetés-azonosítójára.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációja](/cli/azure)
- További szkripteket: [Azure CLI-minták az Azure Database for PostgreSQL szolgáltatáshoz](../sample-scripts-azure-cli.md)
- Olvassa el a méretezéssel kapcsolatos további információk: [Szolgáltatásszintek](../concepts-service-tiers.md) és [számítási és tárolási egységekkel](../concepts-compute-unit-and-storage.md)
