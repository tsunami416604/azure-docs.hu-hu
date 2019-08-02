---
title: Azure-Service Fabric önálló fürt frissítése | Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Service Fabric önálló fürtjének verzióját vagy konfigurációját.  T?
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: bf99d5d59354745508d8ca88abfc4b42fe608025
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599804"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Önálló Service Fabric-fürt frissítése és frissítése

Bármely modern rendszer esetében a minőségének megtervezése kulcsfontosságú a termék hosszú távú sikerességének megvalósításához. Az Azure Service Fabric önálló fürt egy saját erőforrás. Ez a cikk ismerteti, hogy mi frissíthető vagy frissíthető.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó háló verziójának szabályozása
Győződjön meg arról, hogy a fürt mindig [támogatott Service Fabric verziót](service-fabric-versions.md)futtat. Ha a Microsoft bejelenti Service Fabric új verziójának kiadását, az előző verzió a támogatás végére van megjelölve, a bejelentés dátumától számítva legalább 60 nappal. Az új kiadások a [Service Fabric csapat blogjában](https://blogs.msdn.microsoft.com/azureservicefabric/)jelennek meg. Az új kiadás elérhető az adott pontra való választáshoz.

Beállíthatja úgy a fürtöt, hogy a Microsoft által kiadott automatikus háló-frissítéseket fogadja, vagy manuálisan kiválaszthatja azt a támogatott Fabric-verziót, amelyre a fürtöt be szeretné állítani. További információért olvassa el [a fürtön futó Service Fabric verziójának frissítése](service-fabric-cluster-upgrade-windows-server.md)című témakört.

## <a name="customize-configuration-settings"></a>Konfigurációs beállítások testreszabása

A *ClusterConfig. JSON* fájlban számos különböző [konfigurációs beállítás](service-fabric-cluster-manifest.md) állítható be, például a fürt és a csomópont tulajdonságainak megbízhatósági szintje.  További információért olvassa el [az önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)című témakört.  Számos más, fejlettebb beállítások is testreszabhatók.  További információkért olvassa el [Service Fabric a fürt hálójának beállításait](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Csomópont tulajdonságainak megadása
Előfordulhat, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontokon futnak a fürtben. Előfordulhat például, hogy egyes számítási feladatok GPU vagy SSD-ket igényelnek, míg mások esetleg nem. A fürt minden egyes csomópont-típusához hozzáadhat egyéni csomópont-tulajdonságokat a fürtcsomópontok számára. Az elhelyezési megkötések az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomópont-tulajdonságot választanak ki. Az elhelyezési megkötések határozzák meg a szolgáltatások futtatásának helyét.

Az elhelyezési megkötések, a csomópont-tulajdonságok és a definiált beállítások használatával kapcsolatos részletekért olvassa el a [csomópont-tulajdonságok és az elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)című témakört.
 

## <a name="add-capacity-metrics"></a>Kapacitási mérőszámok hozzáadása
Az egyes csomópont-típusoknál hozzáadhat egyéni kapacitási mérőszámokat, amelyeket az alkalmazásokban használni szeretne a betöltés jelentéséhez. A kapacitási mérőszámok betöltésének jelentésével kapcsolatos részletekért tekintse meg a fürt és a metrikák, [valamint](service-fabric-cluster-resource-manager-metrics.md)a terhelések [leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) Service Fabric fürterőforrás-kezelő dokumentumait.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Az operációs rendszer javítása a fürtcsomópontok között
A javítási előkészítési alkalmazás (POA) egy Service Fabric alkalmazás, amely az operációs rendszer javítását automatizálja egy Service Fabric-fürtön állásidő nélkül. A [Windowshoz készült patch](service-fabric-patch-orchestration-application.md) -előkészítési alkalmazás üzembe helyezhető a fürtön úgy, hogy a javításokat koordinált módon telepítse, miközben a szolgáltatások rendelkezésre állását is megőrizheti. 


## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan szabhatja testre a [Service Fabric-fürtök](service-fabric-cluster-fabric-settings.md) néhány beállítását
* Ismerje meg, hogyan [méretezheti a fürtöt és ki](service-fabric-cluster-scale-up-down.md)
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md) megismerése

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
