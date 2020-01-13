---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása a PowerShell használatával
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist a PowerShell használatával
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b855fde88173fe9a14a964ba1f9fd07aa74d85eb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911980"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sablon](create-cluster-database-resource-manager.md)  

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet alkalmazások, webhelyek, IoT-eszközök és egyebek nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre a PowerShell használatával. A PowerShell-parancsmagokat és parancsfájlokat Windows, Linux vagy [Azure Cloud Shell](../cloud-shell/overview.md) rendszeren is futtathatja az [az. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) használatával az Azure adatkezelő-fürtök és-adatbázisok létrehozásához és konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-parameters"></a>Paraméterek konfigurálása

A következő lépések nem szükségesek, ha parancsokat futtat Azure Cloud Shellban. Ha helyileg futtatja a CLI-t, kövesse az 1. & 2. lépést az Azure-ba való bejelentkezéshez és az aktuális előfizetés beállításához:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Állítsa be azt az előfizetést, amelyben létre szeretné hozni a fürtöt:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Amikor helyileg futtatja az Azure CLI-t vagy a Azure Cloud Shell, telepítenie kell az az. Kusto modult az eszközre:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő parancs használatával:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | Név | *mykustocluster* | A fürt kívánt neve.|
   | Termékváltozat | *D13_v2* | A fürthöz használni kívánt SKU. |
   | ResourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    További választható paramétereket is használhat, például a fürt kapacitását.

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Ha az eredmény `provisioningState`t tartalmaz a `Succeeded` értékkel, akkor a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő parancs használatával:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Annak a fürtnek a neve, ahová az adatbázist létre kívánja hozni.|
   | Név | *mykustodatabase* | Az adatbázis neve.|
   | ResourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |
   | SoftDeletePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a lekérdezés számára elérhető marad. |
   | HotCachePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a gyorsítótárban lesz tárolva. |

1. Futtassa a következő parancsot a létrehozott adatbázis megtekintéséhez:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Az erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, az az összes adatbázisát is törli. A fürt törléséhez használja a következő parancsot:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Következő lépések

* [További az. Kusto parancsok](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Adatbevitel az Azure Adatkezelő .NET Standard SDK-val (előzetes verzió)](net-standard-ingest-data.md)
