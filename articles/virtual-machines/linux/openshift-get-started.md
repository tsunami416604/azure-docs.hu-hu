---
title: OpenShift az Azure áttekintése |} A Microsoft Docs
description: Az Azure-ban az OpenShift áttekintése.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542424"
---
# <a name="openshift-in-azure"></a>Az OpenShift az Azure-ban

OpenShift egy nyílt és bővíthető alkalmazás tárolóplatform, amely csökkenti a Docker és a Kubernetes az Enterprise.  

OpenShift Kubernetes tartalmaz, a tárolóvezénylés és a felügyeleti. Fejlesztőközpontú ad hozzá, valamint műveletek-központú eszközök adott engedélyezése:

- Alkalmazások gyors fejlesztésére.
- Könnyű üzembe helyezés és méretezés.
- Hosszú távú életciklus karbantartási csoportokhoz és alkalmazásokhoz.

Nincsenek elérhető az OpenShift több verzióját.  Verzió csak két érhetők el még ma az Azure-ban üzembe: OpenShift Tárolóplatform és OKD (korábbi nevén az OpenShift Origin).

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Container Platform az olyan vállalati felhasználásra kész [kereskedelmi verzió](https://www.openshift.com) származó és a Red Hat által támogatott. Ebben a verzióban az ügyfelek vásárol a szükséges jogosultságok az OpenShift Tárolóplatform, és telepítési és felügyeleti az egész infrastruktúrát ábrázoló felelős.

"Saját" a teljes platform, mert a helyszíni adatközpontját a, vagy a nyilvános felhőben (például az Azure-ra) telepíthetik azt.

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Az Azure Red Hat OpenShift egy teljes körűen felügyelt ajánlat az Azure-ban futó OpenShift. Ez a szolgáltatás közösen kezelt és a Microsoft és a Red Hat által támogatott. A fürt az ügyfél Azure-előfizetésben helyezi üzembe helyezni. A szolgáltatás általánosan elérhető körül 2019. május a javítást. Külön dokumentáció a felügyelt szolgáltatás lesz elérhető, ha a szolgáltatás már általánosan elérhető

## <a name="okd"></a>OKD

OKD van egy [nyílt forráskódú](https://www.okd.io/) felsőbb rétegbeli projektje, amely támogatja a közösségi OpenShift. OKD a CentOS vagy a Red Hat Enterprise Linux (RHEL) is telepíthető.

## <a name="next-steps"></a>További lépések

- [Az OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az OpenShift Container Platform az Azure-beli üzembe helyezése](./openshift-container-platform.md)
- [Az OpenShift Container Platform önállóan felügyelt Piactéri ajánlat üzembe helyezése](./openshift-marketplace-self-managed.md)
- [OpenShift az Azure Stack üzembe helyezése](./openshift-azure-stack.md)
- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
