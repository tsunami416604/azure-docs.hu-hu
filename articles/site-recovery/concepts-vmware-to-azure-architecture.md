---
title: "Az Azure Site Recovery architektúrájáról Azure replikációs VMware |} Microsoft Docs"
description: "Ez a cikk áttekintést összetevők és használható, ha a helyszíni VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery architektúra"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 4dd9d4f5f26d70f9130f48e2bf40ce71943a6c6d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware Azure replikációs-architektúra

Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és helyreállítása a VMware virtuális gépek (VM) helyszíni VMware-hely és az Azure közötti használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Az architektúra összetevői

A következő tábla és a kép adja meg az Azure-bA VMware-replikációhoz használt összetevők áttekintése.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure Storage-fiók és Azure-hálózatot. | A tárfiók tárolja a replikált adatokat a helyszíni virtuális gépek. Azure virtuális gépek jönnek létre a replikált adatok futtatásakor a feladatátvétel a helyszíni Azure-bA. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló számítógépén** | Egy egyetlen a helyi számítógépen. Azt javasoljuk, hogy kell futtatnia, mint a VMware virtuális gép telepítését követően a letöltött OVF-sablonok.<br/><br/> Helyszíni Site Recovery minden összetevőjét, többek között a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón a gép fut. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és az Azure közötti kommunikáció, és kezeli a replikációs adatokat.<br/><br/> **Folyamatkiszolgáló**: a konfigurációs kiszolgálón alapértelmezés szerint telepítve. Akkor kap replikációs adatokat; optimalizálja a gyorsítótárazás, tömörítés és titkosítás; és az Azure Storage küldi. A folyamatkiszolgáló is Azure Site Recovery mobilitási szolgáltatás telepít virtuális gépeket szeretne replikálni, és végrehajtja a helyszíni gépeket automatikus észlelése. A központi telepítés növekedésével további, különálló folyamat kiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.<br/><br/> **Fő célkiszolgáló**: a konfigurációs kiszolgálón alapértelmezés szerint telepítve. Az Azure-ból a feladat-visszavétel során kezeli azt a replikációs adatokat. A nagyméretű telepítések esetében hozzáadhat egy további, külön fő célkiszolgáló feladat-visszavételre.
**VMware-kiszolgálók** | VMware virtuális gépeket üzemeltető helyszíni vSphere ESXi kiszolgálókon. Azt javasoljuk, hogy a vCenter-kiszolgáló a gazdagépek felügyeletéhez. | Site Recovery üzembe helyezése során hozzáadja a Recovery Services-tároló VMware-kiszolgálók.
**Replikált gépek** | A VMware virtuális gépek, amelyek replikálja a mobilitási szolgáltatás telepítve van. | Azt javasoljuk, hogy lehetővé tegye az automatikus telepítési adatok a folyamatkiszolgálótól. Azt is megteheti manuálisan telepítse a szolgáltatást, vagy egy automatikus telepítési módszer, például a System Center Configuration Manager használatára.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. Az Azure erőforrások és a helyszíni összetevőinek előkészítése.
2. Adja meg a Recovery Services-tároló forrás replikációs beállításokat. Ez a folyamat részeként a helyszíni konfigurációs kiszolgáló beállítása. Ez a kiszolgáló, a VMware virtuális gépként telepíti, előkészített OVF sablon letöltése, és importálni szeretné a VMware virtuális gép létrehozása.
3. Adja meg a cél replikációs beállítások, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást a VMware virtuális gépek esetén.
4. A replikációs házirendet, és egy kezdeti másolatot készít a virtuális gép adatai megfelelően a gépek replikálásához Azure Storage replikálódik.
5. Kezdeti replikáció befejezését követően, a változási különbözeteket az Azure-bA replikálását kezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.

    * Gépek kommunikálni a konfigurációs kiszolgáló HTTPS 443-as portot a bejövő, a replikáció kezelését.

    * Gépek replikációs adatokat küldeni a folyamatkiszolgáló HTTPS 9443 porton bejövő (módosítható).

    * Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.

    * A folyamatkiszolgáló adatokat fogad az forrásgépek, optimalizálja és titkosítja azokat, és küld az Azure Storage 443-as porton keresztül kimenő.

    * Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez a módszer akkor hasznos, ha a gépeken ugyanaz az alkalmazás fut, és konzisztens kell.

6. Forgalom replikálja az Azure storage nyilvános végpontok az interneten keresztül. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) szolgáltatását is használhatja. Replikálásához forgalom a pont-pont virtuális magánhálózat (VPN) keresztül egy helyszíni hely Azure-ba nem támogatott.


**VMware-ből az Azure replikációs folyamat**

![Replikációs folyamat](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután replikáció be van állítva, és futtatja a vész helyreállítási részletezéshez (feladatátvételi teszt) ellenőrizze, hogy minden a várt módon működik, futtatása feladatátvétel és a feladat-visszavétel szükség van.

1. Egyetlen gép feladatátvételt, vagy hozzon létre több virtuális gépek a feladatátvétel tartalmazó helyreállítási terveket.

2. Amikor feladatátvételt, Azure virtuális gépek replikált adatokból jönnek létre az Azure storage.

3. Után váltanak ki a kezdeti feladatátvételi, véglegesítheti a munkaterhelés elérése az Azure virtuális gép elindításához.

Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
1. A feladat-visszavétel infrastruktúra beállításához szüksége többek között:

    * **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, állít be egy Azure virtuális gép az Azure-ból replikáció kezelésére folyamat kiszolgálója. Ez a virtuális gép a feladatok visszaadását követően törölhető.

    * **VPN-kapcsolat**: a feladat-visszavételt, kell egy VPN-kapcsolat (vagy ExpressRoute) Azure-hálózatában a helyszíni hely.

    * **Külön fő célkiszolgáló**: alapértelmezés szerint a fő célkiszolgáló telepítése sikeres volt, a konfigurációs kiszolgáló a helyszíni VMware virtuális gép feladat-visszavétel kezeli. Ha sikertelen biztonsági nagy mennyiségű forgalom van szüksége, beállítása egy önálló helyszíni fő célkiszolgáló erre a célra.

    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ezzel a házirend-automatikusan létrejött, amikor a helyszíni Azure létrehozta a replikációs házirend.
2. Miután az összetevő a következő helyen, feladat-visszavétel három lépésben történik:

    a. 1. fázis: Lássa el újból védelemmel az Azure virtuális gépeket, hogy azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.

    b. 2. fázis: Feladatátvételt végez a helyszíni hely.

    c. 3. fázis: Miután munkaterhelések ismét sikertelen, újból engedélyezi a helyszíni virtuális gépek replikációját.

**Az Azure-ból VMware feladat-visszavétel**

![Feladat-visszavétel](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](tutorial-vmware-to-azure.md) VMware Azure replikáció engedélyezéséhez.
