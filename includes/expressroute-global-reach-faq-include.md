---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178855"
---
### <a name="what-is-expressroute-global-reach"></a>Mi az ExpressRoute globális elérés?

Az ExpressRoute Global Reach egy Olyan Azure-szolgáltatás, amely a Microsoft globális hálózatán keresztül köti össze a helyszíni hálózatokat az ExpressRoute szolgáltatáson keresztül. Ha például kaliforniában van egy privát adatközpontja, amely a Szilícium-völgyben található ExpressRoute-hoz, texasi pedig egy másik magánadatközpontja a dallasi ExpressRoute-hoz csatlakozik az ExpressRoute Global Reach-hez, akkor a privát adatközpontokat a két ExpressRoute-kapcsolaton keresztül, és a több adatközpont-forgalom áthalad a Microsoft hálózati gerincén.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hogyan engedélyezhetem vagy tilthatom le az ExpressRoute globális elérést?

Az ExpressRoute globális elérését az ExpressRoute-áramkörök összekapcsolásával engedélyezheti. Az áramkörök leválasztásával letilthatja a funkciót. Lásd a [konfigurációt](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Szükségem van ExpressRoute Premium-ra az ExpressRoute globális eléréséhez?

Ha az ExpressRoute-áramkörök ugyanabban a geopolitikai régióban vannak, nincs szükség expressroute Premium-ra ahhoz, hogy összekapcsolja őket. Ha két ExpressRoute-kapcsolat különböző geopolitikai régióban található, mindkét kapcsolathoz ExpressRoute Premium-kapcsolatszükséges a közöttük való kapcsolat engedélyezéséhez. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hogyan kell fizetnem az ExpressRoute Globális Elérésért?

Az ExpressRoute lehetővé teszi a helyszíni hálózatés a Microsoft felhőszolgáltatások közötti kapcsolatot. Az ExpressRoute Global Reach lehetővé teszi a kapcsolatot a saját helyszíni hálózatai között a meglévő ExpressRoute-áramkörökön keresztül, kihasználva a Microsoft globális hálózatát. Az ExpressRoute globális elérésszámlázása a meglévő ExpressRoute-szolgáltatástól elkülönítve történik. Minden ExpressRoute-kapcsolaton engedélyezi ezt a szolgáltatást. Az ExpressRoute Globális elérés által engedélyezett helyszíni hálózatok közötti forgalmat a forrásnál kimenő forgalom, a célon pedig a kimenő forgalom díja határozza meg. Az árak azáramkörök zónáján alapulnak.

### <a name="where-is-expressroute-global-reach-supported"></a>Hol támogatott az ExpressRoute globális elérése?

Az ExpressRoute globális elérése [bizonyos országokban/régiókban vagy helyeken](../articles/expressroute/expressroute-global-reach.md)támogatott. Az ExpressRoute-áramköröket az adott országokban/régiókban vagy helyeken lévő társviszony-létesítési helyeken kell létrehozni.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Kettőnél több helyszíni hálózattal rendelkezem, amelyek mindegyike ExpressRoute-kapcsolathoz csatlakozik. Engedélyezhetem az ExpressRoute Globális elérésnek, hogy az összes helyszíni hálózatomat összekapcsolják?

Igen, mindaddig, amíg az áramkörök a támogatott országokban/régiókban vannak. Egyszerre két ExpressRoute-áramkört kell csatlakoztatnia. Teljesen hálózott hálózat létrehozásához fel kell sorolnia az összes áramkörpárt, és meg kell ismételnie a konfigurációt. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Engedélyezhetem az ExpressRoute globális elérését két ExpressRoute-kapcsolati kapcsolati kapcsolati kapcsolati kapcsolati kapcsolati kapcsolati kapcsolati hely között?

Nem. A két áramkörnek különböző társviszony-létesítési helyekről kell származnia. Ha egy támogatott országban/régióban egy metró egynél több ExpressRoute-társviszony-létesítési hely, összekapcsolhatja az ExpressRoute-áramkörök létre különböző társviszony-létesítési helyeken az adott metróban. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Ha az ExpressRoute Global Reach engedélyezve van az X és az Y áramkör között, valamint az Y és a Z áramkör között, az X és z áramkörhöz csatlakoztatott helyszíni hálózataim a Microsoft hálózatán keresztül beszélnek egymással?

Nem. A helyszíni hálózatok bármelyike közötti kapcsolat engedélyezéséhez explicit módon kell csatlakoztatnia a megfelelő ExpressRoute-áramköröket. A fenti példában az X és a Z áramkört kell csatlakoztatnia. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Mi az a hálózati átviteli érték, amelyre számíthatok a helyszíni hálózatok között, miután engedélyeztem az ExpressRoute globális elérési útját?

A helyszíni hálózatok közötti hálózati átviteli, az ExpressRoute Global Reach által engedélyezett, a két ExpressRoute-kapcsolatcsoport közül a kisebb korlátozza. A telephelyek és az Azure-ba irányuló forgalom és a helyiségek között lévő forgalom ugyanazon a kapcsolaton osztozik, és ugyanazok a sávszélesség-korlát vonatkoznak rájuk. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Az ExpressRoute Global Reach segítségével milyen korlátok vonatkoznak a hirdethető útvonalak számára és a megkapom a megkapható útvonalak számára vonatkozóan?

Az Azure-beli privát társviszony-létesítési útvonalak száma a Microsoft számára 4000-en marad egy standard áramkörön, vagy 10000 egy prémium szintű kapcsolatcsoporton. Az Azure-beli privát társviszony-létesítéssorán a Microsofttól kapott útvonalak száma az Azure virtuális hálózatainak útvonalainak és az ExpressRoute Global Reach-en keresztül csatlakoztatott egyéb helyszíni hálózatokútvonalainak összege lesz. Győződjön meg arról, hogy a helyszíni útválasztón beállította a megfelelő maximális előtagkorlátot. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Mi az ExpressRoute globális eléréssla?

Az ExpressRoute globális elérése ugyanazt a [rendelkezésre állási SLA-t](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) biztosítja, mint a rendszeres ExpressRoute-szolgáltatás.
