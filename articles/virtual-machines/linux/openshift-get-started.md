---
title: OpenShift az Azure áttekintése |} A Microsoft Docs
description: Az Azure-ban az OpenShift áttekintése.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002516"
---
# <a name="openshift-in-azure"></a>OpenShift az Azure-ban

OpenShift egy nyílt és bővíthető alkalmazás tárolóplatform, amely csökkenti a Docker és a Kubernetes az Enterprise.  

OpenShift Kubernetes tartalmaz, a tárolóvezénylés és a felügyeleti. Fejlesztőközpontú ad hozzá, valamint műveletek-központú eszközök adott engedélyezése:

- Alkalmazások gyors fejlesztésére.
- Könnyű üzembe helyezés és méretezés.
- Hosszú távú életciklus karbantartási csoportokhoz és alkalmazásokhoz.

OpenShift több verziója érhető el:

- OpenShift tárolóplatform
- OpenShift az Azure-beli (teljes körűen felügyelt OpenShift körül Q1 CY2019 végén hamarosan elérhető)
- OKD (Formerly OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

Ebben a cikkben ismertetett öt verzióinak csak kettő érhető el még ma, az ügyfelek üzembe helyezése az Azure-ban: OpenShift Tárolóplatform és OKD.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Container Platform az olyan vállalati felhasználásra kész [kereskedelmi verzió](https://www.openshift.com) származó és a Red Hat által támogatott. Ebben a verzióban az ügyfelek vásárol a szükséges jogosultságok az OpenShift Tárolóplatform, és telepítési és felügyeleti az egész infrastruktúrát ábrázoló felelős.

"Saját" a teljes platform, mert a helyszíni adatközpontját a, vagy a nyilvános felhőben (például az Azure-ra) telepíthetik azt.

## <a name="openshift-on-azure"></a>OpenShift On Azure

OpenShift az Azure-ban egy teljes körűen felügyelt ajánlat az Azure-ban futó OpenShift. Ez a szolgáltatás közösen kezelt és a Microsoft és a Red Hat által támogatott. A fürt az ügyfél Azure-előfizetésben helyezi üzembe helyezni. A szolgáltatás jelenleg privát előzetes verzióban érhető el, és körül Q1 CY2019 végén általánosan elérhető a javítás. Az ügyfelek részt venni benne a Private Preview verzióban, kérjük, töltse ki a [jelölési űrlapot](https://aka.ms/openshiftazureinterest).  További információkat nyújtanak, az ajánlat beolvasása közelebb általánosan elérhető

## <a name="okd-formerly-openshift-origin"></a>OKD (Formerly OpenShift Origin)

OKD van egy [nyílt forráskódú](https://www.okd.io/) felsőbb rétegbeli projektje, amely támogatja a közösségi OpenShift. OKD a CentOS vagy a Red Hat Enterprise Linux (RHEL) is telepíthető.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Red Hat által felügyelt dedikált van *egybérlős* OpenShift Tárolóplatform használó OpenShift. Red Hat kezeli az összes, az alapul szolgáló infrastruktúra (virtuális gépek, az OpenShift fürt, hálózatkezelési, tárolási, stb.). A fürt egy ügyfél és egy nyilvános felhőben (például az Azure-t). A kiindulási fürt alkalmazás négy csomópontot tartalmaz, és minden díjat éves és fizetős előre.

## <a name="openshift-online"></a>OpenShift Online

Online van egy Red Hat által felügyelt *több-bérlős* az OpenShift Tárolóplatform használó. Red Hat kezeli az összes, az alapul szolgáló infrastruktúra (például virtuális gépek, az OpenShift fürt, hálózati és storage). 

Ebben a verzióban az ügyfél helyez üzembe tárolókat, de nem szabályozza, mely állomások keresztül a tárolók futtatásához. Mivel Online több-bérlős, tárolók az ugyanazon Virtuálisgép-gazdagépek más felhasználóktól tárolókként is található. Költsége van tárolónként.

## <a name="next-steps"></a>További lépések

- [Az OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az OpenShift Container Platform az Azure-beli üzembe helyezése](./openshift-container-platform.md)
- [Az Azure-ban OKD üzembe helyezése](./openshift-okd.md)
- [OpenShift az Azure Stack üzembe helyezése](./openshift-azure-stack.md)
- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
