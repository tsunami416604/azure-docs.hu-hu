---
title: "Hogyan működik a VMware-replikáció az Azure-ba az Azure Site Recoveryben? | Microsoft Docs"
description: "Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépeknek és fizikai kiszolgálóknak az Azure Site Recovery szolgáltatással az Azure-ba történő replikációjakor használt összetevőkről és architektúráról"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: vmware-walkthrough-architecture
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 81f02c1277ae8a2c377ca0d6db67ec4211e9aa5e
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017

---



<a id="how-does-vmware-replication-to-azure-work-in-site-recovery" class="xliff"></a>

# Hogyan működik a VMware-replikáció az Azure-ba a Site Recoveryben?

Ez a cikk leírja a helyszíni VMware virtuális gépeknek és Windows/Linux fizikai kiszolgálóknak az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással az Azure-ba történő replikációjakor használt összetevőkről és folyamatokról.

Amikor fizikai helyszíni kiszolgálókat replikál az Azure-ba, a replikációs forgatókönyv ugyanazokat az összetevőket és folyamatokat használja, mint a VMware virtuálisgép-replikálás, a következő eltérésekkel:

- VMware virtuális gép helyett fizikai kiszolgálót használhat konfigurációs kiszolgálóként.
- A feladat-visszavételhez helyszíni VMware-infrastruktúrára van szükség. Fizikai gép nem használható a feladat-visszavételhez.

Megjegyzéseket a cikk alján tehet, kérdéseket pedig az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.


<a id="architectural-components" class="xliff"></a>

## Az architektúra összetevői

VMware virtuális gépek és fizikai kiszolgálók replikálásában számos összetevőnek része van.

**Összetevő** | **Hely** | **Részletek**
--- | --- | ---
**Azure** | Az Azure-ban szüksége van egy Azure-fiókra, egy Azure Storage-fiókra és egy Azure-hálózatra. | A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | Egy helyszíni felügyeleti kiszolgáló (VMware virtuális gép), amelyen az üzembe helyezéshez szükséges valamennyi helyszíni összetevő, így a konfigurációs kiszolgáló, folyamatkiszolgáló és fő fogadó kiszolgáló is fut | A konfigurációs kiszolgáló összetevő koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A folyamatkiszolgáló ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését.<br/><br/> Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.
 **Fő célkiszolgáló** | Alapértelmezés szerint telepítve van a helyszíni konfigurációs kiszolgálón. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> Ha a feladat-visszavételi adatforgalom kötetei nagyok, a feladat-visszavételhez üzembe helyezhet egy másik fő célkiszolgálót.
**VMware-kiszolgálók** | A VMware virtuális gépek vSphere ESXi-kiszolgálókon futnak, és a gazdagépek felügyeletéhez egy vCenter-kiszolgáló üzembe helyezését javasoljuk. | Vegyen fel VMware-kiszolgálókat a Recovery Services-tárolóba.
**Replikált gépek** | A mobilitási szolgáltatás az összes replikálni kívánt VMware virtuális gépen telepítve lesz. A szolgáltatás manuálisan is telepíthető az egyes gépekre, de leküldéses telepítés is végrehajtható a folyamatkiszolgálóról.

Az egyes összetevők telepítési előfeltételeiről és követelményeiről a [támogatási mátrix](site-recovery-support-matrix-to-azure.md) ad tájékoztatást.

**1. ábra: Összetevők: VMware-ről Azure-ra**

![Összetevők](./media/site-recovery-components/arch-enhanced.png)

<a id="replication-process" class="xliff"></a>

## Replikációs folyamat

1. Az üzemelő példány beállításának részeként az Azure-összetevőket és egy Recovery Services-tárolót is be kell állítani. A tárolóban meg kell adni a replikáció forrás- és célhelyét, be kell állítani a konfigurációs kiszolgálót, fel kell venni VMware-kiszolgálókat, replikációs szabályzatot kell létrehozni, üzembe kell helyezni a mobilitási szolgáltatást, engedélyezni kell a replikálást, illetve futtatni kell egy feladatátvételi tesztet.
2.  A gépek a replikációs szabályzat szerint kezdik meg a replikálást, és az adatok kezdeti másolata az Azure-tárolóba lesz replikálva.
4. Az Azure változáskülönbözeteinek replikációja a kezdeti replikálás befejezése után kezdődik el. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - A replikációs folyamat kezelése érdekében a replikálást végző gépek a 443-as bejövő HTTPS-porton kommunikálnak a konfigurációs kiszolgálóval.
    - A replikálást végző gépek a 9443-as bejövő HTTPS-porton küldik el a replikációs adatokat a folyamatkiszolgálónak (ez a beállítás konfigurálható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezve van a több virtuális gépre kiterjedő konzisztencia, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
5. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#public-peering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

**2. ábra: Replikáció VMware-ről Azure-ra**

![Továbbfejlesztett](./media/site-recovery-components/v2a-architecture-henry.png)

<a id="failover-and-failback-process" class="xliff"></a>

## Feladatátvételi és feladat-visszavételi folyamat

1. Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon működik-e, igény szerint nem tervezett feladatátvételeket futtathat az Azure-hoz. A tervezett feladatátvétel nem támogatott.
2. Elvégezheti egyetlen gép feladatátvételét, vagy létrehozhat több virtuális gép feladatátvételét tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
3. Feladatátvétel futtatásakor az Azure-ban replikaként létrehozott virtuális gépek jönnek létre. Akkor kell feladatátvételt végezni, ha hozzá szeretne férni a replikaként létrehozott Azure virtuális gép számítási feladataihoz.
4. Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat. Ehhez be kell állítani a feladat-visszavételi infrastruktúrát, el kell kezdeni a gép replikálását a másodlagos helyről az elsődlegesre, valamint nem tervezett feladatátvételt kell futtatni a másodlagos helyről. Ezen feladatátvétel végrehajtását követően az adatok visszakerülnek a helyszíni helyre, és az Azure-ba történő replikációt újra engedélyezni kell. [További információ](site-recovery-failback-azure-to-vmware.md)

A feladat-visszavételre vonatkozó követelmények:


- **Ideiglenes folyamatkiszolgáló az Azure-ban**: ha feladatátvételt követően szeretné visszaadni a feladatokat az Azure-ból, be kell állítania egy folyamatkiszolgálóként üzemelő Azure virtuális gépet, amely kezeli az Azure-ból való replikációt. Ez a virtuális gép a feladatok visszaadását követően törölhető.
- **VPN-kapcsolat**: a feladat-visszavételhez szüksége lesz egy VPN-kapcsolatra (vagy Azure ExpressRoute-ra), amelyet a helyszíni hely Azure-hálózatában kell beállítania.
- **Önálló helyszíni fő célkiszolgáló**: a helyszíni fő célkiszolgáló kezeli a feladat-visszavételt. A fő célkiszolgálót alapértelmezés szerint a felügyeleti kiszolgálóra kell telepíteni, de nagyobb mértékű forgalom feladat-visszavétele esetén érdemes ebből a célból egy önálló helyszíni fő célkiszolgálót is létrehozni.
- **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A replikációs szabályzat létrehozásakor a rendszer ezt automatikusan létrehozza.
- **VMware-infrastruktúra**: A feladatot egy helyszíni VMware virtuális gépnek kell visszavennie. Ez azt jelenti, hogy helyszíni VMware-infrastruktúrára akkor is szükség van, ha helyszíni fizikai kiszolgálókat replikál az Azure-ba.

**3. ábra: VMware-/fizikai gépek közötti feladat-visszavétel**

![Feladat-visszavétel](./media/site-recovery-components/enhanced-failback.png)


<a id="next-steps" class="xliff"></a>

## Következő lépések

[Támogatási mátrix](site-recovery-support-matrix-to-azure.md) áttekintése

