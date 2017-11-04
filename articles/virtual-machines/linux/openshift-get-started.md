---
title: "Az Azure áttekintése OpenShift |} Microsoft Docs"
description: "Az Azure áttekintése OpenShift."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>OpenShift áttekintése

OpenShift egy nyitott és bővíthető tároló platform, amely a vállalat docker és Kubernetes számos lehetőséget kínál.  

OpenShift Kubernetes tároló vezénylési és felügyeleti tartalmazza. Fejlesztői és műveletek-központú eszközök, amelyek lehetővé teszik hozzáadása:

- Gyors alkalmazásfejlesztés
- Egyszerű telepítés és a méretezésről
- A csoportok és alkalmazások hosszú távú életciklus-karbantartás

Nincsenek több által kínált OpenShift, amelyek két érhetők el az Azure-ban futtatni.

- OpenShift Origin
- OpenShift tárolóplatform
- Online OpenShift
- Dedikált OpenShift

A kezelt négy ajánlatok kettő az ügyfelek központi telepítése az Azure saját maguk - OpenShift eredete és OpenShift tároló Platform.

## <a name="openshift-origin"></a>OpenShift Origin

[Nyílt forráskódú](https://www.openshift.org/) , amely támogatja a közösségi OpenShift a felsőbb rétegbeli projekt. Forrás CentOS vagy RHEL telepíthető.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Vállalati kész ([kereskedelmi ajánlat](https://www.openshift.com)) Red Hat Red Hat által támogatott verzióra. Vevő a szükséges jogosultságok OpenShift tároló platform vásárol, és a telepítés és a teljes infrastruktúra kezelése.

Ügyfél "tulajdonosa" a teljes platform, mivel telepítése a helyszíni adatközpontját, nyilvános felhőbe (Azure, AWS, Google, stb.), stb.

## <a name="openshift-online"></a>Online OpenShift

Red Hat felügyelt **több-bérlős** OpenShift (tároló platformot használnak). Red Hat kezeli az összes az alkalmazás mögötti infrastruktúra (virtuális gépek, OpenShift fürt hálózati, tárolási, stb.). 

Ügyfél telepíti a tárolókat, de nem szabályozza, a tárolók futtassa mely állomásokat. Mivel több-bérlős, tárolók előfordulhat, hogy egy szalagon ugyanazon Virtuálisgép-gazdák tárolóként az egyéb ügyfelektől. Költség tároló működik.

## <a name="openshift-dedicated"></a>Dedikált OpenShift

Red Hat felügyelt **single-bérlő** OpenShift (tároló platformot használnak). Red Hat kezeli az összes az alkalmazás mögötti infrastruktúra (virtuális gépek, OpenShift fürt hálózati, tárolási, stb.). A fürt egy ügyfél és a nyilvános felhőben (AWS, Google, Azure - korai 2018 várható). Kiindulási fürt évig $48K / (teljes év társaságuk fizetési) négy alkalmazás olyan csomópontot tartalmaz.

## <a name="next-steps"></a>Következő lépések

- [A OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [OpenShift származási telepítése](./openshift-origin.md)
- [OpenShift tároló Platform telepítése](./openshift-container-platform.md)
- [Utáni telepítési feladatok](./openshift-post-deployment.md)
- [OpenShift üzembe helyezés hibaelhárítása](./openshift-troubleshooting.md)
