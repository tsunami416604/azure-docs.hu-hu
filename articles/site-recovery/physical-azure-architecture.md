---
title: Az Azure-beli fizikai kiszolgáló vész-helyreállítási architektúrája Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk áttekintést nyújt azokról az összetevőkről és architektúráról, amelyeket a helyszíni fizikai kiszolgálók az Azure-ba történő, a Azure Site Recovery szolgáltatással történő helyreállításakor használtak.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: a5d3dfe6457c4b70f0b23c2d8aa7ac5e58e68dc7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814462"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Fizikai kiszolgálóról Azure vész-helyreállítási architektúrára

Ez a cikk a fizikai Windows-és Linux-kiszolgálók egy helyszíni hely és az Azure közötti replikálására, feladatátvételére és helyreállítására használt architektúrát és folyamatokat ismerteti a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra áttekintést nyújt az Azure-ba való fizikai kiszolgálók replikálásához használt összetevőkről.  

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetés és egy Azure-hálózat. | A helyszíni fizikai gépekről replikált adatok tárolása az Azure Managed Disks szolgáltatásban történik. Az Azure-beli virtuális gépek a replikált adatokkal jönnek létre, amikor feladatátvételt hajt végre a helyszínen az Azure-ba. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | A helyszíni Site Recovery összes összetevőjének futtatásához egyetlen helyszíni fizikai gépet vagy VMware virtuális gépet kell telepíteni. A virtuális gép futtatja a konfigurációs kiszolgálót, a feldolgozó kiszolgálót és a fő célkiszolgáló-kiszolgálót. | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt települ. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A Process Server is telepíti a mobilitási szolgáltatást a replikálni kívánt kiszolgálókra.<br/><br/> Az üzembe helyezés során további, különálló folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez.
 **Fő célkiszolgáló** | Alapértelmezés szerint a konfigurációs kiszolgálóval együtt települ. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez.
**Replikált kiszolgálók** | A mobilitási szolgáltatás minden replikált kiszolgálóra telepítve van. | Javasoljuk, hogy engedélyezze az automatikus telepítést a folyamat-kiszolgálóról. Azt is megteheti, hogy manuálisan telepíti a szolgáltatást, vagy automatikus telepítési módszert használ, például System Center Configuration Manager.

**Fizikai – Azure architektúra**

![Összetevők](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikációs folyamat

1. Be kell állítania a központi telepítést, beleértve a helyszíni és az Azure-összetevőket is. A Recovery Services-tárolóban adja meg a replikálás forrását és célját, állítsa be a konfigurációs kiszolgálót, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
2. A gépek a replikációs házirendnek megfelelően replikálódnak, és a kiszolgáló adatainak kezdeti másolatát az Azure Storage-ba replikálja a rendszer.
3. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - A számítógépek a replikációs felügyelet érdekében a HTTPS 443 bejövő porton kommunikálnak a konfigurációs kiszolgálóval.
    - A gépek replikációs adatküldést küldenek a Process Servernek a HTTPS 9443 bejövő porton (módosítható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
4. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](../expressroute/expressroute-circuit-peerings.md#publicpeering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.


**Fizikai – Azure replikálási folyamat**

![Replikációs folyamat](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután beállította a replikálást, és elvégezte a vész-helyreállítási részletezést (feladatátvételi teszt), hogy minden a vártnak megfelelően működik-e, a feladatátvételt és a feladat-visszavételt a szükséges módon futtathatja. Vegye figyelembe:

- A tervezett feladatátvétel nem támogatott.
- A helyszíni VMware virtuális gépekre vissza kell térnie. Ez azt jelenti, hogy helyszíni VMware-infrastruktúrára van szükség, még akkor is, ha helyszíni fizikai kiszolgálókat replikál az Azure-ba.
- Egyetlen gép feladatátvételét hajthatja végre, vagy helyreállítási terveket hozhat létre a több gép feladatátvételéhez.
- Feladatátvétel futtatásakor az Azure-beli virtuális gépek a replikált adatokból jönnek létre az Azure Storage-ban.
- A kezdeti feladatátvétel elindítása után véglegesíti azt az Azure-beli virtuális gép munkaterhelésének megkezdéséhez.
- Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat.
- Be kell állítania egy feladat-visszavételi infrastruktúrát, beleértve a következőket:
    - **Ideiglenes Process Server az Azure-ban**: Az Azure-ból történő feladat-visszavétel érdekében állítson be egy Azure-beli virtuális gépet, amely az Azure-ból történő replikáció kezelésére szolgál. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    - **VPN-kapcsolat**: A feladat-visszavétel érdekében VPN-kapcsolatra (vagy Azure ExpressRoute) van szükség az Azure-hálózatról a helyszíni helyre.
    - **Különálló fő célkiszolgáló**: Alapértelmezés szerint a konfigurációs kiszolgálóval telepített fő célkiszolgáló a helyszíni VMware virtuális gépen kezeli a feladat-visszavételt. Ha azonban nagy mennyiségű forgalmat kell visszaadnia, akkor erre a célra külön helyszíni fő célkiszolgáló szükséges.
    - Feladat- **visszavételi szabályzat**: A helyszíni helyre történő replikáláshoz feladat-visszavételi szabályzatra van szükség. Ezt a rendszer automatikusan létrehozta, amikor a helyszíni rendszerből az Azure-ba hozta létre a replikációs házirendet.
    - **VMware-infrastruktúra**: A feladat-visszavételhez VMware-infrastruktúrára van szükség. Fizikai kiszolgáló nem használható a feladat-visszavételhez.
- Az összetevők elhelyezése után a feladat-visszavétel három lépésben történik:
    - 1\. fázis: Állítsa be újra az Azure-beli virtuális gépeket az Azure-ból a helyszíni VMware virtuális gépekre való replikáláshoz.
    - 2\. fázis: Futtasson feladatátvételt a helyszíni helyre.
    - 3\. fázis: A munkaterhelések visszaállítása után engedélyezze újra a replikációt.

**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

[Ezt az oktatóanyagot](physical-azure-disaster-recovery.md) követve engedélyezheti a fizikai kiszolgáló Azure-beli replikálását.
