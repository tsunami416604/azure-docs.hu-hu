---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 17d391a7e6b8ef0558fb73afe363cd96deb60a7d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262527"
---
### <a name="regions"></a>Mely régiók érhetők el?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Szükségem van nyilvános IP-címekre a virtuális gépen?

Amikor az Azure Bastion használatával csatlakozik egy virtuális géphez, nincs szüksége nyilvános IP-címekre azon az Azure-beli virtuális gépen, amelyhez csatlakozik. A megerősített szolgáltatás megnyitja az RDP/SSH-munkamenetet/kapcsolatot a virtuális géppel a virtuális gép magánhálózati IP-címén a virtuális hálózaton belül.

### <a name="is-ipv6-supported"></a>Támogatott-e az IPv6?

Az IPv6 jelenleg nem támogatott. Az Azure Bastion csak az IPv4-t támogatja.

### <a name="rdpssh"></a>Szükség van RDP-vagy SSH-ügyfélre?

Nincs szüksége RDP-vagy SSH-ügyfélre az RDP/SSH eléréséhez az Azure Portal Azure-beli virtuális gépén. A [Azure Portal](https://portal.azure.com) segítségével közvetlenül a böngészőben érheti el az RDP/SSH-hozzáférést a virtuális géphez.

### <a name="rdscal"></a>Szükséges-e az Azure Bastion RDS CAL adminisztratív célokra az Azure által üzemeltetett virtuális gépeken?
Nem, a Windows Server rendszerű virtuális gépekhez az Azure Bastion-hez való hozzáféréshez nincs szükség [RDS CALra](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , ha kizárólag adminisztratív célokra használják.

### <a name="agent"></a>Szükségem van egy, az Azure-beli virtuális gépen futó ügynökre?

Nem kell ügynököt vagy szoftvert telepítenie a böngészőjébe vagy az Azure-beli virtuális gépre. A megerősített szolgáltatás ügynök nélküli, és nem igényel további szoftvereket RDP/SSH-hoz.

### <a name="browsers"></a>Mely böngészők támogatottak?

Használja a Microsoft Edge böngészőt vagy a Google Chrome-ot Windows rendszeren. Apple Mac esetén használja a Google Chrome böngészőt. A Microsoft Edge Chromium a Windows és a Mac rendszereken is támogatott.

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

### <a name="udr"></a>Támogatott-e a felhasználó által megadott útválasztás (UDR) egy Azure-alapú megerősített alhálózaton?

Nem. Az UDR nem támogatott Azure-beli megerősített alhálózaton.
Azokban az esetekben, amelyekben az Azure Bastion és a Azure Firewall/Network Virtual Appliance (NVA) is szerepel ugyanabban a virtuális hálózatban, nem kell kényszeríteni az Azure-beli megerősített alhálózatról érkező forgalmat, hogy Azure Firewall, mert az Azure-és a virtuális gépek közötti kommunikáció privát. További információ: [Azure Firewall mögötti virtuális gépek elérése a Bastion-vel](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Támogatott-e a fájlátvitel az Azure Bastion RDP-munkamenettel?

Keményen dolgozunk új funkciók hozzáadásával. Mostantól a fájlátvitel nem támogatott, de az ütemterv részét képezi. Kérjük, ossza meg velünk az új funkciókkal kapcsolatos visszajelzéseit az [Azure Bastion feedback oldalán](https://feedback.azure.com/forums/217313-networking?category_id=367303).
