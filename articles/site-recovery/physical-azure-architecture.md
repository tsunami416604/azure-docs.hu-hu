---
title: "Az Azure Site Recovery Azure replikációs architektúrát a fizikai kiszolgáló |} Microsoft Docs"
description: "Ez a cikk áttekintést összetevők és használható, ha a helyszíni fizikai kiszolgálók replikálása Azure-bA az Azure Site Recovery szolgáltatással architektúra"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: raynew
ms.openlocfilehash: a8af2ee4a32925603d24aee2403ab504a0ca05a8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="physical-server-to-azure-replication-architecture"></a>Fizikai kiszolgálók Azure replikációs-architektúra

Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és helyreállítása fizikai Windows és Linux-kiszolgálók között egy helyszíni hely és az Azure, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő tábla és a kép adjon meg egy áttekintést nyújt az Azure-bA fizikai kiszolgáló replikációs összetevőt.  

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure storage-fiók és Azure-hálózatot. | A tárfiók tárolja a replikált adatokat a helyszíni virtuális gépek. Azure virtuális gépek jönnek létre a replikált adatok egy sikertelen történő futtatásakor a helyszíni Azure-bA. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | Egyetlen helyszíni fizikai gép vagy VMware virtuális gép központi telepítése a Site Recovery összetevők futtatása az összes az helyszíni. A virtuális gép fut, a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón. | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A folyamatkiszolgáló is telepíti a mobilitási szolgáltatást a replikálni kívánt kiszolgálókon.<br/><br/> A központi telepítés növekedésével további, különálló folyamat kiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.
 **Fő célkiszolgáló** | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> A nagyméretű telepítések esetében hozzáadhat egy további, külön fő célkiszolgáló feladat-visszavételre.
**A replikált kiszolgálók** | A mobilitási szolgáltatás telepítve van az egyes kiszolgálókon replikálja. | Azt javasoljuk, hogy engedélyezi az automatikus telepítési adatok a folyamatkiszolgálótól. Másik lehetőségként a szolgáltatás manuális telepítése, vagy egy automatikus telepítési módszer, például a System Center Configuration Manager használata.

**Fizikai Azure-architektúra**

![Összetevők](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. A központi telepítését, beleértve a helyszíni és az Azure összetevők beállítása. A Recovery Services-tárolónak kell megadni a forrásként és célként, állítsa be a konfigurációs kiszolgáló, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
2. Gépek replikálásához a replikációs házirendet, és egy kezdeti másolatot készít a kiszolgáló adatait az Azure storage replikálódik.
3. Kezdeti replikáció befejezését követően, a változási különbözeteket az Azure-bA replikálását kezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - Gépek kommunikálni a konfigurációs kiszolgáló HTTPS 443-as portot a bejövő, a replikáció kezelését.
    - Gépek replikációs adatokat küldeni a folyamatkiszolgáló HTTPS 9443 porton bejövő (módosítható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
4. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.


**Fizikai Azure replikáció folyamatban**

![Replikációs folyamat](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Replikáció be van állítva, és egy vész helyreállítási részletezéshez (feladatátvételi teszt) ellenőrizze, hogy minden a várt módon működik futtatását, miután futtatása feladatátvétel és a feladat-visszavétel szükség van. Vegye figyelembe:

- A tervezett feladatátvétel nem támogatott.
- Meg kell tudni vissza egy helyszíni VMware virtuális gép. Ez azt jelenti, hogy egy helyszíni VMware-infrastruktúra szükséges, akkor is, ha a helyszíni fizikai kiszolgálók replikálása az Azure-bA.
- Egyetlen gép feladatátvételt, vagy helyreállítási terveket, egyszerre több gép feladatátadását hozhat létre.
- Amikor feladatátvételt, Azure virtuális gépek replikált adatokból jönnek létre az Azure storage.
- Után váltanak ki a kezdeti feladatátvételi, véglegesítheti a munkaterhelés elérése az Azure virtuális gép elindításához.
- Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
- A feladat-visszavétel infrastruktúra beállításához szüksége többek között:
    - **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, állít be egy Azure virtuális gép folyamat kiszolgálója, az Azure-ból replikáció kezelésére. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    - **VPN-kapcsolat**: a feladat-visszavételt, kell egy VPN-kapcsolat (vagy Azure ExpressRoute) Azure-hálózatában a helyszíni hely.
    - **Külön fő célkiszolgáló**: alapértelmezés szerint a fő célkiszolgáló telepítése sikeres volt, a konfigurációs kiszolgálón, a helyszíni VMware virtuális gép, a feladat-visszavétel kezeli. Azonban ha vissza nagy mennyiségű forgalom sikertelen van szüksége, érdemes beállítania egy önálló helyszíni fő célkiszolgáló erre a célra.
    - **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez automatikusan jött létre, amikor a helyszíni Azure létrehozta a replikációs házirend.
    - **VMware-infrastruktúra**: egy VMware-infrastruktúra feladat-visszavételhez szüksége. Fizikai kiszolgáló nem használható a feladat-visszavételhez.
- Miután az összetevő a következő helyen, feladat-visszavétel három lépésben történik:
    - 1. fázis: Lássa el újból védelemmel az Azure virtuális gépeket, hogy azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    - 2. fázis: Feladatátvételt végez a helyszíni hely.
    - 3. fázis: Után munkaterhelések ismét sikertelen, akkor engedélyezi replikációs.

**Az Azure-ból VMware feladat-visszavétel**

![Feladat-visszavétel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](physical-azure-disaster-recovery.md) fizikai kiszolgálók Azure-replikációs szolgáltatására való engedélyezéséhez.
