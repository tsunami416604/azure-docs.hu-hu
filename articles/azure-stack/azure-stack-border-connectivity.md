---
title: "Kapcsolódási hálózati integráció szempontjai integrált Azure verem rendszerek szegély |} Microsoft Docs"
description: "Ismerje meg, mi mindent datacenter szegély hálózati kapcsolattal többcsomópontos Azure verem tervezését."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: c1a5496f3ab9a625d7d97c3096ae89100b7c5592
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="border-connectivity"></a>Szegély kapcsolat 
Egy fontos előfeltétel sikeres integrált Azure verem rendszerek üzembe helyezését, a művelet és a felügyeleti hálózati integráció tervezésre is. Szegély kapcsolat tervezési kezdődik-e használni a dinamikus útválasztási peremátjáró protokollal (BGP) kiválasztásával. Egy 16 bites BGP az autonóm rendszer számának (nyilvános vagy magán) hozzárendelése ehhez, vagy használja a statikus útválasztás, ahol alapértelmezett statikus útvonal hozzá van rendelve a szegély eszközök.

> [!IMPORTANT]
> Felső részén rack (TOR) kapcsolók igényel 3. rétegbeli kimenő kapcsolatok Point-to-Point IP-címek (/ 30 hálózatok) a fizikai interfészen konfigurált. 2. rétegbeli kimenő portokhoz Azure verem műveletek TOR-kapcsolók használata nem támogatott. 

## <a name="bgp-routing"></a>BGP-Útválasztás
Például a BGP egy dinamikus útválasztási protokoll használata biztosítja, hogy, hogy a rendszer mindig ismeri a hálózati módosítások, és megkönnyíti a felügyeletet. 

Ahogy a következő ábrán is látható, a magánhálózati IP-címe a hirdetési a TOR-kapcsoló a hely korlátozódik előtag-lista használatával. Az előtag listák megtagadja a magánhálózati IP-alhálózatok és a kapcsolatot a szabályok és a szegély az útvonal-térképe telepítené azt.

A szoftver terhelés terheléselosztó Állapotfigyelője dinamikusan hirdethető meg a virtuális IP-címek, a TOR-eszközökre az Azure-verem megoldás társak belül futó.

Hogy azonnal és transzparens módon felhasználói forgalom helyreállít a hiba, a VPC vagy a TOR-eszközök közötti konfigurált MLAG engedélyezi, hogy a több váz hivatkozás összevonása a gazdagépek és a HSRP vagy VRRP biztosító hálózati redundancia az IP-hálózatok.

![BGP-Útválasztás](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>a statikus útválasztás
Statikus útvonalak használatával több rögzített konfiguráció hozzáadása a szegély és a TOR-eszközökre. Ez bármilyen változás előtt alapos elemzése szükséges. Konfigurációs hiba okozta problémák több időt vehet attól függően, hogy a módosítások visszaállítani. Nem az ajánlott útválasztási módszer, de esetén támogatott.

Azure verem integrálja ezzel a módszerrel a hálózati környezethez, a szegély eszköz kell konfigurálni a TOR-eszközökön, hogy külső hálózat felé menő forgalom mutató statikus útvonalak nyilvános virtuális IP-címmel.

A TOR-eszközök az összes forgalom küldése a szegély eszközök statikus alapértelmezett útvonalat kell konfigurálni. Ez a szabály a egy forgalom kivétel magán-, a hely le lesz tiltva alkalmazni a TOR szegély kapcsolat hozzáférés-vezérlési lista használatával.

Ugyanaz, mint az első módszer minden más kell lennie. A BGP dinamikus útválasztási továbbra is használható az állványra belül, mert azt egy alapvető eszköz a SLB és egyéb összetevők és nem lehet letiltották vagy eltávolították.

![a statikus útválasztás](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transzparens proxy
Ha az Adatközpont minden forgalom a proxy használatát igényli, konfigurálnia kell egy *transzparens proxy* kezelnie házirend szerint a zónák a hálózat közötti forgalmat elválasztó állvány származó összes forgalmat feldolgozni.

> [!IMPORTANT]
> Az Azure-verem megoldás nem támogatja a szokásos webes proxykat.  

A transzparens proxy (más néven egy elfogja, beágyazott vagy kényszerített proxy) a hálózati rétegben normál kommunikáció elfogja anélkül, hogy semmilyen különleges ügyfél-konfigurációt. Az ügyfelek nem létezik-e a proxy tisztában lennie kell.

![Transzparens proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>További lépések
[DNS-integráció](azure-stack-integrate-dns.md)