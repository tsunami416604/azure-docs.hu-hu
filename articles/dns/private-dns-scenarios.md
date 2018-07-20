---
title: Az Azure DNS saját zónák forgatókönyvei
description: Az Azure DNS saját zónák használatára vonatkozó gyakori forgatókönyvek áttekintése.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: d84da36ad6b1ef3e2a507a0944aac583861d5ccb
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162167"
---
# <a name="azure-dns-private-zones-scenarios"></a>Az Azure DNS Private Zones forgatókönyvek
Az Azure DNS Private Zones egy virtuális hálózaton belül, valamint virtuális hálózatok közötti érvénnyel a névfeloldást biztosítanak. Ebben a cikkben áttekintjük néhány gyakori forgatókönyvet, amely ezzel a funkcióval megvalósíthatók. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Forgatókönyv: Névfeloldás hatóköre egyetlen virtuális hálózaton
Ebben a forgatókönyvben egy virtuális hálózatot az Azure-ban, amely rendelkezik egy Azure-erőforrások száma, beleértve a virtuális gépek (VM) rendelkezik. Szeretné feloldani az egy adott tartomány nevét (DNS-zóna) a virtuális hálózaton belül az erőforrásokat, és a személyes és nem érhető el az internetről névfeloldás van szüksége. Továbbá a virtuális gépek a virtuális hálózaton belül, szüksége lesz automatikusan regisztrálja őket a DNS-zónát az Azure. 

Ebben a forgatókönyvben az alábbi ábrának megfelelően. "A" nevű virtuális hálózatot két virtuális gép (a VM1-c és c-VM2) tartalmazza. Ezek mindegyike rendelkezik rendelt magánhálózati IP-címek. Hozzon létre egy contoso.com nevű privát zónát, és a egy regisztrációs virtuális hálózatként a virtuális hálózat összekapcsolása, az Azure DNS automatikusan létrehozza A két rekordot a zónában módon. Most már c-VM1 c-VM2.contoso.com feloldani a DNS-lekérdezéseket a DNS-választ, amely tartalmazza a magánhálózati IP-c-VM2 fog kapni. Ezenkívül a magánhálózati IP-c-VM1 (10.0.0.1) származó c-VM2 fordított DNS-lekérdezés (PTR) a várt módon c-VM1 nevét tartalmazó DNS-választ fog kapni. 

![Egyetlen virtuális hálózati feloldás](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>. Forgatókönyv: Névfeloldás virtuális hálózatok között

Ebben a forgatókönyvben a leggyakoribb eset, amikor több virtuális hálózat privát zónát társítani kell. Ebben a forgatókönyvben illeszkednek architektúrák, például a Központ-küllő modell ahol, mely több más küllő virtuális hálózatokhoz csatlakoznak, központi központi virtuális hálózaton van. A központi központi virtuális hálózaton a regisztrációs virtuális hálózatként privát zónához lehet társítani, és a feloldási virtuális hálózatok, virtuális hálózatok Társhálózatai lehet társítani. 

Az alábbi ábrán ez a forgatókönyv egyszerű verziója ahol csak két virtuális hálózat – a és B. A regisztrációs virtuális hálózatként van kijelölve, és a B elsődlegesként lett megjelölve a feloldási virtuális hálózatot. A cél az, mindkét virtuális hálózat egy közös zóna contoso.com megosztásához. Amikor létrejön a zóna és a megoldás és a regisztrációs virtuális hálózatok a zónához van csatolva, az Azure automatikusan regisztrálnak DNS-rekordok a virtuális gépek (a VM1-c és c-VM2), a virtuális hálózat A. Manuálisan is hozzáadhat DNS-rekordok a zónához a virtuális gépek feloldási virtuális hálózatban b Ezzel a beállítással azt tapasztalhatja továbbító és fordított irányú DNS-lekérdezések a következő viselkedés:
* A feloldási virtuális hálózatot B, c-VM1.contoso.com, a b-VM1 a DNS-lekérdezést a magánhálózati IP-c-VM1 tartalmazó DNS-választ fog kapni.
* A feloldási virtuális hálózatot a B 10.1.0.1, a b-VM2 fordított DNS (PTR) lekérdezés a teljes tartománynév b VM1.contoso.com tartalmazó DNS-választ fog kapni. A hiba oka, hogy címfeloldási DNS-lekérdezések hatóköre az azonos virtuális hálózatban. 
* A feloldási virtuális hálózatot a B 10.0.0.1, a b-VM3 fordított DNS (PTR) lekérdezés eredménye NXDOMAIN fog kapni. A hiba oka, hogy címfeloldási DNS-lekérdezések csak hatóköre az azonos virtuális hálózatban. 


![Több virtuális hálózati megoldások](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Forgatókönyv: Split zónaneveket funkció

Ebben a forgatókönyvben egy alkalmazási helyzet, vegye figyelembe a különböző DNS-névfeloldási viselkedése attól függően, hol helyezkedik el az ügyfél a (Azure-beli megoldásban vagy az interneten), amelyre az azonos DNS-zóna rendelkezik. Például előfordulhat, hogy az alkalmazás, amely különböző funkciókat, vagy viselkedés privát és nyilvános verziójával rendelkezik, de a ugyanazon verziójával is tartománynév használni kívánt. Ebben a forgatókönyvben az Azure DNS megvalósíthatók hozzon létre egy nyilvános DNS-zónát, valamint a privát zónát, ugyanazzal a névvel.

Az alábbi ábra bemutatja az ebben a forgatókönyvben. Két virtuális gép (a VM1-c és c-VM2) rendelkező, mind magánhálózati IP-címek rendelkező virtuális hálózatot A és a lefoglalt nyilvános IP-címek. Hozzon létre egy nyilvános DNS-zónát a contoso.com, és ezek a virtuális gépek DNS-rekordokat a zónában, a nyilvános IP-címek regisztrálhat. Privát DNS-zónát is adja meg A regisztrációs virtuális hálózatként contoso.com is hozzon létre. Az Azure automatikusan regisztrálja a virtuális gépek egy a rekordot a privát zónához, azok magánhálózati IP-címek mutat be.

Most egy internetes ügyfél kiad egy DNS-lekérdezés, c-VM1.contoso.com keresse ki, ha az Azure ad vissza a nyilvános IP-cím rekordot a nyilvános zónából. Ha ugyanazt a DNS-lekérdezést ad ki egy másik virtuális gépről (például: C-VM2) ugyanazon a virtuális hálózaton egy, az Azure vissza fogja küldeni a magánhálózati IP-rekordot a privát zónából. 

![Split Brian felbontás](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>További lépések
További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

Ismerje meg, hogyan [privát DNS-zóna létrehozása](./private-dns-getstarted-powershell.md) az Azure DNS-ben.

További információ a DNS-zónák és rekordok funkcionáló: [DNS-zónák és rekordok áttekintése](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

