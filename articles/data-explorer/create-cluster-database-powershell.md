---
title: Azure Data Explorer-fürt & adatbázis létrehozása a Powershell használatával
description: Megtudhatja, hogyan hozhat létre Azure Data Explorer-fürtöt és -adatbázist a PowerShell használatával
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560592"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Azure Data Explorer-fürt és-adatbázis létrehozása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM sablon](create-cluster-database-resource-manager.md)  

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. Ebben a cikkben hozzon létre egy fürtöt és egy adatbázist a Powershell használatával. PowerShell-parancsmagokat és parancsfájlokat futtathat Windowson, Linuxon vagy [az Azure Cloud Shellben](../cloud-shell/overview.md) az [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) segítségével az Azure Data Explorer-fürtök és -adatbázisok létrehozásához és konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a cikk megköveteli az Azure CLI 2.0.4-es vagy újabb verziója. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-parameters"></a>Paraméterek konfigurálása

A következő lépések nem szükségesek, ha parancsokat futtat az Azure Cloud Shellben. Ha helyileg futtatja a CLI-t, kövesse az 1 & 2.

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Állítsa be az előfizetést oda, ahol létre szeretné hozni a fürtöt:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Az Azure CLI helyi vagy azure cloud shell ben történő futtatásakor telepítenie kell az Az.Kusto modult az eszközére:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. Hozza létre a fürtöt a következő paranccsal:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | Név | *mykustocluster* | A fürt kívánt neve.|
   | SKU | *D13_v2* | A fürthöz használt termékváltozat. |
   | ResourceGroupName | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |

    További választható paraméterek, például a fürt kapacitása további választható paramétereket is használhat.

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt sikeresen létrejött-e:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Ha az `provisioningState` eredmény `Succeeded` tartalmazza az értéket, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer-fürtben

1. Hozza létre az adatbázist a következő paranccsal:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Annak a fürtnek a neve, amelyben az adatbázis létrejön.|
   | Név | *mykustoadatbázis* | Az adatbázis neve.|
   | ResourceGroupName | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |
   | SoftDeletePeriod között | *3650:00:00:00* | Az az idő, amerre az adatok lekérdezhetők. |
   | HotCacheIdőszak | *3650:00:00:00* | Az az idő, amerre az adatok a gyorsítótárban maradnak. |

1. A létrehozott adatbázis megtekintéséhez futtassa a következő parancsot:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Fürt törlésekor az összes benne lévő adatbázist is törli. A fürt törléséhez használja a következő parancsot:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>További lépések

* [További Az.Kusto parancsok](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Adatok betöltése az Azure Data Explorer .NET Standard SDK (előzetes verzió) használatával](net-standard-ingest-data.md)
