---
title: "Különbségek az Azure Service Fabric Linux- és Windows-verziója között | Microsoft Docs"
description: "Az Azure Service Fabric előzetes verzió Linux- és Windows-verziója közötti különbségek."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 7b80bb7d4a4e6a1b4cf47ce87200f47339785c53
ms.contentlocale: hu-hu
ms.lasthandoff: 08/24/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>A Service Fabric Linux (előzetes verziójú) és Windows (általánosan elérhető) rendszerhez készült verziója közötti különbségek

Mivel a Service Fabric Linux rendszeren még előzetes verzióban van, ezért néhány szolgáltatás csak Windows rendszeren támogatott, Linuxon nem. Idővel ugyanazok a szolgáltatások lesznek elérhetőek, amikor a Service Fabric általánosan elérhetővé válik Linux rendszeren. A jövőbeli kiadásokban a funkciók eltérései egyre csekélyebbé válnak. A legújabb elérhető kiadások (Windows rendszeren 5.6, Linuxon 5.5) között az alábbi eltérések állnak fenn: 

* Reliable Collections (és a Reliable Stateful Services) 
* ReverseProxy 
* Önálló telepítő 
* Jegyzékfájlok XML-sémaérvényesítése 
* Konzol-átirányítás 
* Fault Analysis Service (FAS)
* Docker compose, kötet- és naplózási illesztők tárolókhoz 
* Tárolók és szolgáltatások erőforrás-szabályozása 
* DNS-szolgáltatás
* Azure Active Directory-támogatás
* Egyes PowerShell-parancsok parancssori felületi megfelelője 
* Csak a PowerShell-parancsok egy része futtatható Linux-fürtökön (a következő szakaszban leírtak szerint).

>[!NOTE]
>A konzolátirányítás nem támogatott éles fürtökben, még Windows rendszeren sem.

A fejlesztői eszközök eltérnek Windows és Linux rendszeren. Windows rendszeren a VisualStudio, a PowerShell, a VSTS és az ETW, míg Linuxon a Yeoman, az Eclipse, a Jenkins és az LTTng érhető el.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>PowerShell-parancsmagok, amelyek nem működnek Linux rendszerű Service Fabric-fürtökön

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Következő lépések
* [A fejlesztőkörnyezet előkészítése Linuxon](service-fabric-get-started-linux.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Az első CSharp-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
* [A Service Fabric parancssori felület használata az alkalmazások kezeléséhez](service-fabric-application-lifecycle-sfctl.md)

