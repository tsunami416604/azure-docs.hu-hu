---
title: "Tekintse át a VMware-replikáció az Azure architektúra |} Microsoft Docs"
description: "Ez a cikk áttekintést összetevők és használható, ha a helyszíni VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery szolgáltatással architektúra"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d03d2dd3-2455-4ca8-a942-a342030ee6ce
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: ac1151d15a88650f5845cb879cd210e9f7cba0fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware Azure replikációs-architektúra

Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és VMware virtuális gépek (VM) helyszíni VMware-hely és az Azure közötti helyreállítás használata a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő tábla és a kép adja meg az Azure-bA VMware-replikációhoz használt összetevők áttekintése.  

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure storage-fiók és Azure-hálózatot. | A tárfiók tárolja a replikált adatokat a helyszíni virtuális gépek. Azure virtuális gépek jönnek létre a replikált adatok egy sikertelen történő futtatásakor a helyszíni Azure-bA. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | Egyetlen helyszíni VMware virtuális gép központi telepítése a Site Recovery összetevők futtatása az összes az helyszíni. A virtuális gép fut, a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón. | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> Szeretne replikálni, virtuális gépeken is telepíti a mobilitási szolgáltatást a folyamatkiszolgáló és a virtuális gépek automatikus felderítését végzi, a helyszíni VMware-kiszolgálók.<br/><br/> A központi telepítés növekedésével további, különálló folyamat kiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.
 **Fő célkiszolgáló** | A konfigurációs kiszolgáló együtt alapértelmezés szerint telepítve. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> A nagyméretű telepítések esetében hozzáadhat egy további, külön fő célkiszolgáló feladat-visszavételre.
**VMware-kiszolgálók** | VMware virtuális gépeket üzemeltető helyszíni vSphere ESXi kiszolgálókon. Azt javasoljuk, hogy a vCenter-kiszolgáló a gazdagépek felügyeletéhez. | Site Recovery üzembe helyezése során hozzáadja a Recovery Services-tároló VMware-kiszolgálók.
**Replikált gépek** | A mobilitási szolgáltatás telepítve van a VMware virtuális gépek, amelyek replikálja. | Azt javasoljuk, hogy engedélyezi az automatikus telepítési adatok a folyamatkiszolgálótól. Másik lehetőségként a szolgáltatás manuális telepítése, vagy egy automatikus telepítési módszer, például a System Center Configuration Manager használata. 

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. A központi telepítését, beleértve a helyszíni és az Azure összetevők beállítása. A Recovery Services-tárolónak kell megadni a forrásként és célként, állítsa be a konfigurációs kiszolgáló, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
2. A replikációs házirendet, és egy kezdeti másolatot készít a virtuális gép adatai megfelelően a gépek replikálásához a rendszer replikálja az Azure storage.
3. Kezdeti replikáció befejezését követően, a változási különbözeteket az Azure-bA replikálását kezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - Gépek kommunikálni a konfigurációs kiszolgáló HTTPS 443-as portot a bejövő, a replikáció kezelését.
    - Gépek replikációs adatokat küldeni a folyamatkiszolgáló HTTPS 9443 porton bejövő (módosítható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezi a virtuális Gépre kiterjedő konzisztencia, a replikációs csoport gépek kommunikálhatnak egymással 20004 porton keresztül. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
4. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.


**VMware-ből az Azure replikációs folyamat**

![Replikációs folyamat](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Replikáció be van állítva, és egy vész helyreállítási részletezéshez (feladatátvételi teszt) ellenőrizze, hogy minden a várt módon működik futtatását, miután futtatása feladatátvétel és a feladat-visszavétel szükség van.

1. Egyetlen gép feladatátvételt, vagy hozzon létre helyreállítási terveket, több virtuális gépek a feladatátvétel.
2. Amikor feladatátvételt, Azure virtuális gépek replikált adatokból jönnek létre az Azure storage.
3. Után váltanak ki a kezdeti feladatátvételi, véglegesítheti a munkaterhelés elérése az Azure virtuális gép elindításához.

Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
1. A feladat-visszavétel infrastruktúra beállításához szüksége többek között:
    - **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, állít be egy Azure virtuális gép folyamat kiszolgálója, az Azure-ból replikáció kezelésére. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    - **VPN-kapcsolat**: a feladat-visszavételt, kell egy VPN-kapcsolat (vagy Azure ExpressRoute) Azure-hálózatában a helyszíni hely.
    - **Külön fő célkiszolgáló**: alapértelmezés szerint a fő célkiszolgáló telepítése sikeres volt, a konfigurációs kiszolgálón, a helyszíni VMware virtuális gép, a feladat-visszavétel kezeli. Azonban ha vissza nagy mennyiségű forgalom sikertelen van szüksége, érdemes beállítania egy önálló helyszíni fő célkiszolgáló erre a célra.
    - **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez automatikusan jött létre, amikor a helyszíni Azure létrehozta a replikációs házirend.
2. Miután az összetevő a következő helyen, feladat-visszavétel három lépésben történik:
    - 1. fázis: Lássa el újból védelemmel az Azure virtuális gépeket, hogy azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    - 2. fázis: Feladatátvételt végez a helyszíni hely.
    - 3. fázis: Után munkaterhelések ismét sikertelen, akkor engedélyezi a helyszíni virtuális gépek replikálásához.

**Az Azure-ból VMware feladat-visszavétel**

![Feladat-visszavétel](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Következő lépések

Tekintse át a támogatási mátrix oktatóanyag VMware Azure replikáció engedélyezéséhez.
Futtassa a feladatátvételi és a feladat-visszavétel.