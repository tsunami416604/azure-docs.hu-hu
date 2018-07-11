---
title: VMware-ből az Azure-bA architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk áttekintést az Azure-bA az Azure Site Recovery a helyszíni VMware virtuális gépek replikálásakor használt összetevőkről és architektúráról
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 48adf61dc0f1796b820e1e14ca509d4618c6256b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920567"
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware-ből az Azure-replikáció architektúrája

Ez a cikk azt ismerteti, architektúrájának és folyamatainak használatos, ha replikálni, a feladatátvétel és helyreállítás a VMware virtuális gépek (VM) egy helyszíni VMware-hely és az Azure közötti használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Az architektúra összetevői

A következő táblázat és grafikus adja meg a VMware – Azure replikálás használt összetevők magas szintű nézetét.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, az Azure Storage-fiók és Azure-hálózatra. | A tárfiókot a replikált adatokat a helyszíni virtuális gépek tárolódik. Ha feladatátvételt végez a helyszínről az Azure-bA az Azure virtuális gépek a replikált adatokkal jönnek létre. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló gép** | Egy a helyszíni gépen. Azt javasoljuk, hogy kell futtatnia, VMware virtuális gépként helyezhető a letöltött OVF-sablon.<br/><br/> A gép fut, a helyszíni Site Recovery minden összetevőjét, többek között a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgáló. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.<br/><br/> **A folyamatkiszolgáló**: a konfigurációs kiszolgáló alapértelmezés szerint telepítve. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket és elküldi azt az Azure Storage. A folyamatkiszolgáló Ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a virtuális gépeket szeretne replikálni, és elvégzi a helyszíni gépek automatikus felderítését. Az üzembe helyezés növekedésével további, külön folyamatkiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.<br/><br/> **Fő célkiszolgáló**: a konfigurációs kiszolgáló alapértelmezés szerint telepítve. Az Azure-ból a feladat-visszavétel során kezeli azt a replikációs adatokat. Nagyméretű környezetekben hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.
**VMware-kiszolgálók** | VMware virtuális gépek helyszíni vSphere ESXi-kiszolgálókon lévő üzemeltetett. Azt javasoljuk, hogy a gazdagépek felügyeletéhez egy vCenter-kiszolgálóra. | Site Recovery üzembe helyezése során a Recovery Services-tároló vegyen fel VMware-kiszolgálókat.
**Replikált gépek** | A mobilitási szolgáltatás telepítve van az egyes replikált VMware virtuális Gépeken. | Azt javasoljuk, hogy lehetővé tegye az automatikus telepítési adatok a folyamatkiszolgálótól. Azt is megteheti a szolgáltatás manuális telepítése vagy egy automatikus központi telepítési módszer, például a System Center Configuration Managerrel használja.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Konfigurációs lépések

A széles körű beállítása a VMware Azure-beli vészhelyreállításához vagy az áttelepítés lépései a következők:

1. **Állítsa be az Azure-összetevőket**. Azure-fiók megfelelő engedélyekkel, egy Azure storage-fiókot, az Azure virtuális hálózat és egy Recovery Services-tároló van szüksége. [További információk](tutorial-prepare-azure.md).
2. **Állítsa be a helyszíni**. Ezek közé tartozik a fiók létrehozásával a VMware-kiszolgálón úgy, hogy a Site Recovery képes automatikusan felderíteni a virtuális gépek replikálása, állítson be egy fiókot, amely a mobilitási szolgáltatás összetevőt telepíteni a replikálni kívánt virtuális gépeken is használható, és annak ellenőrzésére, hogy a VMware-kiszolgálók és a virtuális gépek megfeleljenek az előfeltételeknek. Igény szerint is előkészítheti, ezeket az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően. A Site Recovery virtuális Gépet replikálja az Azure storage-fiókba, és az adatok használatával, amikor feladatátvételt végez az Azure-bA az Azure virtuális gépeket hoz létre. [További információk](vmware-azure-tutorial-prepare-on-premises.md).
3. **A replikáció beállítása**. Úgy dönt, hogy hová szeretné replikálni. Konfigurálja a forrás replikációs környezet létrehozásával egy egyetlen helyszíni VMware virtuális gép (a konfigurációs kiszolgáló), amely futtatja az összes a helyszíni Site Recovery-összetevők, amelyekre szüksége. A telepítés után regisztrálja a konfigurációs kiszolgáló gép a Recovery Services-tároló. Ezután válassza ki a cél-beállításokat. [További információk](vmware-azure-tutorial.md).
4. **Replikációs házirend létrehozása**. Létrehoz egy replikációs házirendet, amely meghatározza, hogyan történjen a replikációs. 
    - **Helyreállítási Időkorlát küszöbértéke**: A figyelési állapotok Ha replikációs megadott időn belül nem jelentkezik, egy riasztás (és opcionálisan egy e-mailt) jelenik meg. Például ha beállította a helyreállítási Időkorlát küszöbértéke – 30 percet, és a egy probléma megakadályozza, hogy a replikáció le a teljesítményfigyelésből 30 percig, az esemény jön létre. Ez a beállítás nincs hatással a replikáció. Replikáció folyamatos, és néhány perces időközönként létrehozott helyreállítási pontok
    - **Adatmegőrzési**: helyreállítási pont megőrzési Megadja, mennyi ideig helyreállítási pontok az Azure-ban kell tárolni. Adjon meg egy 0 és a premium storage 24 óra közötti értéket, vagy akár 72 órát standard szintű tárolóra vonatkozó. Adhatók át a legutóbbi helyreállítási pontot, vagy egy tárolt pont Ha az érték nagyobb, mint nulla. A megőrzési időszak után a helyreállítási pontok üríti ki.
    - **Összeomlás-konzisztens pillanatképekkel**: alapértelmezés szerint a Site Recovery összeomlás-konzisztens pillanatképeket készít, és helyreállítási pontok létrehozása velük néhány perces időközönként. Helyreállítási pont összeomlás-konzisztens, ha az adatok egymáshoz összetevők összes írási sorrend egységes, amilyenek korábban voltak azonnali, a helyreállítási pont létrehozása. Segít jobban megérteni, imagine egy áramkimaradás vagy egy hasonló esemény után a számítógép merevlemez-meghajtón lévő adatok állapotát. Összeomlás-konzisztens helyreállítási pont általában elegendő, ha az alkalmazás úgy lett kialakítva, egy összeomlási adatok inkonzisztenciákat nélkül helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatképek**: Ha ez az érték nem nulla, a virtuális gépen futó mobilitási szolgáltatás próbál-e előállítani a rendszer alkalmazáskonzisztens pillanatképeket és helyreállítási pontokat. Az első pillanatkép készül a kezdeti replikáció befejeződése után. Ezt követően pillanatképet készíteni a megadott gyakorisággal. Helyreállítási pont akkor alkalmazáskonzisztens, ha írási sorrend mellett egységes, futó alkalmazások összes üzemeltetési feladataik elvégzéséhez, és lemezre (alkalmazás leépítése) a puffer ürítése. Alkalmazáskonzisztens helyreállítási pontok használata akkor javasolt, például az SQL, Oracle és az Exchange adatbázis-alkalmazások esetében. Ha egy összeomlás-konzisztens pillanatkép elegendő, ezt az értéket 0-ra is állítható.  
    - **Több virtuális gépre kiterjedő konzisztencia**: igény szerint létrehozhat egy replikációs csoportot. Ezután, amikor a replikáció engedélyezése virtuális gépek gyűjthet a csoportba. A replikációt a virtuális gépek replikálása egy csoportba, és feladatátvételkor összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontok megosztottak. Ezt a beállítást használjon óvatosan, mivel ez befolyásolhatja a fájlrendszerek több gépen begyűjthető szükséges pillanatképként számítási feladatok teljesítményére. Csak akkor válassza ezt, ha a virtuális gépek ugyanazt a számítási feladatok és konzisztens kell futtatni, és a virtuális gépek hasonló churns rendelkeznek. Egy csoport legfeljebb 8 virtuális gépeket adhat hozzá. 
5. **Virtuális gép replikálásának engedélyezése**. Végül engedélyezheti a helyszíni VMware virtuális gépek replikációját. Ha létrehozott egy fiókot a mobilitási szolgáltatás telepítése, és meg, hogy a Site Recovery kell tennie egy ügyfélleküldéses telepítést, majd a mobilitási szolgáltatás települ az egyes virtuális Gépeken, amelyekre a replikáció engedélyezése. [További információk](vmware-azure-tutorial.md#enable-replication). Ha létrehozott egy replikációs csoportot a virtuális gépre kiterjedő konzisztencia, a virtuális gépek ehhez a csoporthoz is hozzáadhat.
6. **Feladatátvételi teszt**. Után minden be van állítva, érdemes egy feladatátvételi tesztet, ellenőrizze, hogy virtuális gépeket átadja a feladatokat az Azure elvárt módon. [További információk](tutorial-dr-drill-azure.md).
7. **Feladatátvétel**. Ha még csak át a virtuális gépek az Azure - feladatátvételt teljes valósítható meg. Vészhelyreállítás beállítása, ha a teljes feladatátvétel szükség szerint futtathatja. Teljes vészhelyreállítás az Azure-ba, a feladatátvételt követően, sikertelen lehet a helyszíni hely, és elérhetővé válik. [További információk](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi egy virtuális gép replikációját, elkezdi replikálni fogja a providerhez kiadott a replikációs házirend. 
2. Forgalom replikálja az Azure storage nyilvános végpontokat az interneten keresztül. Másik lehetőségként használhatja az Azure ExpressRoute [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Forgalom a helyek közötti virtuális magánhálózati (VPN) keresztül egy helyszíni hely Azure-ba történő nem támogatott.
3. Az Azure storage replikálása egy kezdeti másolatot készít a virtuális gép adatait.
4. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. Kommunikáció a következőképpen történik:

    - Virtuális gépek a helyszíni konfigurációs kiszolgálóval HTTPS a 443-as porton bejövő kommunikációt, a replikáció kezelését.
    - A konfigurációs kiszolgáló koordinálja a replikációt az Azure-ral HTTPS 443-as kimenő porton keresztül.
    - Virtuális gépek a replikációs adatokat a folyamatkiszolgálónak (a konfigurációs kiszolgáló gépen futó) HTTPS 9443-as porton bejövő küldése. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.


**VMware – Azure replikációs folyamat**

![Replikációs folyamat](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután replikáció be van állítva, és futtat egy vészhelyreállítási próba végrehajtása (teszt feladatátvétel) ellenőrizze, hogy minden a várt módon működik, futtathatja feladatátvétel és feladat-visszavétel szükség szerint.

1. Egyetlen gép feladatátvétel futtatása, vagy hozzon létre egy helyreállítási tervek egyszerre több virtuális gép feladatátvételét. Egyetlen gép feladatátvételi helyett egy helyreállítási terv előnye többek között:
    - Alkalmazás-függőségek modellezheti fel a virtuális gépek között az alkalmazás egy helyreállítási tervben.
    - Parancsfájlok, Azure runbookok hozzáadása, és manuális műveletek esetén.
2. A kezdeti feladatátvitel után véglegesítheti a számítási feladatok elérése az Azure virtuális gép elindításához.
3. Ha az elsődleges helyszíni hely megint elérhetővé válik, előkészítheti a feladat-visszavételhez. Annak érdekében, hogy a feladat-visszavételt, akkor be kell állítania egy feladat-visszavételi infrastruktúrát, beleértve:

    * **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, beállíthat egy Azure virtuális gépek kezelése az Azure-ból replikációs folyamat kiszolgálója. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: feladat-visszavételt, kell egy VPN-kapcsolat (vagy ExpressRoute), az Azure-hálózatot a helyszíni helyre.
    * **Különálló fő célkiszolgálót**: alapértelmezés szerint a fő célkiszolgáló, amelyen telepítve lett a konfigurációs kiszolgálóval, az a helyszíni VMware virtuális gép feladat-visszavétel kezeli. Ha sikertelen biztonsági nagy mértékű forgalom van szüksége, állítsa be egy önálló helyszíni fő célkiszolgálót erre a célra.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan hozott létre, a replikációs házirend létrehozva a helyszínről az Azure-bA.
4. Miután az összetevő a következő helyen, feladat-visszavétel három művelet történik:

    - 1. fázis: Az Azure virtuális gépek ismételt védelme, így azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    -  2. fázis: Feladatátvétel futtatása a helyszíni helyre.
    - 3. fázis: Számítási feladatok sikertelenek voltak vissza, miután újból engedélyezi a helyszíni virtuális gépek replikációját.
    
 
**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](vmware-azure-tutorial.md) VMware engedélyezése az Azure-bA.
