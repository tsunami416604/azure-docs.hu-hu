---
title: Feladatátvétel futtatása vész-helyreállítási során az Azure Site Recovery szolgáltatással
description: Hogyan lehet feladatátvételi virtuális gépek/fizikai kiszolgálók az Azure-ba az Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471268"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Feladatátvétel futtatása a helyszíni és az Azure-ba

Ez a cikk azt ismerteti, hogyan lehet a helyszíni gépek et az Azure-ba az Azure Site Recovery szolgáltatásban [át.](site-recovery-overview.md)

## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg](failover-failback-overview.md) a feladatátvételi folyamat a vész-helyreállítási.
- Ha több gépet szeretne átvenni, [ismerje meg,](recovery-plan-overview.md) hogyan gyűjthetgépeket egy helyreállítási tervben.
- Mielőtt egy teljes feladatátvétel, futtasson egy [vész-helyreállítási gyakorlat annak](site-recovery-test-failover-to-azure.md) érdekében, hogy minden a várt módon működik.

## <a name="prepare-to-connect-after-failover"></a>Felkészülés a feladatátvétel utáni csatlakozásra

Győződjön meg arról, hogy csatlakozhat az Azure virtuális gépek, amelyek feladatátvétel után jönnek létre, az alábbiakban számos dolgot kell tennie a helyszíni feladatátvétel előtt.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Helyszíni csatlakozás előkészítése a feladatátvétel után

Ha rdp/SSH-t használó Azure-virtuális gépekhez szeretne csatlakozni a feladatátvétel után, számos dolgot kell tennie a helyszíni feladatátvétel előtt.

**Feladatátvétel után** | **Helyen** | **Műveletek**
--- | --- | ---
**Windows t futtató Azure virtuális gép** | Helyszíni gép feladatátvétel előtt | Az Azure virtuális gép interneten keresztül való eléréséhez engedélyezze az RDP-t, és győződjön meg arról, hogy a TCP- és UDP-szabályok nyilvánosak, és hogy az RDP a **Windows tűzfal** > **engedélyezett alkalmazásai**összes profiljához engedélyezett. **Public**<br/><br/> Az Azure virtuális gép elérése a helyek közötti kapcsolaton keresztül, rdp engedélyezése a számítógépen, és győződjön meg arról, hogy rdp engedélyezett a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások,** **a tartományi és magánhálózatok.**<br/><br/> <br/><br/> Távolítsa el a statikus állandó útvonalakat és a WinHTTP proxyt. Győződjön meg arról, hogy az operációs rendszer SAN-házirendje **OnlineAll**. [További információ](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows-frissítések a virtuális gép, amikor elindítja a feladatátvételt. Előfordulhat, hogy a Windows update akkor indul el, amikor feladatátvételt végez, és a frissítés befejezéséig nem tud bejelentkezni a virtuális gépre.
**Linuxot futtató Azure virtuális gép** | Helyszíni gép feladatátvétel előtt | Győződjön meg arról, hogy a biztonságos rendszer szolgáltatás a virtuális gép van beállítva, hogy automatikusan elindul a rendszer indításakor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.


## <a name="run-a-failover"></a>Feladatátvétel futtatása

Ez az eljárás azt ismerteti, hogy miként futtathatok feladatátvételt a [helyreállítási tervhez](site-recovery-create-recovery-plans.md). Ha egyetlen virtuális gép feladatátvételt szeretne futtatni, kövesse a [VMware virtuális gép,](vmware-azure-tutorial-failover-failback.md)a [fizikai kiszolgáló](physical-to-azure-failover-failback.md)vagy a [Hyper-V virtuális gép](hyper-v-azure-failover-failback-tutorial.md)utasításait.


Futtassa a helyreállítási terv feladatátvételét az alábbiak szerint:

1. A Site Recovery tárolóban válassza a **Helyreállítási tervek** > *recoveryplan_name*lehetőséget.
2. Kattintson **a Feladatátvétel gombra.**

    ![Feladatátvétel](./media/site-recovery-failover/Failover.png)

3. A **feladatátvételi** > **feladatátvételi irányban**hagyja meg az alapértelmezett, ha az Azure-ba replikál.
4. A **Feladatátvétel**csoportban válassza ki azt a **helyreállítási pontot,** amelynek feladatátvételt kell megkezdenie.

    - **Utolsó**: Használja a legújabb pontot. Ez feldolgozza a Site Recovery szolgáltatásnak küldött összes adatot, és minden géphez létrehoz egy helyreállítási pontot. Ez a beállítás biztosítja a legalacsonyabb RPO (helyreállításipont) mert a feladatátvétel után létrehozott virtuális gép rendelkezik az összes adatot, amely replikált site recovery a feladatátvétel aktiválásakor.
   - **Legutóbbi feldolgozott:** Ezzel a beállítással a hely-helyreállítás által már feldolgozott legújabb helyreállítási pontra irányítható fel a virtuális gépek. A legújabb feldolgozott helyreállítási pontot a virtuális gép **legújabb helyreállítási pontjaiban láthatja.** Ez a beállítás alacsony RTO-t biztosít, mivel a feldolgozatlan adatok feldolgozására nem fordítunk időt
   - **Legújabb alkalmazás-konzisztens:** Ezzel a beállítással nem virtuális gépek át a legújabb alkalmazás konzisztens helyreállítási pont, amely a Site Recovery által feldolgozott.
   - **Legújabb több virtuális gép feldolgozása**: Ezzel a beállítással virtuális gépek, amelyek egy replikációs csoport feladatátvétel a legújabb közös multi-VM konzisztens helyreállítási pont. Más virtuális gépek feladatátvételt a legújabb feldolgozott helyreállítási pont. Ez a beállítás csak olyan helyreállítási tervekesetében érhető el, amelyek legalább egy virtuális gép több virtuális gép konzisztenciájával rendelkeznek.
   - **Legújabb többvm-alkalmazás-konzisztens:** Ezzel a beállítással a replikációs csoport részét játszó virtuális gépek feladatátvételt a legújabb közös multi-VM alkalmazás-konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb alkalmazáskonzisztens helyreállítási pont. Csak olyan helyreállítási tervekhez, amelyek legalább egy virtuális gép több virtuális gép konzisztenciával engedélyezve van.
   - **Egyéni**: Nem érhető el helyreállítási tervekhez. Ez a beállítás csak az egyes virtuális gépek feladatátvételére szolgál.

5. Válassza **a Leállító gép lehetőséget a feladatátvétel megkezdése előtt,** ha azt szeretné, hogy a Site Recovery állítsa le a forrásvirtuális gépeket a feladatátvétel megkezdése előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul.  

    > [!NOTE]
    > Ha feladatátvételi feladatokat hiper-V virtuális gépek, shutdown megpróbálja szinkronizálni és replikálni a helyszíni adatokat, amelyek még nem küldték el a szolgáltatásnak, a feladatátvétel elkísérése előtt. 

6. Kövesse a feladatátvételfolyamatot a **Feladatok** lapon. Még ha hiba történik is, a helyreállítási terv a teljes ig tart.
7. A feladatátvétel után jelentkezzen be a virtuális gép érvényesítéséhez. 
8. Ha a feladatátvételhez különböző helyreállítási pontra szeretne váltani, használja a **Helyreállítási pont módosítása**című gombot.
9. Ha készen áll, véglegesítheti a feladatátvételt. A **Commit** művelet törli a szolgáltatással elérhető összes helyreállítási pontot. A **Helyreállítási pont módosítása** beállítás a továbbiakban nem lesz elérhető.

## <a name="run-a-planned-failover-hyper-v"></a>Tervezett feladatátvétel futtatása (Hyper-V)

Futtathat egy tervezett feladatátvételt a Hyper-V virtuális gépekhez.

- A tervezett feladatátvétel nulla adatvesztéses feladatátvételi lehetőség.
- Tervezett feladatátvétel esetén először a forrás virtuális gépek leállnak, a legújabb adatok szinkronizálása, majd a feladatátvétel aktiválódik.
- Egy tervezett feladatátvételt futtat a **Tervezett feladatátvételi** lehetőség használatával. A rendszeres feladatátvételhez hasonlóan fut.
 
## <a name="track-failovers"></a>Feladatátvételek nyomon követése

A feladatátvételhez számos feladat kapcsolódik.

![Feladatátvétel](./media/site-recovery-failover/FailoverJob.png)

- **Előfeltételek ellenőrzése:** Biztosítja, hogy a feladatátvételhez szükséges feltételek teljesülnek.
- **Feladatátvétel:** Feldolgozza az adatokat, hogy egy Azure virtuális gép hozható létre belőle. Ha a **Legújabb** helyreállítási pontot választotta, a rendszer létrehozza a helyreállítási pontot a szolgáltatásnak küldött adatokból.
- **Start**: Létrehoz egy Azure virtuális gép az előző lépésben feldolgozott adatok használatával.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt:** A feladatátvétel megkezdése előtt a virtuális gép replikációja leállt. Ha megszakítja a folyamatban lévő feladat, feladatátvétel leáll, de a virtuális gép nem indul el replikálni. A replikáció nem indítható újra.


### <a name="extra-failover-time"></a>Extra feladatátvételi idő

Bizonyos esetekben a virtuális gép feladatátvétele köztes lépést igényel, amely általában körülbelül 8–10 percet vesz igénybe. Ezek azok a gépek, amelyeket érint ez a további lépés/idő:

* A 9.8-nál régebbi Mobility szolgáltatásverziót futtató Virtuális Gép.VMware virtual machines running a Mobility service version back than 9.8.
* Fizikai kiszolgálók és a Hyper-V virtuális gépek fizikai kiszolgálóként védett.
* VMware Linux virtuális gépek.
* VMware virtuális gépek, amelyeken ezek az illesztőprogramok nincsenek jelen rendszerindító illesztőprogramként:
    * storvsc között
    * vmbus között
    * storflt között
    * intelide
    * Atapi
* VMware virtuális gépek, amelyek nem rendelkeznek DHCP engedélyezve, függetlenül attól, hogy dhcp-vagy statikus IP-címeket használnak.


## <a name="automate-actions-during-failover"></a>Műveletek automatizálása feladatátvétel közben

Előfordulhat, hogy a feladatátvétel során szeretné automatizálni a műveleteket. Ehhez parancsfájlokat vagy Azure-automatizálási runbookokat használhat a helyreállítási tervekben.

- [További információ](site-recovery-create-recovery-plans.md) a helyreállítási tervek létrehozásáról és testreszabásáról, beleértve a parancsfájlok hozzáadását is.
- [Ismerje meg](site-recovery-runbook-automation.md) az Azure Automation runbookok helyreállítási tervekhez való hozzáadását.


## <a name="configure-settings-after-failover"></a>Beállítások konfigurálása feladatátvétel után

### <a name="retain-drive-letters-after-failover"></a>Meghajtóbetűjelek megtartása feladatátvétel után

A Site Recovery kezeli a meghajtóbetűjelek megőrzését. Ha a virtuális gép replikációja során kizárja a lemezeket, [tekintse át a működésre vonatkozó példát.](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)

### <a name="prepare-in-azure-to-connect-after-failover"></a>Felkészülés az Azure-ban a feladatátvétel utáni csatlakozásra

Ha az RDP vagy Az SSH használatával feladatátvétel után létrehozott Azure-virtuális gépekhez szeretne csatlakozni, kövesse a táblázatban összefoglalt követelményeket.

**Feladatátvétel** | **Helyen** | **Műveletek**
--- | --- | ---
**Windows t futtató Azure virtuális gép** | Azure virtuális gép feladatátvétel után |  [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az RDP-port.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** segítségével ellenőrizze a virtuális gép képernyőképének ellenőrzéséhez.<br/><br/> Ha nem tud csatlakozni, ellenőrizze, hogy a virtuális gép fut-e, és tekintse át ezeket a [hibaelhárítási tippeket.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Linuxot futtató Azure virtuális gép** | Azure virtuális gép feladatátvétel után | A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az SSH-port.<br/><br/> [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** egy screenshot a virtuális gép.<br/><br/>

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="set-up-ip-addressing"></a>IP-címzés beállítása

- **Belső IP-címek:** Az Azure virtuális gép belső IP-címének beállításához a feladatátvétel után néhány lehetőség közül választhat:
    - Ugyanazt az IP-címet: Használhatja ugyanazt az IP-címet az Azure virtuális gépen, mint a helyszíni gép számára lefoglalt.
    - Használjon másik IP-címet: Az Azure virtuális géphez használhat másik IP-címet.
    - [További információ](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) a belső IP-címek beállításáról.
- **Külső IP-címek**: A nyilvános IP-címeket megőrizheti a feladatátvételkor. A feladatátvételi folyamat részeként létrehozott Azure virtuális gépekhez hozzá kell rendelni egy Azure nyilvános IP-címet, amely elérhető az Azure-régióban. Nyilvános IP-címet manuálisan vagy helyreállítási tervvel automatizálva rendelhet hozzá. [További információ](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>További lépések

Miután feladatátvételt, újra kell védenie az Azure virtuális gépek replikálásának megkezdéséhez a helyszíni helyre. A replikáció működése után, ha készen áll, visszaveheti a helyszíni adatokat.

- [További információ](failover-failback-overview.md#reprotectionfailback) az újravédelemről és a feladat-visszavételről.
- [Felkészülés](vmware-azure-reprotect.md) a VMware újravédelmére és a feladat-visszavételre.
- [Visszavétel](hyper-v-azure-failback.md) Hyper-V virtuális gépek.
- [Ismerje meg](physical-to-azure-failover-failback.md) a fizikai kiszolgálók feladatátvételi és feladat-visszavételi folyamatát.

