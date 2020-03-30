---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374075"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Mely régiók érhetők el?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Szükségem van egy nyilvános IP-címre a virtuális gépemen?

Amikor az Azure Bastion használatával csatlakozik egy virtuális géphez, nincs szüksége nyilvános IP-címre az On Azure virtuális gépen, amelyhez csatlakozik. A Bástya szolgáltatás megnyitja az RDP/SSH munkamenet/kapcsolat a virtuális gép pelenka a virtuális gép privát IP-a virtuális hálózaton belül.

### <a name="is-ipv6-supported"></a>Támogatott az IPv6?

Jelenleg az IPv6 nem támogatott. Az Azure Bastion csak az IPv4 protokollt támogatja.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Szükségem van RDP vagy SSH ügyfélre?

Nincs szükség RDP vagy SSH-ügyfél az RDP/SSH eléréséhez az Azure virtuális gép az Azure Portalon. Az [Azure Portal](https://portal.azure.com) használatával rdp/SSH hozzáférést kaphat a virtuális géphez közvetlenül a böngészőben.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Az Azure Bastion igényel RDS CAL felügyeleti célokra az Azure által üzemeltetett virtuális gépeken?
Nem, a Windows Server virtuális gépek hez való hozzáférés az Azure Bastion nem igényel [RDS cal,](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) ha kizárólag felügyeleti célokra használják.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hány egyidejű RDP- és SSH-munkamenetet támogat az egyes Azure-bástyák?
Mind az RDP, mind az SSH egy használatalapú protokoll. A munkamenetek magas használata azt eredményezi, hogy a megerősített gazdagép alacsonyabb számú munkamenetet támogat. Az alábbi számok normál napi munkafolyamatokat feltételeznek.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Szükségem van egy ügynök fut az Azure virtuális gépen?

Nem kell telepítenie egy ügynököt vagy bármilyen szoftvert a böngészőben vagy az Azure virtuális gépén. A Bastion szolgáltatás ügynök nélküli, és nem igényel további szoftvert az RDP/SSH-hoz.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Mely böngészők támogatottak?

Használja a Microsoft Edge böngészőt vagy a Google Chrome-ot Windows rendszeren. Apple Mac esetén használja a Google Chrome böngészőt. A Microsoft Edge Chromium windowsos és maces rendszeren is támogatott.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Szükség van-e szerepkörökre a virtuális gépek eléréséhez?

A kapcsolat létrejöttéhez a következő szerepkörökszükségesek:

* Olvasói szerepkör a virtuális gépen
* Olvasói szerepkör a virtuális gép privát IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure Bastion erőforráson

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Mi az árképzés?

További tájékoztatás a [díjszabási lapon](https://aka.ms/BastionHostPricing) olvasható.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Miért jelenik meg a "A munkamenet lejárt" hibaüzenet a Megerősített munkamenet kezdete előtt?

Munkamenetet csak az Azure Portalról kell kezdeményezni. Jelentkezzen be az Azure Portalon, és kezdje újra a munkamenetet. Ha közvetlenül egy másik böngészőmunkamenetből vagy lapról lép az URL-címre, ez a hiba várható. Ez segít biztosítani, hogy a munkamenet biztonságosabb, és hogy a munkamenet csak az Azure Portalon keresztül érhető el.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Milyen billentyűzetkiosztások támogatottak a bastion távoli munkamenete során?

Az Azure Bastion jelenleg támogatja az en-us-qwerty billentyűzetkiosztást a virtuális gépen belül.  A billentyűzetkiosztás egyéb területi beállításainak támogatása folyamatban van.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>A felhasználó által definiált útválasztás (UDR) támogatott egy Azure Bastion alhálózaton?

Nem. Az UDR nem támogatott az Azure Bastion alhálózaton.
Az Azure Bastion és az Azure Firewall/Network Virtual Appliance (NVA) ugyanazon a virtuális hálózatban lévő forgatókönyvek esetében nem kell az Azure Bastion alhálózatról az Azure Firewall-re irányuló forgalmat kényszerítenie, mivel az Azure Bastion és a virtuális gépek közötti kommunikáció privát. További információ: [A virtuális gépek elérése az Azure Firewall mögött a Bastion.](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>Az Azure Bastion RDP-munkamenet támogatja a fájlátvitelt?

Keményen dolgozunk, hogy új funkciókat. Jelenleg a fájlátvitel nem támogatott, de része az ütemtervünknek. Kérjük, ossza meg visszajelzését az új funkciókról az [Azure Bastion Feedback oldalon.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hogyan kezelhetem a központi telepítési hibákat?

Tekintse át a hibaüzeneteket, és szükség [szerint nyújtson be támogatási kérelmet az Azure Portalon.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) A telepítési hibák az [Azure előfizetési korlátokból, kvótákból és korlátozásokból](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)eredhetnek. Pontosabban az ügyfelek előfordulhat, hogy korlátozza az előfizetésenként engedélyezett nyilvános IP-címek számát, ami az Azure Bastion üzembe helyezését sikertelensé teszi.
