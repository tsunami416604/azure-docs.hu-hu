---
title: "Az Azure Site Recovery architektúrájáról Azure replikációs VMware |} Microsoft Docs"
description: "Ez a cikk áttekintést összetevők és használható, ha a helyszíni VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery szolgáltatással architektúra"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 7999f23d167c6e8a7bcaf3a817e0cd2e80a1d649
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware Azure replikációs-architektúra

Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és VMware virtuális gépek (VM) helyszíni VMware-hely és az Azure közötti helyreállítás használata a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő tábla és a kép adja meg az Azure-bA VMware-replikációhoz használt összetevők áttekintése.  

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure storage-fiók és Azure-hálózatot. | A tárfiók tárolja a replikált adatokat a helyszíni virtuális gépek. Azure virtuális gépek jönnek létre a replikált adatok egy sikertelen történő futtatásakor a helyszíni Azure-bA. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló számítógépén** | Egy egyetlen a helyi számítógépen. Azt javasoljuk, hogy a VMware virtuális gépként, amelyek a letöltött OVF-sablonok alapján telepített futtatja.<br/><br/> Helyszíni Site Recovery minden összetevőjét, beleértve a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón a gép fut. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és az Azure közötti kommunikáció, és kezeli a replikációs adatokat.<br/><br/> **Folyamatkiszolgáló**: a konfigurációs kiszolgálón alapértelmezés szerint telepítve. Replikációs adatok fogadása, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, és elküldi azt az Azure storage. A folyamatkiszolgáló is telepíti a mobilitási szolgáltatást a virtuális gépeken szeretne replikálni, és végrehajtja a helyszíni gépeket automatikus észlelése. A központi telepítés növekedésével további, különálló folyamat kiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.<br/><br/>  **Fő célkiszolgáló**: a konfigurációs kiszolgálón alapértelmezés szerint telepítve. Az Azure-ból a feladat-visszavétel során kezeli azt a replikációs adatokat. A nagyméretű telepítések esetében hozzáadhat egy további, külön fő célkiszolgáló feladat-visszavételre.
**VMware-kiszolgálók** | VMware virtuális gépeket üzemeltető helyszíni vSphere ESXi kiszolgálókon. Azt javasoljuk, hogy a vCenter-kiszolgáló a gazdagépek felügyeletéhez. | Site Recovery üzembe helyezése során hozzáadja a Recovery Services-tároló VMware-kiszolgálók.
**Replikált gépek** | A mobilitási szolgáltatás telepítve van a VMware virtuális gépek, amelyek replikálja. | Azt javasoljuk, hogy engedélyezi az automatikus telepítési adatok a folyamatkiszolgálótól. Másik lehetőségként a szolgáltatás manuális telepítése, vagy egy automatikus telepítési módszer, például a System Center Configuration Manager használata.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1.  Előkészítheti az Azure-erőforrások és a helyszíni összetevők.
2.  A Recovery Services-tároló forrás replikációs beállításokat kell megadni. Ez a folyamat részeként beállíthatja a helyszíni konfigurációs kiszolgáló. A VMware virtuális gép kiszolgálót telepíteni, előkészített OVF sablon letöltése, és importálni szeretné a VMware virtuális gép létrehozása.
3. Adja meg a cél replikációs beállítások, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást a VMware virtuális gépek esetén.
4.  A replikációs házirendet, és egy kezdeti másolatot készít a virtuális gép adatai megfelelően a gépek replikálásához a rendszer replikálja az Azure storage.
5.  Kezdeti replikáció befejezését követően, a változási különbözeteket az Azure-bA replikálását kezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - Gépek kommunikálni a konfigurációs kiszolgáló HTTPS 443-as portot a bejövő, a replikáció kezelését.
    - Gépek replikációs adatokat küldeni a folyamatkiszolgáló HTTPS 9443 porton bejövő (módosítható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezi a virtuális Gépre kiterjedő konzisztencia, a replikációs csoport gépek kommunikálhatnak egymással 20004 porton keresztül. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
6.  Forgalom replikálja az Azure storage nyilvános végpontok, az interneten keresztül. Másik lehetőségként használhatja a zure ExpressRoute [nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.


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


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](tutorial-vmware-to-azure.md) VMware Azure replikáció engedélyezéséhez.
