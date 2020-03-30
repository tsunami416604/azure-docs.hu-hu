---
title: Az Azure-beli virtuális gépek replikációjának hibaelhárítása az Azure Site Recovery szolgáltatással
description: Replikáció – hiba elhárítása az Azure VM vész-helyreállítási szolgáltatásában az Azure Site Recovery szolgáltatással
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276665"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Replikáció – problémamegoldás az Azure virtuális gép vész-helyreállítási szolgáltatásában

Ez a cikk ismerteti az Azure Site Recovery gyakori problémáit, amikor replikálja és helyreállíta az Azure virtuális gépeket egyik régióból a másikba. Azt is bemutatja, hogyan háríthatók el a gyakori problémák. A támogatott konfigurációkról további információt az [Azure virtuális gépek replikálásának támogatási mátrixában talál.](site-recovery-support-matrix-azure-to-azure.md)

Az Azure Site Recovery konzisztensen replikálja az adatokat a forrásrégióból a vész-helyreállítási régióba. Emellett 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ha a Site Recovery 60 percig nem tud helyreállítási pontokat létrehozni, az adatokkal figyelmezteti:

Hibaüzenet: "Nincs összeomlás-konzisztens helyreállítási pont elérhető a virtuális gép számára az elmúlt 60 percben."</br>
Hibaazonosító: 153007

A következő szakaszok az okokat és a megoldásokat ismertetik.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>Magas adatváltozási érték a forrás virtuális gépen

Az Azure Site Recovery létrehoz egy eseményt, ha az adatváltozási sebesség a forrás virtuális gépen magasabb, mint a támogatott korlátok. Annak ellenőrzéséhez, hogy a probléma a nagy lemorzsolódás miatt van-e, nyissa meg **a Replikált elemek** > **virtuálisgép-események** > **– az elmúlt 72 órában című témakört.**
Meg kell jelennie az "Adatváltozási arány a támogatott határokon túl" eseménynek:

![Az Azure Site Recovery lap, amely túl magas adatváltozási arányt mutat](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha az eseményt választja, a következő pontos lemezadatokat kell látnia:

![Az adatváltozási sebesség esemény részleteinek megjelenítése](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a tesztjeinken alapulnak, de nem fedhetik le az összes lehetséges alkalmazásbemeneti-kimeneti (I/O) kombinációt. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.

Két korlátot kell figyelembe venni: adatlemorzsolódás lemezenként és adatforgalom virtuális gépenként. Nézzük meg a prémium P20 lemezt az alábbi táblázatban egy példa. Egyetlen virtuális gép esetén a Site Recovery lemezenként 5 MB/s lemorzsolódást képes kezelni, legfeljebb öt ilyen lemezzel. Site Recovery legfeljebb 25 MB/s teljes lemorzsolódás virtuális gépenként.

**Replikáció tárolási célja** | **A forráslemez átlagos I/O-mérete** |**Átlagos adatforgalom a forráslemezhez** | **A forrásadat-lemez napi összes adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB lemezenként

### <a name="solution"></a>Megoldás

Az Azure Site Recovery korlátozza az adatváltozási sebességet, a lemez típusától függően. Ha meg szeretné tudni, hogy a probléma ismétlődő vagy ideiglenes-e, keresse meg az érintett virtuális gép adatváltozási sebességét. Nyissa meg a forrás virtuális gépet, keresse meg a metrikákat a **Figyelés**területen, és adja hozzá a metrikákat az alábbi képernyőképen látható módon:

![Az adatváltozási sebesség megtalálásának háromlépéses folyamatát megjelenítő oldal](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Válassza **a Metrika hozzáadása**lehetőséget, és adja hozzá az operációs rendszer **lemezírási bájtjainak/mp-t,** **és adja hozzá az adatlemez írási bájtjait/mp.**
1. Figyelje a tüske, ahogy az a screenshot.
1. Tekintse meg az operációs rendszer lemezein és az összes adatlemezen futó összes írási műveletet. Ezek a metrikák előfordulhat, hogy nem ad információt a lemezenként i szinten, de azt jelzik, hogy a teljes mintát az adatok lemorzsolódás.

Az adatváltozási sebesség megugrása alkalmi adatburst-ből származhat. Ha az adatváltozási sebesség nagyobb, mint 10 MB/s (Premium esetén) vagy 2 MB/s (standard esetén), és leáll, a replikáció felzárkózik. Ha a lemorzsolódás következetesen jóval meghaladja a támogatott korlátot, fontolja meg az alábbi lehetőségek egyikét:

- A nagy adatváltozást okozó lemez kizárása: Először tiltsa le a replikációt. Ezután kizárhatja a lemezt a [PowerShell](./azure-to-azure-exclude-disks.md)használatával.
- Módosítsa a vész-helyreállítási tárolólemez szintjét: Ez a beállítás csak akkor lehetséges, ha a lemez adatváltozása 20 MB/s-nál kisebb. Tegyük fel, hogy egy P10-lemezzel rendelkező virtuális gép adatlemorzsolódása 8 MB/s-nál nagyobb, de 10 MB/s-nál kisebb. Ha az ügyfél a védelem során használhatja a P30-lemezt a céltároláshoz, a probléma megoldható. Ez a megoldás csak a prémium szintű felügyelt lemezeket használó gépek esetében lehetséges. Kövesse az alábbi lépéseket:

    1. Nyissa meg az érintett replikált számítógép **lemezeit,** és másolja a replikalemez nevét.
    1. Nyissa meg a felügyelt lemez replikáját.
    1. Előfordulhat, hogy megjelenik egy szalagcím **az Áttekintés** ben, amely azt mondja, hogy egy SAS URL-t hoztak létre. Jelölje ki ezt a szalagcímet, és szakítsa meg az exportálást. Hagyja figyelmen kívül ezt a lépést, ha nem látja a szalagcímet.
    1. Amint a SAS URL-címe visszavonásra kerül, lépjen a felügyelt lemez **konfigurációja** című területre. Növelje a méretet úgy, hogy a Site Recovery támogatja a megfigyelt lemorzsolódási arányt a forráslemezen.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>Hálózati kapcsolódási problémák

### <a name="network-latency-to-a-cache-storage-account"></a>Hálózati késés egy gyorsítótár-tárfiókhoz

A Site Recovery replikált adatokat küld a gyorsítótár-tárfiókba. Előfordulhat, hogy hálózati késést tapasztal, ha az adatok feltöltése egy virtuális gépről a gyorsítótár-tárfiók lassabb, mint 4 MB 3 másodperc alatt.

A késéssel kapcsolatos probléma ellenőrzéséhez használja az [AzCopy programot.](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) Ezzel a parancssori segédprogrammal adatokat tölthet fel a virtuális gépről a gyorsítótártárfiókba. Ha a késés magas, ellenőrizze, hogy hálózati virtuális készüléket (NVA) használ-e a virtuális gépek kimenő hálózati forgalmának szabályozásához. A készülék szabályozása csökkenhet, ha az összes replikációs forgalom áthalad az NVA-n.

Azt javasoljuk, hogy hozzon létre egy hálózati szolgáltatás végpontot a virtuális hálózatban a "Storage", hogy a replikációs forgalom nem megy az NVA. További információ: [Network virtual appliance configuration](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Hálózati kapcsolat

A Site Recovery replikáció működéséhez a virtuális gépnek kimenő kapcsolatot kell biztosítania adott URL-címekhez vagy IP-tartományokhoz. Előfordulhat, hogy a virtuális gép egy tűzfal mögött, vagy használja a hálózati biztonsági csoport (NSG) szabályok at a kimenő kapcsolat szabályozásához. Ha igen, problémákat tapasztalhat. Ha meg szeretne győződni arról, hogy az összes URL-cím csatlakoztatva van, olvassa el [a Hely-helyreállítási URL-címek kimenő kapcsolata című témakört.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>153006-os azonosító : Nincs elérhető alkalmazáskonzisztens helyreállítási pont a virtuális géphez az elmúlt "X" percben

Az alábbiakban néhány a leggyakoribb kérdések.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Ismert probléma az SQL Server 2008/2008 R2 kiszolgálón

**A javítás:** Ismert probléma van az SQL Server 2008/2008 R2 kiszolgálóval. Tekintse meg az [Azure Site Recovery Agent vagy más, nem összetevőtől rendelkező VSS biztonsági mentés sikertelen, az SQL Server 2008 R2 rendszert üzemeltető kiszolgálók esetében.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Az Azure Site Recovery feladatok sikertelenek az SQL Server-példányok bármely verzióját tartalmazó kiszolgálókon, AUTO_CLOSE db-ekkel

**A javítás:** Tekintse meg a [cikket Nem összetevő VSS biztonsági mentések, például az Azure Site Recovery feladatok sikertelenek a kiszolgálókon, amelyek ENSQL Server-példányokat üzemeltetnek AUTO_CLOSE DB-kkel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Ismert probléma az SQL Server 2016 és 2017 verzióiban

**A javítás:** Olvassa el a Hiba című [cikket, amikor nem összetevőalapú biztonsági másolattal biztonsági másolatot készít egy virtuális gépről az SQL Server 2016-ban és 2017-ben](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Az Azure Storage Spaces közvetlen konfigurálását használja

**A javítás:** Az Azure Site Recovery nem tud alkalmazáskonzisztens helyreállítási pontot létrehozni a közvetlen tárolási helyekhez. [Konfigurálja a replikációs házirendet](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>További okok a VSS-sel kapcsolatos problémák miatt:

A további hibaelhárításhoz ellenőrizze a forrásgépen található fájlokat, hogy a hiba pontos hibakódja jelenjen meg:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

A hibák megkereséséhez keresse meg a "vacpError" karakterláncot a vacp.log fájl szerkesztőben való megnyitásával.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Az előző példában a **2147754994-es** hibakód jelzi a mondatot követő hibát.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS író nincs telepítve - Hiba 2147221164

**A javítás:** Alkalmazáskonzisztenciacímke létrehozásához az Azure Site Recovery a Kötet árnyékmásolata szolgáltatást (VSS) használja. A Site Recovery telepít egy VSS-szolgáltatót a működéséhez az alkalmazáskonzisztencia-pillanatképek készítéséhez. Az Azure Site Recovery szolgáltatásként telepíti ezt a VSS-szolgáltatót. Ha a VSS-szolgáltató nincs telepítve, az alkalmazás konzisztencia-pillanatkép létrehozása sikertelen lesz. Ez azt mutatja, a hiba Azonosító 0x80040154 "Osztály nincs regisztrálva." Olvassa el a [VSS-író telepítési hibaelhárításával kapcsolatos cikket.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS író le van tiltva - Hiba 2147943458

**A javítás:** Az alkalmazás konzisztenciacímke létrehozásához az Azure Site Recovery vss-t használ. A Site Recovery telepít egy VSS-szolgáltatót a működéséhez az alkalmazáskonzisztencia-pillanatképek készítéséhez. Ez a VSS-szolgáltató szolgáltatásként van telepítve. Ha nincs engedélyezve a VSS-szolgáltató szolgáltatás, az alkalmazás konzisztencia-pillanatkép létrehozása sikertelen lesz. Ez azt a hibát mutatja, hogy "A megadott szolgáltatás le van tiltva, és nem indítható el (0x80070422)."

Ha a VSS le van tiltva:

- Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa Automatikus értékre **van-e**állítva.
- Indítsa újra a következő szolgáltatásokat:
   - VSS szolgáltatás
   - Azure Site Recovery VSS-szolgáltató
   - VDS szolgáltatás

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS SZOLGÁLTATÓ NOT_REGISTERED - Hiba 2147754756

**A javítás:** Az alkalmazás konzisztenciacímke létrehozásához az Azure Site Recovery vss-t használ. Ellenőrizze, hogy az Azure Site Recovery VSS-szolgáltató szolgáltatás telepítve van-e.

A VSS-szolgáltató újratelepítéséhez használja az alábbi parancsokat:
1. Meglévő szolgáltató eltávolítása: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
1. VSS-szolgáltató újratelepítése: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Ellenőrizze, hogy a VSS-szolgáltató szolgáltatás indítási típusa Automatikus értékre **van-e**állítva.

Indítsa újra a következő szolgáltatásokat:
- VSS szolgáltatás
- Azure Site Recovery VSS-szolgáltató
- VDS szolgáltatás
