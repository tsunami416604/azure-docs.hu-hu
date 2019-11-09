---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b4e479405a9606a8353785828d0c9c94ef8c32ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850372"
---
### <a name="regions"></a>Mely régiók érhetők el?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Szükségem van nyilvános IP-címekre a virtuális gépen?

NINCS szüksége nyilvános IP-címekre azon Azure-beli virtuális gépen, amelyhez az Azure Bastion szolgáltatáshoz csatlakozik. A megerősített szolgáltatás megnyitja az RDP/SSH-munkamenetet/kapcsolatot a virtuális géppel a virtuális gép magánhálózati IP-címén a virtuális hálózaton belül.

### <a name="rdpssh"></a>Szükség van RDP-vagy SSH-ügyfélre?

Nincs szükség RDP- vagy SSH-ügyfélre ahhoz, hogy RDP/SSH-csatlakozást építsen ki az Azure-beli virtuális gépekhez az Azure Portalon. A [Azure Portal](https://portal.azure.com) segítségével közvetlenül a böngészőben érheti el az RDP/SSH-hozzáférést a virtuális géphez.

### <a name="agent"></a>Szükségem van egy, az Azure-beli virtuális gépen futó ügynökre?

Sem a böngészőben, sem az Azure-beli virtuális gépen nem kell telepítenie ügynököt vagy más szoftvereket. A Bastion szolgáltatás ügynök nélküli, és nem szükséges hozzá semmilyen más szoftver az RDP/SSH-csatlakozáshoz.

### <a name="browsers"></a>Mely böngészők támogatottak?

Használja a Microsoft Edge böngészőt vagy a Google Chrome-ot Windows rendszeren. Apple Mac gépek esetén használja a Google Chrome böngészőt. A Microsoft Edge Chromium is támogatva van mind Windows, mind Mac rendszereken.

### <a name="roles"></a>Vannak olyan szerepkörök, amelyek szükségesek a virtuális gépek eléréséhez?

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

### <a name="pricingpage"></a>Mi a díjszabás?

További tájékoztatás a [díjszabási lapon](https://aka.ms/BastionHostPricing) olvasható.

### <a name="session"></a>Miért kapok "a munkamenet lejárt" hibaüzenetet, mielőtt megkezdődik a megerősített munkamenet?

A munkamenetet csak a Azure Portal kezdeményezheti. Jelentkezzen be a Azure Portalba, és kezdje újra a munkamenetet. Ha közvetlenül egy másik böngésző-munkamenetből vagy-lapról nyitja meg az URL-címet, a rendszer ezt a hibát várta. Segít biztosítani, hogy a munkamenet biztonságosabb legyen, és hogy a munkamenet csak a Azure Portalon keresztül legyen elérhető.

### <a name="keyboard"></a>Milyen billentyűzetkiosztások támogatottak a megerősített távoli munkamenet során?

Az Azure Bastion jelenleg a virtuális gépen belüli en-us-QWERTY billentyűzet-elrendezést támogatja.  A billentyűzet egyéb területi beállításainak támogatása folyamatban van.

