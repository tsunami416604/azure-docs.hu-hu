---
title: Azure CLI-szkript – Kiszolgálókonfigurációk módosítása
description: Ez a CLI-példaszkript felsorol minden elérhető kiszolgálókonfigurációs lehetőséget, és frissíti az egyik lehetőség értékét.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: c570982fed746b01f5a8c15edb94bdeac679b248
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545610"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló konfigurációinak felsorolása és frissítése az Azure CLI használatával
Ez a CLI-példaszkript felsorolja egy Azure Database for PostgreSQL-kiszolgáló összes elérhető konfigurációs paraméterét, valamint azok engedélyezett értékeit, és beállítja a *log_retention_days* paramétert egy, az alapértelmezett értéktől eltérő értékre.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Felsorolja egy Azure Database for PostgreSQL-kiszolgáló konfigurációit. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Frissíti egy Azure Database for PostgreSQL-kiszolgáló konfigurációját. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Megjeleníti egy Azure Database for PostgreSQL-kiszolgáló konfigurációját. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Azure CLI-minták az Azure Database for PostgreSQL szolgáltatáshoz](../sample-scripts-azure-cli.md)
- A kiszolgálóparaméterekkel kapcsolatos további információért lásd [a kiszolgálóparaméterek az Azure Portalon történő konfigurálását](../howto-configure-server-parameters-using-portal.md) ismertető szakaszt.
