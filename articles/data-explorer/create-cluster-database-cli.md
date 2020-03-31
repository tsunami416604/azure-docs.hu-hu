---
title: Hozzon létre egy Azure Data Explorer &-fürtöt az Azure CLI-vel
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer-fürtöt és -adatbázist az Azure CLI használatával
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561935"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure Data Explorer-fürt és-adatbázis létrehozása az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. Ebben a cikkben hozzon létre egy fürtöt és egy adatbázist az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához szüksége van egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a cikk megköveteli az Azure CLI 2.0.4-es vagy újabb verziója. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-the-cli-parameters"></a>A CLI-paraméterek konfigurálása

A következő lépések nem szükségesek, ha parancsokat futtat az Azure Cloud Shellben. Ha helyileg futtatja a CLI-t, az alábbi lépésekkel jelentkezzen be az Azure-ba, és állítsa be aktuális előfizetését:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

1. Állítsa be az előfizetést oda, ahol létre szeretné hozni a fürtöt. Cserélje `MyAzureSub` le a használni kívánt Azure-előfizetés nevére:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. Hozza létre a fürtöt a következő paranccsal:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | név | *azureclitest* | A fürt kívánt neve.|
   | Sku | *D13_v2* | A fürthöz használt termékváltozat. |
   | resource-group | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |

    További választható paraméterek, például a fürt kapacitása további választható paramétereket is használhat.

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt sikeresen létrejött-e:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Ha az `provisioningState` eredmény `Succeeded` tartalmazza az értéket, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer-fürtben

1. Hozza létre az adatbázist a következő paranccsal:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | fürtnév | *azureclitest* | Annak a fürtnek a neve, amelyben az adatbázis létrejön.|
   | név | *cliadatbázis* | Az adatbázis neve.|
   | resource-group | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |
   | soft-delete-időszak | *P365D* | Azt jelzi, hogy az adatok mennyi ideig lesznek lekérdezhetők. További információt az [adatmegőrzési szabályzatban](/azure/kusto/concepts/retentionpolicy) talál. |
   | hot-cache-időszak | *P31D* | Azt jelzi, hogy mennyi ideig lesznek az adatok a gyorsítótárban tárolva. További információt a [gyorsítótár-házirendben](/azure/kusto/concepts/cachepolicy) talál. |

1. A létrehozott adatbázis megtekintéséhez futtassa a következő parancsot:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Fürt törlésekor az összes benne lévő adatbázist is törli. A fürt törléséhez használja a következő parancsot:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>További lépések

* [Adatok betöltése az Azure Data Explorer Python-tár használatával](python-ingest-data.md)
