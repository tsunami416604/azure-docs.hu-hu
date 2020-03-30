---
title: OpenShift az Azure-ban – áttekintés
description: Az OpenShift áttekintése az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035428"
---
# <a name="openshift-in-azure"></a>OpenShift az Azure-ban

Az OpenShift egy nyílt és bővíthető tárolóalkalmazás-platform, amely a Docker-t és a Kubernetes-t a vállalathoz hozza.  

OpenShift tartalmazza kubernetes a tároló vezénylési és felügyeleti. Fejlesztőközpontú és műveletközpontú eszközöket ad hozzá, amelyek lehetővé teszik:

- Gyors alkalmazásfejlesztés.
- Egyszerű üzembe helyezés és méretezés.
- Hosszú élettartamú karbantartás csapatok és alkalmazások számára.

Az OpenShift több verziója is elérhető.  Ezek közül a verziók közül csak kettő érhető el ma az ügyfelek számára az Azure-ban: az OpenShift Container Platform és az OKD (korábban OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft Azure Red Hat OpenShift az Azure-ban futó OpenShift teljes körűen felügyelt ajánlata. Ezt a szolgáltatást a Microsoft és a Red Hat közösen kezeli és támogatja. További részletekaz [Azure Red Hat OpenShift Service](https://docs.microsoft.com/azure/openshift/) dokumentációjában találhatók.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

A Container Platform egy nagyvállalati használatra kész [kereskedelmi verzió](https://www.openshift.com) a Red Hat-tól, és támogatja. Ezzel a verzióval az ügyfelek megvásárolják az OpenShift Container Platform szükséges jogosultságait, és felelősek a teljes infrastruktúra telepítéséért és felügyeletéért.

Mivel az ügyfelek "birtokolják" a teljes platformot, telepíthetik azt a helyszíni adatközpontjukban vagy egy nyilvános felhőben (például az Azure-ban).

## <a name="okd"></a>OKD

Az OKD az OpenShift [nyílt forráskódú](https://www.okd.io/) upstream projektje, amelyet a közösség támogat. Az OKD beépíthető CentOS vagy Red Hat Enterprise Linux (RHEL) rendszerre.

## <a name="next-steps"></a>További lépések

- [Az OpenShift gyakori előfeltételeinek konfigurálása az Azure-ban](./openshift-container-platform-3x-prerequisites.md)
- [OpenShift container platform telepítése az Azure-ban](./openshift-container-platform-3x.md)
- [OpenShift container platform saját felügyelt piactéri ajánlatának üzembe helyezése](./openshift-container-platform-3x-marketplace-self-managed.md)
- [OpenShift üzembe helyezése az Azure Stackben](./openshift-azure-stack.md)
- [Telepítés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift központi telepítésének – problémamegoldás](./openshift-container-platform-3x-troubleshooting.md)
