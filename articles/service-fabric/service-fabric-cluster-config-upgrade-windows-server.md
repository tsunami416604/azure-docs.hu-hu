---
title: Önálló Azure Service Fabric-fürt konfigurációjának frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítse a konfigurációt, amely egy önálló Service Fabric-fürtön futtatja.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 88846845f1f8ffc71fb193e134a18ec38f619141
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855181"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Önálló fürt konfigurációjának frissítése 

Minden olyan modern rendszerbe arra, hogy frissítse a termék hosszú távú sikeréhez kulcsfontosságú, hogy. Azure Service Fabric-fürt saját erőforrás. Ez a cikk ismerteti az önálló Service Fabric-fürt konfigurációs beállításainak frissítése.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Testre szabhatja a ClusterConfig.json fájlban fürtbeállítások
Önálló fürtök úgy vannak konfigurálva, keresztül a *ClusterConfig.json* fájlt. A különböző beállításokkal kapcsolatos további információkért lásd: [egy különálló Windows-fürt konfigurációs beállításainak](service-fabric-cluster-manifest.md).

Hozzáadása, frissítése vagy távolítsa el a beállításokat a `fabricSettings` szakaszba a [fürt tulajdonságai](./service-fabric-cluster-manifest.md#cluster-properties) szakasz *ClusterConfig.json*. 

Például a következő JSON ad hozzá egy új beállítás *MaxDiskQuotaInMB* , a *diagnosztikai* szakaszba `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Miután módosította a beállításokat a ClusterConfig.json fájlban [a fürt konfigurációjának tesztelése](#test-the-cluster-configuration) , majd [a fürt konfigurációjának frissítése](#upgrade-the-cluster-configuration) kívánja alkalmazni a fürtön. 

## <a name="test-the-cluster-configuration"></a>A fürt konfigurációjának tesztelése
A konfiguráció frissítése kezdeményez, mielőtt a különálló csomag a következő PowerShell-parancsfájl futtatásával tesztelheti az új fürt konfigurációs JSON-fájlt:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Vagy használja ezt a parancsfájlt:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Bizonyos konfigurációk nem lehet frissíteni, például a végpontok, fürt nevét, IP-Címét, stb. Az új fürt JSON konfigurációs van tesztelve lett a régit, és a PowerShell-ablakban hibát jelez, ha probléma van.

## <a name="upgrade-the-cluster-configuration"></a>A fürt konfigurációjának frissítése
A fürt konfiguráció frissítése a frissítéshez futtassa [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A konfiguráció frissítése frissítési tartomány által feldolgozott frissítési tartományban.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Fürt tanúsítvány konfigurációjának frissítése
A fürttanúsítvány fürtcsomópontok közötti hitelesítéshez használatos. A tanúsítványváltás extra körültekintően kell hajtható végre, mert a sikertelen blokkolja a fürt csomópontok közötti kommunikációt.

Négy beállítások támogatottak:  

* Egy tanúsítvány frissítése: A frissítés elérési útja (elsődleges) -> tanúsítvány B (elsődleges) tanúsítvány tanúsítvány C (elsődleges) -> ->...

* Kettős tanúsítvány frissítése: A frissítés elérési útja (elsődleges) -> Tanúsítvány tanúsítvány-(elsődleges), és B (másodlagos) -> tanúsítvány B (elsődleges) -> (elsődleges) tanúsítvány B és C (másodlagos) > tanúsítvány C (elsődleges) ->...

* Tanúsítvány típusa frissítés: tanúsítvány ujjlenyomat-alapú konfigurációs <> – Köznapinév-alapú tanúsítvány konfigurálását. Ha például a tanúsítvány-ujjlenyomat (elsődleges) és ujjlenyomatát B (másodlagos) -> tanúsítvány CommonName c

* Tanúsítvány-kibocsátó ujjlenyomatát frissítés: A frissítési útvonalat a tanúsítvány CN = A, IssuerThumbprint et1 = (elsődleges) tanúsítvány CN -> A, IssuerThumbprint = et1, et2 = (elsődleges) tanúsítvány CN -> A, IssuerThumbprint = et2 = (elsődleges).


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre az egyes [Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md).
* Ismerje meg [alkalmazásfrissítések](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
