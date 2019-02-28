---
title: 'Gyors útmutató: Az Azure Data Explorer fürt és adatbázis létrehozása a parancssori felület használatával'
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer fürt és adatbázis az Azure CLI-vel
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 357f0efcf7300545d10113c92702d9fed4aad049
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958015"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>Az Azure Data Explorer fürt és adatbázis létrehozása a parancssori felület használatával

Ebben a rövid útmutató egy Azure Data Explorer fürt és adatbázis létrehozása az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata az Azure CLI, az ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-the-cli-parameters"></a>A CLI paramétereinek konfigurálása

A következő lépéseket nem szükséges, ha a parancsok az Azure Cloud Shellben futtatja. Ha helyileg futtatja a parancssori Felületet, kövesse az alábbi lépéseket, jelentkezzen be az Azure-ba, és állítsa be a jelenlegi előfizetésében:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

2. Ha azt szeretné, hogy a fürt létrehozása az előfizetés beállításához. Cserélje le `MyAzureSub` használni kívánt Azure-előfizetés nevére:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. A fürt létrehozásához a következő paranccsal:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | név | *azureclitest* | A fürt kívánt nevét.|
   | termékváltozat | *D13_v2* | A Termékváltozat a fürthöz használt. |
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.

2. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Ha az eredmény tartalmazza `provisioningState` együtt a `Succeeded` érték, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Az adatbázis létrehozása a következő paranccsal:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster-name | *azureclitest* | A fürt, ahol létrejön az adatbázis neve.|
   | név | *clidatabase* | Az adatbázis neve.|
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | soft-delete-period | *3650:00:00:00* | Mennyi ideig megtartott adatok lekérdezhetők. |
   | hot-cache-period | *3650:00:00:00* | Mennyi ideig megtartott adatok a gyorsítótárban. |

2. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. A következő paranccsal törölje a fürtöt:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)
