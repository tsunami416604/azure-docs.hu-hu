---
title: Önálló fürt konfigurációjának frissítése
description: Ismerje meg, hogyan frissítheti az önálló Service Fabric-fürtöt futtató konfigurációt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610122"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Önálló fürt konfigurációjának frissítése 

Minden modern rendszer, a frissítés képessége kulcsfontosságú a hosszú távú sikere a termék. Az Azure Service Fabric-fürt egy saját erőforrás. Ez a cikk ismerteti, hogyan frissítheti az önálló Service Fabric-fürt konfigurációs beállításait.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Fürtbeállítások testreszabása a ClusterConfig.json fájlban
Az önálló fürtök konfigurálása a *ClusterConfig.json* fájlon keresztül történt. A különböző beállításokról az [önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)című témakörben olvashat bővebben.

A `fabricSettings` *clusterconfig.json* [fürttulajdonságok](./service-fabric-cluster-manifest.md#cluster-properties) szakaszának szakaszában adhat hozzá, frissíthet vagy eltávolíthat beállításokat. 

A következő JSON például hozzáad egy új *MaxDiskQuotaInMB* `fabricSettings`beállítást a *Diagnosztika* szakaszhoz:

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

Miután módosította a FürtConfig.json fájl beállításait, [tesztelje a fürtkonfigurációt,](#test-the-cluster-configuration) majd [frissítse a fürtkonfigurációt,](#upgrade-the-cluster-configuration) hogy alkalmazza a beállításokat a fürtre. 

## <a name="test-the-cluster-configuration"></a>A fürtkonfiguráció tesztelése
A konfigurációs frissítés megkezdése előtt tesztelheti az új fürtkonfigurációjának JSON-ját a következő PowerShell-parancsfájl futtatásával az önálló csomagban:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Vagy használja ezt a szkriptet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Egyes konfigurációk nem frissíthetők, például végpontok, fürtnév, csomópont IP-címe stb. Az új fürtkonfiguráció JSON a régivel szemben, és hibákat okoz a PowerShell ablakban, ha probléma merül fel.

## <a name="upgrade-the-cluster-configuration"></a>A fürtkonfiguráció frissítése
A fürtkonfiguráció frissítésének frissítéséhez futtassa a [Start-ServiceFabricConfigurationUpgrade segédprogramot.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) A konfigurációs frissítés frissítési tartomány továbbfejlesztett tartománydolgozza fel.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Fürttanúsítvány-konfiguráció frissítése
A fürtcsomópontok közötti hitelesítéshez fürttanúsítvány t használnak. A tanúsítványváltást fokozott óvatossággal kell végrehajtani, mert a hiba blokkolja a fürtcsomópontok közötti kommunikációt.

Négy lehetőség támogatott:  

* Egyetlen tanúsítvány frissítés: A frissítési útvonal az A tanúsítvány (elsődleges) -> B tanúsítvány (elsődleges) -> C tanúsítvány (elsődleges) ->....

* Dupla tanúsítvány frissítése: A frissítési útvonal az A tanúsítvány (elsődleges) -> A tanúsítvány (elsődleges) és B (másodlagos) -> B tanúsítvány (elsődleges) -> B tanúsítvány (elsődleges) és C (másodlagos) -> C tanúsítvány (elsődleges) ->....

* Tanúsítványtípus-frissítés: Ujjlenyomatalapú tanúsítványkonfiguráció <-> CommonName-alapú tanúsítványkonfiguráció. Például a Tanúsítvány ujjlenyomata A (Elsődleges) és a B ujjlenyomat (másodlagos) -> A tanúsítvány commonname C.

* A tanúsítvány kiállítójának ujjlenyomat-frissítése: A frissítési útvonal a Certificate CN=A,IssuerThumbprint=IT1 (Primary) -> Certificate CN=A,IssuerThumbprint=IT1,IT2 (Primary) -> Certificate CN=A,IssuerThumbprint=IT2 (Elsődleges).


## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan szabhatja testre a [Service Fabric egyes fürtbeállításait.](service-fabric-cluster-fabric-settings.md)
* További információ [a fürt be- és kiméretezése.](service-fabric-cluster-scale-up-down.md)
* További információ az [alkalmazásfrissítésekről.](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
