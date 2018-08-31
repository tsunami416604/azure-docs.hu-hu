---
title: OpenShift az Azure áttekintése |} A Microsoft Docs
description: Az Azure-ban az OpenShift áttekintése.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
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
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190343"
---
# <a name="openshift-in-azure"></a>OpenShift az Azure-ban

OpenShift egy nyílt és bővíthető alkalmazás tárolóplatform, amely csökkenti a Docker és a Kubernetes az Enterprise.  

OpenShift Kubernetes tartalmaz, a tárolóvezénylés és a felügyeleti. Hozzáadja a fejlesztői és a műveletek központú eszközeivel azáltal, hogy:

- Alkalmazások gyors fejlesztésére.
- Könnyű üzembe helyezés és méretezés.
- Hosszú távú életciklus karbantartási csoportokhoz és alkalmazásokhoz.

OpenShift több verziója érhető el:

- OKD (korábbi nevén az OpenShift Origin)
- OpenShift tárolóplatform
- OpenShift Online
- OpenShift dedikált

A négy verziók kezelt ebben a cikkben, csak az ügyfelek üzembe helyezése az Azure-ban kettő: OpenShift Origin és az OpenShift Tárolóplatform.

## <a name="okd-formerly-openshift-origin"></a>OKD (korábbi nevén az OpenShift Origin)

OKD van egy [nyílt forráskódú](https://www.okd.io/) felsőbb rétegbeli projektje, amely támogatja a közösségi OpenShift. OKD a CentOS vagy a Red Hat Enterprise Linux (RHEL) is telepíthető.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Container Platform az olyan vállalati felhasználásra kész [kereskedelmi verzió](https://www.openshift.com) származó és a Red Hat által támogatott. Ebben a verzióban az ügyfelek vásárol a szükséges jogosultságok az OpenShift Tárolóplatform, és telepítési és felügyeleti az egész infrastruktúrát ábrázoló felelős.

"Saját" a teljes platform, mert azok a helyi adatközpontban vagy a nyilvános felhőben (például Azure, az AWS vagy a Google) telepíthetik azt.

## <a name="openshift-online"></a>OpenShift Online

Online van egy Red Hat által felügyelt *több-bérlős* az OpenShift Tárolóplatform használó. Red Hat kezeli az összes, az alapul szolgáló infrastruktúra (például virtuális gépek, az OpenShift fürt, hálózati és storage). 

Ebben a verzióban az ügyfél helyez üzembe tárolókat, de nem szabályozza, mely állomások keresztül a tárolók futtatásához. Mivel Online több-bérlős, tárolók az ugyanazon Virtuálisgép-gazdagépek más felhasználóktól tárolókként is található. Költsége van tárolónként.

## <a name="openshift-dedicated"></a>OpenShift dedikált

Red Hat által felügyelt dedikált van *egybérlős* az OpenShift Tárolóplatform használó. Red Hat kezeli az összes, az alapul szolgáló infrastruktúra (virtuális gépek, az OpenShift fürt, hálózatkezelési, tárolási, stb.). A fürt egy ügyfél és egy nyilvános felhőben (például az AWS vagy a Google, a korai 2018-ban várható Azure-ral). A kiindulási fürt 48,000 $ / év (előre fizetett) az alkalmazás négy csomópontot tartalmaz.

## <a name="next-steps"></a>További lépések

- [Az OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az Azure-ban az OpenShift Origin telepítése](./openshift-origin.md)
- [Az OpenShift Container Platform az Azure-beli üzembe helyezése](./openshift-container-platform.md)
- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
