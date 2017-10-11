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
ms.date: 05/31/2017
ms.openlocfilehash: 201db294ce362ef3e09cbe62f48bd51c8ea94dbb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>A MySQL-kiszolgáló létrehozása és konfigurálása egy tűzfalszabályt, az Azure parancssori felület használatával
Ez a parancsfájlpélda CLI adatbázist hoz létre Azure MySQL-kiszolgáló, és konfigurálja egy kiszolgálószintű tűzfalszabályt. Miután a parancsfájl sikeresen fut, a MySQL-kiszolgáló elérhető-e az összes Azure-szolgáltatások és a konfigurált IP-cím.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda szerkeszteni a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát.
[!code-azurecli-interactive[fő](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Azure-adatbázis létrehozása a MySQL és a kiszolgálószintű tűzfalszabály.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[fő](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "az erőforráscsoportot.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az mysql-kiszolgáló létrehozása](/cli/azure/mysql/server#create) | Az adatbázisokat üzemeltető MySQL kiszolgáló létrehozása. |
| [az mysql kiszolgáló tűzfal létrehozása](/cli/azure/mysql/server/firewall-rule#create) | Létrehoz egy tűzfalszabály engedélyezése a megadott IP-címtartomány a kiszolgáló és a tartozó adatbázisok. |
| [az csoport törlése](/cli/azure/group#delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [MySQL az Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
