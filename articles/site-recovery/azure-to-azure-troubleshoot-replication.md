---
title: Azure-beli virtuális gépek replikálásának hibája Azure Site Recovery
description: Az Azure-beli virtuális gép vész-helyreállítással való replikálásának elhárítása Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190809"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Az Azure-beli virtuális gép vész-helyreállítási replikációjának elhárítása

Ez a cikk az Azure-beli virtuális gépek egyik régióból egy másik régióba való replikálásával és helyreállításával kapcsolatos gyakori problémákat ismerteti Azure Site Recovery. Azt is ismerteti, hogyan lehet elhárítani a gyakori problémákat. További információ a támogatott konfigurációkról: Azure-beli [virtuális gépek replikálásának támogatási mátrixa](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery folyamatosan replikálja az adatait a forrás régióból a vész-helyreállítási régióba. Egy összeomlás-konzisztens helyreállítási pontot is létrehoz 5 percenként. Ha Site Recovery nem tud helyreállítási pontokat létrehozni 60 percre, akkor a következő információkat értesíti:

Hibaüzenet: "a virtuális géphez nem érhető el összeomlás-konzisztens helyreállítási pont az elmúlt 60 percben."</br>
Hiba azonosítója: 153007

Az alábbi szakaszok az okait és megoldásait ismertetik.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Magas adatváltozási arány a forrásként szolgáló virtuális gépen

Azure Site Recovery esemény jön létre, ha a forrás virtuális gép adatváltozási sebessége meghaladja a támogatott határértékeket. Ha szeretné megtekinteni, hogy a probléma magas adatforgalom miatt történt-e, nyissa meg a **replikált elemeket** > **VM** > **Events-Last 72 órát**.
Meg kell jelennie az "adatváltozási arány a támogatott korlátokon túl" értéknek:

![Azure Site Recovery lap, amely túl magas adatváltozási sebességet mutat](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha kijelöli az eseményt, a lemez pontos adatait kell megjelennie:

![Az adatváltozási arányt esemény részleteit megjelenítő oldal](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a tesztek alapján működnek, de nem fedik le az összes lehetséges Application input-output (I/O) kombinációt. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.

A virtuális gépenként két korlátozást kell figyelembe venni: a lemezen tárolt adatváltozások és adatváltozások száma. Nézzük meg a prémium szintű P20 lemezt a következő táblázatban egy példára. Egyetlen virtuális gép esetében a Site Recovery legfeljebb öt lemezből álló, legfeljebb 5 MB/s adatforgalom kezelésére képes. A Site Recovery virtuális gépenként legfeljebb 25 MB/s teljes adatforgalom megengedett.

**Replikáció tárolási célja** | **A forrásoldali lemez átlagos I/O-mérete** |**Adatforgalom átlagos adatváltozása a forrásoldali lemezen** | **Adatváltozások napi száma a forrásadatok lemezén**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

### <a name="solution"></a>Megoldás

A Azure Site Recovery a lemez típusától függően korlátozza az adatváltozások sebességét. Ha szeretné megtudni, hogy ez a probléma ismétlődő vagy ideiglenes, keresse meg az érintett virtuális gép adatváltozási arányát. Nyissa meg a forrás virtuális gépet, keresse meg a **figyelés**területen található mérőszámokat, és adja hozzá a metrikákat az alábbi képernyőképen látható módon:

![Az adatváltozási arány megkeresésének három lépésből álló folyamatát bemutató oldal](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Válassza a **metrika hozzáadása**lehetőséget, és adja hozzá az **operációsrendszer-lemezt írási sebesség (bájt/s** ) és **az adatlemez írási sebessége (bájt/s**)
1. Figyelje meg a nyársat a képernyőképen látható módon.
1. Megtekintheti az operációsrendszer-lemezek és az összes adatlemez együttes összes írási műveletét. Előfordulhat, hogy ezek a metrikák nem biztosítanak információt a lemezen, de az adatforgalom teljes mintázatát jelzik.

Az adatváltozási arányban előforduló csúcsok időnként adattörésből származhatnak. Ha az adatváltozás sebessége nagyobb, mint 10 MB/s (prémium szintű) vagy 2 MB/s (standard), és a rendszer leállítja a replikálást, a replikáció felveszi. Ha a forgalom folyamatosan meghaladja a támogatott korlátot, vegye figyelembe a következő lehetőségek egyikét:

- Zárja ki a nagy adatváltozási arányt okozó lemezt: először tiltsa le a replikálást. Ezután kizárhatja a lemezt a [PowerShell](./azure-to-azure-exclude-disks.md)használatával.
- A vész-helyreállítási tároló lemezének módosítása: Ez a beállítás csak akkor lehetséges, ha a lemezes adatforgalom kevesebb, mint 20 MB/s. Tegyük fel, hogy egy P10-lemezzel rendelkező virtuális gépnek 8 MB/s-nál nagyobb adatváltozása van, de kevesebb, mint 10 MB/s. Ha az ügyfél a védelem során P30-lemezt használhat a célként szolgáló tárolóhoz, akkor a probléma megoldható. Ez a megoldás csak Premium-Managed Disks használó gépek esetén lehetséges. Kövesse az alábbi lépéseket:

    1. Nyissa meg az érintett replikált gép **lemezeit** , és másolja a replika lemez nevét.
    1. Nyissa meg a felügyelt lemez ezen replikáját.
    1. Előfordulhat, hogy megjelenik egy szalagcím az **áttekintésben** , amely szerint egy sas URL-cím lett létrehozva. Jelölje ki ezt a szalagcímet, és szakítsa meg az exportálást. Ha nem látja a szalagcímet, hagyja figyelmen kívül ezt a lépést.
    1. A SAS URL-címének visszavonása után lépjen a felügyelt lemez **konfigurációjában** . Növelje a méretet, hogy a Site Recovery támogassa a megfigyelt adatforgalom mértékét a forrásoldali lemezen.

## <a name="Network-connectivity-problem"></a>Hálózati kapcsolati problémák

### <a name="network-latency-to-a-cache-storage-account"></a>Hálózati késés egy gyorsítótárbeli Storage-fiókhoz

Site Recovery replikált adatokat küld a gyorsítótár Storage-fiókba. Előfordulhat, hogy a hálózati késés akkor fordul elő, ha egy virtuális gépről a gyorsítótárbeli Storage-fiókba történő feltöltése 3 másodpercnél rövidebb, mint 4 MB.

A késéssel kapcsolatos problémák kereséséhez használja a [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). A parancssori segédprogrammal adatok tölthetők fel a virtuális gépről a cache Storage-fiókba. Ha a késés magas, ellenőrizze, hogy egy hálózati virtuális berendezést (NVA) használ-e a virtuális gépek kimenő hálózati forgalmának szabályozására. Előfordulhat, hogy a készülék szabályozva van, ha az összes replikációs forgalom a NVA halad át.

Javasoljuk, hogy hozzon létre egy hálózati szolgáltatási végpontot a virtuális hálózaton a "Storage" számára, hogy a replikálási forgalom ne lépjen a NVA. További információ: [hálózati virtuális berendezések konfigurálása](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Hálózati kapcsolat

Ahhoz, hogy Site Recovery replikáció működjön, a virtuális gépnek hozzá kell adnia a kimenő kapcsolatot adott URL-címekhez vagy IP-tartományokhoz. Lehet, hogy a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatokat. Ha igen, problémák léphetnek fel. Az összes URL-cím csatlakoztatása előtt tekintse meg [site Recovery URL-címek kimenő kapcsolatát](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>153006-es AZONOSÍTÓJÚ hiba – nem érhető el alkalmazás-konzisztens helyreállítási pont a virtuális géphez az elmúlt "X" percben

A leggyakoribb problémák a következők:

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Ismert probléma az SQL Server 2008/2008 R2-ben

**Javítás**: az SQL Server 2008/2008 R2 ismert problémája. A [SQL Server 2008 R2-t futtató kiszolgálók esetében a Azure site Recovery Agent vagy más, nem összetevővel rendelkező VSS biztonsági mentést nem sikerül végrehajtani](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>A Azure Site Recovery feladatok sikertelenek azon kiszolgálókon, amelyek a SQL Server példányok bármely verzióját üzemeltetik AUTO_CLOSE-adatbázisok

A **javítás módja**: a [nem összetevővel rendelkező VSS-alapú biztonsági másolatok, például a Azure site Recovery feladatok sikertelenek a SQL Server példányokat üzemeltető kiszolgálókon AUTO_CLOSE-adatbázisok esetén](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Ismert probléma a SQL Server 2016-es és 2017-es verziójában

A **javítás módja**: a cikkre vonatkozó [hiba akkor fordul elő, amikor biztonsági mentést készít egy olyan virtuális gépről, amely nem összetevő-alapú biztonsági másolattal rendelkezik SQL Server 2016 és 2017](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)-es verzióban.

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Az Azure Közvetlen tárolóhelyek konfigurációját használja

**Javítás**: a Azure site Recovery nem tudja létrehozni az alkalmazás konzisztens helyreállítási pontját közvetlen tárolóhelyek-konfigurációhoz. [Konfigurálja a replikációs házirendet](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>A VSS-vel kapcsolatos problémák több oka is van:

A további hibaelhárításhoz tekintse meg a forrás gépen található fájlokat a hiba pontos hibakódjának lekéréséhez:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

A fájlban található hibák megkereséséhez keressen rá a "vacpError" karakterláncra, és nyissa meg a vacp. log fájlt egy szerkesztőben.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Az előző példában a **2147754994** az a hibakód, amely a mondat utáni hibával kapcsolatos információkat mutatja.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>A VSS-író nincs telepítve – 2147221164-es hiba

**Javítás**: az alkalmazás konzisztencia-címkézésének létrehozásához Azure Site Recovery a kötet ÁRNYÉKMÁSOLATA szolgáltatás (VSS) protokollt használja. Site Recovery telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Azure Site Recovery telepíti ezt a VSS-szolgáltatót szolgáltatásként. Ha a VSS-szolgáltató nincs telepítve, az alkalmazás konzisztencia-pillanatképének létrehozása sikertelen lesz. A 0x80040154 "osztály nincs regisztrálva" hibaüzenetet jeleníti meg. A [VSS-író telepítésével kapcsolatos hibaelhárításról](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)a cikkből tájékozódhat.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>A VSS-író le van tiltva – 2147943458-es hiba

**Javítás**: az alkalmazás konzisztencia-címkéjének létrehozásához Azure site Recovery a VSS-t használja. Site Recovery telepíti a VSS-szolgáltatót a működéséhez, hogy az alkalmazás konzisztencia-pillanatképeket készítsen. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha nincs engedélyezve a VSS-szolgáltató szolgáltatása, az alkalmazás konzisztencia-pillanatképének létrehozása sikertelen lesz. A következő hibaüzenetet jeleníti meg: "a megadott szolgáltatás le van tiltva, és nem indítható el (0x80070422)."

Ha a VSS le van tiltva:

- Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus**értékre van-e állítva.
- Indítsa újra a következő szolgáltatásokat:
   - VSS szolgáltatás
   - Azure Site Recovery VSS-szolgáltató
   - VDS szolgáltatás

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS-szolgáltató NOT_REGISTERED – 2147754756-es hiba

**Javítás**: az alkalmazás konzisztencia-címkéjének létrehozásához Azure site Recovery a VSS-t használja. Győződjön meg arról, hogy telepítve van-e a Azure Site Recovery VSS-szolgáltató szolgáltatás.

A VSS-szolgáltató újratelepítéséhez használja a következő parancsokat:
1. Meglévő szolgáltató eltávolítása: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
1. VSS-szolgáltató újratelepítése: C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd

Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa **automatikus**értékre van-e állítva.

Indítsa újra a következő szolgáltatásokat:
- VSS szolgáltatás
- Azure Site Recovery VSS-szolgáltató
- VDS szolgáltatás
