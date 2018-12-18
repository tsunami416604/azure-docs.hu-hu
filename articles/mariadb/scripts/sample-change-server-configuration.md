---
title: Azure CLI-szkript – Kiszolgálókonfigurációk módosítása
description: Ez a CLI-példaszkript felsorolja az összes elérhető kiszolgálókonfigurációt, és frissíti az innodb_lock_wait_timeout értékét.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 514f2fe7e8c2b94e175bc6f6fb0cec8ea3d0c9c6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547422"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Az Azure Database for MariaDB-kiszolgálót az Azure CLI-konfigurációinak felsorolása és frissítése
A CLI-példaszkript felsorolja az összes elérhető konfigurációs paraméterét, valamint azok engedélyezett értékeit Azure Database for MariaDB-kiszolgáló, és beállítja a *innodb_lock_wait_timeout* , az alapértelmezett értéktől eltérő értékre.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb-kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Felsorolja egy Azure Database for MariaDB-kiszolgáló konfigurációit. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Frissíti egy Azure Database for MariaDB-kiszolgáló konfigurációját. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Megjeleníti egy Azure Database for MariaDB-kiszolgáló konfigurációját. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Azure CLI-minták az Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- A kiszolgáló paramétereinek további információkért lásd: [hogyan való konfigurálása kiszolgálóparaméterek az Azure Database for MariaDB](../howto-server-parameters.md).
