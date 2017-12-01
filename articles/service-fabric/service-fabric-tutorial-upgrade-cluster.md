---
title: "Azure Service Fabric-futtatókörnyezet frissítése |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet frissíteni az Azure által üzemeltetett Service Fabric-fürt a futásidejű a PowerShell használatával."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.openlocfilehash: 5057a741a60bf9632f15dbc47236791e06f5bf6b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="upgrade-the-runtime-of-a-service-fabric-cluster"></a>A Service Fabric-fürt futásidejű frissítése

Ez az oktatóanyag része egy sor és bemutatja, hogyan lehet frissíteni a Service Fabric-futtatókörnyezet egy Azure Service Fabric-fürt. Ez az oktatóanyag kijelző futó Azure Service Fabric-fürtök számára készült, és nem vonatkozik a különálló Service Fabric fürt.

> [!WARNING]
> Ez az oktatóanyag részét PowerShell szükséges. A fürt futásidejű frissítésére vonatkozó támogatási még nem támogatott az Azure CLI-eszközök által. Másik lehetőségként a fürt frissítése a portálon. További információkért lásd: [egy Azure Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md).

Ha a fürt már fut a legújabb Service Fabric-futtatókörnyezet, nem szükséges ehhez a lépéshez. Azonban ez a cikk az Azure Service Fabric-fürt bármely támogatott futásidejű telepítendő használható.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Olvassa el a fürt verziószáma
> * A fürt verzió

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) Azure-sablon használatával
> * [Bejövő vagy kimenő fürt méretezése](service-fabric-tutorial-scale-cluster.md)
> * A futtatókörnyezet egy fürt frissítése
> * [A Service Fabric az API Management központi telepítését](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) vagy [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) az Azure-on
- Ha telepít egy Windows-fürt, a Windows környezet beállítása. Telepítés [Visual Studio 2017](http://www.visualstudio.com) és a **Azure fejlesztési**, **ASP.NET és a webes fejlesztési**, és **.NET Core platformfüggetlen fejlesztésekhez**munkaterhelések.  Hozzon létre egy [.NET fejlesztőkörnyezet](service-fabric-get-started.md).
- Ha a Linux-fürt központi telepítése, állítsa be a Java-fejlesztőkörnyezet a [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md).  Telepítse a [háló CLI szolgáltatás](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Az Azure-fiókjába történő bejelentkezéshez jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>A futásidejű verzióra

Miután csatlakozott az Azure-ba, jelölve az előfizetést, amely tartalmazza a Service Fabric-fürt, a fürt futásidejű verzióját kaphat.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Vagy csak az összes fürt listájának lekérése az előfizetéshez a következő:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Megjegyzés: a **ClusterCodeVersion** érték. Ez az érték a következő szakaszban használható.

## <a name="upgrade-the-runtime"></a>A futtatókörnyezet frissítése

Az érték **ClusterCodeVersion** az előző szakaszából a `Get-ServiceFabricRuntimeUpgradeVersion` derítsen fel, melyik verziója érhetők el, és frissítse parancsmaggal. Ez a parancsmag csak a számítógép csatlakozik az internethez futtathatja. Például, ha szeretné, hogy milyen sikerült frissítenie verziójából származó futtatókörnyezet-verzió jelenik meg `5.7.198.9494`, használja a következő parancsot:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Verziók listáját az Azure Service Fabric-fürt frissítése újabb futtatókörnyezettel állapítható meg. Például ha verzió `6.0.219.9494` frissítsen, az alábbi parancs segítségével frissítse a fürt rendelkezésére áll.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> A fürt futásidejű frissítés egy hosszú ideig is eltarthat. PowerShell le van tiltva, amíg a frissítés van folyamatban. A frissítés állapotának ellenőrzéséhez használhatja egy másik PowerShell-munkamenetben.

A frissítés állapota figyelhető vagy PowerShell vagy a `sfctl` CLI-t.

Először csatlakozzon a fürthöz az oktatóanyag első részét létrehozott SSL-tanúsítvánnyal. Használja a `Connect-ServiceFabricCluster` parancsmag vagy `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Következő lépésként az `Get-ServiceFabricClusterUpgrade` vagy `sfctl cluster upgrade-status` állapotának megjelenítése. Hasonló a következő eredmény jelenik meg.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>Összegzés
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A fürt futásidejű verziójának
> * A fürt futásidejű frissítése
> * A frissítés figyelése

A következő előzetes a következő oktatóanyag segítséget nyújt a Service Fabric-fürt API Management telepítése.
> [!div class="nextstepaction"]
> [A Service Fabric az API Management központi telepítését](service-fabric-tutorial-deploy-api-management.md)
