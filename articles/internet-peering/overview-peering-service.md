---
title: Internetes társ-és peering szolgáltatás
titleSuffix: Azure
description: Internetes társ-és peering szolgáltatás
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75775704"
---
# <a name="internet-peering-vs-peering-service"></a>Internetes társ-és peering szolgáltatás

Az internetes társítás a Microsoft globális hálózata (AS8075) és a szállítók vagy a szolgáltatók hálózata közötti kapcsolatokra utal. A szolgáltató a következő, az ügyfél és a Microsoft hálózata közötti optimális útválasztást biztosító, megbízható és nagy teljesítményű nyilvános kapcsolat biztosítására is lehetőséget nyújt a szolgáltatói partnereknek.

## <a name="about-peering-service"></a>A Peering Service ismertetése
Az egyenrangú szolgáltatás a kulcsfontosságú szolgáltatók közötti partnerségi program, amely a vállalati felhasználók számára biztosít legjobb nyilvános Internet-kapcsolatot. A program részét képező partnereink közvetlen, magasan elérhető, Geo-redundáns kapcsolatokkal és optimalizált útválasztással rendelkeznek a Microsofthoz. A társítási szolgáltatás a Microsoft kapcsolódási portfóliójának kiegészítése:
*   ExpressRoute a IaaS-vagy a Pásti-erőforrásokhoz való magánhálózati kapcsolathoz (a magánhálózati IP-cím támogatása)
    *   Partner-alapú kapcsolat
    *   Közvetlen 100%-os kapcsolat a Microsofttal
*   Az interneten keresztüli IPSEC a felhőhöz való VPN-kapcsolathoz
*   SD-WAN-kapcsolat az Azure-ral a Virtual WAN használatával

A partneri szolgáltatás célként megadott szegmense az SaaS-kapcsolat, az SD-WAN-ügyfelek az internetet szeretnék megtenni az ág és minden olyan ügyfél számára, akik kettős stratégiai MPLS és nagyvállalati szintű internettel rendelkeznek.

Az Microsoft Cloudhoz való csatlakozáskor az elsődleges cél az, hogy csökkentse a késést azáltal, hogy csökkenti a felhasználói helyről a Microsoft globális hálózatra irányuló, a Microsoft nyilvános hálózati gerincét, amely összekapcsolja a Microsoft összes adatközpontját és több Felhőbeli alkalmazás belépési pontját. Tekintse [meg a legjobb kapcsolódás és teljesítmény az Office 365-ben](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694)című témakört.

> [!div class="mx-imgBorder"]
> ![Elosztott hozzáférés képe](./media/distributed-access.png)

A globális vállalat egyes fiókirodái a fenti ábrán a partner hálózatán keresztül a lehető legközelebbi Microsoft Edge-helyhez csatlakoznak.

**A társ-szolgáltatási ügyfél előnyei:**
* Az optimális teljesítmény és megbízhatóság érdekében a legjobb nyilvános útválasztás az interneten keresztül Microsoft Cloud szolgáltatásokhoz.
* Lehetőség az előnyben részesített SP kiválasztására a Microsoft Cloudhoz való kapcsolódáshoz.
* A forgalmi adatok, például a késési jelentések és az előtag-figyelés.
* Az optimális hálózati ugrások (ugrások) Microsoft Cloudból.
* Route Analytics és statisztika – Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) útvonal-rendellenességek (szivárgás/eltéríti észlelés) és az optimálisnál rosszabb útválasztás.

## <a name="peering-service-partnership-requirements"></a>Társ-szolgáltatási partnerségi követelmények
* Kapcsolódás Microsoft Cloudhoz az ügyfél számára legközelebb eső helyen. A partneri szolgáltató a felhasználó felé legközelebb eső Microsoft Edge-re irányítja át a felhasználói forgalmat. Hasonlóképpen, a felhasználó felé irányuló forgalom esetén a Microsoft átirányítja a forgalmat (a BGP-címkével) a felhasználóhoz legközelebb eső helyre, az SP pedig továbbítja a forgalmat a felhasználónak.
* A partner magas rendelkezésre állású, nagy teljesítményű és földrajzilag redundáns kapcsolatot tart fenn a Microsoft globális hálózatával.
* A partner kihasználhatja meglévő társait, hogy támogassa a partneri szolgáltatást, ha az megfelel a követelményeknek

## <a name="faq"></a>GYIK
A gyakori kérdésekért lásd: [peering Service – gyakori](service-faqs.md)kérdések.

## <a name="next-steps"></a>További lépések

* További információ az ügyfelek által nyújtott előnyökről a [partneri szolgáltatással](https://docs.microsoft.com/azure/peering-service/).
* Ismerje meg, hogy miként engedélyezhető a közvetlen társ-kezelés a társ-szolgáltatási [partneri útmutatóban](walkthrough-peering-service-all.md).
