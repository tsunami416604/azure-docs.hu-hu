---
title: "Figyelése és hibaelhárítása szempontjából a virtuális gépek és fizikai kiszolgálók védelmét |} Microsoft Docs"
description: "Az Azure Site Recovery koordinálja a replikáció, feladatátvétel és a helyszíni kiszolgálók Azure vagy egy másodlagos adatközpontba rajta található virtuális gép helyreállítása. Ez a cikk segítségével figyelésére és hibaelhárítására a Virtual Machine Manager vagy a Hyper-V hely védelmét."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 2d033e5af13660c99aba813c58b743bf94a6b95a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Figyelése és hibaelhárítása szempontjából a virtuális gépek és fizikai kiszolgálók védelme
A figyelési és hibaelhárítási útmutató segítségével megtudhatja, hogyan replikálás állapotának nyomon követése és technikák hibaelhárítása az Azure Site Recovery.

## <a name="understand-the-components"></a>Összetevők megismerése
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>VMware virtuális gép vagy fizikai kiszolgálók helyen történő központi telepítés a helyszíni és az Azure közötti replikáció
Helyszíni VMware virtuális gép vagy fizikai kiszolgálók és az Azure közötti helyreállítás beállításához, be kell állítania a konfigurációs kiszolgáló, a fő célkiszolgáló és a kiszolgáló-összetevők folyamat a virtuális gép vagy a kiszolgáló. Ha engedélyezi a védelmet a forráskiszolgálón, Azure Site Recovery telepíti a Microsoft Azure App Service Mobile Apps szolgáltatásának. A helyszíni kimaradás után és a forrás után kiszolgáló átadja a feladatokat Azure, állítsa be az Azure-ban egy folyamatkiszolgálóra és egy fő célkiszolgálóra a helyi, hogy a forráskiszolgálón, a helyszíni ügyfél igényel.

![VMware vagy fizikai helyen történő központi telepítés, a helyszíni és az Azure közötti replikáció](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>A Virtual Machine Manager helyen történő központi telepítés a helyszíni helyek közötti replikáció
Két helyszíni hely közötti adatbázis-helyreállítási beállításához szüksége az Azure Site Recovery provider letöltése és telepítése, a Virtual Machine Manager kiszolgálón. A szolgáltató annak érdekében, hogy az összes Azure-portálról előidéző műveletekkel helyszíni műveletek beolvasása lefordítani internetkapcsolat szükséges.

![A Virtual Machine Manager helyen történő központi telepítés a helyszíni helyek közötti replikáció](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>A Virtual Machine Manager helyen történő központi telepítés a helyszíni helyek és az Azure közötti replikáció
A helyszíni helyek és az Azure közötti helyreállítás beállításakor szüksége az Azure Site Recovery provider letöltése és telepítése, a Virtual Machine Manager kiszolgálón. Is szeretné telepíteni az Azure Recovery Services Agent, amelyek telepítve kell lennie minden Hyper-V gazdagépen. [További](site-recovery-hyper-v-azure-architecture.md) további információt.

![A Virtual Machine Manager helyen történő központi telepítés a helyszíni helyek és az Azure közötti replikáció](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Hyper-V hely központi telepítésének a helyszíni helyek és az Azure közötti replikáció
Ez a folyamat hasonlít a Virtual Machine Manager telepítés. Az egyetlen különbség, hogy a Hyper-V-gazdagéphez az Azure Site Recovery provider és az Azure Recovery Services Agent beolvasása telepítve. [További információk](site-recovery-hyper-v-azure-architecture.md). .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Konfigurációs, védelmi és helyreállítási műveletek figyelése
Az Azure Site Recovery minden művelet naplózva, és nyomon követheti a a **feladatok** fülre. Konfigurációs, védelmi és helyreállítási hiba, lépjen a **feladatok** lapra, és keresse meg a hibákat.

![A feladatok lapján is sikertelen szűrő](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Ha talál meg a hibákat a **feladatok** fülre, kattintson a feladatra, majd **hiba legutolsó részletes adatai** , hogy a feladat.

![A hiba részletes ADATAIT gomb](media/site-recovery-monitoring-and-troubleshooting/image4.png)

A hiba részletes adatait segítségével azonosíthatók a lehetséges ok és a javaslat a problémára.

![Egy adott feladat kapcsolatos hiba részleteit megjelenítő párbeszédpanel](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Az előző példában egy másik művelet folyamatban úgy tűnik, hogy a védelem konfigurálása sikertelen lehet okozza. Hárítsa el a problémát a javaslat alapján, és kattintson a **indítsa újra a** való indítsa el újra a műveletet.

![Az Újraindítás gombra, a feladatok lap](media/site-recovery-monitoring-and-troubleshooting/image6.png)

A **indítsa újra a** , nincs lehetőség minden műveletnél. Ha egy művelet nem rendelkezik a **indítsa újra a** lehetőséget, lépjen vissza az objektumhoz, majd végezze el újra a műveletet. Bármely, a folyamatban lévő feladat megszakíthatja használatával a **Mégse** gombra.

![A Mégse gomb](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>A virtuális gépek replikálás állapotának figyelése
Az Azure portál segítségével távolról figyelése az Azure Site Recovery-szolgáltatókat az egyes a védett entitás. Kattintson a **védett elemek**, és kattintson a **VMM-FELHŐKBEN** vagy **védelmi csoportok**. A **VMM-FELHŐKBEN** lap csak az, hogy a Virtual Machine Manager a megadott központi telepítéseknél használható. Az egyéb forgatókönyvek, a védett entitás alatt állnak a **védelmi csoportok** fülre.

![A VMM-Felhőkben és védelmi csoportok beállításai](media/site-recovery-monitoring-and-troubleshooting/image8.png)

A védett entitás kattintson a megfelelő felhőre, vagy az alsó ablaktáblán látható védelmi csoport összes rendelkezésre álló műveletek megjelenítéséhez.

![A kijelölt védett entitás elérhető lehetőségek](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Amint az előző képernyőképet, van-e a virtuális gép állapotát **kritikus**. Kattintson a **hiba legutolsó részletes adatai** gomb a hiba megtekintéséhez alján. Alapján a **lehetséges okok** és **javaslat**, a probléma megoldásához.

![A hiba részletes adatait gomb](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Hibák és javaslatok hiba legutolsó részletes adatai párbeszédpanel](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> Ha minden aktív műveletek végrehajtása közben, vagy sikertelen, lépjen a **feladatok** a hibát egy adott feladat megtekintéséhez a korábban említett megtekintése.
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>A helyszíni Hyper-V problémák elhárítása
A helyszíni Hyper-V manager konzol csatlakozzon, válassza ki a virtuális gépet, és tekintse meg a replikációs állapotát.

![A beállítás a Hyper-V manager konzolon a replikációs állapotának megtekintéséhez](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Ebben az esetben **replikálás állapotának** van **kritikus**. Kattintson a jobb gombbal a virtuális gépet, és kattintson **replikációs** > **replikálás állapotának megtekintése** a részletek megtekintéséhez.

![Egy adott virtuális gép replikációs állapotát](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Ha a replikáció a virtuális gép fel van függesztve, kattintson a jobb gombbal a virtuális gépet, és kattintson **replikációs** > **replikálás folytatása**.

![A beállítással indítsa el a replikálást a Hyper-V manager konzolon](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Ha egy virtuális gép áttelepítést végez egy új Hyper-V gazdagépen, amely a fürt vagy egy önálló gépre belül, és a Hyper-V gazdagépen keresztül Azure Site Recovery van konfigurálva, a virtuális gép replikálása nem tudnák negatív hatással lehet. Győződjön meg arról, hogy az új Hyper-V állomás megfelel-e az előfeltételeknek, és az Azure Site Recovery segítségével konfigurálhatók.

### <a name="event-log"></a>Eseménynapló
| Eseményforrások | Részletek |
| --- |:--- |
| **Alkalmazások és a szolgáltatás Logs/Microsoft/VirtualMachineManager/kiszolgáló/Admin** (Virtual Machine Manager-kiszolgáló) |Hasznos naplózási számos különböző a Virtual Machine Manager-problémák hibaelhárításához nyújt. |
| **Alkalmazások és a naplók vagy MicrosoftAzureRecoveryServices/replikációs szolgáltatás** (Hyper-V gazdagép) |Sok Microsoft Azure Recovery Services Agent problémák hasznos naplózási biztosít. <br/> ![A Hyper-V gazdagép replikációs eseményforrás helye](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Alkalmazások és a szolgáltatás Logs/Microsoft/Azure Site Recovery/szolgáltató/műveleti** (Hyper-V gazdagép) |Sok Microsoft Azure Site Recovery szolgáltatás problémák hasznos naplózási biztosít. <br/> ![A Hyper-V gazdagép működési események forrás helye](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Alkalmazások és a Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V gazdagép) |Hasznos naplózási sok Hyper-V Virtuálisgép-felügyeleti problémák elhárításához nyújt. <br/> ![Hyper-V gazdagéphez a Virtual Machine Manager forrás helye](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Hyper-V replikációs naplózási beállítások
Az összes Hyper-V replikáció tartozó eseményeket a Hyper-V-VMMS\\rendszergazda naplóban található alkalmazások és szolgáltatásnaplók\\Microsoft\\Windows. A Hyper-V virtuális gép kezelési szolgáltatás emellett engedélyezheti az elemzési naplóját. Ahhoz, hogy ez a napló, először ellenőrizze az elemzési és hibakeresési naplók az Eseménynapló használatával megtekinthető. Nyissa meg az eseménynaplót, és kattintson **nézet** > **elemzési és hibakeresési naplók megjelenítése**.

![Az elemzési és hibakeresési naplók megjelenítése lehetőséget.](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Az elemzési log utasítás végrehajtása alatt látható **Hyper-V-VMMS**.

![Az elemző naplózni a fát megjelenítő](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Az a **műveletek** ablaktáblán kattintson a **napló engedélyezése**. Az engedélyezése után megjelenik **Teljesítményfigyelő** , egy **eseménykövető munkamenet** alatt **adatgyűjtő-csoportosítók.**

![A Teljesítményfigyelő fában esemény-nyomkövetési munkamenet](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Az összegyűjtött információk megtekintéséhez állítsa le a nyomkövetési munkamenet a napló letiltásával. Mentse a naplót, és nyissa meg ismét az eseménynaplóban, vagy más eszközök segítségével tetszés szerint konvertálja.

## <a name="reach-out-for-microsoft-support"></a>For Microsoft Support érheti el
### <a name="log-collection"></a>Naplógyűjtést
A Virtual Machine Manager-hely védelmét, el [támogatási diagnosztikai Platform (SDP) eszközzel Azure Site Recovery naplógyűjtést](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) gyűjthet a szükséges naplókat.

Hyper-V hely védelmét, töltse le a [eszköz](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) és végrehajtja a Hyper-V gazdagépen a naplók gyűjtéséhez.

VMware vagy fizikai kiszolgáló használata esetén tekintse meg [VMware és fizikai hely védelme az Azure Site Recovery naplógyűjtést](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) gyűjthet a szükséges naplókat.

Az eszköz összegyűjti a naplók helyi % LocalAppData%\ElevatedDiagnostics egy véletlenszerűen almappájában.

![A Hyper-V hely védelem látható minta lépéseket.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása
Az Azure Site Recovery egy támogatási jegy növeléséhez elérhetők a Azure támogatja a következő URL-cím segítségével <http://aka.ms/getazuresupport>.

## <a name="kb-articles"></a>Tudásbáziscikkek
* [A védett virtuális gépek a feladatátvételt vagy az Azure-bA áttelepíteni a meghajtó betűjelével megőrzése](http://support.microsoft.com/kb/3031135)
* [A helyszíni Azure védelem hálózati sávszélesség-használat kezelése](https://support.microsoft.com/kb/3056159)
* [Az Azure Site Recovery: "a fürt erőforrás nem található" hibaüzenet jelenik meg a virtuális gép védelmének engedélyezése](http://support.microsoft.com/kb/3010979)
* [Ismertetése és hibaelhárítása a Hyper-V replikáció útmutató](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Azure Site Recovery előforduló hibákat, és azok megoldásait
Az alábbiakban gyakori hibák és azok megoldásait. Minden egyes hibához egy külön wiki lapján ismertetését.

### <a name="general"></a>Általános kérdések
* <span style="color:green;">ÚJ</span> [feladatai hiba miatt sikertelenül működő "művelet van folyamatban." Hiba 505, 514, 532.](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">ÚJ</span> ["Kiszolgáló nem csatlakozik az internethez" hiba miatt sikertelen feladatok. Hiba történt a 25018.](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Beállítás
* [A Virtual Machine Manager-kiszolgáló belső hiba miatt nem regisztrálható. Tekintse meg a hiba a feladatok nézetben további részleteket a Site Recovery portálon. Futtassa a telepítőt a kiszolgáló regisztrálásához.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [A Hyper-V helyreállítás-kezelő tárolóba nem létesíthető kapcsolat. Ellenőrizze a proxybeállítások helyességét, vagy próbálkozzon újra később.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Konfiguráció
* [Nem hozható létre védelmi csoport: Hiba történt a kiszolgálók listájának beolvasása közben.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Hyper-V gazdagépfürt legalább egy statikus hálózati adaptert tartalmaz, vagy nem csatlakoztatott adapterek DHCP használatára vannak konfigurálva.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [A Virtual Machine Manager nincs engedélye a művelet befejezéséhez.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [A tárfiók védelmének konfigurálása közben az előfizetésen belüli nem választható ki.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Védelem
* <span style="color:green;">ÚJ</span> [hiba miatt sikertelenül működő "Védelmi nem sikerült konfigurálni a virtuális gép" védelem engedélyezése. Hiba 60007, 40003.](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">ÚJ</span> [Védelemengedélyezési hiba miatt sikertelenül működő "Védelmi nem sikerült engedélyezni a virtuális gépet." Hiba történt a 70094.](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">ÚJ</span> [lesz élő áttelepítési hiba 23848 – a virtuális gép élő típus használatával lesz áthelyezve. Ez érvénytelenítheti a virtuális gép helyreállítási védelmi állapotát.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [Sikertelen volt, mert az ügynök nincs telepítve, a gazdagépen védelmének engedélyezése.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [Nem található megfelelő gazdagép a replika virtuális gép - miatt alacsony számítási erőforrásokat.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [A megfelelő gazdagép a replika virtuális gép nem található – nincs logikai hálózathoz csatlakoztatott miatt.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [Nem lehet kapcsolódni a replika gazdaszámítógépen - nem létesíthető kapcsolat.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Helyreállítás
* A Virtual Machine Manager nem tudja végrehajtani a gazdagépműveletet:
  * [Feladatok átadása a virtuális gép a kiválasztott helyreállítási pont: Általános hozzáférés megtagadva hiba.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Hyper-V feladatok átadása a virtuális gép a kiválasztott helyreállítási pont nem tudta: a művelet megszakítva.  Próbálja meg egy újabb helyreállítási pontot. (0x80004004).](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * Egy kapcsolat a kiszolgálóval létesített (0x00002EFD) nem lehet.
    * [Hyper-V nem sikerült engedélyezni a fordított replikáció indítása virtuális géphez.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Hyper-V virtuális gép virtuális gépek replikálásának engedélyezése sikertelen volt.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [Virtuális gép nem véglegesíthető.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [A helyreállítási terv tartalmaz a virtuális gépek, amelyek nem tervezett feladatátvételre alkalmas állapotban.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [A virtuális gép nem tervezett feladatátvételre alkalmas állapotban.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [Virtuális gép nem fut, és ki nem üzemkész.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [Sávon kívüli művelet történt egy virtuális gép és a feladatátvétel véglegesítését nem sikerült.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* A feladatátvételi teszt
  * [Nem lehet kezdeményezni a feladatátvételt, mert folyamatban van a feladatátvételi teszthez.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">ÚJ</span> feladatátvételi időtúllépése "PreFailoverWorkflow feladat WaitForScriptExecutionTaskTimeout" miatt a virtuális gép vagy az alhálózatot, amelybe a gép tartozik társított hálózati biztonsági csoport konfigurációs beállításai. Tekintse meg ["PreFailoverWorkflow feladat WaitForScriptExecutionTaskTimeout"](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) részleteiről.

### <a name="configuration-server-process-server-master-target"></a>Konfigurációs kiszolgáló, a folyamatkiszolgáló, a fő célkiszolgáló
* [Az ESXi-állomáson, amelyen a PS/CS üzemeltetett, mivel a virtuális gépek meghiúsul, és a lila képernyő.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Hibaelhárítás a feladatátvételt követően távoli asztal
* Sok ügyfél rendelkezik tapasztalt problémák csatlakozni a feladatokat átadó virtuális gép az Azure-ban. [Használja a virtuális gép az RDP a hibaelhárítási dokumentum](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Egy nyilvános IP-cím hozzáadása a resource manager virtuális gépen
Ha a **Connect** a portálon gomb szürkén jelenik meg, és az Express Route vagy helyek VPN-kapcsolaton keresztül nem csatlakozik Azure, újra kell létrehozni és hozzárendelni a nyilvános IP-címnek a virtuális gép távoli asztal/megosztott rendszerhéj használata előtt. Ezután felvehet egy nyilvános IP-cím a virtuális gép hálózati adapterén.  

![Egy nyilvános IP-cím hozzáadása a hálózati adapteren a virtuális gép feladatátvételt](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
