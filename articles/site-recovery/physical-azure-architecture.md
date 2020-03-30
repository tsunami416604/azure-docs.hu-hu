---
title: Fizikai kiszolgálóvész-helyreállítási architektúrája az Azure Site Recovery szolgáltatásban
description: Ez a cikk áttekintést nyújt a helyszíni fizikai kiszolgálók vész-helyreállítási során az Azure-ba az Azure Site Recovery szolgáltatással használt összetevőkről és architektúráról.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162837"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fizikai kiszolgálókról Azure-ba történő vészhelyreállítás architektúrája

Ez a cikk ismerteti az architektúra és a folyamatok replikálása, feladatátvétel, és a fizikai Windows és Linux-kiszolgálók között egy helyszíni hely és az Azure, az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával használt.

## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra magas szintű nézetet biztosít az Azure-ba fizikai kiszolgáló-replikációhoz használt összetevőkről.

| **Összetevő** | **Követelmény** | **Részletek** |
| --- | --- | --- |
| **Azure** | Egy Azure-előfizetés és egy Azure-hálózat. | A helyszíni fizikai gépekreplikált adatait az Azure által felügyelt lemezek tárolják. Az Azure virtuális gépek jönnek létre a replikált adatokat, amikor a helyszíni Azure-ból feladatátvétel futtatásakor. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz. |
| **Folyamatkiszolgáló** | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt telepítve. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A folyamatkiszolgáló a Replikálni kívánt kiszolgálókra is telepíti a Mobilitás szolgáltatást.<br/><br/> A központi telepítés növekedésével további, különálló folyamatkiszolgálókat adhat hozzá a nagyobb mennyiségű replikációs forgalom kezeléséhez. |
| **Fő célkiszolgáló** | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt telepítve. | Az Azure-ból való visszavétel során kezeli a replikációs adatokat.<br/><br/> Nagy telepítések esetén hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez. |
| **Replikált kiszolgálók** | A Mobilitás szolgáltatás minden replikált kiszolgálóra telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a folyamatkiszolgálóról. Vagy manuálisan is telepítheti a szolgáltatást, vagy használhat automatikus központi telepítési módszert, például a Configuration Managert. |

**Fizikairól Azure-ra architektúra**

![Összetevők](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. Beállíthatja a központi telepítést, beleértve a helyszíni és az Azure-összetevőket. A Helyreállítási szolgáltatások tárolójában megadhatja a replikációs forrást és a célt, beállíthatja a konfigurációs kiszolgálót, létrehozhat egy replikációs házirendet, és engedélyezheti a replikációt.
1. A gépek replikálják a replikációs házirendet, és a kiszolgálóadatok kezdeti másolatát replikálja az Azure storage-ba.
1. A kezdeti replikáció befejezése után megkezdődik az Azure-ba történő különbözeti módosítások replikációja. A gép nyomon követett módosításait a _.hrl_ kiterjesztésű fájlban tartják.
   - A gépek a 443-as HTTPS-porton lévő konfigurációs kiszolgálóval kommunikálnak a replikáció kezeléséhez.
   - A gépek a 9443-as HTTPS-porton küldik a replikációs adatokat a folyamatkiszolgálónak bejövő HTTPS-porton (módosíthatók).
   - A konfigurációs kiszolgáló vezényli a replikációkezelést az Azure-ral a 443-as kimenő HTTPS-porton keresztül.
   - A folyamatkiszolgáló adatokat fogad a forrásgépekről, optimalizálja és titkosítja azokat, és https-porton keresztül küldi el az Azure storage-ba a 443-as kimenő HTTPS-porton keresztül.
   - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ezek a csoportok akkor hasznosak, ha a gépek ugyanazt a számítási feladatot futtatják, és konzisztensnek kell lenniük.
1. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/about-public-peering.md) szolgáltatását is használhatja.

   > [!NOTE]
   > A helyszíni helyről és az Azure ExpressRoute [privát társviszony-létesítési](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)helyről származó helyközi VPN nem támogatja a replikációt.

**Fizikai és Azure replikációs folyamat**

![Replikációs folyamat](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

A replikáció beállítása után futtathat egy vész-helyreállítási gyakorlatot (teszt feladatátvételt), hogy ellenőrizze, hogy minden a várt módon működik-e. Ezután átadhatja a feladatátvételt, és szükség szerint visszaveheti. Vegye figyelembe a következő elemeket:

- A tervezett feladatátvétel nem támogatott.
- A helyszíni VMware virtuális gépnek vissza kell adnia a feladatát. Helyszíni VMware-infrastruktúrára van szükség, még akkor is, ha helyszíni fizikai kiszolgálókat replikál az Azure-ba.
- Egy gép feladatátvétele, vagy helyreállítási tervek létrehozása, több gép együttes feladatátvételhez.
- Feladatátvétel futtatásakor az Azure-beli virtuális gépek replikált adatokból jönnek létre az Azure storage-ban.
- Miután a kezdeti feladatátvétel aktiválódik, véglegesíti, hogy indítsa el a számítási feladatok elérését az Azure virtuális gépről.
- Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
- Feladat-visszavételi infrastruktúra beállítása, amely a következőket tartalmazza:
  - **Ideiglenes folyamatkiszolgáló az Azure-ban:** Az Azure-ból való visszavételhez be kell állítania egy Azure-beli virtuális gép, amely folyamatkiszolgálóként működik, és kezeli az Azure-ból történő replikációt. Törölheti ezt a virtuális gép után feladat-vissza befejezése után.
  - **VPN-kapcsolat**: A feladat-visszavételhez szükség van egy VPN-kapcsolat (vagy Az Azure ExpressRoute) az Azure-hálózatból a helyszíni hely.
  - **Külön fő célkiszolgáló**: Alapértelmezés szerint a feladat-visszavételi kezeli a fő célkiszolgáló, amely telepítve volt a konfigurációs kiszolgáló a helyszíni VMware vm. Ha nagy mennyiségű forgalmat kell visszaadnia, be kell állítania egy külön helyszíni fő célkiszolgálót.
  - **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A szabályzat automatikusan létrejött, amikor létrehozta a replikációs szabályzatot a helyszíni Azure-ba.
  - **VMware-infrastruktúra:** A feladat-visszavételhez vmware-infrastruktúrára van szükség. Fizikai kiszolgáló nem használható a feladat-visszavételhez.
- Miután az összetevők a helyükön vannak, a visszavétel három szakaszban történik:
  - **1. szakasz:** Az Azure virtuális gépek újbóli védelme, hogy azok replikálni az Azure-ból vissza a helyszíni VMware virtuális gépek.
  - **2. szakasz**: Feladatátvétel futtatása a helyszíni helyre.
  - **3. szakasz:** Miután a munkaterhelések sikertelenek voltak, újra engedélyezi a replikációt.

**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>További lépések

Az Azure-ba irányuló fizikai kiszolgálók vész-helyreállítási szolgáltatásának beállításához olvassa el az [útmutatót.](physical-azure-disaster-recovery.md)
