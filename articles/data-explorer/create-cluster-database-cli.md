---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist az Azure CLI használatával
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bd53a8e29254af617b6cfa68935a191a50fc526c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326767"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához Azure-előfizetésre van szükség. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-the-cli-parameters"></a>A CLI paramétereinek konfigurálása

A következő lépések nem szükségesek, ha parancsokat futtat Azure Cloud Shellban. Ha helyileg futtatja a CLI-t, kövesse az alábbi lépéseket az Azure-ba való bejelentkezéshez és az aktuális előfizetés beállításához:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

1. Állítsa be azt az előfizetést, amelyben létre szeretné hozni a fürtöt. Cserélje le a `MyAzureSub` értéket a használni kívánt Azure-előfizetés nevére:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő parancs használatával:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | name | *azureclitest* | A fürt kívánt neve.|
   | sku | *D13_v2* | A fürthöz használni kívánt SKU. |
   | resource-group | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    További választható paramétereket is használhat, például a fürt kapacitását.

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Ha az eredmény tartalmazza `provisioningState` az `Succeeded` értéket, a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő parancs használatával:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | fürt neve | *azureclitest* | Annak a fürtnek a neve, ahová az adatbázist létre kívánja hozni.|
   | name | *clidatabase* | Az adatbázis neve.|
   | resource-group | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |
   | soft-delete-period | *P365D* | Azt jelzi, hogy mennyi időt kell megőrizni az adat a lekérdezés számára. További információ: [adatmegőrzési szabályzat](/azure/kusto/concepts/retentionpolicy) . |
   | Gyors gyorsítótár – időszak | *P31D* | Azt jelzi, hogy mennyi ideig maradnak az adat a gyorsítótárban. További információért lásd: [gyorsítótár](/azure/kusto/concepts/cachepolicy) -szabályzat. |

1. Futtassa a következő parancsot a létrehozott adatbázis megtekintéséhez:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Az erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, az az összes adatbázisát is törli. A fürt törléséhez használja a következő parancsot:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>További lépések

* [Adatbevitel az Azure Adatkezelő Python Library használatával](python-ingest-data.md)
