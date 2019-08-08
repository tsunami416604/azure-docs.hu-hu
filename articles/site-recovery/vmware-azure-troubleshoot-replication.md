---
title: A VMware virtuális gépeknek és fizikai kiszolgálóknak az Azure-ba való vész-helyreállításával kapcsolatos replikációs problémák elhárítása Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő vész-helyreállításával kapcsolatos gyakori replikációs problémákra vonatkozó hibaelhárítási információkat nyújt a Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 54686a96385532e17fe0ac6e59058b91b40c1342
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742568"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>A VMware virtuális gépek és a fizikai kiszolgálók replikációs problémáinak elhárítása

Ez a cikk a helyszíni VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő replikálásával kapcsolatos gyakori problémákat és bizonyos hibákat ismerteti, amelyek a [site Recovery](site-recovery-overview.md)használatával történnek.

## <a name="step-1-monitor-process-server-health"></a>1\. lépés: A folyamat kiszolgáló állapotának figyelése

Site Recovery a [folyamat-kiszolgáló](vmware-physical-azure-config-process-server-overview.md#process-server) segítségével fogadja és optimalizálja a replikált fájlokat, és elküldi azt az Azure-nak.

Azt javasoljuk, hogy a portálon figyelje a folyamat-kiszolgálók állapotát, hogy biztosítsa a csatlakozást és a megfelelő működést, és hogy a replikálás a folyamat-kiszolgálóhoz társított forrásoldali gépek esetében is folyamatban van.

- [További információ a](vmware-physical-azure-monitor-process-server.md) folyamat-kiszolgálók figyeléséről.
- [Az ajánlott eljárások áttekintése](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- A folyamat-kiszolgáló állapotának [hibáinak megoldása](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) .

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2\. lépés: Kapcsolódási és replikálási problémák elhárítása

A kezdeti és a folyamatos replikálási hibákat gyakran a forráskiszolgáló és a Process Server, illetve a Process Server és az Azure közötti csatlakozási problémák okozzák. 

A problémák megoldásához a [kapcsolat és a replikálás hibaelhárítása](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3\. lépés: A replikáláshoz nem elérhető forrásoldali gépekkel kapcsolatos hibák megoldása

Ha a Site Recovery használatával szeretné engedélyezni a replikálást, a gép a következő okok egyike miatt nem érhető el:

* **Két virtuális gép ugyanazzal a példány UUID-val**: Ha a vCenter két virtuális gépnek ugyanaz a példány UUID azonosítója, a konfigurációs kiszolgáló által felderített első virtuális gép megjelenik a Azure Portal. A probléma megoldásához győződjön meg arról, hogy egyetlen példány UUID-je sincs két virtuális gépen. Ez a forgatókönyv általában olyan példányokban látható, ahol a biztonsági mentési virtuális gép aktívvá válik, és bekerül a felderítési rekordba. Tekintse [meg a VMware – Azure Azure site Recovery: Ismétlődő vagy elavult bejegyzések](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) törlése a megoldáshoz.
* **Helytelen vCenter felhasználói hitelesítő adatok**: Győződjön meg arról, hogy a megfelelő vCenter hitelesítő adatokat adta hozzá a konfigurációs kiszolgáló a OVF sablon vagy az egyesített telepítő használatával történő beállításakor. A telepítés során hozzáadott hitelesítő adatok ellenőrzéséhez tekintse meg az [automatikus felderítés hitelesítő adatainak módosítása](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)című témakört.
* a **vCenter nem megfelelő jogosultságokkal rendelkezik**: Ha a vCenter való hozzáféréshez megadott engedélyek nem rendelkeznek a szükséges engedélyekkel, előfordulhat, hogy a virtuális gépek felderítése sikertelen. Győződjön meg arról, hogy a [fiók előkészítése automatikus felderítéshez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) című témakörben leírt engedélyek hozzáadódnak a vCenter felhasználói fiókhoz.
* **Azure site Recovery felügyeleti kiszolgálók**: Ha a virtuális gép felügyeleti kiszolgálóként van használatban a következő szerepkörök közül egyet vagy többet: konfigurációs kiszolgáló/scale-out Process Server/fő célkiszolgáló, nem fogja tudni kiválasztani a virtuális gépet a portálról. A felügyeleti kiszolgálók nem replikálhatók.
* **Már védett/átadott feladatátvétel Azure site Recovery szolgáltatásokon keresztül**: Ha a virtuális gép már védett, vagy Site Recovery keresztül átadja a feladatátvételt, a virtuális gép nem érhető el védelemre a portálon. Győződjön meg arról, hogy a portálon keresett virtuális gépet más felhasználók vagy más előfizetések már nem védik.
* a **vCenter nincs csatlakoztatva**: Ellenőrizze, hogy a vCenter csatlakoztatott állapotban van-e. Az ellenőrzéshez nyissa meg a Recovery Services-tárolót > Site Recovery infrastruktúra > konfigurációs kiszolgálók > kattintson a megfelelő konfigurációs kiszolgálóra, > egy panel megnyílik a jobb oldalon a társított kiszolgálók részleteivel. Ellenőrizze, hogy a vCenter csatlakoztatva van-e. Ha "nincs csatlakoztatva" állapotú, oldja meg a problémát, majd frissítse a [konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép fel lesz sorolva a portálon.
* **ESXi**kikapcsolva: Ha az ESXi-gazdagép, amelyben a virtuális gép található, kikapcsolt állapotban van, a virtuális gép nem jelenik meg, vagy nem lesz kiválasztható a Azure Portal. Kapcsolja be az ESXi-gazdagépet, [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép fel lesz sorolva a portálon.
* **Újraindítás függőben**: Ha van függőben lévő újraindítás a virtuális gépen, akkor nem fogja tudni kiválasztani a gépet Azure Portalon. Győződjön meg arról, hogy elvégezte a függőben lévő újraindítási tevékenységeket, [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép fel lesz sorolva a portálon.
* Az **IP-cím nem található**: Ha a virtuális gépen nincs érvényes IP-cím társítva, akkor nem fogja tudni kiválasztani a gépet Azure Portal. Győződjön meg arról, hogy érvényes IP-címet rendel a virtuális géphez, és [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Ezt követően a virtuális gép fel lesz sorolva a portálon.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>A védett virtuális gépek hibáinak megoldása a portálon

A Site Recovery alatt replikált virtuális gépek nem érhetők el a Azure Portalban, ha ismétlődő bejegyzések vannak a rendszerben. Az elavult bejegyzések törlésének és a probléma megoldásának megismeréséhez tekintse [meg a következőt: Azure site Recovery VMware – Azure: Ismétlődő vagy elavult bejegyzések](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)törlése.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

### <a name="initial-replication-issues-error-78169"></a>Kezdeti replikálási problémák [78169-es hiba]

A fentiek alapján biztosíthatja, hogy a kapcsolat, a sávszélesség vagy az idő szinkronizálásával kapcsolatos problémák ne legyenek elérhetők, ügyeljen a következőkre:

- A víruskereső szoftver nem blokkolja a Azure Site Recovery. [További](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) információ a Azure site Recoveryhoz szükséges mappák kizárásáról.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Hiányzó alkalmazás-konzisztens helyreállítási pontok [Error 78144]

 Ez a Kötet árnyékmásolata szolgáltatás (VSS) problémái miatt fordul elő. A probléma megoldása: 
 
- Ellenőrizze, hogy a Azure Site Recovery ügynök telepített verziója legalább 9.22.2-e. 
- Győződjön meg arról, hogy a VSS-szolgáltató szolgáltatásként van telepítve a Windows-szolgáltatásokban, valamint ellenőrizze a Komponensszolgáltatások MMC-t, hogy ellenőrizze, Azure Site Recovery VSS-szolgáltató szerepel-e a listáján.
- Ha a VSS-szolgáltató nincs telepítve, olvassa el a [telepítési hibák elhárítása című cikket](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus**értékre van-e állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

- Ha SQL-vagy Exchange-munkaterheléseket futtat, ellenőrizze az alkalmazás-írók naplóit a hibákért. Gyakori hibák és a megoldásuk rögzítése a következő cikkekben történik:
    -  [SQL Server adatbázis automatikus lezárási beállítása TRUE (igaz)](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 nem újrapróbálkozást lehetővé tevő hibát dobott](https://support.microsoft.com/help/4504103)
    - [Ismert probléma a SQL Server 2016-es és 2017-es verziójában](https://support.microsoft.com/help/4493364)
    - [Gyakori probléma az Exchange-kiszolgálókkal 2013 és 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Nagy forgalmú forrásoldali gépek [Error 78188]

Lehetséges okok:
- A virtuális gép listázott lemezén lévő adatváltozási sebesség (bájt/s) nagyobb, mint a replikációs cél Storage-fióktípus [Azure site Recovery támogatott korlátai](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) .
- Az elváltozási arány hirtelen megszeghető, mivel a feltöltésre váró nagy mennyiségű adatok.

A probléma megoldásához:
- Győződjön meg arról, hogy a célként megadott Storage-fiók típusa (standard vagy prémium) a forrásnál az adatforgalom arányára vonatkozó követelményként van kiépítve.
- Ha már rendelkezik egy prémium szintű felügyelt lemezre (asrseeddisk-típus), győződjön meg arról, hogy a lemez mérete támogatja a megfigyelt adatforgalom mértékét Site Recovery korlátként. Szükség esetén növelheti a asrseeddisk méretét. Kövesse az alábbi lépéseket:
    - Navigáljon az érintett replikált gép lemezek paneljére, és másolja a replika lemez nevét
    - Navigáljon a replika felügyelt lemezéhez
    - Előfordulhat, hogy megjelenik egy szalagcím az Áttekintés panelen, amely azt jelzi, hogy egy SAS URL-cím lett létrehozva. Kattintson erre a szalagcímre, és szakítsa meg az exportálást. Ha nem látja a szalagcímet, hagyja figyelmen kívül ezt a lépést.
    - Amint visszavonják a SAS URL-címét, lépjen a felügyelt lemez konfiguráció paneljére, és növelje a méretet, hogy az ASR támogassa a forrás lemezen megfigyelt adatforgalom mértékét.
- Ha a megfigyelt forgalom ideiglenes, várjon néhány órát, amíg a függőben lévő adatok feltöltése megtörténik, és létre kell hozni a helyreállítási pontokat.
- Ha a lemez olyan nem kritikus fontosságú adatokhoz is tartalmaz, mint például az ideiglenes naplók, az adatok tesztelése stb., érdemes lehet máshová áthelyezni ezeket az adatfájlokat, vagy teljes mértékben kizárni ezt a lemezt
- Ha a probléma továbbra is fennáll, az Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview) segítségével tervezze meg a replikálást.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>A szívverés nélküli forrásoldali gépek [Error 78174]

Ez akkor fordul elő, ha a forrásszámítógépen Azure Site Recovery mobilitási ügynök nem kommunikál a konfigurációs kiszolgálóval (CS).

A probléma megoldásához kövesse az alábbi lépéseket a forrás virtuális gépről a konfigurációs kiszolgálóra való hálózati kapcsolat ellenőrzéséhez:

1. Ellenőrizze, hogy a forrásszámítógép fut-e.
2. Jelentkezzen be a forrásoldali gépre olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
3. Ellenőrizze, hogy a következő szolgáltatások futnak-e, és ha nem indítja újra a szolgáltatásokat:
   - Svagents (inmage Scout VX-ügynök)
   - Inmage Scout Application szolgáltatás
4. A forrásoldali gépen vizsgálja meg a naplófájlokat a következő helyen:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Kiszolgáló feldolgozása szívverés nélkül [806-es hiba]
Ha a Process Server (PS) nem rendelkezik szívveréssel, akkor a következőt kell ellenőriznie:
1. A PS virtuális gép működése folyamatban van
2. A hiba részleteiért olvassa el a következő naplókat a PS-ben:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Fő célkiszolgáló szívverés nélkül [hiba 78022]

Ez akkor fordul elő, ha Azure Site Recovery mobilitási ügynök a fő célhelyen nem kommunikál a konfigurációs kiszolgálóval.

A probléma megoldásához kövesse az alábbi lépéseket a szolgáltatás állapotának ellenőrzéséhez:

1. Ellenőrizze, hogy a fő célként megadott virtuális gép fut-e.
2. Jelentkezzen be a fő célként megadott virtuális gépre olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
    - Ellenőrizze, hogy a svagents szolgáltatás fut-e. Ha fut, indítsa újra a szolgáltatást.
    - A hiba részleteinek megtekintéséhez keresse meg a naplófájlokat a következő helyen:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>78144-es AZONOSÍTÓJÚ hiba – nem érhető el alkalmazás-konzisztens helyreállítási pont a virtuális géphez az utolsó "XXX" percben

A leggyakoribb problémák némelyike alább látható

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1\. ok: Ismert probléma az SQL Server 2008/2008 R2-ben 
**A javítás módja** : Ismert probléma van az SQL Server 2008/2008 R2-vel. Tekintse meg ezt a TUDÁSBÁZISCIKK [Azure site Recovery Agent vagy más, nem összetevővel rendelkező VSS biztonsági mentést, amely a SQL Server 2008 R2-t futtató kiszolgálókon meghiúsul.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2\. ok: Azure Site Recovery feladatok meghiúsulnak a SQL Server példányok bármely verzióját üzemeltető kiszolgálókon a AUTO_CLOSE-adatbázisok segítségével 
**A javítás módja** : Tudásbáziscikk [](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3\. ok: Ismert probléma a SQL Server 2016-es és 2017-es verziójában
**A javítás módja** : Tudásbáziscikk [](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>További okok a VSS-vel kapcsolatos problémák miatt:

A további hibaelhárításhoz tekintse meg a forrás gépen található fájlokat a hiba pontos hibakódjának lekéréséhez:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hogyan lehet megtalálni a hibákat a fájlban?
Keresse meg a "vacpError" karakterláncot a vacp. log fájl egy szerkesztőben való megnyitásával
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

A fenti példában a **2147754994** a hibakód, amely az alább látható hibával kapcsolatos információkat mutatja

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>A VSS-író nincs telepítve – 2147221164-es hiba 

*A javítás módja*: Az alkalmazás konzisztencia-címkéjének létrehozásához Azure Site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja. Telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha a VSS-szolgáltató szolgáltatás nincs telepítve, az alkalmazás konzisztencia-pillanatképének létrehozása sikertelen lesz, mert a 0x80040154 "osztály nincs regisztrálva" érték jelenik meg. </br>
[A VSS-író telepítésével kapcsolatos hibaelhárításról szóló cikkben](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) olvashat 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>A VSS-író le van tiltva – 2147943458-es hiba

**A javítás módja**: Az alkalmazás konzisztencia-címkéjének létrehozásához Azure Site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja. Telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha a VSS-szolgáltató szolgáltatás le van tiltva, az alkalmazás konzisztencia-pillanatképének létrehozása meghiúsul a következő hibakóddal: "a megadott szolgáltatás le van tiltva, és nem indítható el (0x80070422)". </br>

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus**értékre van-e állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-szolgáltató NOT_REGISTERED – 2147754756-es hiba

**A javítás módja**: Az alkalmazás konzisztencia-címkéjének létrehozásához Azure Site Recovery a Microsoft Kötet árnyékmásolata szolgáltatást (VSS) használja. Ellenőrizze, hogy telepítve van-e a Azure Site Recovery VSS-szolgáltató szolgáltatás. </br>

- Próbálja megismételni a szolgáltató telepítését a következő parancsok használatával:
- Meglévő szolgáltató eltávolítása: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Telepítse újra C:\Program Files (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Install.cmd
 
Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus**értékre van-e állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye fel kérdéseit a [Azure site Recovery fórumba](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Aktív közösségünk van, és az egyik mérnökeink segítenek Önnek.
