---
title: Egy önálló Azure Service Fabric-fürt frissítése |} A Microsoft Docs
description: További információ az, illetve egy Azure Service Fabric önálló fürt konfigurációjának frissítése.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661497"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>A frissítés és önálló Service Fabric-fürt frissítése

Minden modern rendszeren bővíthetőség for designing fontos a termék hosszú távú sikert eléréséhez. Egy Azure Service Fabric önálló fürt saját erőforrás. Ez a cikk bemutatja, milyen frissíthetőek vagy frissíteni.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fabric-verziót a fürtön futó szabályozása
Győződjön meg arról, hogy mindig fut-e a fürt egy [Service Fabric verziója támogatott](service-fabric-versions.md). A kiadás a Service Fabric egy új verzióját a Microsoft bejelenti, ha az előző verziója legalább a közlemény 60 nap után támogatásuk van megjelölve. Új kiadásokkal már bejelentettünk [a Service Fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/). Ezen a ponton kiválasztása az új kiadásban érhető el.

Beállíthatja, hogy a fürt automatikus hálófrissítések fogadásához, azok a Microsoft által kiadott, vagy ha manuálisan végrehajtja a rendszer a fürt támogatott fabric verziója. További információkért olvassa el [a fürtön futó Service Fabric verziófrissítéséhez](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Konfigurációs beállítások testre szabása

Számos különböző [konfigurációs beállítások](service-fabric-cluster-manifest.md) állítható be a *ClusterConfig.json* fájl, például a fürt és a csomópont tulajdonságait megbízhatósági szintjét.  További tudnivalókért olvassa el a [önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md).  Számos más, speciális, beállítások is testre.  További információkért olvassa el [Service Fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Csomópont-tulajdonságok meghatározása
Néha érdemes győződjön meg arról, hogy bizonyos munkaterhelések csak bizonyos típusú, a fürtben található csomópontok futtassa. Például néhány számítási feladat lehet szükség gpu-kat vagy SSD-k, míg mások nem. A fürtben csomóponttípusok mindegyikéhez fürtcsomópontok egyéni csomópont tulajdonságokat adhat hozzá. Elhelyezési korlátozások az egyes szolgáltatásai, válassza ki egy vagy több csomópont-tulajdonságok csatolt utasításokat. Elhelyezési korlátozások határozza meg, ahol szolgáltatásainak futnia kell.

Elhelyezési korlátozások, a csomópont-tulajdonságok és hogyan adhat meg hozzájuk használatára vonatkozó további tudnivalókért olvassa el [csomópont-tulajdonságok és elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Adja hozzá a kapacitási mérőszámot
Minden egyes csomóponttípus betöltésének jelentéséhez az alkalmazásban használni kívánt egyéni kapacitási mérőszámokat adhat hozzá. Betöltésének jelentéséhez a kapacitási mérőszámot használatának részletes ismertetéséért tekintse meg a Service Fabric-fürt Resource Manager-dokumentumok a [a fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md) és [metrikák és a terhelés](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Az operációs rendszer, a fürtcsomópontok javítása
A patch orchestration application (POA) a Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javításának Service Fabric-fürt, üzemkimaradás nélkül. A [Patch Orchestration Application for Windows](service-fabric-patch-orchestration-application.md) telepíthetők a javítások telepítése összehangolt módon folyamatosan elérhető szolgáltatások megtartva a fürtön. 


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre az egyes a [service fabric-fürt hálóbeállítások](service-fabric-cluster-fabric-settings.md)
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md)
* Ismerje meg [alkalmazásfrissítések](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
