---
title: "Azure Site Recovery – ajánlott eljárás | Microsoft Docs"
description: "A cikk az Azure Site Recovery üzembe helyezésének ajánlott eljárásait írja le"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Felkészülés az Azure Site Recovery üzembe helyezésére

Ez a cikk az Azure Site Recovery üzembe helyezésére való felkészülést ismerteti.

## <a name="protecting-hyper-v-virtual-machines"></a>Hyper-V virtuális gépek védelme

A Hyper-V virtuális gépek védelmére több üzembe helyezési lehetősége van. A helyszíni Hyper-VM-eket az Azure-ba és másodlagos adatközpontokba is lehet replikálni. Az egyes üzembe helyezéseknek eltérők a követelményei.

**Követelmény** | **Replikálás az Azure-ba (VMM-mel)** | **Hyper-V-alapú (nem VMM-mel kezelt) virtuális gépek replikálása az Azure-ba** | **Hyper-V-alapú virtuális gépek replikálása másodlagos helyre (VMM-mel)** | **Részletek**
---|---|---|---|---
**VMM** | System Center 2012 R2 rendszeren futó VMM <br/><br/>Legalább egy VMM-felhő, amely egy vagy több VMM-gazdagépcsoportot tartalmaz. | NA | Az elsődleges és másodlagos helyeket futtató VMM-kiszolgálókon a System Center 2012 SP1-es vagy újabb verziója fut a legújabb frissítésekkel. <br/><br/> Legalább egy felhő minden VMM-kiszolgálón. A felhőkön be kell állítani a Hyper-V-kapacitásprofilt.<br/><br/> A forrásfelhőnek legalább egy VMM-gazdagépcsoporttal kell rendelkeznie. | Választható. Nem kell üzembe kell helyeznie a System Center VMM-et a Hyper-V virtuális gépek Azure-ban történő replikálásához, ebben az esetben azonban győződjön meg róla, hogy a VMM-kiszolgáló megfelelően van beállítva. Ebbe beletartozik a megfelelő VMM-verzió futtatásának, valamint a felhő beállításának ellenőrzése.
**Hyper-V** | Legalább egy, Windows Server 2012 R2 vagy újabb rendszert futtató Hyper-V-gazdakiszolgáló a helyszíni helyen | Legalább egy Hyper-V-kiszolgáló a forrás- és célhelyeken, amelyeken a legalább a Windows Server 2012 fut a legújabb frissítésekkel, valamint rendelkeznek internetkapcsolattal.<br/><br/> A Hyper-V-kiszolgálóknak egy gazdagépcsoportban kell lenniük egy VMM-felhőben. | Legalább egy Hyper-V-kiszolgáló a forrás- és célhelyeken, amelyeken a legalább a Windows Server 2012 fut a legújabb frissítésekkel, valamint rendelkeznek internetkapcsolattal.<br/><br/> A Hyper-V-kiszolgálóknak egy gazdagépcsoportban kell elhelyezkedniük egy VMM-felhőben. |
**Virtuális gépek** | Legalább egy virtuális gép a forrás Hyper-V-gazdakiszolgálón | Legalább egy virtuális gép a Hyper-V-gazdakiszolgálón a forrás VMM-felhőben | Legalább egy virtuális gép a Hyper-V-gazdakiszolgálón a forrás VMM-felhőben. |  Az Azure-ba replikált virtuális gépeknek meg kell felelniük az Azure virtuális gépekre vonatkozó előfeltételeinek
**Azure-fiók** | Egy Azure-fiókra, valamint előfizetésre lesz szüksége a Site Recovery szolgáltatáshoz. | Egy Azure-fiókra, valamint előfizetésre lesz szüksége a Site Recovery szolgáltatáshoz. | NA | Ha nem rendelkezik fiókkal, kezdje az ingyenes próbaverzióval.
**Azure Storage tárterület** | Egy Azure Storage-fiókelőfizetésre lesz szüksége, amelyben engedélyezve van a georeplikáció. | Egy Azure Storage-fiókelőfizetésre lesz szüksége, amelyben engedélyezve van a georeplikáció. | NA | A fióknak és az Azure Site Recovery-tárolónak ugyanabban a régióban kell elhelyezkednie, és ugyanahhoz az előfizetéshez kell tartoznia.
**Hálózat** | Állítsa be a hálózatleképezést annak érdekében, hogy az azonos hálózaton feladatátvételt végrehajtó gépek képesek legyenek csatlakozni egymáshoz, attól függetlenül, hogy melyik helyreállítási tervhez tartoznak. Emellett, ha a cél Azure-hálózatban hálózati átjáró van konfigurálva, a virtuális gépek képesek csatlakozni a többi helyszíni virtuális géphez. Ha nem állítja be a hálózatleképezést, kizárólag az azonos helyreállítási tervben feladatátvételt végrehajtó virtuális gépek fognak tudni csatlakozni egymáshoz. | NA |  <br/><br/>Állítsa be a hálózatleképezést annak biztosításához, hogy a virtuális gépek a megfelelő hálózatokhoz csatlakozzanak a feladatátvételt követően, illetve a replika virtuális gépek optimálisan helyezkedjenek el a Hyper-V-gazdakiszolgálókon. Ha nem konfigurálja a hálózatleképezést, a replikált gépek nem fognak csatlakozni egyetlen VM-hálózathoz sem a feladatátvétel után. |  A hálózatleképezés VMM-el történő beállításához győződjön meg róla, hogy a VMM logikai és VM-hálózatai megfelelően vannak konfigurálva.
**Szolgáltatók és ügynökök** | Az üzembe helyezés során telepíti az Azure Site Recovery Providert a VMM-kiszolgálókra. A VMM-felhőben lévő Hyper-V-kiszolgálókon telepítse az Azure Recovery Services ügynököt. | Az üzembe helyezés során telepíteni fogja az Azure Site Recovery Providert és az Azure Recovery Services ügynököt is a Hyper-V-gazdakiszolgálón vagy fürtön| Az üzembe helyezés során telepíti az Azure Site Recovery Providert a VMM-kiszolgálókra. A VMM-felhőben lévő Hyper-V-kiszolgálókon telepítse az Azure Recovery Services ügynököt. | A szolgáltatók és ügynökök az interneten keresztül, titkosított HTTPS-kapcsolaton keresztül csatlakoznak a Site Recovery-hez. Nem kell tűzfalkivételeket hozzáadnia, illetve speciális proxykat létrehoznia a Provider csatlakoztatásához.
**Internetkapcsolat** | Csak a VMM-kiszolgálóknak van szükségük internetkapcsolatra | Csak a Hyper-V-gazdakiszolgálóknak van szükségük internetkapcsolatra | Csak VMM-kiszolgálóknak van szükségük internetkapcsolatra | A virtuális gépekre semmit nem kell telepíteni, és nem csatlakoznak közvetlenül az internethez.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>A VMware virtuális gépek vagy fizikai kiszolgálók védelme

A VMware virtuális gépek, illetve Windows/Linux rendszerű kiszolgálók védelmére több üzembe helyezési lehetősége van. Replikálhatja azokat az Azure-ba vagy egy másodlagos adatközpontba. Az egyes üzembe helyezéseknek eltérők a követelményei.

**Követelmény** | **VMware virtuális gépek vagy fizikai kiszolgálók replikálása az Azure-ba** | * **VMware virtuális gépek/fizikai kiszolgálók replikálása másodlagos helyre**  
---|---|---
**Elsődleges hely** | **Folyamatkiszolgáló**: egy dedikált Windows-kiszolgáló (fizikai vagy virtuális) | **Folyamatkiszolgáló**: egy dedikált Windows-kiszolgáló (fizikai vagy VMware virtuális gép<br/><br/>  
**Másodlagos helyszíni hely** | NA | **Konfigurációs kiszolgáló**: egy dedikált Windows-kiszolgáló (fizikai vagy virtuális) <br/><br/> **Fő célkiszolgáló**: egy dedikált kiszolgáló (fizikai vagy virtuális). A konfigurálást a Windows segítségével végezze el Windows rendszerű gépek, illetve a Linux segítségével a Linux rendszerű gépek esetében.
**Azure** | **Előfizetés**: Egy előfizetésre lesz szüksége a Site Recovery szolgáltatáshoz. <br/><br/> **Storage-fiók**: Egy Storage-fiókra lesz szüksége, amelyen engedélyezve van a georeplikáció. A fióknak és a Site Recovery-tárolónak ugyanabban a régióban kell elhelyezkednie, és ugyanahhoz az előfizetéshez kell tartoznia. <br/><br/> **Konfigurációs kiszolgáló**: A konfigurációs kiszolgálót Azure-beli virtuális gépként kell beállítania <br/><br/> **Fő célkiszolgáló**: A fő célkiszolgálót Azure-beli virtuális gépként kell beállítania <br/><br/> A konfigurálást a Windows segítségével végezze el Windows rendszerű gépek, illetve a Linux segítségével a Linux rendszerű gépek esetében.<br/><br/> **Azure virtuális hálózat**: Egy Azure virtuális hálózatra lesz szüksége, amelyen üzembe helyezi a konfigurációs kiszolgálót és a fő célkiszolgálót. Ennek ugyanabban a régióban kell elhelyezkednie és ugyanahhoz az előfizetéshez kell tartoznia, mint az Azure Site Recovery-tároló | NA  
**Virtuális gépek/fizikai kiszolgálók** | Legalább egy VMware virtuális gép vagy fizikai Windows-/Linux-kiszolgáló.<br/><br/>Az üzembe helyezés során a mobilitási szolgáltatás az összes gépre telepítve lesz| Legalább egy VMware virtuális gép vagy fizikai Windows-/Linux-kiszolgáló.<br/><br/> Az üzembe helyezés során a rendszer minden gépre telepíti a Unified Agent ügynököt.




## <a name="azure-virtual-machine-requirements"></a>Az Azure virtuális gépek követelményei

A Site Recovery szolgáltatást az Azure által támogatott bármely operációs rendszert futtató virtuális gép és fizikai kiszolgáló replikálásához üzembe helyezheti. Ez a Windows és a Linux legtöbb verzióját magában foglalja. Meg kell győződnie róla, hogy a védeni kívánt helyszíni virtuális gépek megfelelnek az Azure követelményeinek.


## <a name="optimizing-your-deployment"></a>Az üzemelő példány optimalizálása

Az alábbi tippek segíthetnek az üzemelő példány optimalizálásában és méretezésében.

- **Az operációs rendszer kötetének mérete**: A virtuális gépek Azure-ba való replikálásakor az operációs rendszer kötetének mérete nem lehet nagyobb mint 1 TB. Ha ennél nagyobb kötetekkel rendelkezik, manuálisan áthelyezheti azokat egy másik lemezre a az üzembe helyezés megkezdése előtt.
- **Adatlemez mérete**: Az Azure-ba való replikáláskor egy virtuális gép legfeljebb 32 adatlemezt tartalmazhat, amelyek mérete egyenként legfeljebb 1 TB lehet. Gyakorlatilag legfeljebb ~32 TB méretű virtuális gépek replikálását és feladatátvételét végezheti el.
- **Helyreállítás terv korlátai**: A Site Recovery több ezer virtuális gépre méretezhető. A helyreállítási tervek a feladatátvételt együtt végrehajtó alkalmazások modelljeként lett kialakítva, ezért a helyreállítási tervben szereplő gépek száma 50-re van korlátozva.
- **Az Azure szolgáltatási korlátai**: Minden Azure-előfizetésre alapértelmezett korlátok vonatkoznak a magok, felhőszolgáltatások stb. tekintetében. Javasoljuk, hogy futtasson egy feladatátvételi tesztet az előfizetésében rendelkezésre álló erőforrások érvényesítése érdekében. Ezeket a korlátokat az Azure ügyfélszolgálatánál módosíthatja.
- **Kapacitástervezés**: Tervezzen a méretezésre és a teljesítményre.
- **Replikációs sávszélesség**: Ha alacsony a replikációs sávszélessége, vegye figyelemre a következőket:
    - **ExpressRoute**: A Site Recovery működik az Azure ExpressRoute- és WAN-optimalizálókkal, például a Riverbed szolgáltatással.
    - **Replikációs forgalom**: A Site Recovery egy intelligens kezdeti replikációt hajt végre, amely a teljes VHD helyett csak az adatblokkok használja. A következő replikációk során a rendszer csak a módosításokat replikálja.
    - **Hálózati forgalom**: A Windows QoS segítségével a cél IP-címen és porton alapuló házirend beállításával szabályozhatja a replikációhoz használt hálózati forgalmat.  Emellett, ha az Azure Backup Agent ügynök használatával hajtja végre az Azure-ba replikálást, konfigurálhatja a szabályozást az ügynökhöz.
- **RTO**: Ha meg szeretné mérni a Site Recovery-vel várható helyreállítási időre vonatkozó célkitűzést (RTO), javasoljuk, hogy futtasson egy feladatátvételi tesztet, és tekintse meg a Site Recovery-feladatokat a műveletek befejezéséhez szükséges idő elemzéséhez. Ha az Azure-ba végez feladatátvételt, a legjobb RTO érdekében javasoljuk, hogy automatizálja az összes manuális műveletet az Azure automatizálási és helyreállítási tervekkel való integráció révén.
- **RPO**: A Site Recovery támogatja a közel szinkronban működő helyreállítási időkorlátot (RPO) az Azure-ba történő replikálás során. Ez a módszer elegendően nagy sávszélességet feltételez az adatközpont és az Azure között.

