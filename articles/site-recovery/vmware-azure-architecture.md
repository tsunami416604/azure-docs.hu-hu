---
title: VMware virtuális gép vész-helyreállítási architektúrája az Azure Site Recovery szolgáltatásban
description: Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépek vész-helyreállítási szolgáltatásának azure-beli Azure-ba történő vészutáni helyreállításához használt összetevőkről és architektúráról az Azure Site Recovery szolgáltatással
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: ccf258594aa68fc9b5d0189c9ada640078e0ba6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514867"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware az Azure vész-helyreállítási architektúrához

Ez a cikk ismerteti az architektúra és a folyamatok, amikor üzembe helyezi a vész-helyreállítási replikáció, feladatátvétel és a virtuális gépek helyreállítása között egy helyszíni VMware-hely és az Azure Site Recovery szolgáltatás használatával az Azure [Site Recovery](site-recovery-overview.md) szolgáltatás használatával a VMware virtuális gépek (VM-ek) telepítésekor használt.


## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és a grafika magas szintű nézetet biztosít a VMware vész-helyreállítási azure-ba használt összetevőkről.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetés, Az Azure Storage-fiók gyorsítótár, felügyelt lemez és az Azure-hálózat. | A helyszíni virtuális gépekreplikált adatait az Azure storage tárolja. Az Azure virtuális gépek jönnek létre a replikált adatokat, amikor a helyszíni Azure-ból feladatátvétel futtatásakor. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló gépe** | Egyetlen helyszíni gép. Azt javasoljuk, hogy futtassa vmware vm, amely egy letöltött OVF sablonból telepíthető.<br/><br/> A számítógép futtatja az összes helyszíni site recovery összetevőt, beleértve a konfigurációs kiszolgálót, a folyamatkiszolgálót és a fő célkiszolgálót. | **Konfigurációs kiszolgáló:** Koordinálja a helyszíni és az Azure közötti kommunikációt, és kezeli az adatreplikációt.<br/><br/> **Folyamatkiszolgáló**: Alapértelmezés szerint telepítve a konfigurációs kiszolgálón. Replikációs adatokat fogad; gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja; és elküldi az Azure Storage-nak. A folyamatkiszolgáló ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni gépek automatikus felderítését. A központi telepítés növekedésével további, különálló folyamatkiszolgálókat adhat hozzá a nagyobb mennyiségű replikációs forgalom kezeléséhez.<br/><br/> **Fő célkiszolgáló**: Alapértelmezés szerint telepítve a konfigurációs kiszolgálón. Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat. Nagy telepítések esetén hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.
**VMware-kiszolgálók** | A VMware virtuális gépek a helyszíni vSphere ESXi kiszolgálókon találhatók. Javasoljuk, hogy a gazdagépek kezelése vCenter-kiszolgáló. | A Site Recovery központi telepítése során vmware-kiszolgálókat ad hozzá a Helyreállítási szolgáltatások tárolójához.
**Replikált gépek** | A Mobility Service minden egyes replikált VMware virtuális gépre telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a folyamatkiszolgálóról. Azt is megteheti, hogy manuálisan telepíti a szolgáltatást, vagy automatikus telepítési módszert használ, például a Configuration Managert.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a virtuális gép replikációját, megkezdődik az Azure storage-ba történő kezdeti replikáció a megadott replikációs szabályzat használatával. Vegye figyelembe a következőket:
    - VMware virtuális gépek, replikáció blokkszintű, közel folyamatos, a virtuális gépen futó Mobilitási szolgáltatás ügynök használatával.
    - A replikációs házirend beállításai érvényesek:
        - **RPO-küszöbérték**. Ez a beállítás nincs hatással a replikációra. Segít a megfigyelésben. Egy esemény előjön, és adott esetben egy e-mailt küldött, ha az aktuális RPO meghaladja a megadott küszöbértéket.
        - **Helyreállítási pont megőrzése**. Ez a beállítás határozza meg, hogy milyen messzire vissza az időben szeretne menni, ha a zavar lép fel. A prémium szintű tárhely maximális megőrzése 24 óra. A normál tárolás 72 óra. 
        - **Alkalmazáskonzisztens pillanatképek**. Az alkalmazáskonzisztens pillanatkép az alkalmazás igényeitől függően 1–12 óránként is igénybe vehet. A pillanatképek szabványos Azure blob-pillanatképek. A virtuális gépen futó mobilitási ügynök ehhez a beállításhoz megfelelően kéri a VSS-pillanatképet, és a replikációs adatfolyam konzisztens pontjaként könyvjelzőket, amelyek pontról időre vannak.

2. A forgalom replikálódik az Azure storage nyilvános végpontjaira az interneten keresztül. Az Azure ExpressRoute-ot a [Microsoft társviszony-létesítésével is használhatja.](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) A helyszíni helyről az Azure-ra irányuló, helyek közötti virtuális magánhálózaton (VPN) keresztül imitáló forgalom replikálása nem támogatott.
3. A kezdeti replikáció befejezése után megkezdődik az Azure-ba történő különbözeti módosítások replikációja. A rendszer elküldi a számítógép nyomon követett módosításait a folyamatkiszolgálónak.
4. A kommunikáció a következőképpen történik:

    - A virtuális gépek a HTTPS 443-as porton lévő helyszíni konfigurációs kiszolgálóval kommunikálnak a replikáció kezeléséhez.
    - A konfigurációs kiszolgáló vezényli a replikációt az Azure-ral HTTPS 443 kimenő porton keresztül.
    - A virtuális gépek replikációs adatokat küldenek a folyamatkiszolgálónak (amely a konfigurációs kiszolgálógépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi az Okat az Azure storage-ba a 443-as kimenő porton keresztül.
5. A replikációs adatok naplói először egy gyorsítótár-tárfiókban az Azure-ban. Ezeket a naplókat feldolgozzák, és az adatok at egy Azure felügyelt lemez (asr seed disk néven). A helyreállítási pontok ezen a lemezen jönnek létre.




**VMware az Azure replikációs folyamatához**

![Replikációs folyamat](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

A replikáció beállítása után, és futtat egy vész-helyreállítási gyakorlat (teszt feladatátvétel), hogy ellenőrizze, hogy minden a várt módon működik, futtathatja feladatátvétel és feladat-visszavétel, ahogy kell.

1. Egyetlen gép sikertelen futtatása, vagy hozzon létre egy helyreállítási terveket, hogy feladatátvételi több virtuális gép egyszerre. Az egyetlen gépes feladatátvétel helyett a helyreállítási terv előnye a következők:
    - Modellezheti az alkalmazásfüggőségeket úgy, hogy egyetlen helyreállítási tervbe belefoglalásával az alkalmazás összes virtuális gépét bevonta.
    - Hozzáadhat parancsfájlokat, Azure runbookokat, és szüneteltetheti a manuális műveleteket.
2. A kezdeti feladatátvétel aktiválása után véglegesíti, hogy indítsa el a számítási feladatok elérését az Azure virtuális gépről.
3. Ha az elsődleges helyszíni hely ismét elérhető, előkészítheti a feladat-visszavétel. A visszavételhez létre kell hoznia egy feladat-visszavételi infrastruktúrát, többek között a következőket:

    * **Ideiglenes folyamatkiszolgáló az Azure-ban:** Az Azure-ból való visszavételhez be kell állítania egy Azure-beli virtuális gép-kiszolgálót, amely az Azure-ból történő replikáció kezelésére szolgál. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: A feladat-visszavételhez szükség van egy VPN-kapcsolat (vagy ExpressRoute) az Azure-hálózatról a helyszíni helyre.
    * **Külön fő célkiszolgáló**: Alapértelmezés szerint a fő célkiszolgáló, amely telepítve volt a konfigurációs kiszolgálóa a helyszíni VMware VM kezeli a feladat-visszavétel. Ha nagy mennyiségű forgalmat kell visszaadnia, ehhez hozzon létre egy külön helyszíni fő célkiszolgálót.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan létrejön, amikor létrehoz egy replikációs szabályzatot a helyszíni Azure-ba.
4. Az összetevők helyének meglépése után a feladat-visszavétel három műveletben történik:

    - 1. szakasz: Az Azure virtuális gépek újbóli védelme, hogy azok replikálni az Azure-ból vissza a helyszíni VMware virtuális gépek.
    -  2. szakasz: Feladatátvétel futtatása a helyszíni helyre.
    - 3. szakasz: Miután a számítási feladatok sikertelenek voltak, újra engedélyezni e replikációt a helyszíni virtuális gépek.
    
 
**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Kövesse [ezt az oktatóanyagot](vmware-azure-tutorial.md) a VMware Azure-replikációengedélyezéséhez.
