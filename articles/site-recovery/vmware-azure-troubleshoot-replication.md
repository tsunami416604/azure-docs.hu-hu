---
title: A VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő vészhelyreállítása replikációs problémáinak elhárítása az Azure Site Recovery használatával | Microsoft dokumentumok
description: Ez a cikk hibaelhárítási információkat tartalmaz a vmware virtuális gépek és a fizikai kiszolgálók vész-helyreállítási során az Azure-ba az Azure Site Recovery használatával kapcsolatos gyakori replikációs problémák hibaelhárítási információit.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: f91ee5654b4add37d3cce4f875be1f9c2b398ab9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259493"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációs problémáinak elhárítása

Ez a cikk néhány gyakori problémát és konkrét hibát ismertet, amelyek a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba replikálásakor a Site Recovery használatával [találkozhatnak.](site-recovery-overview.md)

## <a name="step-1-monitor-process-server-health"></a>1. lépés: A folyamatkiszolgáló állapotának figyelése

A Site Recovery a [folyamatkiszolgáló segítségével](vmware-physical-azure-config-process-server-overview.md#process-server) fogadja és optimalizálja a replikált adatokat, és elküldi azokat az Azure-ba.

Azt javasoljuk, hogy figyelje a folyamatkiszolgálók állapotát a portálon, hogy megbizonyosodjon arról, hogy azok csatlakoztatva vannak és megfelelően működnek, és hogy a folyamatkiszolgálóhoz társított forrásgépek replikációja folyamatban van.

- További információ a folyamatkiszolgálók [figyeléséről.](vmware-physical-azure-monitor-process-server.md)
- [Az ajánlott eljárások áttekintése](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- Folyamatkiszolgáló állapotának [elhárítása.](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2. lépés: Kapcsolódási és replikációs problémák elhárítása

A kezdeti és a folyamatban lévő replikációs hibákat gyakran a forráskiszolgáló és a folyamatkiszolgáló, illetve a folyamatkiszolgáló és az Azure közötti kapcsolódási problémák okozzák.

A problémák megoldásához [hárítsa el a kapcsolódási és replikációs hibákat.](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3. lépés: A replikációra nem elérhető forrásgépek hibaelhárítása

Amikor a Site Recovery használatával próbálja meg kiválasztani a forrásgépet a replikáció engedélyezéséhez, előfordulhat, hogy a számítógép a következő okok valamelyike miatt nem érhető el:

* **Két virtuális gép ugyanazzal a példánysal UUID:** Ha két virtuális gép a vCenter ugyanazt a példányt UUID, a konfigurációs kiszolgáló által felderített első virtuális gép jelenik meg az Azure Portalon. A probléma megoldásához győződjön meg arról, hogy nincs két virtuális gép ugyanazt a példányt UUID. Ez a forgatókönyv gyakran látható olyan esetekben, amikor egy biztonsági mentési virtuális gép aktívvá válik, és be van jelentkezve a felderítési rekordok. Tekintse meg az [Azure Site Recovery VMware-Azure: Hogyan lehet megtisztítani a duplikált vagy elavult bejegyzéseket](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) a megoldáshoz.
* **Helytelen vCenter felhasználói hitelesítő adatok:** Győződjön meg arról, hogy a megfelelő vCenter hitelesítő adatokat adta hozzá, amikor az OVF sablon vagy az egyesített beállítás használatával állítja be a konfigurációs kiszolgálót. A telepítés során hozzáadott hitelesítő adatok ellenőrzéséről az [Automatikus felderítés hitelesítő adatainak módosítása](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)című témakörben látható.
* **vCenter elégtelen jogosultságok:** Ha a vCenter eléréséhez megadott engedélyek nem rendelkeznek a szükséges engedélyekkel, előfordulhat, hogy nem sikerül felderíteni a virtuális gépeket. Győződjön meg arról, hogy a [fiók előkészítése automatikus felderítésre](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) című részben leírt engedélyek hozzá adódnak a vCenter felhasználói fiókjához.
* **Azure Site Recovery felügyeleti kiszolgálók:** Ha a virtuális gép felügyeleti kiszolgálóként van használva a következő szerepkörök közül egy vagy több alatt - Konfigurációs kiszolgáló /kibővített folyamatkiszolgáló / Fő célkiszolgáló, akkor nem fogja tudni kiválasztani a virtuális gépet a portálról. A felügyeleti kiszolgálók nem replikálhatók.
* **Már védett/feladatátvételt az Azure Site Recovery szolgáltatásokon keresztül:** Ha a virtuális gép már védett vagy feladatátvételi a Site Recovery, a virtuális gép nem érhető el a portálon védelemre. Győződjön meg arról, hogy a portálon keresett virtuális gép már nem védett más felhasználó vagy egy másik előfizetés alatt.
* **vCenter nincs csatlakoztatva:** Ellenőrizze, hogy a vCenter csatlakoztatott állapotban van-e. Az ellenőrzéshez nyissa meg a Helyreállítási szolgáltatások tárolóját > site recovery infrastruktúra > konfigurációs kiszolgálók > Kattintson a megfelelő konfigurációs kiszolgálóra, > a jobb oldalon megnyílik egy panel a kapcsolódó kiszolgálók adataival. Ellenőrizze, hogy a vCenter csatlakoztatva van-e. Ha "Nincs csatlakoztatva" állapotban van, oldja meg a problémát, majd [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép megjelenik a portálon.
* **ESXi kikapcsolt:** Ha ESXi host, amely alatt a virtuális gép található, ki van kapcsolva, majd a virtuális gép nem lesz felsorolva, vagy nem lesz kiválasztható az Azure Portalon. Kapcsolja be az ESXi állomást, [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server) a portálon. Ezt követően a virtuális gép megjelenik a portálon.
* **Újraindítás függőben:** Ha van egy függőben lévő újraindítás a virtuális gépen, majd nem fogja tudni kiválasztani a gépet az Azure Portalon. Győződjön meg arról, hogy a függőben lévő újraindítási tevékenységek et szeretné [végrehajtani, frissítse a konfigurációs kiszolgálót.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) Ezt követően a virtuális gép megjelenik a portálon.
* **IP-cím nem található:** Ha a virtuális gép nem rendelkezik érvényes IP-címmel társított, majd nem fogja tudni kiválasztani a gépet az Azure Portalon. Győződjön meg arról, hogy érvényes IP-címet szeretne rendelni a virtuális géphez, [frissítse a konfigurációs kiszolgálót.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) Ezt követően a virtuális gép megjelenik a portálon.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>A portálon szürkere szürkelő védett virtuális gépek hibáinak elhárítása

A Site Recovery szolgáltatás alatt replikált virtuális gépek nem érhetők el az Azure Portalon, ha ismétlődő bejegyzések vannak a rendszerben. Az elavult bejegyzések törléséről és a probléma megoldásáról az [Azure Site Recovery VMware-azure: A duplikált vagy elavult bejegyzések törlése](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)című dokumentumból olvashat.

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nincs összeomlási konzisztens helyreállítási pont a virtuális gép számára az utolsó "XXX" percben

A leggyakoribb problémák közül néhány at az alábbiakban soroljuk fel

### <a name="initial-replication-issues-error-78169"></a>Kezdeti replikációs problémák [78169- es hiba]

A fenti, győződjön meg arról, hogy nincs kapcsolat, sávszélesség vagy idő szinkron kapcsolatos kérdések, győződjön meg arról, hogy:

- Nincs víruskereső szoftver blokkolja az Azure Site Recovery. [További](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) információ az Azure Site Recovery-hez szükséges mappakizárásokról.

### <a name="source-machines-with-high-churn-error-78188"></a>Nagy lemorzsolódású forrásgépek [hiba: 78188]

Lehetséges okok:
- Az adatváltozási sebesség (írási bájt/mp) a virtuális gép felsorolt lemezein több, mint az [Azure Site Recovery támogatott korlátok](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) a replikációs céltárfiók típusa.
- Van egy hirtelen tüske a lemorzsolódási arány, amely miatt nagy mennyiségű adat van függőben a feltöltéshez.

A probléma megoldása:
- Győződjön meg arról, hogy a céltárfiók típusa (Standard vagy Premium) a forrásnál a lemorzsolódási sebesség követelményének megfelelően van kiépítve.
- Ha már replikálja a prémium szintű felügyelt lemezre (asrseeddisk típus), győződjön meg arról, hogy a lemez mérete támogatja a megfigyelt lemorzsolódási sebesség, mint a site recovery korlátok. Szükség esetén növelheti az asrseeddisk méretét. Kövesse az alábbi lépéseket:
    - Keresse meg az érintett replikált gép Lemez paneljét, és másolja a replikalemez nevét
    - Keresse meg ezt a replikáter kezelt lemezt
    - Előfordulhat, hogy egy szalagcím jelenik meg az Áttekintés panelen, amely azt mondja, hogy egy SAS URL-cím lett létrehozva. Kattintson erre a bannerre, és szakítsa meg az exportálást. Hagyja figyelmen kívül ezt a lépést, ha nem látja a bannert.
    - Amint a SAS URL-cím visszavonásra kerül, lépjen a Felügyelt lemez Konfiguráció paneljére, és növelje a méretét, hogy az ASR támogassa a megfigyelt lemorzsolódási arányt a forráslemezen
- Ha a megfigyelt lemorzsolódás ideiglenes, várjon néhány órát, amíg a függőben lévő adatok feltöltése felzárkózni, és helyreállítási pontok létrehozása.
- Ha a lemez nem kritikus adatokat tartalmaz, például ideiglenes naplókat, tesztadatokat stb., fontolja meg az adatok áthelyezését máshol, vagy teljesen zárja ki a lemezt a replikációból
- Ha a probléma továbbra is fennáll, a Site Recovery [központi telepítési tervezőjében](site-recovery-deployment-planner.md#overview) megtervezheti a replikációt.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Szívverés nélküli forrásgépek [hiba: 78174]

Ez akkor fordul elő, ha az Azure Site Recovery Mobility ügynök a forrásgépen nem kommunikál a konfigurációs kiszolgálóval (CS).

A probléma megoldásához az alábbi lépésekkel ellenőrizze a hálózati kapcsolatot a forrásvirtuális gép és a Config Server között:

1. Ellenőrizze, hogy fut-e a forrásgép.
2. Jelentkezzen be a forrásgépre rendszergazdai jogosultságokkal rendelkező fiókkal.
3. Ellenőrizze, hogy futnak-e a következő szolgáltatások, és indítsa-e újra a szolgáltatásokat:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. A forrásgépen vizsgálja meg a helyszínen található naplókat a hiba részleteiért:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-with-no-heartbeat-error-806"></a>Szívverés nélküli folyamatkiszolgáló [806- os hiba]
Abban az esetben, ha a folyamatkiszolgáló (PS) nem hoz szívverést, ellenőrizze, hogy:
1. A PS Virtuális gép működik
2. Ellenőrizze a következő naplók a PS a hiba részleteit:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Fő célkiszolgáló szívverés nélkül [hiba: 78022]

Ez akkor fordul elő, ha az Azure Site Recovery Mobility ügynök a fő tárolón nem kommunikál a konfigurációs kiszolgálóval.

A probléma megoldásához az alábbi lépésekkel ellenőrizze a szolgáltatás állapotát:

1. Ellenőrizze, hogy a fő cél virtuális gép fut.Verify that the master target vm is running.
2. Jelentkezzen be a fő célvirtuális géprendszergazdai jogosultságokkal rendelkező fiókhasználatával.
    - Ellenőrizze, hogy fut-e a szvagensok szolgáltatása. Ha fut, indítsa újra a szolgáltatást
    - Ellenőrizze a naplókat a helyszínen a hiba részleteit:

          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. A fő cél konfigurációs kiszolgálóval történő regisztrálásához keresse meg a **%PROGRAMDATA%\ASR\Agent**mappát, és futtassa a következőt a parancssorban:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Hiba azonosító: 78144-es azonosító : Nincs elérhető alkalmazáskonzisztens helyreállítási pont a virtuális géphez az utolsó XXX percben

A [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27-es](site-recovery-whats-new.md#update-rollup-39) mobilitási ügynök verzióiban fejlesztések történtek a VSS telepítési hibák kezelésének kezelésére. Győződjön meg arról, hogy a legújabb verziót a legjobb útmutatást a VSS hibák elhárítása.

A leggyakoribb problémák közül néhány at az alábbiakban soroljuk fel

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1. ok: Ismert probléma az SQL Server 2008/2008 R2 rendszerben
**A javítás:** Ismert probléma van az SQL Server 2008/2008 R2 kiszolgálóval. Kérjük, olvassa el ezt a tudásbáziscikket, [az Azure Site Recovery Agent vagy más, nem összetevőből álló VSS biztonsági mentés sikertelen az SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) rendszert üzemeltető kiszolgálóesetében

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2. ok: Az Azure Site Recovery feladatok sikertelenek az SQL Server-példányok bármely verzióját tartalmazó kiszolgálókon, AUTO_CLOSE db-ekkel
**Javítás:** Olvassa el a Kb [cikket](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3. ok: Ismert probléma az SQL Server 2016-ban és 2017-ben
**Javítás:** Olvassa el a Kb [cikket](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)


### <a name="more-causes-due-to-vss-related-issues"></a>További okok miatt VSS kapcsolatos problémák:

A további hibaelhárításhoz ellenőrizze a forrásgépen található fájlokat, hogy pontoshiba-kódot kapjon:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hogyan lehet megtalálni a hibákat a fájlban?
Keresse meg a "vacpError" karakterláncot a vacp.log fájl szerkesztőben való megnyitásával

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

A fenti példában **a 2147754994-es** hibakód a hibakódról szól, ahogy az alább látható

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS író nincs telepítve - Hiba 2147221164

*A javítás:* Alkalmazáskonzisztenciacímke létrehozásához az Azure Site Recovery a Microsoft Volume Shadow copy Service (VSS) szolgáltatást használja. Telepíti a VSS-szolgáltató működését az alkalmazás konzisztencia-pillanatképek készítéséhez. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Abban az esetben, ha a VSS-szolgáltató szolgáltatás nincs telepítve, az alkalmazás konzisztencia pillanatkép létrehozása sikertelen lesz a hiba id 0x80040154 "Osztály nincs regisztrálva". </br>
Cikk [hivatkozása a VSS-írók telepítésével kapcsolatos hibaelhárításhoz](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS író le van tiltva - Hiba 2147943458

**A javítás:** Alkalmazáskonzisztenciacímke létrehozásához az Azure Site Recovery a Microsoft Volume Shadow copy Service (VSS) szolgáltatást használja. Telepíti a VSS-szolgáltató működését az alkalmazás konzisztencia-pillanatképek készítéséhez. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha a VSS-szolgáltató szolgáltatás le van tiltva, az alkalmazás konzisztencia-pillanatképlétrehozása sikertelen lesz a "A megadott szolgáltatás le van tiltva, és nem indítható el(0x80070422)". </br>

- Ha a VSS le van tiltva,
    - Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa Automatikus értékre **van-e**állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS SZOLGÁLTATÓ NOT_REGISTERED - Hiba 2147754756

**A javítás:** Alkalmazáskonzisztenciacímke létrehozásához az Azure Site Recovery a Microsoft Volume Shadow copy Service (VSS) szolgáltatást használja.
Ellenőrizze, hogy az Azure Site Recovery VSS-szolgáltató szolgáltatás telepítve van-e vagy sem. </br>

- Próbálkozzon újra a szolgáltató telepítésével a következő parancsokkal:
- Meglévő szolgáltató eltávolítása: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Újratelepítés: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa Automatikus értékre **van-e**állítva.
    - Indítsa újra a következő szolgáltatásokat:
        - VSS szolgáltatás
        - Azure Site Recovery VSS-szolgáltató
        - VDS szolgáltatás

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye fel kérdését az [Azure Site Recovery fórumon.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) Aktív közösségünk van, és az egyik mérnökünk tud önnek segíteni.
