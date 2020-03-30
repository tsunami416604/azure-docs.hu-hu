---
title: VMware virtuális gépek előkészítése az Azure Site Recovery újbóli védelmére és feladat-visszavételére
description: Felkészülés a VMware virtuális gépek feladat-visszavételére az Azure Site Recovery feladatátvétele után
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257185"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Felkészülés a VMware virtuális gépek újbóli védelmére és feladat-visszavételére

A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók azure-ba [való feladatátvétele](site-recovery-failover.md) után újra megvédi a feladatátvétel után létrehozott Azure virtuális gépeket, hogy azok replikálódjanak a helyszíni helyre. Az Azure-ból a helyszíni replikációval ezután visszaléphet az Azure-ból a helyszíni feladatátvételre, amikor készen áll.

Mielőtt folytatna, gyors áttekintést kaphat erről a videóról, amely bemutatja, hogyan lehet visszaadni az Azure-ból egy helyszíni webhelyre.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Újravédelmi/feladat-visszavételi összetevők

Az Azure-ból való újbóli védelemhez és visszavételhez számos összetevőre és beállításra van szükség.

**Összetevő**| **Részletek**
--- | ---
**Helyszíni konfigurációs kiszolgáló** | A helyszíni konfigurációs kiszolgálónak futnia kell, és csatlakoznia kell az Azure-hoz.<br/><br/> A virtuális gép, amelynek nem sikerült vissza, léteznie kell a konfigurációs kiszolgáló adatbázisában. Ha a katasztrófa hatással van a konfigurációs kiszolgálóra, állítsa vissza ugyanazzal az IP-címmel, hogy megbizonyosodjon arról, hogy a feladat-visszavétel működik.<br/><br/>  Ha a replikált gépek IP-címei megmaradtak a feladatátvételen, a helyek közötti kapcsolatot (vagy ExpressRoute-kapcsolatot) létre kell hozni az Azure virtuális gépek és a konfigurációs kiszolgáló feladat-visszavételi hálózati adaptere között. A megőrzött IP-címek esetében a konfigurációs kiszolgálónak két hálózati adapterre van szüksége – az egyika forrásgép-kapcsolathoz, a másik az Azure feladat-visszavételi kapcsolatához. Ezzel elkerülhető a forrás alhálózati címtartományainak átfedése, és a virtuális gépek felett lévő feladatátvétel.
**Kiszolgáló feldolgozása az Azure-ban** | Az Azure-ban egy folyamatkiszolgálóra van szüksége, mielőtt visszaléphetne a helyszíni webhelyre.<br/><br/> A folyamatkiszolgáló adatokat fogad a védett Azure virtuális gépről, és elküldi azokat a helyszíni helyről.<br/><br/> Alacsony késleltetésű hálózatra van szüksége a folyamatkiszolgáló és a védett virtuális gép között, ezért azt javasoljuk, hogy a nagyobb replikációs teljesítmény érdekében telepítse a folyamatkiszolgálót az Azure-ban.<br/><br/> A proof-of-concept, használhatja a helyszíni folyamat kiszolgáló, és ExpressRoute privát társviszony-létesítés.<br/><br/> A folyamatkiszolgálónak abban az Azure-hálózatban kell lennie, amelyben a feladatátvételi virtuális gép található. A folyamatkiszolgálónak képesnek kell lennie a helyszíni konfigurációs kiszolgálóval és a fő célkiszolgálóval való kommunikációra is.
**Külön fő célkiszolgáló** | A fő célkiszolgáló feladat-visszavételi adatokat fogad, és alapértelmezés szerint a Windows fő célkiszolgálója a helyszíni konfigurációs kiszolgálón fut.<br/><br/> A fő célkiszolgálóhoz legfeljebb 60 lemez csatolható. A feladat-visszavétel alatt álló virtuális gépek összesen több mint 60 lemezzel rendelkeznek, vagy ha nagy mennyiségű forgalmat ad vissza, hozzon létre egy külön fő célkiszolgálót a feladat-visszavételhez.<br/><br/> Ha a gépek több virtuálisgép-konzisztencia replikációs csoportjába kerülnek, a virtuális gépeknek mind Windows-nak kell lenniük, vagy mindNek Linuxnak kell lenniük. Hogy miért? Mivel a replikációs csoportban lévő összes virtuális gépnek ugyanannak a fő célkiszolgálónak kell lennie, és a fő célkiszolgálónak ugyanazzal az operációs rendszerrel kell rendelkeznie (ugyanazzal vagy magasabb verzióval), mint a replikált gépek.<br/><br/> A fő célkiszolgáló nem rendelkezik pillanatképekkel a lemezeken, ellenkező esetben az újravédelem és a feladat-visszavétel nem fog működni.<br/><br/> A fő cél nem rendelkezhet Paravirtual SCSI-vezérlővel. A vezérlő csak LSI logikai vezérlő lehet. LSI logikai vezérlő nélkül az újravédelem sikertelen.
**Feladat-visszavételreplikációs házirend** | A helyszíni helyre való replikáláshoz feladat-visszavételi házirendre van szükség. Ez a szabályzat automatikusan létrejön, amikor létrehoz egy replikációs szabályzatot az Azure-ba.<br/><br/> A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 15 perces RPO-küszöbértékre van beállítva, a helyreállítási pontok 24 órás megtartása, és az alkalmazáskonzisztens pillanatkép-gyakoriság 60 perc. A házirend nem szerkeszthető. 
**Helyek közötti VPN/ExpressRoute privát társviszony-létesítés** | Az újravédelemhez és a feladat-visszavételhez helyek közötti VPN-kapcsolatra vagy ExpressRoute-alapú privát társviszony-létesítésre van szükség az adatok replikálásához. 


## <a name="ports-for-reprotectionfailback"></a>Portok az újravédelemhez/feladat-visszavételhez

Számos portnak nyitva kell lennie az újbóli védelem/feladat-visszavétel érdekében. Az alábbi ábra a portokat és a visszavételi folyamat újbóli védelmét/visszavételét mutatja be.

![Feladat-átvételi és feladat-visszavételi portok](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Folyamatkiszolgáló üzembe helyezése az Azure-ban

1. [Állítson be egy folyamatkiszolgálót](vmware-azure-set-up-process-server-azure.md) az Azure-ban feladat-visszavételhez.
2. Győződjön meg arról, hogy az Azure virtuális gépek elérhetik a folyamatkiszolgálót. 
3. Győződjön meg arról, hogy a helyek közötti VPN-kapcsolat vagy az ExpressRoute privát társviszony-létesítő hálózat a folyamatkiszolgálóról a helyszíni helyre történő adatküldéshez elegendő sávszélességgel rendelkezik.

## <a name="deploy-a-separate-master-target-server"></a>Külön fő célkiszolgáló telepítése

1. Vegye figyelembe a fő célkiszolgáló [követelményeit és korlátait.](#reprotectionfailback-components)
2. Hozzon létre egy [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) vagy [Linux](vmware-azure-install-linux-master-target.md) fő célkiszolgálót, hogy megfeleljen az újravédeni és visszaadni kívánt virtuális gépek operációs rendszerének.
3. Győződjön meg arról, hogy nem használja a Storage vMotion-t a fő célkiszolgálóhoz, vagy a feladat-visszavétel sikertelen lehet. A virtuális gép nem tud elindulni, mert a lemezek nem érhetők el.
    - Ennek elkerülése érdekében zárja ki a fő célkiszolgálót a vMotion listából.
    - Ha egy fő cél egy Storage vMotion feladaton megy keresztül az újravédelem után, a fő célkiszolgálóhoz csatlakoztatott védett virtuálisgép-lemezek áttelepülnek a vMotion feladat céljára. Ha ezt követően megpróbálja visszaadni a feladatátvételt, a lemezleválás sikertelen lesz, mert a lemezek nem találhatók. Ezután nehéz megtalálni a lemezeket a tárfiókokban. Ebben az esetben keresse meg őket manuálisan, és csatolja őket a virtuális géphez. Ezt követően a helyszíni virtuális gép indítható.

4. Adatmegőrzési meghajtó hozzáadása a meglévő Fő Windows-fő célkiszolgálóhoz. Adjon hozzá egy új lemezt, és formázza a meghajtót. A megőrzési meghajtó segítségével állítsa le a pontokat, amikor a virtuális gép replikálja vissza a helyszíni helyre. Vegye figyelembe ezeket a feltételeket. Ha nem teljesülnek, a meghajtó nem szerepel a fő célkiszolgálón:
    - A kötet et semmilyen más célra nem használják, például replikációs célként, és nincs zárolási módban.
    - A kötet nem gyorsítótár-kötet. A folyamatkiszolgáló és a fő cél egyéni telepítési kötete nem jogosult adatmegőrzési kötetre. Ha a folyamatkiszolgáló és a fő cél kötetre van telepítve, a kötet a fő cél gyorsítótár-kötete.
    - A kötet fájlrendszertípusa nem FAT vagy FAT32.
    - A kötet kapacitása nem nulla.
    - A Windows alapértelmezett megőrzési kötete az R kötet.
    - A Linux alapértelmezett megőrzési kötete /mnt/retention.

5. Ha meglévő folyamatkiszolgálót használ, adjon hozzá egy meghajtót. Az új meghajtónak meg kell felelnie az utolsó lépés követelményeinek. Ha a megőrzési meghajtó nem jelenik meg, nem jelenik meg a portál kijelölési legördülő listájában. Miután hozzáad egy meghajtót a helyszíni fő célhoz, a meghajtó megjelenése akár 15 percet is igénybe vehet, amíg a meghajtó megjelenik a portálon. Frissítheti a konfigurációs kiszolgálót, ha a meghajtó 15 perc elteltével nem jelenik meg.
6. Telepítse a VMware-eszközöket vagy a nyílt vm-eszközöket a fő célkiszolgálóra. Az eszközök nélkül a fő cél ESXi gazdagépének adattárai nem észlelhetők.
7. Állítsa be a lemezt. EnableUUID=true beállítás a fő cél virtuális gép konfigurációs paramétereiben a VMware-ben. Ha ez a sor nem létezik, adja hozzá. Ez a beállítás szükséges ahhoz, hogy a VMDK-nak egységes UUID-t biztosítson, hogy megfelelően csatlakoztatható legyen.
8. A vCenter Server hozzáférési követelményeinek ellenőrzése:
    - Ha a virtuális gép, amelynek a visszaadott vissza a VMware vCenter Server által felügyelt ESXi-gazdagépen van, a fő célkiszolgálónak hozzáférésre van szüksége a helyszíni virtuálisgép-virtuálisgép-lemez (VMDK) fájlhoz, hogy a replikált adatokat a virtuális gép lemezeibe írhassa. Győződjön meg arról, hogy a helyszíni virtuális gép adattár a fő célállomás olvasási/írási hozzáféréssel van csatlakoztatva.
    - Ha a virtuális gép nem egy VMware vCenter Server által felügyelt ESXi-gazdagépen található, a Site Recovery új virtuális gépet hoz létre az újravédelem során. Ez a virtuális gép azESXi-állomáson jön létre, amelyen létrehozza a fő célkiszolgáló virtuális gép. Válassza ki az ESXi gazdagép gondosan, a virtuális gép létrehozásához a kívánt gazdagépen. A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.
    - Egy másik lehetőség, ha a helyszíni virtuális gép már létezik a feladat-visszavétel, a feladat-visszavétel előtt törölni. A feladat-visszavétel ezután létrehoz egy új virtuális gép ugyanazon az állomáson, mint a fő cél ESXi állomás. Ha egy másik helyre küld vissza, az adatok ugyanabba az adattárba és ugyanabba az ESXi-állomásra lesznek visszatérítve, mint a helyszíni főkiszolgáló által használt.
9. A virtuális gép virtuális gépekre visszaadott fizikai gépek esetében a fő célkiszolgálót futtató gazdagép felderítését kell elvégeznie, mielőtt újra megvédheti a gépet.
10. Ellenőrizze, hogy az ESXi-állomás, amelyen a fő cél virtuális gép rendelkezik legalább egy virtuálisgép-fájlrendszer (VMFS) adattár csatolták hozzá. Ha nincs VMFS-adattár csatolva, az adattár bemenet az újravédelmi beállítások üres, és nem tudja folytatni.


## <a name="next-steps"></a>További lépések

A virtuális gép [újbóli védelme.](vmware-azure-reprotect.md)
