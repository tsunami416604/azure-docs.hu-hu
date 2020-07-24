---
title: Azure parancssori felület előfeltételei az Azure HPC cache-hez
description: Telepítési lépések, mielőtt az Azure CLI-t Azure HPC-gyorsítótár létrehozására vagy módosítására használhatja
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099247"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI beállítása az Azure HPC cache-hez

Az alábbi lépéseket követve előkészítheti a környezetet, mielőtt az Azure CLI-t használja az Azure HPC-gyorsítótár létrehozásához vagy kezeléséhez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure HPC cache az Azure CLI 2,7-es vagy újabb verzióját igényli. Futtassa `az --version` a parancsot a telepített verzió és a függő kódtárak megkereséséhez. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Az Azure HPC cache bővítmény telepítése

Az Azure HPC cache functions nem része a fő kód alapjának, ezért telepítenie kell a bővítmény-referenciát is. Kövesse az alábbi utasításokat.

1. Bejelentkezés

   Jelentkezzen be az az [login](/cli/azure/reference-index#az-login) paranccsal, ha a parancssori felület helyileg telepített verzióját használja.

    ```azurecli
    az login
    ```

    A hitelesítési folyamat befejezéséhez kövesse a terminálban megjelenő lépéseket.

   > [!TIP]
   > Ha több előfizetéssel rendelkezik, ki kell választania egyet. Válassza ki, amikor elindít egy Cloud Shell-munkamenetet az Azure Portalon, vagy kövesse az [Azure CLI-vel](/cli/azure/get-started-with-azure-cli#sign-in) való első lépések című témakör utasításait az előfizetés parancssorból történő beállításához.

2. Az Azure CLI-bővítmény telepítése

   Az Azure HPC cache-függvények Azure CLI-bővítményként vannak megadva – az alapszintű CLI-csomag még nem része. A használatához telepítenie kell a bővítmény hivatkozását.

   Az Azure CLI-bővítmények lehetővé teszi a kísérleti és az előzetes kiadású parancsok elérését. További információ a bővítményekről, például a frissítésről és az eltávolításról: [bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

   Telepítse az Azure HPC cache bővítményét a következő parancs futtatásával:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Alapértelmezett erőforráscsoport beállítása (nem kötelező)

A HPC-cache parancsok többsége megköveteli a gyorsítótár erőforráscsoport átadását. Az alapértelmezett erőforráscsoportot az [az configure](/cli/azure/reference-index#az-configure)paranccsal állíthatja be.

## <a name="next-steps"></a>További lépések

Az Azure CLI-bővítmény telepítését és a bejelentkezést követően az Azure CLI használatával hozhat létre és kezelhet Azure HPC gyorsítótár-rendszereket.

* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md)
* [Azure CLI HPC – gyorsítótár dokumentációja](/cli/azure/ext/hpc-cache/hpc-cache)
