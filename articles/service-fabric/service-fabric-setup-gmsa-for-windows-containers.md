---
title: GMSA beállítása az Azure Service Fabric tárolószolgáltatásaihoz
description: Ismerje meg most a felügyelt szolgáltatásfiókok (gMSA) beállítását az Azure Service Fabricben futó tárolóhoz.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639207"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA beállítása a Service Fabric en futó Windows-tárolókhoz

A gMSA (csoport felügyelt szolgáltatásfiókok) beállításához`credspec`a fürt összes csomópontján egy hitelesítőadat-specifikációs fájl ( ) kerül elhelyezésre. A fájl a virtuális gép kiterjesztésével minden csomópontra másolható.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. A fájlról `credspec` további információt a [Hitelesítő adatok specifikációjának létrehozása című témakörben talál.](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec) A hitelesítő adatok `Hostname` specifikációja és a címke az alkalmazásjegyzékben van megadva. A `Hostname` címkének meg kell egyeznie a gMSA-fiók nevével, amely alatt a tároló fut.  A `Hostname` címke lehetővé teszi, hogy a tároló kerberos-hitelesítéssel hitelesítse magát a tartomány más szolgáltatásai számára.  Az alkalmazásjegyzékben `Hostname` szereplő `credspec` és a minta megadása a következő kódrészletben látható:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Következő lépésként olvassa el a következő cikkeket:

* [Windows-tároló központi telepítése a Service Fabric szolgáltatásba Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése linuxos Service Fabric-re](service-fabric-get-started-containers-linux.md)
