---
title: 'Gyors útmutató: Az Azure Data Explorer fürt és adatbázis létrehozása PowerShell használatával'
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer fürt és adatbázis PowerShell-lel
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287523"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Az Azure Data Explorer fürt és adatbázis létrehozása PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Ebben a rövid útmutató egy Azure Data Explorer fürt és adatbázis létrehozása PowerShell használatával.

PowerShell-parancsmagok és parancsfájlok is futtathatja a Windows, Linux, vagy a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) létrehozása és konfigurálása [Azure adatkezelő](https://docs.microsoft.com/azure/kusto/ ).

A [ **Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Az Azure PowerShell-lel és **Az.Kusto**, a következő feladatok végrehajtására:

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="configure-parameters"></a>Paraméterek konfigurálása

A következő lépéseket nem szükséges, ha a parancsok az Azure Cloud Shellben futtatja. Ha helyileg futtatja a parancssori Felületet, kövesse az alábbi lépéseket, jelentkezzen be az Azure-ba, és állítsa be a jelenlegi előfizetésében:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ha azt szeretné, hogy a fürt létrehozása az előfizetés beállításához.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Az eszköz Az.Kusto modul telepítése:
    
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

2. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

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

2. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

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

További Az.Kusto parancsokat annak [ **Itt**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Rövid útmutató: Adatokat az Azure SDK-val Data Explorer .NET Standard (előzetes verzió)](net-standard-ingest-data.md)
