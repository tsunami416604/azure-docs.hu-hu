---
title: Privát zónák forgatókönyvei – Azure DNS
description: Ebben a cikkben az Azure DNS-beli magánzónák használatának gyakori forgatókönyveiből olvashat.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939343"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS-környezettel kapcsolatos zónák forgatókönyvei

Az Azure DNS-hálózati zónák névfeloldást biztosítanak egy virtuális hálózaton belül, valamint a virtuális hálózatok között. Ebben a cikkben néhány gyakori forgatókönyvet vizsgálunk, amelyek ezzel a funkcióval valósíthatók meg.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Eset: Egyetlen virtuális hálózatra ható névfeloldás
Ebben a forgatókönyvben egy virtuális hálózat az Azure-ban, amely számos Azure-erőforrások benne, beleértve a virtuális gépek (Virtuális gépek). A virtuális hálózaton belül ről szeretné feloldani az erőforrásokat egy adott tartománynév (DNS-zóná) segítségével, és a névfeloldásnak magánjellegűnek kell lennie, és nem érhető el az internetről. Továbbá a virtuális hálózatokon belüli virtuális gépek, az Azure-nak automatikusan regisztrálnia kell őket a DNS-zónába. 

Ez a forgatókönyv az alábbiakban látható. Az "A" nevű virtuális hálózat két virtuális gépet tartalmaz (VNETA-VM1 és VNETA-VM2). Ezek mindegyike privát IP-k társítva. Miután létrehozott egy contoso.com nevű privát zónát, és ezt a virtuális hálózatot regisztrációs virtuális hálózatként kapcsolta össze, az Azure DNS automatikusan két A rekordot hoz létre a zónában az ábrázolt módon. Most a VNETA-VM1-től a VNETA-VM2.contoso.com feloldásához leadott DNS-lekérdezések dns-választ kapnak, amely a VNETA-VM2 privát IP-címét tartalmazza. Továbbá a VNETA-VM1 (10.0.0.1) Private IP-címére vonatkozó fordított DNS-lekérdezés (PTR) a várt módon megkapja a VNETA-VM1 nevét tartalmazó DNS-választ. 

![Egyetlen virtuális hálózat felbontása](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Eset: Névfeloldás virtuális hálózatokon keresztül

Ez a forgatókönyv a leggyakoribb eset, ahol egy privát zónát több virtuális hálózathoz kell társítania. Ez a forgatókönyv illeszkedik architektúrák, például a Hub-and-Küllőmodell, ahol van egy központi Hub virtuális hálózat, amelyhez több más küllős virtuális hálózatok csatlakoznak. A központi hub virtuális hálózat a regisztrációs virtuális hálózat egy privát zónához kapcsolható, a küllős virtuális hálózatok pedig megoldási virtuális hálózatokként. 

Az alábbi ábrán a forgatókönyv egy egyszerű verziója látható, ahol csak két virtuális hálózat van – A és B. Az A regisztrációs virtuális hálózatként, a B pedig megoldási virtuális hálózatként van kijelölve. A cél az, hogy mindkét virtuális hálózat közös zónát contoso.com. A zóna létrehozásakor, és a megoldás és a regisztráció virtuális hálózatok kapcsolódnak a zónához, az Azure automatikusan regisztrálja a virtuális gépek (VNETA-VM1 és VNETA-VM2) DNS-rekordjait az A virtuális hálózatról. A B feloldási virtuális hálózatban a virtuális gépek zónájába manuálisan is hozzáadhat DNS-rekordokat. Ezzel a beállítással a következő viselkedést fogja megfigyelni a továbbító és fordított DNS-lekérdezéseknél:
* A VNETB-VM1 DNS-lekérdezése a B feloldási virtuális hálózatban VNETA-VM1.contoso.com esetén a VNETA-VM1 privát IP-címét tartalmazó DNS-választ kap.
* A 10.1.0.1-es feloldási virtuális hálózat B virtuális hálózatának VNETB-VM2 fordított DNS-lekérdezése a Teljes tartománynév-VNETB-VM1.contoso.com tartalmazó DNS-választ kap.  
* A VNETB-VM3 10.0.0.1-es feloldási virtuális hálózatB részében található VNETB-VM3 fordított DNS-lekérdezése NXDOMAIN-t kap. Ennek az az oka, hogy a fordított DNS-lekérdezések hatóköre csak ugyanahhoz a virtuális hálózathoz tartozik. 


![Több virtuális hálózati felbontás](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Forgatókönyv: Split-Horizon funkció

Ebben a forgatókönyvben van egy használati eset, ahol szeretné megvalósítani a különböző DNS-feloldási viselkedés attól függően, hogy hol az ügyfél ül (az Azure-on belül vagy az interneten kívül), ugyanazon DNS-zónában. Előfordulhat például, hogy az alkalmazás egy privát és nyilvános verziója eltérő funkciókkal vagy viselkedéssel rendelkezik, de mindkét verzióhoz ugyanazt a tartománynevet szeretné használni. Ez a forgatókönyv az Azure DNS-sel egy nyilvános DNS-zóna és egy privát zóna létrehozásával valósítható meg, azonos nevű.

Az alábbi ábra ezt a forgatókönyvet ábrázolja. Van egy virtuális hálózat, amely két virtuális gép (VNETA-VM1 és VNETA-VM2), amelyek mind a privát IP-k és a nyilvános IP-k lerendelt. Hozzon létre egy nyilvános DNS-zónát contoso.com nevű, és regisztrálja a nyilvános IP-k ezen virtuális gépek DNS-rekordok a zónán belül. Létrehoz egy privát DNS-zónát is, amelyet contoso.com a regisztrációs virtuális hálózatként megadott A értéket. Az Azure automatikusan regisztrálja a virtuális gépeket A-rekordként a privát zónába, a privát IP-kre mutatva.

Most, amikor egy internetes ügyfél kiad egy DNS-lekérdezést, hogy VNETA-VM1.contoso.com keressen, az Azure visszaadja a nyilvános IP-rekordot a nyilvános zónából. Ha ugyanazt a DNS-lekérdezést egy másik virtuális gép (például: VNETA-VM2) ugyanabban a virtuális hálózatban, az Azure visszaadja a privát IP-rekord a privát zónából. 

![Brian felbontásfelosztása](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>További lépések
További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

Ismerje meg, hogyan [hozhat létre privát DNS-zónát](./private-dns-getstarted-powershell.md) az Azure DNS-ben.

A DNS-zónákról és -rekordokról a következő felkeresi a következő [témakört: DNS-zónák és rekordok – áttekintés](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

