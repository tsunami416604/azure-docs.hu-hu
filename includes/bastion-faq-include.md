---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: efb32631c5ee1eedece6d2a06b94702b602ed418
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276114"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Mely régiók érhetők el?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Szükségem van nyilvános IP-címekre a virtuális gépen?

Amikor az Azure Bastion használatával csatlakozik egy virtuális géphez, nincs szüksége nyilvános IP-címekre azon az Azure-beli virtuális gépen, amelyhez csatlakozik. A megerősített szolgáltatás megnyitja az RDP/SSH-munkamenetet/kapcsolatot a virtuális géppel a virtuális gép magánhálózati IP-címén a virtuális hálózaton belül.

### <a name="is-ipv6-supported"></a>Támogatott-e az IPv6?

Az IPv6 jelenleg nem támogatott. Az Azure Bastion csak az IPv4-t támogatja.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Szükség van RDP-vagy SSH-ügyfélre?

Nincs szüksége RDP-vagy SSH-ügyfélre az RDP/SSH eléréséhez az Azure Portal Azure-beli virtuális gépén. A [Azure Portal](https://portal.azure.com) segítségével közvetlenül a böngészőben érheti el az RDP/SSH-hozzáférést a virtuális géphez.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Szükségem van egy, az Azure-beli virtuális gépen futó ügynökre?

Nem kell ügynököt vagy szoftvert telepítenie a böngészőjébe vagy az Azure-beli virtuális gépre. A megerősített szolgáltatás ügynök nélküli, és nem igényel további szoftvereket RDP/SSH-hoz.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hány egyidejű RDP-és SSH-munkamenetet támogat az Azure-alapú megerősített szolgáltatás?

Az RDP és az SSH egy használaton alapuló protokoll. A munkamenetek magas kihasználtsága miatt a megerősített állomás támogatja a munkamenetek alacsonyabb összesített számát. Az alábbi számok a napi munkafolyamatokat feltételezik.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Milyen funkciók támogatottak egy RDP-munkamenetben?

Jelenleg csak a szöveg másolása/beillesztése támogatott. Az olyan funkciók, mint a fájlmásolás, nem támogatottak. Kérjük, ossza meg velünk az új funkciókkal kapcsolatos visszajelzéseit az [Azure Bastion feedback oldalán](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Mely böngészők támogatottak?

Használja a Microsoft Edge böngészőt vagy a Google Chrome-ot Windows rendszeren. Apple Mac esetén használja a Google Chrome böngészőt. A Microsoft Edge Chromium a Windows és a Mac rendszereken is támogatott.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Hol tárolja az Azure Bastion az ügyféladatokat?

Az Azure Bastion nem helyezi át és nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Vannak olyan szerepkörök, amelyek szükségesek a virtuális gépek eléréséhez?

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Mi a díjszabás?

További tájékoztatás a [díjszabási lapon](https://aka.ms/BastionHostPricing) olvasható.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Szükséges-e az Azure Bastion RDS CAL adminisztratív célokra az Azure által üzemeltetett virtuális gépeken?
Nem, a Windows Server rendszerű virtuális gépekhez az Azure Bastion-hez való hozzáféréshez nincs szükség [RDS CALra](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , ha kizárólag adminisztratív célokra használják.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Milyen billentyűzetkiosztások támogatottak a megerősített távoli munkamenet során?

Az Azure Bastion jelenleg a virtuális gépen belüli en-us-QWERTY billentyűzet-elrendezést támogatja.  A billentyűzet egyéb területi beállításainak támogatása folyamatban van.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Támogatott-e a felhasználó által megadott útválasztás (UDR) egy Azure-alapú megerősített alhálózaton?

Nem. Az UDR nem támogatott Azure-beli megerősített alhálózaton.
Azokban az esetekben, amelyekben az Azure Bastion és a Azure Firewall/Network Virtual Appliance (NVA) is szerepel ugyanabban a virtuális hálózatban, nem kell kényszeríteni az Azure-beli megerősített alhálózatról érkező forgalmat, hogy Azure Firewall, mert az Azure-és a virtuális gépek közötti kommunikáció privát. További információ: [Azure Firewall mögötti virtuális gépek elérése a Bastion-vel](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Miért kapok "a munkamenet lejárt" hibaüzenetet, mielőtt megkezdődik a megerősített munkamenet?

A munkamenetet csak a Azure Portal kezdeményezheti. Jelentkezzen be a Azure Portalba, és kezdje újra a munkamenetet. Ha közvetlenül egy másik böngésző-munkamenetből vagy-lapról nyitja meg az URL-címet, a rendszer ezt a hibát várta. Segít biztosítani, hogy a munkamenet biztonságosabb legyen, és hogy a munkamenet csak a Azure Portalon keresztül legyen elérhető.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hogyan kezeli az üzembe helyezési hibákat?

Tekintse át a hibaüzeneteket, és szükség szerint [küldjön egy támogatási kérést a Azure Portal](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) . Az üzembe helyezési hibák az [Azure-előfizetések korlátozásait, kvótáit és megkötéseit](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)okozhatják. Az ügyfelek az előfizetések által engedélyezett nyilvános IP-címek számának korlátozásával járhatnak, ami miatt az Azure Bastion üzembe helyezése sikertelen lesz.
