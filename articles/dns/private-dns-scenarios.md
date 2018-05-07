---
title: Az Azure DNS-saját zónák forgatókönyvek |} Microsoft Docs
description: Azure saját DNS-zónák használatára vonatkozó gyakori forgatókönyvek áttekintése.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure saját DNS-zónák forgatókönyvek
Saját DNS-zónák Azure virtuális hálózaton belül, valamint virtuális hálózatok közötti névfeloldás biztosítása. Ebben a cikkben úgy tekintünk olyan gyakori forgatókönyveket tartalmaz, amely a szolgáltatás használatával kell megvalósítani. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Forgatókönyv: Névfeloldás hatókörű egyetlen virtuális hálózathoz
Ebben a forgatókönyvben az Azure-ban, amely rendelkezik egy Azure-erőforrások száma, beleértve a virtuális gépek (VM) virtuális hálózattal rendelkezik. Szeretné feloldani a virtuális hálózaton keresztül egy adott tartomány nevét (DNS-zóna) lévő a erőforrásait, és meg kell-e a névfeloldás magán és nem elérhető az internetről. Továbbá a virtuális gépekhez a VNETEN belül, kell Azure automatikusan regisztrálja őket a DNS-zónát. 

Ebben a forgatókönyvben az alábbi ideális. "A" nevű virtuális hálózat két virtuális gépet (VNETA-VM1 VNETA-vm2 virtuális gépnek) tartalmaz. Ezek mindegyikének rendelkezik tartozó privát IP-címek. Miután neve contoso.com saját zóna létrehozása és csatolása a virtuális hálózat regisztrációs virtuális hálózati, Azure DNS automatikusan létrehozza két A típusú rekordot a zónában, ábrázolva. Most VNETA-VM1 VNETA-VM2.contoso.com feloldani a DNS-lekérdezéseket a DNS-választ, amely tartalmazza a privát IP-címe VNETA-vm2 virtuális gépnek fog kapni. Ezenkívül a saját IP-címe (10.0.0.1) VNETA-VM1 VNETA-vm2 virtuális gépnek kibocsátott névkeresési DNS-lekérdezést (PTR) a DNS-választ, amely VNETA-VM1, a nevét tartalmazza a várt módon fog kapni. 

![Egyetlen virtuális hálózati feloldás](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Forgatókönyv: Virtuális hálózatok közötti feloldása

Ebben a forgatókönyvben a helyzet gyakori kell egy titkos zóna társítandó több virtuális hálózat. Ebben a forgatókönyvben például a központ Küllős modell architektúrák fér. Amennyiben nincs, mely több más Küllős virtuális hálózatokhoz csatlakoznak, központi Hub virtuális hálózat. A központi Hub virtuális hálózat és a regisztrációs virtuális hálózata titkos zónához lehet társítani, és a Küllős virtuális hálózatok feloldási virtuális hálózatokként lehet társítani. 

Az alábbi ábrán látható az ebben a forgatókönyvben egy egyszerű verziójában ahol csak két virtuális hálózat – az A és B. A kijelölt regisztrációs virtuális hálózat és a B feloldási virtuális hálózat van kijelölve. Mindkét virtuális hálózat megosztani egy közös contoso.com zóna célja. Ha a zóna jön létre, és a megoldás és a regisztrációs virtuális hálózatok a zónához van csatolva, Azure automatikusan regisztrálja DNS-rekordokat a virtuális gépek (VNETA-VM1 és VNETA-vm2 virtuális gépnek) a virtuális hálózati A. a Is kézzel is felveheti a DNS-rekordokat a zónába virtuális gépek felbontása a virtuális hálózatban a b kiszolgálóra. Ez a beállítás megfigyelheti a cím- és DNS-lekérdezéseket az alábbiakra:
* A DNS-lekérdezések VNETB-VM1 a megoldás virtuális hálózat B, VNETA-VM1.contoso.com, a saját IP-címe VNETA-VM1 tartalmazó DNS-választ kap.
* A névkeresési DNS (PTR) lekérdezés VNETB-vm2 virtuális gépnek a megoldás virtuális hálózat B, 10.1.0.1, a DNS-választ, amely tartalmazza a teljes tartománynév VNETB VM1.contoso.com fog kapni. A hiba oka, hogy az azonos virtuális hálózatban címfeloldási DNS-lekérdezések hatóköre. 
* A névkeresési DNS (PTR) lekérdezés VNETB-VM3 a megoldás virtuális hálózat B, 10.0.0.1, NXDOMAIN fog kapni. A hiba oka, hogy a címfeloldási DNS-lekérdezések csak hatókörű ugyanahhoz a virtuális hálózathoz. 


![Több virtuális hálózati megoldások](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Forgatókönyv: Vegyes-Horizon funkció

Ebben a forgatókönyvben megvalósításához másik DNS-névfeloldási viselkedése attól függően, hogy hol helyezkedik el az ügyfelet a (Azure belül vagy az interneten), ahová használati eset ugyanazt a DNS-zóna rendelkezik. Például előfordulhat, hogy az alkalmazás különböző funkcióit vagy viselkedés privát és nyilvános verziójával rendelkezik, de szeretné használni mindkét verzióihoz ugyanazon a néven. Ebben a forgatókönyvben is kell megvalósítani, az Azure DNS-ben hozzon létre egy nyilvános DNS-zónát, valamint a Magánfelhő zóna ugyanazzal a névvel.

A következő ábra szemlélteti az ebben a forgatókönyvben. Egy virtuális hálózatban A két virtuális gépet (VNETA-VM1 VNETA-vm2 virtuális gépnek) rendelkező mindkét privát IP-cím van, és lefoglalja a nyilvános IP-címek. Hozzon létre egy nyilvános DNS-zónát contoso.com, és a nyilvános IP-címek regisztrálni a virtuális gépeken, a DNS-rekordokat a zónán belül. Is hozzon létre egy saját DNS-zónát, más néven A megadásával a regisztrációs virtuális hálózatként contoso.com. Azure automatikusan regisztrálja a virtuális gépek rögzíti a saját zónához, a saját IP-címek mutat.

Most egy internetes ügyfél VNETA-VM1.contoso.com keresésére DNS-lekérdezést ad ki, amikor Azure visszatér a nyilvános IP-cím rekord a nyilvános zónájából. Ha a ugyanazt a DNS-lekérdezést ad ki egy másik virtuális gépről (például: VNETA-vm2 virtuális gépnek) az azonos virtuális hálózatban A Azure a privát IP-címrekordhoz visszatér a titkos zónájából. 

![Vegyes Brian felbontás](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>További lépések
További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

Megtudhatja, hogyan [hozzon létre egy saját DNS-zóna](./private-dns-getstarted-powershell.md) Azure DNS-ben.

További információk a DNS-zónák és rekordok ellátogatva: [DNS-zónák és áttekintése rögzíti](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

