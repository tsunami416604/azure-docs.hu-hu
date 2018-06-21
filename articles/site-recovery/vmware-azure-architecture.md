---
title: Az Azure Site Recovery architektúrájáról Azure replikációs VMware |} Microsoft Docs
description: Ez a cikk áttekintést összetevők és használható, ha a helyszíni VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery architektúra
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 61c283c178936c98a9a18509c1b46035e48f8f24
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285270"
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

![Összetevők](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Konfigurációs lépések

Az Azure vész-helyreállítási vagy áttelepítési VMware beállításával kapcsolatos általános lépéseket a következők:

1. **Állítsa be az Azure összetevők**. Az Azure-fiók a megfelelő engedélyekkel, egy Azure storage-fiókot, egy Azure virtuális hálózatra és Recovery Services-tároló van szüksége. [További információk](tutorial-prepare-azure.md).
2. **Állítsa be a helyszíni**. Ezek közé tartozik az a VMware Server fiók beállítása, hogy a Site Recovery automatikusan fel tud deríteni replikálni, virtuális gépeket telepíteni a mobilitási szolgáltatás összetevőt a replikálni kívánt virtuális gépeken használt fiók beállítása és ellenőrzése, hogy a VMware-kiszolgálók és a virtuális gépek Előfeltételek felel meg. Opcionálisan készítheti elő a feladatátvételt követően Azure virtuális gépeken való kapcsolódáshoz. A Site Recovery replikálja a virtuális gép adatainak Azure storage-fiók, és létrehozza az Azure virtuális gépek használatát az adatokat, ha a feladatátvételt az Azure-bA. [További információk](vmware-azure-tutorial-prepare-on-premises.md).
3. **A replikáció beállítása**. Válassza ki a helyét a replikálni kívánt. Konfigurálja a forrás replikációs környezet egyetlen helyszíni VMware virtuális gép (a konfigurációs kiszolgáló) rendszert futtató összes az helyszíni, amelyekre szüksége van a Site Recovery-összetevők beállítását. A telepítés után a konfigurációs kiszolgáló számítógépén a Recovery Services-tároló rögzítheti. Ezután válassza ki a cél beállításai. [További információk](vmware-azure-tutorial.md).
4. **Hozzon létre egy replikációs házirendet**. Létrehozhat egy replikációs házirendet, amely meghatározza, hogyan történjen a replikáció. 
    - **Helyreállítási Időkorlát küszöbértéke**: A figyelés beállítás állapotok, ha a replikáció a megadott időn belül nem történik, egy riasztást (és választhatóan egy e-mailt) jelenik meg. Például beállíthatja a helyreállítási Időkorlát küszöbértéke – 30 percet, és egy probléma megakadályozza, hogy a replikációs 30 percig le, ha az jön létre. Ez a beállítás nincs hatással a replikáció. Replikáció folyamatos, és a helyreállítási pontokat hoz létre néhány percenként
    - **Megőrzési**: helyreállítási pont megőrzési határozza meg, hogy mennyi ideig helyreállítási pontok kell tartani az Azure-ban. Adjon meg egy értéket 0 és 24 óra, a prémium szintű storage, vagy akár 72 óra szabványos tárolására. Átveheti a legutóbbi helyreállítási pontot, vagy egy tárolt pontot, ha a nullánál nagyobb értéket. A megőrzési időszak után a helyreállítási pontok kiürítésekor.
    - **Összeomlás-konzisztens pillanatképek**: alapértelmezés szerint a Site Recovery összeomlás-konzisztens pillanatképek vesz igénybe, és helyreállítási pontok velük hoz létre a néhány percenként. A helyreállítási pont összeomlás-konzisztens, ha a egymáshoz az összetevők mindegyike írási magasrendű konzisztens, azonnali, mint a helyreállítási pont létrehozása. Jobb megértése érdekében, képzelhető el, a számítógép merevlemez-meghajtón lévő adatok állapotának áramkimaradás vagy hasonló esemény után. Összeomlás-konzisztens helyreállítási pontot az általában elegendő, ha az alkalmazást úgy tervezték, hogy egy összeomlási adatok inkonzisztenciákat nélkül helyreállíthatók.
    - **Alkalmazáskonzisztens pillanatképek**: Ha ez az érték nem nulla, a mobilitási szolgáltatást a virtuális gépen kísérletek fájl rendszer alkalmazáskonzisztens pillanatképeket és a helyreállítási pontok létrehozásához. Az első pillanatfelvétel kezdeti replikáció befejezése után. Ezt követően pillanatfelvételeket készít a megadott gyakorisággal. Helyreállítási pont alkalmazáskonzisztens akkor, ha írási-sorrendje nem egységes, futó alkalmazások végrehajtani a műveleteket, és kiüríteni a lemezre (alkalmazás leépítése) puffer. Alkalmazáskonzisztens helyreállítási pontokat például az SQL, Oracle, és az Exchange adatbázis-alkalmazások használata ajánlott. Ha egy összeomlás-konzisztens pillanatkép elegendő, ez az érték 0 állítható be.  
    - **Több virtuális Gépre kiterjedő konzisztencia**: nem kötelezően hozhat létre egy replikációs csoport. Ezután, amikor a replikáció engedélyezése virtuális gépek gyűjthet, hogy a csoportba. A replikációt a virtuális gépek replikálásához csoportosíthatja, és megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokat, amikor a feladatátvételt. Ezt a beállítást használjon gondosan, mivel több számítógépen összegyűjteni szükséges pillanatképként számítási feladat teljesítményére gyakorolt. Csak erre, ha a virtuális gépek futtatásához ugyanazokat a munkaterhelés és kell lenniük a konzisztens, és a virtuális gépek rendelkezik hasonló biztosítsanak. Legfeljebb 8 virtuális gépeket adhat hozzá egy csoporthoz. 
5. **Engedélyezze a virtuális gép replikációját**. Végül lehetővé teszik a helyszíni VMware virtuális gépek replikációját. Ha hoztak létre fiókot telepíteni a mobilitási szolgáltatást, és meg, hogy a Site Recovery kell tennie egy leküldéses telepítési, majd a mobilitási szolgáltatást telepíteni szeretné minden, amelynek a replikáció engedélyezése virtuális gépen. [További információk](vmware-azure-tutorial.md#enable-replication). Ha létrehozott egy replikációs csoportot a virtuális Gépre kiterjedő konzisztencia, a virtuális gépek ehhez a csoporthoz is hozzáadhat.
6. **Feladatátvételi teszt**. Után minden be van állítva, ellenőrizze, hogy virtuális gépek feladatátadást elvárt végezhet feladatátvételi tesztet. [További információk](tutorial-dr-drill-azure.md).
7. **Feladatátvételi**. Ha még csupán az áttelepítést a virtuális gépek Azure - feladatátvételt teljes ehhez. Ha katasztrófa utáni helyreállítás beállítása, teljes feladatátvevő futtathatja, szükség van. Teljes vész-helyreállítási az Azure-ba, a feladatátvétel után, sikertelen lehet a helyszíni webhelyre, és a rendelkezésre álló. [További információk](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a virtuális gépek replikálása, megkezdi a replikációs nyilatkozatnak replikálni. 
2. Forgalom replikálja az Azure storage nyilvános végpontok az interneten keresztül. Alternatív megoldásként használhatja az Azure ExpressRoute [nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikálásához forgalom a pont-pont virtuális magánhálózat (VPN) keresztül egy helyszíni hely Azure-ba nem támogatott.
3. Egy kezdeti másolatot készít a virtuális gép adatait a rendszer replikálja az Azure storage.
4. Kezdeti replikáció befejezését követően, a változási különbözeteket az Azure-bA replikálását kezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. Kommunikáció a következőképpen történik:

    - Virtuális gépek kommunikálni a helyszíni konfigurációs kiszolgáló HTTPS 443-as portot a bejövő, a replikáció kezelését.
    - A konfigurációs kiszolgáló koordinálja a replikálás az Azure-ral HTTPS 443-as kimenő porton keresztül.
    - Virtuális gépek elküldeni a replikációs adatok a folyamatkiszolgálótól (a konfigurációs kiszolgáló gépen futó) HTTPS 9443 port bejövő. Ez a port módosítható.
    - A folyamatkiszolgáló kap replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztül kimenő.


**VMware-ből az Azure replikációs folyamat**

![Replikációs folyamat](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután replikáció be van állítva, és futtatja a vész helyreállítási részletezéshez (feladatátvételi teszt) ellenőrizze, hogy minden a várt módon működik, futtatása feladatátvétel és a feladat-visszavétel szükség van.

1. Futtassa az egy gépen sikertelen lesz, vagy hozzon létre helyreállítási tervek egyszerre több virtuális gép feladatátvételt. Egyetlen gép feladatátvételi helyett egy helyreállítási terv előnye a következők:
    - A modell alkalmazás-függőségeit a virtuális gépek belefoglalja egy helyreállítási tervben az alkalmazás között.
    - Parancsfájlok, Azure runbookok hozzáadása, és manuális műveletek a felfüggesztését.
2. Után váltanak ki a kezdeti feladatátvételi, véglegesítheti a munkaterhelés elérése az Azure virtuális gép elindításához.
3. A helyszíni elsődleges hely újra nem érhető el, előkészítheti a visszaállítása sikertelen. A feladat-visszavétel infrastruktúra beállításához szükséges ahhoz, hogy a feladat-visszavételt, beleértve:

    * **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, állít be egy Azure virtuális gép az Azure-ból replikáció kezelésére folyamat kiszolgálója. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: a feladat-visszavételt, kell egy VPN-kapcsolat (vagy ExpressRoute) Azure-hálózatában a helyszíni hely.
    * **Külön fő célkiszolgáló**: alapértelmezés szerint a fő célkiszolgáló telepítése sikeres volt, a konfigurációs kiszolgáló a helyszíni VMware virtuális gép feladat-visszavétel kezeli. Ha sikertelen biztonsági nagy mennyiségű forgalom van szüksége, beállítása egy önálló helyszíni fő célkiszolgáló erre a célra.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ezzel a házirend-automatikusan létrejött, amikor a helyszíni Azure létrehozta a replikációs házirend.
4. Miután a helyen van, feladat-visszavétel három művelet történik:

    - 1. fázis: Lássa el újból védelemmel az Azure virtuális gépeket, hogy azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    -  2. fázis: Feladatátvételt végez a helyszíni hely.
    - 3. fázis: Miután munkaterhelések ismét sikertelen, újból engedélyezi a helyszíni virtuális gépek replikációját.
    
 
**Az Azure-ból VMware feladat-visszavétel**

![Feladat-visszavétel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](vmware-azure-tutorial.md) VMware Azure replikáció engedélyezéséhez.
