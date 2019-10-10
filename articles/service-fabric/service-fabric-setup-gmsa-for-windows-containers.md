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
ms.author: atsenthi
ms.openlocfilehash: 45fc4c924a8fb794ad81529de74b98ee812f46c5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170424"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA beállítása a Service Fabric-on futó Windows-tárolók számára

A gMSA (csoportosan felügyelt szolgáltatásfiókok) beállításához a hitelesítő adatok meghatározására szolgáló fájl (`credspec`) a fürt összes csomópontjára kerül. A fájl egy virtuálisgép-bővítmény használatával is másolható az összes csomópontra.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. További információt a `credspec` fájlról a [hitelesítő adatok specifikációjának létrehozása](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)című témakörben talál. A hitelesítő adatok specifikációja és a `Hostname` címke meg van adva az alkalmazás jegyzékfájljában. A `Hostname` címkének egyeznie kell azzal a gMSA-fiók nevével, amelyet a tároló futtat.  A `Hostname` címke lehetővé teszi, hogy a tároló hitelesítse magát a tartomány többi szolgáltatásával a Kerberos-hitelesítés használatával.  Az alkalmazás jegyzékfájljának `Hostname` és a `credspec` megadására szolgáló minta az alábbi kódrészletben látható:

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
