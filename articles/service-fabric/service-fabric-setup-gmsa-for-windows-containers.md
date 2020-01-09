---
title: Az Azure Service Fabric Container Services gMSA beállítása
description: Ismerkedjen meg az Azure Service Fabric rendszerű tárolók csoportosan felügyelt szolgáltatásfiókok (gMSA) beállításával.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639207"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA beállítása a Service Fabric-on futó Windows-tárolók számára

A gMSA (csoportosan felügyelt szolgáltatásfiókok) beállításához a rendszer a hitelesítő adatok meghatározására szolgáló fájlt (`credspec`) helyezi a fürt összes csomópontjára. A fájl egy virtuálisgép-bővítmény használatával is másolható az összes csomópontra.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. További információt a `credspec` fájlról a [hitelesítő adatok specifikációjának létrehozása](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)című témakörben talál. A hitelesítő adatok specifikációja és a `Hostname` címke meg van adva az alkalmazás jegyzékfájljában. A `Hostname` címkének egyeznie kell azzal a gMSA-fiók nevével, amelyet a tároló futtat.  A `Hostname` címke lehetővé teszi, hogy a tároló hitelesítse magát a tartomány többi szolgáltatásával a Kerberos-hitelesítés használatával.  Az alkalmazás jegyzékfájljában a `Hostname` és az `credspec` megadására szolgáló minta az alábbi kódrészletben látható:

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
