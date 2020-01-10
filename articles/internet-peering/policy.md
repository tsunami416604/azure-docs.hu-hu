---
title: Microsoft-partneri házirend
titleSuffix: Azure
description: Microsoft-partneri házirend
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775224"
---
# <a name="peering-policy"></a>Egyenrangú házirend
A Microsoft általános követelményeit a hálózatról az alábbi részben ismertetjük. Ezek a közvetlen társ-és Exchange-kérelmekre egyaránt érvényesek.

## <a name="technical-requirements"></a>Technikai követelmények

* Egy teljesen redundáns hálózat, amely elegendő kapacitással rendelkezik a forgalom zsúfoltság nélküli cseréjéhez.
* A társ nyilvánosan irányítható autonóm rendszerszámmal (ASN) fog rendelkezni.
* Mind az IPv4-, mind az IPv6-támogatás támogatott, és a Microsoft mindkét típusú munkamenetet arra vár, hogy minden egyes egyenrangú helyen.
* Az MD5 nem támogatott.
* **ASN-részletek:**
    * A Microsoft a következő ASN együtt kezeli a AS8075: AS8068, AS8069, AS12076. A AS8075-társítással rendelkező ASN teljes listájáért TEKINTse meg a MICROSOFTra vonatkozó hivatkozást.
    * A Microsofttal folytatott összes fél beleegyezik abba, hogy semmilyen körülmények között nem fogad el útvonalakat a AS12076 (Express Route), és minden társon ki kell szűrnie a AS12076.
* **Útválasztási házirend:**
    * A társnak legalább egy nyilvánosan irányítható/24 szintűnek kell lennie.
    * A Microsoft felülírja a beérkezett többszörös kilépéseket (MED).
    * A Microsoft szívesebben kap BGP-közösségi címkéket a társaitól, hogy jelezze az útvonal eredetét.
    * A társnak az útvonalakat egy nyilvános internetes útválasztási regisztrációs (IRR) adatbázisban kell regisztrálnia a szűrés céljából, és jóhiszemű erőfeszítéseket tesz az információk naprakészen tartásához.
    * Javasoljuk, hogy a társaink a Microsofttal való összekapcsolási munkamenetekben a 1000 (IPv4) és a 100 (IPv6) útvonalakat is beállítsák.
    * Ha előzetesen nem határoz meg külön megállapodást, a partnereknek minden olyan helyen be kell jelenteniük a konzisztens útvonalakat, ahol a Microsofttal foglalkoznak.
    * Általánosságban elmondható, hogy a AS8075-vel folytatott társ-munkamenetek az összes MICROSOFT-útvonalat hirdetik. Az AS8075-összekötők Afrikában és Ázsiában az adott régióra vonatkozó útvonalakra korlátozódnak.
    * Egyik fél sem hoz létre statikus útvonalat, a Last Resort útvonalát, vagy más fél számára a BGP-n keresztül nem bejelentett útvonalon továbbítja a forgalmat.
    * A partnereknek meg kell felelniük a [MANRS](https://www.manrs.org/) iparági szabványoknak.

## <a name="operational-requirements"></a>Üzemeltetési követelmények
* Egy teljes körűen felhasználható nonstop hálózati operatív központ (NOC), amely segítséget nyújt az összes műszaki és teljesítménnyel kapcsolatos probléma, a biztonsági szabálysértések, a szolgáltatásmegtagadási támadások elutasítása vagy a társon vagy az ügyfeleken kívüli visszaélések megoldásában.
* A partnereknek a [PeeringDB](https://www.peeringdb.com) teljes és naprakész profillal kell rendelkezniük, beleértve a vállalati tartományból és a telefonszámból származó nonstop Noc-e-maileket. Ezen információk alapján érvényesítjük a partner adatait, például a NOC-információkat, a technikai kapcsolattartási adatokat és azok jelenlétét a társas létesítményekben stb. Személyes Yahoo-, Gmail-és Hotmail-fiókok nem fogadhatók el.

## <a name="physical-connection-requirements"></a>Fizikai kapcsolatok követelményei
* A [PeeringDB](https://www.peeringdb.com/net/694) -ben a Microsofttal a közvetlen társ-vagy Exchange-kapcsolattal való kommunikációhoz használható webhelyek listája szerepel.
* **Exchange-társ:**
    * Az összekapcsolásnak a megfelelő 10Gbps-optika használatával kell lennie egy egymódusú szálon.
    * A társak a portok frissítéséhez szükségesek, amikor a csúcsérték kihasználtsága meghaladja a 50%-ot.
* **Közvetlen társítás:**
    * Az összekapcsolásnak a megfelelő 10Gbps vagy 100Gbps-optika használatával kell lennie egy egymódusú szálon.
    * A Microsoft csak az INTERNETSZOLGÁLTATÓval vagy a hálózati szolgáltatókkal hoz létre közvetlen kapcsolatot.
    * A főkiszolgálók a portok frissítését várhatóan akkor is meghaladják, ha a maximális kihasználtság meghaladja a 50%-ot, és az egyes metrókban a különböző kapacitásokat, akár egy helyen, akár a metró több pontján belül megtart
    * Az egyes közvetlen társítások két, két Microsoft Edge-útválasztóból álló kapcsolattal rendelkeznek a társának peremhálózati útválasztói között. A Microsoft kettős BGP-munkameneteket igényel ezeken a kapcsolatokon keresztül. A társ dönthet úgy, hogy nem helyez üzembe redundáns eszközöket a végén.

## <a name="traffic-requirements"></a>Forgalmi követelmények
* Az Exchange-kapcsolaton keresztüli társak esetében legalább 200 MB-os forgalomnak és 2 GB-nál kisebbnek kell lennie.  A 2 GB-os közvetlen munkatársát meghaladó forgalomnak felül kell vizsgálnia.
* A közvetlen összevonáshoz a hálózatról a Microsoft felé irányuló forgalomnak az alábbi minimális követelménynek kell megfelelnie.

    | Geo (Térség)                      | Minimális forgalom a Microsoftnak   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mb/s                       |
    | APAC (India kivételével)      |   2 Gbps                       |
    | APAC (csak India)        | 500 Mb/s                       |
    | Európa                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | Közel-Kelet              | 500 Mb/s                       |
    | n/a                       |   2 Gbps                       |

* **Sokszínűséget**
    * A NA-ban Európa, a APAC és a LATAM legalább három földrajzilag változatos helyen áll, ha lehetséges, és az egyes metrón belüli feladatátvételt lehetővé tevő különböző kapacitást tart fenn.
    * Afrikában, Közel-Keleten és Indiában a lehető legtöbb különböző helyen lehet összekapcsolni. A megfelelő kapacitást kell fenntartania ahhoz, hogy a forgalom a régión belül maradjon.

## <a name="next-steps"></a>Következő lépések

* Ha szeretne többet megtudni a Microsofttal való közvetlen együttműködés beállításához szükséges lépésekről, kövesse a közvetlen összevonási [útmutató](walkthrough-direct-all.md)utasításait.
* A Microsofttal való Exchange-társítás beállításával kapcsolatos lépésekért kövesse az [Exchange-partneri útmutató](walkthrough-exchange-all.md)című témakört.