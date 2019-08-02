---
title: Az Azure Service Fabric Container Services gMSA beállítása | Microsoft Docs
description: Ismerkedjen meg az Azure Service Fabric rendszerű tárolók gMSA beállításával.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: 09994c7676de8470efff1707598ddf32a48e41a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599180"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA beállítása a Service Fabric-on futó Windows-tárolók számára

A gMSA (csoportosan felügyelt szolgáltatásfiókok) beállításához a rendszer a hitelesítő`credspec`adatok specifikációjának fájlját () a fürt összes csomópontjára áthelyezi. A fájl egy virtuálisgép-bővítmény használatával is másolható az összes csomópontra.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. További információt `credspec` a fájlról a hitelesítő [adatok specifikációjának létrehozása](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)című témakörben talál. A hitelesítő adatok specifikációja és a `Hostname` címke meg van adva az alkalmazás jegyzékfájljában. A `Hostname` címkének meg kell egyeznie annak a gMSA-fióknak a nevével, amelyet a tároló futtat.  A `Hostname` címke lehetővé teszi, hogy a tároló a Kerberos-hitelesítés használatával hitelesítse magát a tartományban lévő más szolgáltatásokban.  A `Hostname` és az `credspec` alkalmazás jegyzékfájljának megadására szolgáló minta az alábbi kódrészletben látható:

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
