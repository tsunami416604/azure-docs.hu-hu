---
title: "A helyi Service Fabric-fürt beállításának hibaelhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a javaslatok az a helyi fejlesztési fürtök készlete"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>A helyi fejlesztési fürtöt való beállításának hibaelhárítása
Ha problémát tapasztal az Azure Service Fabric helyi fejlesztési fürtök való interakció során, tekintse át a következő javaslatok a lehetséges megoldásokról.

## <a name="cluster-setup-failures"></a>Fürt telepítési hiba
### <a name="cannot-clean-up-service-fabric-logs"></a>Nem lehet tisztítást végezni a Service Fabric-naplók
#### <a name="problem"></a>Probléma
A DevClusterSetup parancsprogram futtatása közben ehhez hasonló hibaüzenetet jelenik meg:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Megoldás
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg rendszergazdaként egy új PowerShell-ablakot. Most kell tudni sikeresen futtatni a parancsfájlt.

## <a name="cluster-connection-failures"></a>Fürt kapcsolódási hibák
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Service Fabric PowerShell-parancsmagok a rendszer nem ismeri fel az Azure PowerShell
#### <a name="problem"></a>Probléma
Ha futtatja a Service Fabric PowerShell-parancsmagokat például `Connect-ServiceFabricCluster` egy Azure PowerShell-ablakban, ez nem sikerül, arról, hogy a parancsmag nem ismerhető fel. A ennek oka, hogy az Azure PowerShell 32 bites verzióját használja a Windows PowerShell (még akkor is, 64 bites operációs rendszer verzió), mivel a Service Fabric-parancsmagok csak 64 bites környezetben működik.

#### <a name="solution"></a>Megoldás
A Service Fabric parancsmagok futtatása mindig közvetlenül a Windows PowerShell.

> [!NOTE]
> Az Azure PowerShell legújabb verziójának nem hoz létre egy külön helyi, ez már nem jöjjön létre.
> 
> 

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
Zárja be a jelenlegi PowerShell-ablakot, és nyissa meg rendszergazdaként egy új PowerShell-ablakot. Most kell tud sikeresen csatlakozni.

### <a name="fabric-connection-denied-exception"></a>Háló kapcsolat megtagadva kivétel
#### <a name="problem"></a>Probléma
Hibakereséshez a következőből: Visual Studio, a FabricConnectionDeniedException hiba nyílik meg.

#### <a name="solution"></a>Megoldás
Ez a hiba általában akkor következik be, indítsa el a szolgáltatás gazdafolyamatokon manuálisan, ahelyett, hogy a Service Fabric-futtatókörnyezet indítsa el az Ön így megkísérlésekor.

Győződjön meg arról, hogy nem kell minden service projektek állítja be kiindulási projektet a megoldásban. Csak a Service Fabric application projektek indítási projektként kell beállítani.

> [!TIP]
> Ha a telepítés után a helyi fürtön megkezdi a rendellenes viselkedését, visszaállíthatja rajta a helyi fürt manager rendszer rendszertálca alkalmazásban. Ezzel eltávolítja a meglévő fürt, és állítsa be egy újat. Vegye figyelembe, hogy a telepített alkalmazások és a kapcsolódó adatokat távolítja el.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Ismertetése és hibaelhárítása a fürt rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)

