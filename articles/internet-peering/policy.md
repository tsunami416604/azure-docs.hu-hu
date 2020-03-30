---
title: Microsoft társviszony-létesítési szabályzat
titleSuffix: Azure
description: Microsoft társviszony-létesítési szabályzat
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775224"
---
# <a name="peering-policy"></a>Társviszony-létesítési szabályzat
A Microsoft hálózatra vonatkozó általános követelményeit az alábbi szakaszok ismertetik. Ezek a közvetlen társviszony-létesítési és az Exchange társviszony-létesítési kérelmekre egyaránt vonatkoznak.

## <a name="technical-requirements"></a>Technikai követelmények

* Teljesen redundáns hálózat, amely elegendő kapacitással rendelkezik a forgalom torlódás nélküli cseréjéhez.
* A társ nak nyilvánosan irányítható autonóm rendszerszáma (ASN) lesz.
* Mind az IPv4, mind az IPv6 támogatott, és a Microsoft elvárja, hogy mindkét típusú munkamenetet hozzon létre az egyes társviszony-létesítési helyeken.
* Az MD5 nem támogatott.
* **ASN részletek:**
    * A Microsoft az AS8075-öt a következő ASN-ekkel együtt kezeli: AS8068, AS8069, AS12076. Az AS8075 társviszony-létesítést tartalmazó ASN-ek teljes listáját az AS-SET MICROSOFT-ra hivatkozva.
    * A Microsofttal társviszonyban lévő felek minden fél vállalja, hogy semmilyen körülmények között nem fogadja el az AS12076 (Express Route) útvonalait, és minden társán ki kell szűrnie az AS12076-ot.
* **Útválasztási házirend:**
    * A társnak legalább egy nyilvánosan irányítható /24-es elesz.
    * A Microsoft felülírja a kapott multi-exit diszkriminatív (MED) rendszert.
    * A Microsoft inkább BGP közösségi címkéket szeretne kapni a társaktól az útvonal kezdeményezésének jelzésére.
    * A peernek regisztrálnia kell útvonalait egy nyilvános Internetes útválasztási nyilvántartó (IRR) adatbázisban szűrés céljából, és jóhiszeműerőfeszítéseket tesz ezen információk naprakészen tartására.
    * Azt javasoljuk, hogy a társak 1000 (IPv4) és 100 (IPv6) útvonalat állítsanak be a Microsofttal folytatott társviszony-létesítési munkamenetekben.
    * Hacsak nem állapodtak meg előzetesen, a társaknak minden olyan helyen konzisztens útvonalakat kell bejelenteniük, ahol a Microsofttal egyenrangúak.
    * Általánosságban elmondható, hogy az AS8075-tel létesített társviszony-létesítési munkamenetek az összes AS-MICROSOFT-útvonalat hirdetik. Az AFRIKÁban és Ázsiában összekötő AS8075 összeköttetések az adott régióra vonatkozó útvonalakra korlátozódhatnak.
    * Egyik fél sem hoz létre statikus útvonalat, végső útvonalat, vagy más módon nem küld forgalmat a másik félnek egy olyan útvonalra, amelyet nem a BGP-n keresztül jelentettek be.
    * Peers várhatóan betartják [manrs](https://www.manrs.org/) ipari szabványok útvonal biztonság.

## <a name="operational-requirements"></a>Működési követelmények
* A teljes személyzettel 24x7 Network Operations Center (NOC), amely képes segíteni a megoldás minden technikai és teljesítmény problémák, biztonsági jogsértések, szolgáltatásmegtagadási támadások, vagy bármely más visszaélés származó peer vagy ügyfeleik.
* Peers várhatóan egy teljes és up-to-date [profilpeeringDB](https://www.peeringdb.com) beleértve a 24x7 NOC e-mail a vállalati domain és telefonszám. Ezeket az információkat arra használjuk, hogy érvényesítsük a partner adatait, például a NOC-adatokat, a műszaki elérhetőségi adatokat, valamint a társviszony-létesítési létesítményekben való jelenlétüket stb. Személyes Yahoo, Gmail és hotmail fiókok nem fogadhatók el.

## <a name="physical-connection-requirements"></a>Fizikai kapcsolatkövetelményei
* Azok a helyek, ahol közvetlen társviszony-létesítéshez vagy Exchange-társviszony-létesítéshez csatlakozhat a Microsofttal, a [Társviszony-létesítés](https://www.peeringdb.com/net/694) i
* **Cseretárs-létesítés:**
    * Az összekapcsolásnak egymódusú szálon keresztül kell lennie a megfelelő 10 Gbps-os optikával.
    * A társak várhatóan akkor frissítik a portjaikat, ha a csúcskihasználtság meghaladja az 50%-ot.
* **Közvetlen társviszony-létesítés:**
    * Az összekapcsolásnak egymódusú szálon keresztül kell lennie a megfelelő 10 Gbps vagy 100 Gbps optika használatával.
    * A Microsoft csak közvetlen társviszony-létesítést hoz létre az internetszolgáltatóval vagy a hálózati szolgáltatókkal.
    * Peers várhatóan frissíteni a portok, ha csúcskihasználtság meghaladja az 50%, és fenntartani a különböző kapacitás minden metró, akár egy helyen, vagy több helyen a metró.
    * Minden közvetlen társviszony-létesítés két kapcsolatból áll a Társ peremhálózati útválasztóinak két Microsoft peremhálózati útválasztójával. A Microsoftnak két BGP-munkamenetre van szüksége ezeken a kapcsolatokon keresztül. A társ dönthet úgy, hogy nem telepíti a redundáns eszközöket a végén.

## <a name="traffic-requirements"></a>Közlekedési követelmények
* Peers over Exchange társviszony-létesítés kell legalább 200Mb forgalom és kevesebb, mint 2Gb.  A forgalom meghaladja a 2Gb Direct peering felül kell vizsgálni.
* Közvetlen társviszony-létesítés esetén a hálózatról a Microsoftra irányuló forgalomnak az alábbi minimális követelménynek kell megfelelnie.

    | Térség                      | Minimális forgalom a Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mbps                       |
    | APAC (Kivéve India)      |   2 Gbps                       |
    | APAC (csak Indiában)        | 500 Mbps                       |
    | Európa                   |   2 Gbps                       |
    | LATAM között                    |   2 Gbps                       |
    | Közel-Kelet              | 500 Mbps                       |
    | NA                       |   2 Gbps                       |

* **Sokszínűség:**
    * NA, Európa, APAC és LATAM, összekapcsolja legalább három földrajzilag különböző helyeken, ha megvalósítható, és fenntartani a különböző kapacitás, hogy a forgalom feladatátvétel az egyes metrókon.
    * Afrikában, a Közel-Keleten és Indiában a lehető legtöbb különböző helyen lehet összekapcsolódni. Elegendő változatos kapacitást kell fenntartania ahhoz, hogy a forgalom a régióban maradjon.

## <a name="next-steps"></a>További lépések

* A Közvetlen társviszony-létesítés Microsofttal való beállításának lépéseiről a [Közvetlen társviszony-létesítési útmutató című útmutatót is elolvashatja.](walkthrough-direct-all.md)
* Ha többet szeretne tudni az Exchange-társviszony-létesítés Microsofttal való beállításának lépéseiről, kövesse az [Exchange társviszony-létesítési útmutatóját.](walkthrough-exchange-all.md)