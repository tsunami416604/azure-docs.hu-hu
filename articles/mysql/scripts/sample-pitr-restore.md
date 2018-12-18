---
title: Azure CLI-szkript – Azure Database for MySQL-kiszolgáló visszaállítása egy korábbi időpontra
description: Ez a CLI-mintaszkript visszaállítja az Azure Database for MySQL-kiszolgálót egy korábbi időpontban fennálló állapotra.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 8506e6eaac51730cb3d7a20f1d21f1c8bce03bee
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540027"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure Database for MySQL-kiszolgáló visszaállítása az Azure CLI-vel
Ez az Azure CLI-példaszkript egyetlen Azure Database for MySQL-kiszolgálót állít vissza egy korábbi időpontban fennálló állapotra.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a példában leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le az „az monitor”-parancsokban használt előfizetés-azonosítót a saját előfizetés-azonosítójára.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mysql server restore](/cli/azure/mysql/server#restore) | Visszaállít egy kiszolgálót egy biztonsági másolatból. |
| [az group delete](/cli/azure/group#delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Az Azure CLI-minták az Azure Database for MySQL-hez](../sample-scripts-azure-cli.md)
