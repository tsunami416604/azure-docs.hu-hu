---
title: "Azure CLI-szkript – Azure Database for PostgreSQL-kiszolgáló visszaállítása"
description: "Ez az Azure CLI-példaszkript bemutatja, hogyan lehet visszaállítani egy Azure Database for PostgreSQL-kiszolgálót és a hozzá tartozó adatbázisokat egy korábbi időpontban fennálló állapotra."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: c8b099e859e9ab418fd121a987920d037ed3f36d
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló visszaállítása az Azure CLI használatával
Ez az Azure CLI-példaszkript visszaállít egy Azure Database for PostgreSQL-kiszolgálót egy korábbi időpontra.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy frissítéséhez lásd: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le az `az monitor` parancsokban használt előfizetés-azonosítót a saját előfizetés-azonosítójára.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=18-19 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgresql server create](/cli/azure/postgresql/server#az_msql_server_create) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgresql server restore](/cli/azure/postgresql/server#az_msql_server_restore) | Visszaállít egy kiszolgálót egy biztonsági másolatból. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure/overview).
- További szkripteket [az Azure Database for PostgreSQL-hez készült Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- [Egy Azure Database for PostgreSQL-kiszolgáló biztonsági mentése és visszaállítása az Azure Portal használatával](../howto-restore-server-portal.md)
