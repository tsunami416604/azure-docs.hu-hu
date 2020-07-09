---
title: Önálló fürt konfigurációjának frissítése
description: Ismerje meg, hogyan frissítheti az önálló Service Fabric fürtöt futtató konfigurációt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8d0279cc323f7eee87feb2a596a4c2df0b4667e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82790847"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Önálló fürt konfigurációjának frissítése 

Bármely modern rendszer esetében a frissítés a termék hosszú távú sikerességének kulcsa. Az Azure Service Fabric-fürt egy saját erőforrás. Ez a cikk az önálló Service Fabric-fürt konfigurációs beállításainak frissítését ismerteti.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>A fürt beállításainak testreszabása a ClusterConfig.jsfájlban
Az önálló fürtök konfigurálása a *ClusterConfig.js* fájlon keresztül történik. További információ a különböző beállításokról: [önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md).

A (z `fabricSettings` ) *ClusterConfig.jsa* [fürt tulajdonságai](./service-fabric-cluster-manifest.md#cluster-properties) szakaszának szakasza alatt adhatja hozzá, frissítheti vagy eltávolíthatja a beállításokat. 

A következő JSON például egy új beállítást hoz létre a *diagnosztika* szakasz *MaxDiskQuotaInMB* `fabricSettings` :

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

Miután módosította a ClusterConfig.jsbeállításait a fájlon, [tesztelje a fürtöt](#test-the-cluster-configuration) , majd [frissítse a fürt konfigurációját](#upgrade-the-cluster-configuration) , hogy alkalmazza a beállításokat a fürtön. 

## <a name="test-the-cluster-configuration"></a>A fürt konfigurációjának tesztelése
A konfiguráció frissítésének megkezdése előtt tesztelheti az új fürtkonfiguráció JSON-t a következő PowerShell-szkript futtatásával az önálló csomagban:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Vagy használja a következő parancsfájlt:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Egyes konfigurációk nem frissíthetők, például végpontok, fürt neve, csomópont IP-címe stb. Az új fürtkonfiguráció JSON a régivel van tesztelve, és hibák léptek fel a PowerShell-ablakban, ha probléma merül fel.

## <a name="upgrade-the-cluster-configuration"></a>A fürtkonfiguráció frissítése
A fürt konfigurációjának frissítéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)parancsot. A konfiguráció frissítését a frissítési tartomány dolgozza fel.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Fürtkonfiguráció konfigurációjának frissítése
A fürtcsomópontok közötti hitelesítéshez a rendszer a fürt tanúsítványát használja. A tanúsítvány-átváltást óvatosan kell elvégezni, mert a hiba blokkolja a fürtcsomópontok közötti kommunikációt.

Négy lehetőség támogatott:  

* Egyetlen tanúsítvány frissítése: a frissítési útvonal az A tanúsítvány (elsődleges) – > B tanúsítvány (elsődleges) – > tanúsítvány C (elsődleges) – >....

* Dupla tanúsítvány frissítése: a frissítési útvonal az a tanúsítvány (elsődleges) – > tanúsítvány A (elsődleges) és B (másodlagos) – > B tanúsítvány (elsődleges) – > B (elsődleges) és C (másodlagos) – > C (elsődleges) tanúsítvány, >....

* Tanúsítvány típusának frissítése: ujjlenyomat-alapú tanúsítvány-konfiguráció < – > Köznapinév-alapú tanúsítvány-konfiguráció. Például: A Tanúsítvány ujjlenyomata A (elsődleges) és A B ujjlenyomat (másodlagos) – > a C Köznapinév tanúsítvány.

* Tanúsítvány kiállítói ujjlenyomatának frissítése: a frissítési útvonal a következő tanúsítvány: CN = A, IssuerThumbprint = IT1 (elsődleges)-> tanúsítvány CN = A, IssuerThumbprint = IT1, IT2 (elsődleges)-> tanúsítvány CN = A, IssuerThumbprint = IT2 (elsődleges).


## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan szabhatja testre a [Service Fabric fürtkonfiguráció beállításait](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [méretezheti a fürtöt a és a](service-fabric-cluster-scale-in-out.md)szolgáltatásban.
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md)megismerése.

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
