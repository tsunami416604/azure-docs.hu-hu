---
title: Az Azure Service Fabric Container Services gMSA beállítása
description: Ismerkedjen meg az Azure Service Fabric rendszerű tárolók csoportosan felügyelt szolgáltatásfiókok (gMSA) beállításával.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639207"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA beállítása a Service Fabric-on futó Windows-tárolók számára

A gMSA (csoportosan felügyelt szolgáltatásfiókok) beállításához a rendszer a hitelesítő`credspec`adatok specifikációjának fájlját () a fürt összes csomópontjára áthelyezi. A fájl egy virtuálisgép-bővítmény használatával is másolható az összes csomópontra.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. További információt a `credspec` fájlról a [hitelesítő adatok specifikációjának létrehozása](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)című témakörben talál. A hitelesítő adatok specifikációja és a `Hostname` címke meg van adva az alkalmazás jegyzékfájljában. A `Hostname` címkének meg kell egyeznie annak a gMSA-fióknak a nevével, amelyet a tároló futtat.  A `Hostname` címke lehetővé teszi, hogy a tároló a Kerberos-hitelesítés használatával hitelesítse magát a tartományban lévő más szolgáltatásokban.  A `Hostname` és az `credspec` alkalmazás jegyzékfájljának megadására szolgáló minta az alábbi kódrészletben látható:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Következő lépésként olvassa el a következő cikkeket:

* [Windows-tároló üzembe helyezése Service Fabric Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése Linuxon Service Fabric](service-fabric-get-started-containers-linux.md)
