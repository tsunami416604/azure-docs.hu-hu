---
title: Az Azure Site Recovery hibaelhárítás a folyamatban lévő Azure – Azure replikációs problémák |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibák és problémák elhárítása
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: bf24b2d1395e128dc73361670ea93ac938574146
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258789"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Az Azure-ról Azure-beli virtuális gép replikálása folyamatban lévő kapcsolatos problémák elhárítása

Ez a cikk ismerteti a gyakori problémák az Azure Site Recoveryben takarít meg replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba. Azt is bemutatja, hogyan háríthatók el őket. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

Az Azure Site Recovery folyamatosan replikálja az adatokat a forrás-régióból a vészhelyreállítási régióban, és 5 percenként összeomlás-konzisztens helyreállítási pont létrehozása. A Site Recovery 60 percig nem hozható létre helyreállítási pontokat, ha riasztást jelenít meg a következő információkkal:

Hibaüzenet jelenik meg: "Nincs összeomlási elérhető alkalmazáskonzisztens helyreállítási pont a virtuális gép az elmúlt 60 percben."</br>
Hiba azonosítója: 153007 </br>

A következő szakaszok ismertetik a okait és megoldásait.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>A forrás virtuális gépen nagy adatváltozási sebesség
Az Azure Site Recovery egy esemény küldése, ha a forrás virtuális gép adatváltozási sebessége meghaladja a támogatott határértékeket. Ellenőrizze, hogy a probléma nagy forgalom miatt van-e, lépjen a **replikált elemek** > **VM** > **események – elmúlt 72 órában**.
Az esemény "A módosult adatok aránya meghaladja a támogatott határértékeket" megjelennie:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha az eseményt, a pontos lemez adatainak kell megjelennie:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok tesztjeinken alapulnak, de nem fedhetik le az összes lehetséges alkalmazások i/o-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. 

Nincsenek, érdemes figyelembe venni két korlátok, a lemezenkénti adatváltozásról és adatváltozás virtuális gépenként. Ha például tekintsük át az alábbi táblázat a prémium szintű P20 lemez. A Site Recovery képes kezelni 5 MB/s lemez adatforgalom, amely legfeljebb öt ilyen lemezek virtuális gépenkénti, a virtuális gép összes adatváltozással 25 MB/s miatt.

**Replikáció tárolási célja** | **Forráslemez átlagos i/o-mérethez** |**Forráslemez átlagos adatváltozása** | **Forráslemez adatok napi teljes adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

### <a name="solution"></a>Megoldás
Az Azure Site Recovery korlátozások vonatkoznak az adatmódosítási arány, a lemez típusa alapján. Tudja meg, ha a probléma-e ismétlődő vagy pillanatnyi, keresse meg az érintett virtuális gép akkor változik, ha az adatokat. Nyissa meg a forrás virtuális gép, a metrikák alatt található **figyelés**, és adja hozzá a metrikák, ezen a képernyőfelvételen látható módon:

![Három lépéses folyamat lehet megkeresni az adatmódosítási arány](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Válassza ki **metrika hozzáadása**, és adja hozzá **operációs rendszer lemez írási bájt/mp** és **adatok lemezre írása bájt/mp**.
2. Figyelheti a megnövekedett, a képernyőképen látható módon.
3. A nézet az összes írási művelet, operációsrendszer-lemez és az összes adatlemezt együttesen történik. Ezek a metrikák előfordulhat, hogy nem körülményeiről a lemez szinten, de azt jelzik, hogy a teljes minta adatváltozások egyes típusai.

Ha ugrásszerű van egy burst alkalmanként adatokat és az adatok módosítsa a következőre arány értéke nagyobb, mint 10 MB/s (prémium szintű) és 2 MB/néhány (a standard szintű) s idő és előre, replikációs olvasásra lesznek. De ha a forgalom túlmutató a támogatott korlátozza a legtöbbször, lehetőség szerint fontolja meg az alábbi lehetőségek egyikét:

* **Egy magas szintű adatváltozások arány okozó lemezének kizárása**: A lemez használatával kizárhatják [PowerShell](./azure-to-azure-exclude-disks.md). A lemez kizárása kell először tiltsa le a replikációt. 
* **A vész-helyreállítási tárolólemez szintjének módosítása**: Ez a beállítás akkor lehetséges, csak ha a lemez adatváltozásának kevesebb mint 10 MB/s. Tegyük fel, egy P10 lemezt egy virtuális Gépet egy nagyobb, mint a 8 MB/s, de kevesebb mint 10 MB/s adatváltozása tapasztalja. Az ügyfél védelem során P30 lemez használható a céloldali tárolóra, ha a probléma megoldhatók.

## <a name="Network-connectivity-problem"></a>Hálózati kapcsolódási problémák

### <a name="network-latency-to-a-cache-storage-account"></a>Hálózati késés a gyorsítótárfiókba
A Site Recovery a replikált adatokat küld a gyorsítótár tárfiókjához. Előfordulhat, hogy látja, hálózati késés, ha az adatok a virtuális gépről a gyorsítótárfiókba feltöltését lassabb, mint 4 MB-os 3 másodpercen belül. 

Késéssel kapcsolatos probléma ellenőrzéséhez használja [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) feltölteni az adatokat a virtuális gépről a gyorsítótárfiókba. Ha nagy a késés, ellenőrizze, ha egy hálózati virtuális készüléket (nva-t) használ a virtuális gépek kimenő hálózati forgalom szabályozására. A berendezés előfordulhat, hogy leszabályozza, ha a replikációs forgalmat az nva-n keresztül halad. 

Azt javasoljuk, hogy hoz létre egy hálózati végpontot a virtuális hálózat "Tároló" úgy, hogy a replikálás forgalma nem nyissa meg az nva-t. További információkért lásd: [hálózati virtuális berendezés konfigurációjának](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Hálózati kapcsolat
A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike. Gondoskodjon arról, hogy az URL-címeket kapcsolódnak, lásd: [kimenő kapcsolatok esetében a Site Recovery URL-címeinek](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Hiba azonosítója 153006 - nem az elmúlt percben: XXX"a virtuális Géphez elérhető alkalmazáskonzisztens helyreállítási pont

Alább láthatók a leggyakoribb problémák

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1\. ok: Ismert hiba az SQL server 2008 és 2008 R2 
**Hogyan háríthatja el a** : Egy ismert probléma van az SQL server 2008 és 2008 R2. Olvassa el az ebben a Tudásbáziscikkben találja [Azure Site Recovery-ügynök vagy egyéb nem összetevő VSS biztonsági mentése meghiúsul, az SQL Server 2008 R2 üzemeltető kiszolgálót](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>2\. ok: Az Azure Site Recovery-feladatok sikertelenek AUTO_CLOSE adatbázisainak az SQL Server-példányokat bármely verzióját futtató kiszolgálókon 
**Hogyan háríthatja el a** : Tekintse meg a KB-os [cikk](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3\. ok: Ismert probléma az SQL Server 2016 és 2017.
**Hogyan háríthatja el a** : Tekintse meg a KB-os [cikk](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>4\. ok: Storage spaces közvetlen konfigurációt használ
**Hogyan háríthatja el a** : Az Azure Site Recovery nem hozható létre alkalmazáskonzisztens helyreállítási pont Storage spaces közvetlen konfiguráció. Tekintse meg a cikk megfelelően [a replikációs szabályzat konfigurálása](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

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
