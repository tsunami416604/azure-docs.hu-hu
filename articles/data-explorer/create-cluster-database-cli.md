---
title: 'Gyors útmutató: Hozzon létre egy Azure Data Explorer fürt és a CLI-vel adatbázis'
description: Ebben a rövid, megtudhatja, hogyan hozhat létre Azure Data Explorer fürt és adatbázis, Azure CLI használatával
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881116"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Hozzon létre egy Azure Data Explorer fürt és a CLI-vel adatbázis

Ez a rövid útmutató ismerteti, hogyan hozhat létre Azure Data Explorer fürt és adatbázis, Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata az Azure CLI, ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="configure-the-cli-parameters"></a>A CLI paramétereinek konfigurálása

Az alábbi lépések nem szükségesek, ha a parancsokat a Cloud Shellben futtatja. Ha helyileg futtatja a CLI-t, az alábbi lépések elvégzésével jelentkezzen be az Azure-ba, és állítsa be az aktuális előfizetést:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

2. Az előfizetés beállításához, hova szeretné létrehozni a fürtöt. A `MyAzureSub` értéket cserélje le a használni kívánt Azure-előfizetés nevére:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. Hozza létre a fürt a következő paranccsal:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | név | *azureclitest* | A fürt kívánt nevét.|
   | sku | *D13_v2* | A Termékváltozat a fürt számára fogja használni. |
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.

2. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Ha az eredmény tartalmazza a "provisioningState", "Succeeded" értékkel, majd a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Hozzon létre az adatbázist a következő paranccsal:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster-name | *azureclitest* | A fürt, ahol kell létrehozni az adatbázis neve.|
   | név | *clidatabase* | Az adatbázis kívánt nevét.|
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | soft-delete-period | *3650:00:00:00* | Mennyi ideig tartó adatokat meg kell őrizni, hogy lekérdezhetők. |
   | hot-cache-period | *3650:00:00:00* | Mennyi ideig tartó adatokat kell tárolni a gyorsítótárban. |

2. Futtassa a következő parancsot a létrehozott tekintse meg az adatbázist:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. Használja az alábbi parancsot a fürt törléséhez:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)
