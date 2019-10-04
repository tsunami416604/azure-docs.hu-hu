---
title: Azure Site Recovery hibaelhárítás az Azure-ról Azure-ba történő folyamatos replikálási problémákra | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatás vész-helyreállításra való replikálásával kapcsolatos hibák és problémák elhárítása
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 02f3dff4c9649beeadade942f4b32595f8543c2d
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742553"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Az Azure – Azure virtuális gépek replikálásának folyamatban lévő problémáinak elhárítása

Ez a cikk az Azure-beli virtuális gépek egyik régióból egy másik régióba való replikálásával és helyreállításával kapcsolatos gyakori problémákat ismerteti Azure Site Recovery. Emellett azt is ismerteti, hogyan lehet elhárítani ezeket. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery folyamatosan replikálja az adatait a forrás régióból a vész-helyreállítási régióba, és 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ha Site Recovery nem tud helyreállítási pontokat létrehozni 60 percre, akkor a következő információkat értesíti:

Hibaüzenet: "A virtuális géphez nem érhető el összeomlás-konzisztens helyreállítási pont az elmúlt 60 percben."</br>
Hiba azonosítója: 153007 </br>

Az alábbi szakaszok az okait és megoldásait ismertetik.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Magas adatváltozási arány a forrásként szolgáló virtuális gépen
Azure Site Recovery egy eseményt, ha a forrás virtuális gép adatváltozási sebessége meghaladja a támogatott határértékeket. Annak megállapításához, hogy a probléma magas adatforgalom miatt > történt-e, nyissa meg a **replikált elemek** > virtuálisgép **-események – utolsó 72 óra**lehetőséget.
Meg kell jelennie az "adatváltozási arány a támogatott korlátokon túl" értéknek:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha kijelöli az eseményt, a lemez pontos adatait kell megjelennie:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a tesztek alapján működnek, de nem fedik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. 

A virtuális gépenként két korlátozást kell figyelembe venni, a lemezre és az adatforgalomra vonatkozó adatváltozást. Tegyük fel például, hogy a prémium P20 lemezt a következő táblázatban tekintjük át. A Site Recovery legfeljebb 5 MB/s adatváltozást képes kezelni lemezenként, legfeljebb öt ilyen lemezzel, a virtuális gépenként legfeljebb 25 MB/s összegű korlát miatt.

**Replikáció tárolási célja** | **A forrásoldali lemez átlagos I/O-mérete** |**Adatforgalom átlagos adatváltozása a forrásoldali lemezen** | **Adatváltozások napi száma a forrásadatok lemezén**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

### <a name="solution"></a>Megoldás
A Azure Site Recovery a lemez típusától függően korlátozza az adatváltozások arányát. Ha tudni szeretné, hogy ez a probléma ismétlődő vagy pillanatnyi, keresse meg az érintett virtuális gép adatváltozási arányát. Nyissa meg a forrás virtuális gépet, keresse meg a **figyelés**területen található mérőszámokat, és adja hozzá a metrikákat az alábbi képernyőképen látható módon:

![Három lépésből álló folyamat az adatváltozási arány megkereséséhez](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Válassza a **metrika hozzáadása**lehetőséget, és adja hozzá az **operációsrendszer-lemezt írási sebesség (bájt/s** ) és az adatlemez írási sebessége ( **bájt/s**)
2. Figyelje meg a nyársat a képernyőképen látható módon.
3. Megtekintheti az operációsrendszer-lemezek és az összes adatlemez együttes összes írási műveletét. Előfordulhat, hogy ezek a metrikák nem biztosítanak információt a lemezen, de az adatforgalom teljes mintázatát jelzik.

Ha egy tüske alkalmi adattörésből származik, és az adatváltozások aránya nagyobb, mint 10 MB/s (prémium szintű) és 2 MB/s (standard), akkor a replikáció felveszik. Ha azonban a forgalom az idő nagy részében jóval meghaladja a támogatott korlátot, vegye figyelembe az alábbi lehetőségek egyikét, ha lehetséges:

* **A nagy adatváltozási arányt okozó lemez kizárása**: A lemezt a [PowerShell](./azure-to-azure-exclude-disks.md)használatával is kizárhatja. A lemez kizárásához először le kell tiltania a replikálást. 
* **Módosítsa a vész-helyreállítási tároló lemezének szintjét**: Ez a beállítás csak akkor lehetséges, ha a lemez adatváltozása kevesebb, mint 20 MB/s. Tegyük fel, hogy egy P10 lemezzel rendelkező virtuális gép adatváltozása nagyobb, mint 8 MB/s, de kevesebb, mint 10 MB/s. Ha az ügyfél a védelem során P30-lemezt használhat a célként szolgáló tárolóhoz, akkor a probléma megoldható. Vegye figyelembe, hogy ez a megoldás csak a prémium szintű Managed Diskst használó gépek esetében lehetséges. Kövesse az alábbi lépéseket:
    - Navigáljon az érintett replikált gép lemezek paneljére, és másolja a replika lemez nevét
    - Navigáljon a replika felügyelt lemezéhez
    - Előfordulhat, hogy megjelenik egy szalagcím az Áttekintés panelen, amely azt jelzi, hogy egy SAS URL-cím lett létrehozva. Kattintson erre a szalagcímre, és szakítsa meg az exportálást. Ha nem látja a szalagcímet, hagyja figyelmen kívül ezt a lépést.
    - Amint visszavonják a SAS URL-címét, lépjen a felügyelt lemez konfiguráció paneljére, és növelje a méretet, hogy az ASR támogassa a forrás lemezen megfigyelt adatforgalom mértékét.

## <a name="Network-connectivity-problem"></a>Hálózati kapcsolati problémák

### <a name="network-latency-to-a-cache-storage-account"></a>Hálózati késés egy gyorsítótárbeli Storage-fiókhoz
Site Recovery replikált adatokat küld a gyorsítótár Storage-fiókba. Előfordulhat, hogy a hálózati késés akkor fordul elő, ha egy virtuális gépről a gyorsítótárbeli Storage-fiókba való feltöltéskor a rendszer 3 másodpercnél lassabban 4 MB-ot vesz igénybe. 

A késéssel kapcsolatos problémák kereséséhez a [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) használatával töltse fel az adatok feltöltését a virtuális gépről a cache Storage-fiókba. Ha a késés magas, ellenőrizze, hogy egy hálózati virtuális berendezést (NVA) használ-e a virtuális gépek kimenő hálózati forgalmának szabályozására. Előfordulhat, hogy a készülék szabályozva van, ha az összes replikációs forgalom a NVA halad át. 

Javasoljuk, hogy hozzon létre egy hálózati szolgáltatási végpontot a virtuális hálózaton a "Storage" számára, hogy a replikálási forgalom ne lépjen a NVA. További információ: [hálózati virtuális berendezések konfigurálása](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Hálózati kapcsolat
A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatot, akkor előfordulhat, hogy ezek egyike a probléma. Az összes URL-cím csatlakoztatása előtt tekintse meg [site Recovery URL-címek kimenő kapcsolatát](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>153006-es AZONOSÍTÓJÚ hiba – nem érhető el alkalmazás-konzisztens helyreállítási pont a virtuális géphez az utolsó "XXX" percben

A leggyakoribb problémák némelyike alább látható

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1\. ok: Ismert probléma az SQL Server 2008/2008 R2-ben 
**A javítás módja** : Ismert probléma van az SQL Server 2008/2008 R2-vel. Tekintse meg ezt a TUDÁSBÁZISCIKK [Azure site Recovery Agent vagy más, nem összetevővel rendelkező VSS biztonsági mentést, amely a SQL Server 2008 R2-t futtató kiszolgálókon meghiúsul.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>2\. ok: Azure Site Recovery feladatok meghiúsulnak a SQL Server példányok bármely verzióját üzemeltető kiszolgálókon a AUTO_CLOSE-adatbázisok segítségével 
**A javítás módja** : Tudásbáziscikk [](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3\. ok: Ismert probléma a SQL Server 2016-es és 2017-es verziójában
**A javítás módja** : Tudásbáziscikk [](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>4\. ok: A közvetlen tárolóhelyek konfigurációját használja
**A javítás módja** : Azure Site Recovery nem hozható létre konzisztens helyreállítási pont a közvetlen tárolóhelyek konfigurációjában. [A replikációs házirend](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) helyes konfigurálásához olvassa el a cikket.

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
