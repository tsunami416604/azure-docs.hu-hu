---
title: "Az Azure CLI parancsfájl - konfigurációk módosítása |} Microsoft Docs"
description: "Ez a parancsfájlpélda CLI felsorolja az összes rendelkezésre álló kiszolgálói konfiguráció, és frissíti a innodb_lock_wait_timeout."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 08/29/2017
ms.openlocfilehash: 558e7d4ee96a3424a777f9f57679da25f2306efd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Az Azure parancssori felület használatával MySQL-kiszolgáló Azure adatbázisának lista és a frissítés konfigurációk
Ez a parancsfájlpélda CLI felsorolja az összes rendelkezésre álló konfigurációs paraméterek, valamint az engedélyezett értékek az Azure Database MySQL-kiszolgáló, és beállítja a *innodb_lock_wait_timeout* , amely az egyik alapértelmezett értéktől eltérő értékre.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az mysql-kiszolgáló létrehozása](/cli/azure/mysql/server#create) | Az adatbázisokat üzemeltető MySQL kiszolgáló létrehozása. |
| [az mysql konfigurációs kiszolgálólista](/cli/azure/mysql/server/configuration#list) | Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis beállításait a listában. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#set) | Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis konfigurációjának frissítése. |
| [az mysql kiszolgáló konfiguráció megjelenítése](/cli/azure/mysql/server/configuration#show) | Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis konfigurációjának megjelenítése. |
| [az csoport törlése](/cli/azure/group#delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [MySQL az Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
- A kiszolgáló paraméterek további információkért lásd: [hogyan való konfigurálása Server paraméterek MySQL az Azure-adatbázis](../howto-server-parameters.md).
