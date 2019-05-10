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
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c8ebd10544331d3b7d887124542fe4ee2753c714
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233141"
---
# <a name="openshift-in-azure"></a>Az OpenShift az Azure-ban

OpenShift egy nyílt és bővíthető alkalmazás tárolóplatform, amely csökkenti a Docker és a Kubernetes az Enterprise.  

OpenShift Kubernetes tartalmaz, a tárolóvezénylés és a felügyeleti. Fejlesztőközpontú ad hozzá, valamint műveletek-központú eszközök adott engedélyezése:

- Alkalmazások gyors fejlesztésére.
- Könnyű üzembe helyezés és méretezés.
- Hosszú távú életciklus karbantartási csoportokhoz és alkalmazásokhoz.

Nincsenek elérhető az OpenShift több verzióját.  Verzió csak két érhetők el még ma az Azure-ban üzembe: OpenShift Tárolóplatform és OKD (korábbi nevén az OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft Azure Red Hat OpenShift egy teljes körűen felügyelt ajánlat az Azure-ban futó OpenShift. Ez a szolgáltatás közösen kezelt és a Microsoft és a Red Hat által támogatott. Teljes dokumentáció lehet [Itt](https://docs.microsoft.com/azure/openshift/). 

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Container Platform az olyan vállalati felhasználásra kész [kereskedelmi verzió](https://www.openshift.com) származó és a Red Hat által támogatott. Ebben a verzióban az ügyfelek vásárol a szükséges jogosultságok az OpenShift Tárolóplatform, és telepítési és felügyeleti az egész infrastruktúrát ábrázoló felelős.

"Saját" a teljes platform, mert a helyszíni adatközpontját a, vagy a nyilvános felhőben (például az Azure-ra) telepíthetik azt.

## <a name="okd"></a>OKD

OKD van egy [nyílt forráskódú](https://www.okd.io/) felsőbb rétegbeli projektje, amely támogatja a közösségi OpenShift. OKD a CentOS vagy a Red Hat Enterprise Linux (RHEL) is telepíthető.

## <a name="next-steps"></a>További lépések

- [Az OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az OpenShift Container Platform az Azure-beli üzembe helyezése](./openshift-container-platform.md)
- [Az OpenShift Container Platform önállóan felügyelt Piactéri ajánlat üzembe helyezése](./openshift-marketplace-self-managed.md)
- [OpenShift az Azure Stack üzembe helyezése](./openshift-azure-stack.md)
- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
