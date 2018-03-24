---
title: A helyi Azure Service Fabric-fürt beállításának hibaelhárítása |} Microsoft Docs
description: Ez a cikk ismerteti a javaslatok az a helyi fejlesztési fürtök készlete
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 6879a24df434d5bf69c9ba14aa00cdc9cd67df57
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>A helyi fejlesztési fürtöt való beállításának hibaelhárítása
Ha problémát tapasztal az Azure Service Fabric helyi fejlesztési fürtök való interakció során, tekintse át a következő javaslatok a lehetséges megoldásokról.

## <a name="cluster-setup-failures"></a>Fürt telepítési hiba
### <a name="cannot-clean-up-service-fabric-logs"></a>Nem lehet tisztítást végezni a Service Fabric-naplók
#### <a name="problem"></a>Probléma
A DevClusterSetup parancsprogram futtatása közben a következő hiba jelenik meg:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Megoldás
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg rendszergazdaként egy új PowerShell-ablakot. Most sikeresen futtathatja a parancsfájlt.

## <a name="cluster-connection-failures"></a>Fürt kapcsolódási hibák

### <a name="type-initialization-exception"></a>Írja be az inicializálási kivétel
#### <a name="problem"></a>Probléma
Kapcsolódás esetén a fürt PowerShell, a TypeInitializationException hibát látja a System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Megoldás
Az elérésiút-változó beállítása helytelen telepítés során. Jelentkeznie a Windowsból, és jelentkezzen be. Ez frissíti a elérési utat.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Fürt létesített kapcsolat megszakad, a "A objektum be van zárva"
#### <a name="problem"></a>Probléma
Connect-ServiceFabricCluster hívásakor meghiúsul, és ehhez hasonló hibát:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Megoldás
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg rendszergazdaként egy új PowerShell-ablakot.

### <a name="fabric-connection-denied-exception"></a>Háló kapcsolat megtagadva kivétel
#### <a name="problem"></a>Probléma
Hibakereséshez a következőből: Visual Studio, a FabricConnectionDeniedException hiba nyílik meg.

#### <a name="solution"></a>Megoldás
Ez a hiba általában akkor fordul elő, manuálisan indítsa el a szolgáltatás gazdafolyamatokon megkísérlésekor.

Győződjön meg arról, hogy nem kell minden service projektek állítja be kiindulási projektet a megoldásban. Csak a Service Fabric application projektek indítási projektként kell beállítani.

> [!TIP]
> Ha a telepítés után a helyi fürtön megkezdi a rendellenes viselkedését, visszaállíthatja rajta a helyi fürt manager rendszer rendszertálca alkalmazásban. Ezzel eltávolítja a meglévő fürt, és állítsa be egy újat. Vegye figyelembe, hogy a telepített alkalmazások és a kapcsolódó adatokat távolítja el.
> 
> 

## <a name="next-steps"></a>További lépések
* [Ismertetése és hibaelhárítása a fürt rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)

