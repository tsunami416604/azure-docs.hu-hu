---
title: "Az Azure CLI-parancsfájlt minta - Batch-fiók létrehozása |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - Batch-fiók létrehozása"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Batch-fiók létrehozása az Azure parancssori felülettel

Ezt a parancsfájlt hoz létre az Azure Batch-fiók, és megjeleníti a fiók a különféle tulajdonságainak lekérdezése, és frissíti.

## <a name="prerequisites"></a>Előfeltételek

Telepítse az Azure parancssori felület használatával a utasításokat a [Azure parancssori felület telepítési útmutató](https://docs.microsoft.com/cli/azure/install-azure-cli), ha még nem tette meg.

## <a name="batch-account-sample-script"></a>Batch-fiók parancsfájlpéldát

Batch-fiók létrehozásakor alapértelmezés szerint a számítási csomópontok belső rendeli hozzá a Batch szolgáltatás. Lefoglalt számítási csomópontok részt vesznek egy külön core kvótát, és a fiók hitelesítése keresztül megosztott kulcs hitelesítő adatai vagy az Azure Active Dirctory tokent.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Batch-fiók felhasználói előfizetés minta parancsfájl használatával

Úgy is dönthet, hogy a Batch számítási csomópontjainak létrehozása a saját Azure-előfizetésben rendelkezik.
Fiókok kialakítása, amelyek számítási csomópontok a előfizetéssé hitelesíteni kell az Azure Active Directory-tokent keresztül, és a számítási csomópontok lefoglalt az előfizetési kvóta is beleszámít. Fiók létrehozása az ebben a módban, a Key Vault hivatkozás egy kell adnia a fiók létrehozásakor.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Miután a fenti minta parancsfájlok valamelyike, futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, és az összes kapcsolódó erőforrások (beleértve a Batch-fiókok, Azure Storage-fiókokat és Azure kulcstárolójának).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a Batch-fiók és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az a batch-fiók létrehozása](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | A Batch-fiók létrehozása  |
| [az batch-fiók beállítása](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | A Batch-fiók tulajdonságainak frissítése.  |
| [az batch-fiók megjelenítése](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Lekérdezi a megadott Batch-fiók adatait.  |
| [az kötegelt fióklista kulcsok](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Lekérdezi a megadott Batch-fiók hozzáférési kulcsainak listázása.  |
| [az batch-fiók bejelentkezési](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | A megadott Batch-fiók további CLI interakció azonosítja.  |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Tárfiók létrehozása. |
| [az keyvault létrehozása](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Kulcstároló létrehozása. |
| [az keyvault-szabály beállítása](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | A biztonsági házirend a megadott kulcstároló frissítése. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További kötegelt CLI parancsfájl minták megtalálhatók a [Azure Batch CLI dokumentáció](../batch-cli-samples.md).
