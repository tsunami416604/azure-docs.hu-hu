---
title: Az Azure Service Fabric helyi fürtjének beállítása – problémamegoldás
description: Ez a cikk a helyi fejlesztési fürt hibaelhárítására vonatkozó javaslatokat ismerteti
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465499"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Helyi fejlesztői fürtkonfiguráció hibaelhárítása
Ha a helyi Azure Service Fabric-fejlesztői fürttel való interakció során problémát okoz, tekintse át az alábbi javaslatokat a lehetséges megoldásokra vonatkozóan.

## <a name="cluster-setup-failures"></a>Fürttelepítési hibák
### <a name="cannot-clean-up-service-fabric-logs"></a>A Service Fabric-naplók nem törölhető
#### <a name="problem"></a>Probléma
A DevClusterSetup parancsfájl futtatása közben a következő hiba jelenik meg:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Megoldás
Zárja be az aktuális PowerShell-ablakot, és nyisson meg egy új PowerShell-ablakot rendszergazdaként. Most már sikeresen futtathatja a parancsfájlt.

## <a name="cluster-connection-failures"></a>Fürtkapcsolati hibák

### <a name="type-initialization-exception"></a>Típusinicializálás kivétel
#### <a name="problem"></a>Probléma
Amikor a fürthöz csatlakozik a PowerShellben, a System.Fabric.Common.AppTrace hiba typeinitializationexception jelenik meg.

#### <a name="solution"></a>Megoldás
Az elérési út változója nem lett megfelelően beállítva a telepítés során. Jelentkezzen ki a Windows rendszerből, majd jelentkezzen be újra. Ez felfrissíti az utadat.

### <a name="cluster-connection-fails-with-object-is-closed"></a>A fürtkapcsolat megszakad az "Objektum bevan zárva"
#### <a name="problem"></a>Probléma
A Connect-ServiceFabricCluster hívása a következőhöz hasonló hibával sikertelen:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Megoldás
Zárja be az aktuális PowerShell-ablakot, és nyisson meg egy új PowerShell-ablakot rendszergazdaként.

### <a name="fabric-connection-denied-exception"></a>Hálókapcsolat megtagadva kivétel
#### <a name="problem"></a>Probléma
Amikor a Visual Studio hibakeresés, kap egy FabricConnectionDeniedException hiba.

#### <a name="solution"></a>Megoldás
Ez a hiba általában akkor fordul elő, amikor manuálisan próbál elindítani egy szolgáltatásgazda folyamatot.

Győződjön meg arról, hogy a megoldásban nincs beállítva indítási projektként beállított szolgáltatási projekt. Csak a Service Fabric alkalmazásprojekteket kell beállítani indítási projektekként.

> [!TIP]
> Ha a telepítést követően a helyi fürt rendellenesen kezd el dolgozni, a helyi fürtkezelő rendszertálca-alkalmazással alaphelyzetbe állíthatja azt. Ezzel eltávolítja a meglévő fürtöt, és újat állít be. Vegye figyelembe, hogy az összes telepített alkalmazás és a kapcsolódó adatok törlődnek.
> 
> 

## <a name="next-steps"></a>További lépések
* [A fürt ismertetése és hibaelhárítása a rendszerállapot-jelentésekkel](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)

