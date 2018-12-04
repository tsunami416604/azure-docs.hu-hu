---
title: Az Azure-bA az Azure Site Recovery használata vész-helyreállítási fizikai kiszolgáló architektúrája |} A Microsoft Docs
description: Ez a cikk a helyszíni fizikai kiszolgálók-Azure-bA az Azure Site Recovery szolgáltatással vészhelyreállítás során használt összetevőkről és architektúráról áttekintést nyújt.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 5202ba400d4083d75049583fb1a36f71b0e33727
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849003"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fizikai kiszolgáló Azure vész-helyreállítási architektúra

Ez a cikk azt ismerteti, architektúrájának és folyamatainak használatos, ha replikálni, a feladatátvétel és helyreállítás a fizikai Windows és Linux-kiszolgálók között egy helyszíni hely és az Azure használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő táblázat és grafikus adja meg az Azure-bA fizikai kiszolgáló replikációjához használt összetevők magas szintű áttekintést.  

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, az Azure storage-fiók és Azure-hálózatra. | A tárfiókot a replikált adatokat a helyszíni virtuális gépek tárolódik. Azure virtuális gépek létrejönnek a replikált adatokkal futtatásakor feladatátvétel a helyszínről az Azure-bA. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | Egy a helyszíni fizikai gép vagy a VMware virtuális gép üzemel, a futtatásához a helyszíni Site Recovery-összetevőit. A virtuális gép fut, a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgáló. | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt kiszolgálókon.<br/><br/> Az üzembe helyezés növekedésével további, külön folyamatkiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.
 **Fő célkiszolgáló** | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> Nagyméretű környezetekben hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.
**A replikált kiszolgálók** | A mobilitási szolgáltatás minden replikált kiszolgálón telepítve van. | Azt javasoljuk, hogy engedélyezi az automatikus telepítési adatok a folyamatkiszolgálótól. Másik lehetőségként a szolgáltatás manuális telepítése, vagy egy automatikus telepítési módszer, például a System Center Configuration Managerrel használja.

**Fizikairól az Azure-architektúra**

![Összetevők](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. A központi telepítését, beleértve a helyszíni és Azure-összetevők beállítása. A Recovery Services-tárolóban adja meg a replikációs forrást és célt, állítsa be a konfigurációs kiszolgálót, létrehoz egy replikációs házirendet, és engedélyezze a replikációt.
2. A replikációs házirendet, és a egy kezdeti másolatot készít a kiszolgáló adatainak megfelelően a gépek replikálásához az Azure storage replikálása.
3. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - Gépek kommunikálnak a konfigurációs kiszolgáló HTTPS a 443-as porton bejövő, a replikáció kezelését.
    - Gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak HTTPS 9443-as porton bejövő (módosítható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
4. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.


**Fizikairól az Azure-bA folyamat**

![Replikációs folyamat](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután replikáció be van állítva, és futtatott egy vészhelyreállítási próba végrehajtása (teszt feladatátvétel) ellenőrizze, hogy minden a várt módon működik, futtathatja feladatátvétel és feladat-visszavétel szükség szerint. Vegye figyelembe:

- A tervezett feladatátvétel nem támogatott.
- Meg kell feladat-visszavételhez egy helyszíni VMware virtuális gép. Ez azt jelenti, hogy szüksége van a helyszíni VMware-infrastruktúrára, akkor is, ha a helyszíni fizikai kiszolgálók replikálása az Azure-bA.
- Egyetlen gép feladatátvételét, vagy a feladataikat együtt átadó több gépet a helyreállítási terveket hozhat létre.
- Ha feladatátvételt végez, Azure virtuális gépek jönnek létre a replikált adatokat az Azure storage-ban.
- A kezdeti feladatátvitel után véglegesítheti a számítási feladatok elérése az Azure virtuális gép elindításához.
- Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
- Meg kell állítania egy feladat-visszavételi infrastruktúrát, beleértve:
    - **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, beállíthat egy Azure virtuális gép folyamat kiszolgálója, az Azure-ból replikáció kezelésére. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    - **VPN-kapcsolat**: feladat-visszavételt, kell egy VPN-kapcsolat (vagy Azure ExpressRoute), az Azure-hálózatot a helyszíni helyre.
    - **Különálló fő célkiszolgálót**: alapértelmezés szerint a telepítése megtörtént, a konfigurációs kiszolgálón, a helyszíni VMware virtuális gépek, a fő célkiszolgáló kezeli a feladat-visszavétel. Azonban ha sikertelen biztonsági nagy mértékű forgalom van szüksége, érdemes beállította egy önálló helyszíni fő célkiszolgálót erre a célra.
    - **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez automatikusan hozott létre, a replikációs házirend létrehozva a helyszínről az Azure-bA.
    - **VMware-infrastruktúra**: VMware-infrastruktúrára van szüksége a feladat-visszavételhez. Fizikai kiszolgáló nem használható a feladat-visszavételhez.
- Miután az összetevő a következő helyen, feladat-visszavétel három lépésben történik:
    - 1. fázis: Az Azure virtuális gépek ismételt védelme, így azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    - 2. fázis: Feladatátvétel futtatása a helyszíni helyre.
    - 3. fázis: Számítási feladatok sikertelenek voltak vissza, miután újból engedélyezi replikáció.

**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](physical-azure-disaster-recovery.md) engedélyezése az Azure-bA fizikai kiszolgáló.
