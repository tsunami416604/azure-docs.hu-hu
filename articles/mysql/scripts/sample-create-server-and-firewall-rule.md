---
title: "Az Azure CLI Script – a MySQL az Azure-adatbázis létrehozása |} Microsoft Docs"
description: "Ez a parancsfájlpélda CLI adatbázist hoz létre Azure MySQL-kiszolgáló, és konfigurálja egy kiszolgálószintű tűzfalszabályt."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/02/2017
ms.openlocfilehash: ee0d7e1d060492cabc78eeff098057bf9c09c6d1
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>A MySQL-kiszolgáló létrehozása és konfigurálása egy tűzfalszabályt, az Azure parancssori felület használatával
Ez a parancsfájlpélda CLI adatbázist hoz létre Azure MySQL-kiszolgáló, és konfigurálja egy kiszolgálószintű tűzfalszabályt. Miután a parancsfájl sikeresen fut, a MySQL-kiszolgáló elérhető-e az összes Azure-szolgáltatások és a konfigurált IP-cím.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda szerkeszteni a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az mysql-kiszolgáló létrehozása](/cli/azure/mysql/server#az_msql_server_create) | Az adatbázisokat üzemeltető MySQL kiszolgáló létrehozása. |
| [az mysql kiszolgáló tűzfal létrehozása](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Létrehoz egy tűzfalszabály engedélyezése a megadott IP-címtartomány a kiszolgáló és a tartozó adatbázisok. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [MySQL az Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
