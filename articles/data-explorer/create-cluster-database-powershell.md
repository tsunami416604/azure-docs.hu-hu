---
title: 'Gyors útmutató: Az Azure Data Explorer fürt és adatbázis létrehozása PowerShell használatával'
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer fürt és adatbázis PowerShell-lel
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 553564be494b4175cba937b583d49ad84a8d0e66
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526520"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Az Azure Data Explorer fürt és adatbázis létrehozása PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához, először hozzon létre egy fürtöt, és az adott fürt egy vagy több adatbázis létrehozása. Ezután (betöltés) adatokat az adatbázisba fogadására, így vonatkozóan, lekérdezéseket futtathat. Ebben a rövid útmutatóban létrehozhat egy fürtöt, és a egy adatbázis Powershell használatával. PowerShell-parancsmagok és parancsfájlok is futtathatja a Windows, Linux, vagy a [Azure Cloud Shell](../cloud-shell/overview.md) a [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) létrehozása és konfigurálása az Azure Data Explorer fürtök és az adatbázisok.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata az Azure CLI, az ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-parameters"></a>Paraméterek konfigurálása

A következő lépéseket nem szükséges, ha a parancsok az Azure Cloud Shellben futtatja. Ha helyileg futtatja a CLI-t, hajtsa végre az 1. és 2. Jelentkezzen be az Azure-ba, és beállítása a jelenlegi előfizetésében lépéseket:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Állítsa be az előfizetést, ahol azt szeretné, hogy a fürt létrehozása:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Amikor fut az Azure CLI-vel, helyileg vagy az Azure Cloud Shell, az eszközön a Az.Kusto modul telepítenie kell:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. A fürt létrehozásához a következő paranccsal:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | Name (Név) | *mykustocluster* | A fürt kívánt nevét.|
   | SKU | *D13_v2* | A Termékváltozat a fürthöz használt. |
   | ResourceGroupName | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.

1. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Ha az eredmény tartalmazza `provisioningState` együtt a `Succeeded` érték, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Az adatbázis létrehozása a következő paranccsal:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | ClusterName | *mykustocluster* | A fürt, ahol létrejön az adatbázis neve.|
   | Name (Név) | *mykustodatabase* | Az adatbázis neve.|
   | ResourceGroupName | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | SoftDeletePeriod | *3650:00:00:00* | Mennyi ideig megtartott adatok lekérdezhetők. |
   | HotCachePeriod | *3650:00:00:00* | Mennyi ideig megtartott adatok a gyorsítótárban. |

1. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. A következő paranccsal törölje a fürtöt:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>További lépések

* [További Az.Kusto parancsok](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Rövid útmutató: Adatokat az Azure SDK-val Data Explorer .NET Standard (előzetes verzió)](net-standard-ingest-data.md)
