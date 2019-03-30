---
title: A helyi Azure Service Fabric-fürt beállításának hibaelhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti a helyi fejlesztési fürt kapcsolatos készlete
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662805"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>A helyi fejlesztési fürt beállításának hibaelhárítása
Ha a helyi Azure Service Fabric fejlesztési fürtöt használata során problémába futtat, tekintse át az alábbi javaslatok a lehetséges megoldásokról.

## <a name="cluster-setup-failures"></a>A fürtbeállítási hibákhoz
### <a name="cannot-clean-up-service-fabric-logs"></a>Nem lehet tisztítást a Service Fabric-naplók
#### <a name="problem"></a>Probléma
A DevClusterSetup szkript futtatásakor a következő hiba jelenik meg:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Megoldás
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg egy új PowerShell-ablakot rendszergazdaként. Most már sikeresen futtathatja a szkriptet.

## <a name="cluster-connection-failures"></a>Fürt csatlakozási hibák

### <a name="type-initialization-exception"></a>Az inicializálás kivétel típusa
#### <a name="problem"></a>Probléma
Amikor kapcsolódik a fürthöz, a PowerShell, a hibaüzenetet a TypeInitializationException System.Fabric.Common.AppTrace a.

#### <a name="solution"></a>Megoldás
A path változóban nem lett megfelelően beállítva a telepítés során. Jelentkezzen ki a Windows, és jelentkezzen be újra. Ez frissíti a elérési utat.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Fürt létesített kapcsolat megszakad, a "Objektum be van zárva"
#### <a name="problem"></a>Probléma
Connect-ServiceFabricCluster hívása sikertelen, és egy ehhez hasonló:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Megoldás
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg egy új PowerShell-ablakot rendszergazdaként.

### <a name="fabric-connection-denied-exception"></a>Fabric-kapcsolat elutasítva kivétel
#### <a name="problem"></a>Probléma
Hibakeresés a Visual Studióból, kap egy FabricConnectionDeniedException hiba.

#### <a name="solution"></a>Megoldás
Ez a hiba általában akkor fordul elő, amikor megpróbálja manuálisan indítsa el a szolgáltatás gazdafolyamatokon.

Győződjön meg arról, hogy nem kell minden olyan service-projektek, a megoldás indítási projektek beállítása. Csak a Service Fabric-alkalmazás projektek indítási projektként kell beállítani.

> [!TIP]
> Ha a beállítás a telepítés után a helyi fürtön megkezdi rendellenesen viselkedik, visszaállíthatja rajta a helyi cluster manager rendszertálca-alkalmazásra használatával. Ezzel eltávolítja a meglévő fürt és a egy új beállítása. Vegye figyelembe, hogy üzembe helyezett alkalmazások és a kapcsolódó adatok el lesznek távolítva.
> 
> 

## <a name="next-steps"></a>További lépések
* [Ismertetése és hibaelhárítása a fürtön a rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)

