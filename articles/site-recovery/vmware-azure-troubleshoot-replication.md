---
title: VMware virtuális gépek és fizikai kiszolgálók Azure-bA vész-helyreállítási replikációs problémák hibaelhárítása az Azure Site Recovery használatával |} A Microsoft Docs
description: Ez a cikk során a VMware virtuális gépek vészhelyreállítása az általános replikációs problémákat hibaelhárítási információkat és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: ed04c21fc5f3aecb91483dbd1eb7ca5fbf47c3e9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805962"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációjával kapcsolatos problémák elhárítása

Ez a cikk ismerteti az egyes gyakori problémák és a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA replikálása során esetlegesen jelentkező hibaüzenetek [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>1\. lépés: A figyelő folyamat kiszolgáló állapota

Site Recovery használja a [folyamatkiszolgáló](vmware-physical-azure-config-process-server-overview.md#process-server) fogadásához és optimalizálhatja a replikált adatokat, és küldje el az Azure-bA.

Azt javasoljuk, hogy a portálon, győződjön meg arról, hogy kapcsolódnak a hálózathoz, és megfelelően működik, és, hogy a replikáció folyik a folyamat kiszolgálóhoz társított forrásgépek folyamatkiszolgálók állapotának monitorozásához.

- [Ismerje meg](vmware-physical-azure-monitor-process-server.md) folyamat kiszolgálók monitorozásához.
- [Az ajánlott eljárások áttekintése](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Hibaelhárítás](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) folyamatkiszolgáló állapota.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2\. lépés: Csatlakozási és replikációs problémák elhárítása

Kezdeti és folyamatos replikálási hibák gyakran okozza a forráskiszolgáló és a folyamatkiszolgáló között, vagy a folyamatkiszolgáló és az Azure közötti kapcsolódási problémák. 

A problémák megoldásához [kapcsolat és a replikáció hibaelhárítása](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3\. lépés: Forrásgépek, amelyek nem érhetők el a replikáció hibaelhárítása

Válassza ki a forrásoldali gép számára engedélyezze a replikációt a Site Recovery használatával megkísérlésekor a gép esetleg nem érhetők el a következő okok valamelyike:

* **Két virtuális gép egyező példány UUID**: Ha a vcenter-kiszolgáló mellett két virtuális gépet ugyanazon UUID azonosító, az első virtuális gépen észlelt a konfigurációs kiszolgáló által az Azure Portalon látható. A probléma megoldásához, biztosíthatja a nincs két virtuális gépet ugyanazon UUID azonosítója. Ebben a forgatókönyvben általában látható esetekben, ahol a virtuális gép biztonsági mentési aktívvá válik, és az adatfelderítési rekordok be legyen jelentkezve. Tekintse meg [az Azure Site Recovery VMware – Azure: Eltávolítás az ismétlődő vagy elavult bejegyzés](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) megoldásához.
* **Helytelen vCenter-felhasználó hitelesítő adatainak**: Győződjön meg arról, hogy hozzáadta a helyes vCenter hitelesítő adatok beállításakor a konfigurációs kiszolgáló OVF-sablon vagy egyesített telepítő használatával. A telepítés során hozzáadott hitelesítő adatok ellenőrzéséhez tekintse meg a [módosíthatják az automatikus felderítési hitelesítő adatait](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Nincs megfelelő jogosultsága vCenter**: Ha a vCenter eléréséhez megadott engedélyek nem rendelkezik a szükséges engedélyekkel, virtuális gépek felderítése sikertelen történhet meg. Győződjön meg arról, hogy az engedélyek ismertetett [fiók előkészítése automatikus felderítéshez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) hozzáadódnak a vCenter-felhasználói fiókot.
* **Az Azure Site Recovery felügyeleti kiszolgálók**: Ha a virtuális gép egy vagy több, a következő szerepkör - felügyeleti kiszolgáló használják a folyamatkiszolgáló konfigurációs kiszolgáló /scale-out / fő célkiszolgáló, nem fog tudni válassza ki a virtuális gép portálról. Erőforráskészletben nem lehet replikálni.
* **Védett/feladatátvétele már az Azure Site Recovery services használatával**: Ha a virtuális gép már védett, vagy átadta a feladatait a Site Recovery, a virtuális gép nem érhető el, válassza a védelemhez a portálon. Győződjön meg arról, hogy a virtuális gépet a portálon a keresett már nem védett, amelyet semmilyen más felhasználó vagy egy másik előfizetésben.
* **nem csatlakozik a vCenter**: Ellenőrizze, hogy ha vCenter csatlakoztatott állapotban van-e. Győződjön meg arról, lépjen a Recovery Services-tároló > Site Recovery-infrastruktúra > konfigurációs kiszolgálók > kattintson a megfelelő konfigurációs kiszolgáló > a társított kiszolgálók részleteit tartalmazó a jobb oldalon megnyílik egy panel. Ellenőrizze, hogy a vCenter csatlakoztatva van. "Nem csatlakoztatott" állapotban van, ha a probléma megoldásához, majd [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép jelenik meg a portálon.
* **ESXi ki van kapcsolva**: Ha ESXi-gazdagép, amely alatt a virtuális gép található a kikapcsolt állapotban, majd a virtuális gép nem lesznek felsorolva vagy nem lesz kiválasztható az Azure Portalon. Az ESXi-gazdagépen Power [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép jelenik meg a portálon.
* **Függőben lévő újraindítás**: Ha a virtuális gép újraindítása függőben van, majd nem értéket választhatja ki a gépet az Azure Portalon. Győződjön meg arról, a függőben lévő újraindítás tevékenységek végrehajtásához [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép jelenik meg a portálon.
* **Nem található IP**: Ha a virtuális gép nincs társítva érvényes IP-címet, akkor, nem fogja tudni válassza ki a gépet az Azure Portalon. Ellenőrizze, érvényes IP-cím hozzárendelése a virtuális gép [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép jelenik meg a portálon.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Hibaelhárítás a védett virtuális gépek szürkén jelenik meg a portálon

A Site Recovery replikált virtuális gépek nem érhetők el az Azure Portalon, ha a rendszer ismétlődő bejegyzéseket tartalmaz. Ismerje meg, hogyan törli az elavult bejegyzések, és hárítsa el a problémát, tekintse meg [Azure Site Recovery VMware – Azure: Eltávolítás az ismétlődő vagy elavult bejegyzés](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

### <a name="initial-replication-issues-error-78169"></a>[Hiba 78169] kezdeti replikációs problémákat

Keresztül a fenti biztosítása, ha nincs kapcsolat, a sávszélességet vagy időt szinkronizálása a kapcsolódó problémákról, ügyeljen arra, hogy:

- Nincs a víruskereső szoftver nem blokkolja az Azure Site Recovery. Ismerje meg, [további](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) a mappák kizárásának, az Azure Site Recoveryhez szükséges.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Hiányzó alkalmazáskonzisztens helyreállítási pontok [78144 hiba]

 Ez akkor történik, kötet-árnyékmásolata szolgáltatás (VSS) kapcsolatos problémák miatt. A probléma megoldása: 
 
- Ellenőrizze, hogy az Azure Site Recovery agent telepített verziója legalább 9.22.2. 
- Győződjön meg arról, hogy VSS-szolgáltató telepítve van-e a Windows-szolgáltatások szolgáltatásként, és ellenőrizze azt is ellenőrizze, hogy szerepel-e az Azure Site Recovery VSS Provider összetevő szolgáltatás MMC.
- Ha a VSS-szolgáltató nincs telepítve, tekintse meg a [telepítési hibák elhárításáról szóló cikkben](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusát **automatikus**.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS-szolgáltatás
        - Az Azure Site Recovery VSS Provider
        - A VDS szolgáltatás

- Ha Exchange vagy az SQL-számítási feladatok futnak, tekintse meg a naplókat, az ezen alkalmazás írók hibák. Gyakori hibák és azok megoldási rögzítve lesznek a következő cikkeket:
    -  [Az SQL Server-adatbázis automatikus beállítás igaz értékre van beállítva.](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 értesítő – Újrapróbálkozást lehetővé nem tevő – hiba](https://support.microsoft.com/help/4504103)
    - [Ismert probléma az SQL Server 2016 és 2017.](https://support.microsoft.com/help/4493364)
    - [Gyakori hiba az Exchange-kiszolgálók 2013 és 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Magas adatváltozású [78188 hiba] a forrásgépek

Lehetséges okok:
- Az adatmódosítás arány (zapsané bajty/s) a virtuális gép a felsorolt lemezeken van több, mint a [Azure Site Recovery támogatott határértékeket](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) a replikációs cél tárfiók típusához.
- Van egy hirtelen megugrás a lemorzsolódási rátához mely adatok nagy mennyisége miatt folyamatban a feltöltéshez.

A probléma megoldásához:
- Győződjön meg arról, hogy a célként megadott tárfiók típusa (Standard vagy prémium) a lemorzsolódási ráta követelmény forrásnál megfelelően van-e kiépítve.
- Ha a megfigyelt forgalommal ideiglenes, várjon néhány óra alatt a függőben lévő adatok feltöltése olvasásra, és hozzon létre helyreállítási pontokat.
- Ha a probléma továbbra is fennáll, használja a Site Recovery [üzembehelyezés](site-recovery-deployment-planner.md#overview) replikációjának megtervezéséhez segítségével.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Nem érkezett szívverés [78174 hiba] a forrásgépek

Ez akkor történik, ha a forrásoldali virtuális gépen az Azure Site Recovery mobilitási ügynök nem kommunikál az a konfigurációs kiszolgálót (CS).

A probléma megoldása érdekében a következő lépések segítségével ellenőrizze a hálózati kapcsolat a forrás virtuális gép és a konfigurációs kiszolgáló:

1. Ellenőrizze, hogy fut-e a Forrásgépen.
2. Jelentkezzen be egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a Forrásgépen.
3. Ellenőrizze, hogy a következő szolgáltatások futnak-e, és ha nem indítja újra a szolgáltatásokat:
   - Svagents (InMage Scout VX-ügynök)
   - Az InMage Scout alkalmazásszolgáltatás
4. A forrásgépen vizsgálja meg a naplókat a helyen, a hiba részletei:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Nem érkezett szívverés [806 hiba] a folyamatkiszolgáló
Abban az esetben nem érkezett szívverés az a folyamat kiszolgáló (PS) van, ellenőrizze a következőket:
1. PS Virtuális gép működik-e
2. Ellenőrizze a következő bejelentkezésekor a PS a hiba részletei:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Nem érkezett szívverés [78022 hiba] a fő célkiszolgáló

Ez akkor történik, ha az Azure Site Recovery mobilitási ügynököt a fő célkiszolgáló nem kommunikál a konfigurációs kiszolgáló.

A probléma megoldásához használja a szolgáltatás állapotának ellenőrzése a következő lépéseket:

1. Győződjön meg arról, hogy a fő cél virtuális gép fut-e.
2. Jelentkezzen be a fő cél virtuális Gépet egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
    - Győződjön meg arról, hogy a svagents szolgáltatás fut-e. Ha fut, indítsa újra a szolgáltatást
    - Ellenőrizze a naplókat, a helyen, a hiba részletei:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Hiba azonosítója 78144 - nem az elmúlt percben: XXX"a virtuális Géphez elérhető alkalmazáskonzisztens helyreállítási pont

Alább láthatók a leggyakoribb problémák

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1\. ok: Ismert hiba az SQL server 2008 és 2008 R2 
**Hogyan háríthatja el a** : Egy ismert probléma van az SQL server 2008 és 2008 R2. Olvassa el az ebben a Tudásbáziscikkben találja [Azure Site Recovery-ügynök vagy egyéb nem összetevő VSS biztonsági mentése meghiúsul, az SQL Server 2008 R2 üzemeltető kiszolgálót](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>2\. ok: Az Azure Site Recovery-feladatok sikertelenek AUTO_CLOSE adatbázisainak az SQL Server-példányokat bármely verzióját futtató kiszolgálókon 
**Hogyan háríthatja el a** : Tekintse meg a KB-os [cikk](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3\. ok: Ismert probléma az SQL Server 2016 és 2017.
**Hogyan háríthatja el a** : Tekintse meg a KB-os [cikk](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>További okok miatt VSS kapcsolatos problémákat:

További hibaelhárítást, tekintse meg a fájlokat a forrásgépen a pontos hibakódot lekérése sikertelen:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hogyan lehet a fájlban keresse meg a hibákat?
Vacp.log fájl egy szövegszerkesztőben való megnyitásával "vacpError" karakterlánc keresése
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

A fenti példában **2147754994** , mely szerint a hibával kapcsolatos, ahogy az alábbi hiba kódja

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Nincs telepítve a VSS-író - hiba 2147221164 

*Hogyan háríthatja el a*: Konzisztenciacímke application készítése, az Azure Site Recovery Microsoft Kötet árnyékmásolata szolgáltatás (VSS) használja. Az alkalmazás konzisztenciájának pillanatképek ehhez a művelethez VSS-szolgáltatót telepíti. A VSS-szolgáltató szolgáltatás telepítve van. Abban az esetben a VSS-szolgáltató szolgáltatás nincs telepítve, a konzisztencia pillanatkép létrehozásának nem sikerül a hiba azonosítója: 0x80040154 "Osztály nem regisztrált". </br>
Tekintse meg [VSS-író telepítési hibaelhárítási cikk](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-író le van tiltva – hiba 2147943458

**Hogyan háríthatja el a**: Konzisztenciacímke application készítése, az Azure Site Recovery Microsoft Kötet árnyékmásolata szolgáltatás (VSS) használja. Az alkalmazás konzisztenciájának pillanatképek ehhez a művelethez VSS-szolgáltatót telepíti. A VSS-szolgáltató szolgáltatás telepítve van. Abban az esetben a VSS-szolgáltató szolgáltatás le van tiltva, az alkalmazás konzisztenciájának pillanatkép létrehozása sikertelen lesz a hiba azonosítója "a megadott szolgáltatás le van tiltva, és nem lehet started(0x80070422)". </br>

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusát **automatikus**.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS-szolgáltatás
        - Az Azure Site Recovery VSS Provider
        - A VDS szolgáltatás

####  <a name="vss-provider-notregistered---error-2147754756"></a>VSS-SZOLGÁLTATÓ NOT_REGISTERED - 2147754756 hiba

**Hogyan háríthatja el a**: Konzisztenciacímke application készítése, az Azure Site Recovery Microsoft Kötet árnyékmásolata szolgáltatás (VSS) használja. Ellenőrizze, hogy ha az Azure Site Recovery VSS Provider szolgáltatás telepítve van-e, vagy sem. </br>

- Ismételje meg a szolgáltató telepítőprogramját a következő parancsokkal:
- Meglévő-szolgáltató eltávolítása: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Újra kell telepítenie: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusát **automatikus**.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS-szolgáltatás
        - Az Azure Site Recovery VSS Provider
        - A VDS szolgáltatás

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye közzé a kérdését a [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív Közösség van, és egyet a mérnökeinknek a segítségére lehessen.
