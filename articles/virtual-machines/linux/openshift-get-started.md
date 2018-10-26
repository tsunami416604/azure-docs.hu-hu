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
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085439"
---
# <a name="openshift-in-azure"></a>OpenShift az Azure-ban

OpenShift egy nyílt és bővíthető alkalmazás tárolóplatform, amely csökkenti a Docker és a Kubernetes az Enterprise.  

OpenShift Kubernetes tartalmaz, a tárolóvezénylés és a felügyeleti. Fejlesztőközpontú ad hozzá, valamint műveletek-központú eszközök adott engedélyezése:

- Alkalmazások gyors fejlesztésére.
- Könnyű üzembe helyezés és méretezés.
- Hosszú távú életciklus karbantartási csoportokhoz és alkalmazásokhoz.

OpenShift több verziója érhető el:

- OpenShift tárolóplatform
- OpenShift az Azure-beli (teljes körűen felügyelt OpenShift korai CY2019 a hamarosan elérhető)
- OKD (korábbi nevén az OpenShift Origin)
- OpenShift dedikált
- OpenShift Online

Öt verzióinak kezelt ebben a cikkben, csak két érhetők el még ma az Azure-ban üzembe: OpenShift Tárolóplatform és OKD.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Container Platform az olyan vállalati felhasználásra kész [kereskedelmi verzió](https://www.openshift.com) származó és a Red Hat által támogatott. Ebben a verzióban az ügyfelek vásárol a szükséges jogosultságok az OpenShift Tárolóplatform, és telepítési és felügyeleti az egész infrastruktúrát ábrázoló felelős.

"Saját" a teljes platform, mert azok a helyi adatközpontban vagy a nyilvános felhőben (például Azure, az AWS vagy a Google) telepíthetik azt.

## <a name="openshift-on-azure"></a>Az OpenShift Azure rendszeren

OpenShift az Azure-ban egy teljes körűen felügyelt ajánlat az Azure-ban futó OpenShift. Ez a szolgáltatás közösen kezelt és a Microsoft és a Red Hat által támogatott. A fürt az ügyfél Azure-előfizetésben helyezi üzembe helyezni. A szolgáltatás jelenleg privát előzetes verzióban érhető el, és a korai CY 2019 általánosan elérhető a javítás. További információkat nyújtanak, az ajánlat beolvasása közelebb általánosan elérhető

## <a name="okd-formerly-openshift-origin"></a>OKD (korábbi nevén az OpenShift Origin)

OKD van egy [nyílt forráskódú](https://www.okd.io/) felsőbb rétegbeli projektje, amely támogatja a közösségi OpenShift. OKD a CentOS vagy a Red Hat Enterprise Linux (RHEL) is telepíthető.

## <a name="openshift-dedicated"></a>OpenShift dedikált

Red Hat által felügyelt dedikált van *egybérlős* OpenShift Tárolóplatform használó OpenShift. Red Hat kezeli az összes, az alapul szolgáló infrastruktúra (virtuális gépek, az OpenShift fürt, hálózatkezelési, tárolási, stb.). A fürt egy ügyfél és egy nyilvános felhőben (például az AWS vagy a Google). A kiindulási fürt alkalmazás négy csomópontot tartalmaz, és minden díjat éves és fizetős előre.

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
