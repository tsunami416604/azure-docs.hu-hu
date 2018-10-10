---
title: Azure Backup – Gyakori kérdések
description: 'Gyakori kérdésekre adott válaszok a következő témakörökben: az Azure Backup szolgáltatásai, mint például a Recovery Services-tárolók, a biztonsági mentés lehetséges elemei, működése, titkosítás és korlátozások. '
services: backup
author: markgalioto
manager: carmonm
keywords: biztonsági mentés és vészhelyreállítás; biztonsági mentési szolgáltatás
ms.service: backup
ms.topic: conceptual
ms.date: 8/2/2018
ms.author: markgal
ms.openlocfilehash: 9168a67366664f50a49ae04ef8ddc2f7aa9d665b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886487"
---
# <a name="questions-about-the-azure-backup-service"></a>Kérdések az Azure Backup szolgáltatással kapcsolatban
Ez a cikk az Azure Backup-összetevővel kapcsolatos gyakori kérdésekre ad választ. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Ha kérdést szeretne feltenni az Azure Backup szolgáltatással kapcsolatban, kattintson a jobb oldalon található **Megjegyzések** gombra. A megjegyzések a cikk alján jelennek meg. Megjegyzések írásához Livefyre-fiók szükséges. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.


## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott? <br/>
Igen. Akár 500 Recovery Services-tárolók száma előfizetésenként az Azure Backup szolgáltatás, a támogatott régióban hozhat létre. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott? <br/>
Legfeljebb 1000 Azure-beli virtuális gép tárolónként regisztrálhat. Ha a MAB-ügynök használ, legfeljebb 50 MAB-ügynökök tárolónként regisztrálhat. És regisztrálhatja az 50 MAB kiszolgálót/DPM-kiszolgálók egy tárolóba.

### <a name="can-i-use-a-rest-api-to-query-the-size-of-protected-items-in-a-vault-br"></a>A REST API segítségével lekérdezni egy tároló védett elemek méretét? <br/>
Igen, a cikk [Usages - tárolók által listában](https://t.co/2lgIrIaF0J), azokat az információkat, amelyek szerezhető be a Recovery Services-tároló tartalmazza.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Ha a szervezetem egy tárolóval rendelkezik, hogyan tudom az egyik kiszolgáló adatait elszigetelni egy másik kiszolgálóétól az adatok visszaállításakor?<br/>
Minden kiszolgáló, amely ugyanahhoz a tárolóhoz van regisztrálva, képes helyreállítani más, *ugyanazt a hozzáférési kódot használó* kiszolgálók adatainak biztonsági másolatait. Ha olyan kiszolgálókkal rendelkezik, amelyek adatainak biztonsági másolatait szeretné elszigetelni a szervezetében található más kiszolgálóétól, használjon egy erre kijelölt hozzáférési kódot ezekhez a kiszolgálókhoz. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="can-i-migrate-my-vault-between-subscriptions-br"></a>Áttelepítheti a saját tároló előfizetések között? <br/>
Nem. A tároló előfizetési szinten jön létre, és nem lehet hozzárendelni egy másik előfizetésbe.

### <a name="can-i-migrate-backup-data-to-another-vault-br"></a>Áttelepíthetek biztonsági mentési adatokat egy másik tárban? <br/>
Nem. A tárolókban tárolt biztonsági mentési adatok nem helyezhető át egy másik tárban.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup-br"></a>Módosíthatom a GRS az LRS egy biztonsági mentés után? <br/>
Nem. Recovery Services-tárolót is csak tárolási beállításainak módosítása előtt minden biztonsági eltárolta

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>A helyreállítási tárak a Resource Manageren alapulnak. Biztonsági mentési tárak továbbra is támogatottak? <br/>
A Backup-tárolók Recovery Services-tárolók lett konvertálva. Ha Ön nem konvertálható a Backup-tároló Recovery Services-tárolót, majd a Backup-tároló lett konvertálva Recovery Services-tároló az Ön számára.

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Áttelepíthetek egy Backup tárolót egy Recovery Services-tárolóra? <br/>
Minden Backup-tároló Recovery Services-tárolók lett konvertálva. Ha Ön nem konvertálható a Backup-tároló Recovery Services-tárolót, majd a Backup-tároló lett konvertálva Recovery Services-tároló az Ön számára.

## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke
A kérdések részletes listája a [Gyakori kérdések az Azure-beli fájlok és mappák biztonsági mentéséről](backup-azure-file-folder-backup-faq.md) című részben található

## <a name="azure-vm-backup"></a>Azure-beli virtuális gép biztonsági mentése
A kérdések részletes listája a [Gyakori kérdések az Azure-beli virtuális gépek biztonsági mentéséről](backup-azure-vm-backup-faq.md) című részben található

## <a name="back-up-vmware-servers"></a>VMware-kiszolgálók biztonsági mentése

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?
Igen. Az Azure Backup Server használatával biztonsági mentést készíthet a VMware vCenter-, valamint az ESXi-kiszolgálókról az Azure-ban. A támogatott VMware-verziókkal kapcsolatos információkért tekintse meg az [Azure Backup Server védelmi mátrix](backup-mabs-protection-matrix.md)című cikket. Részletes útmutatásért lásd: [VMware-kiszolgáló biztonsági mentése az Azure Backup Server használatával](backup-azure-backup-server-vmware.md).

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster-from-dpm-or-azure-backup-serverbr"></a>Egy teljes helyi VMware vagy Hyper-V fürt helyreállítása a DPM vagy az Azure Backup Server különálló licenc szükséges?<br/>
Nem különálló van szüksége a licencelés VMware vagy Hyper-V-védelem. Ha egy System Center-előfizető, VMware virtuális gépek védelme a DPM használatával. Ha egy System Center-ügyfél nem, az Azure Backup Server (használatalapú fizetés) használhatja a VMware virtuális gépek védelméhez.

## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server és System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Regisztrálhatom a DPM-kiszolgálómat több tárolóhoz? <br/>
Nem. A DPM- vagy MABS- kiszolgálók csak egy tárolóhoz regisztrálhatók.

### <a name="which-version-of-system-center-data-protection-manager-is-supported"></a>A System Center – Data Protection Manager melyik verziója támogatott?
Javasoljuk, hogy telepítse a [legújabb](http://aka.ms/azurebackup_agent) Azure Backup-ügynököt a System Center – Data Protection Manager legújabb kumulatív frissítésén.
- A System Center DPM 2012 R2 [kumulatív frissítés 14](https://support.microsoft.com/help/4043315/update-rollup-14-for-system-center-2012-r2-data-protection-manager) van a legújabb frissítést.
- A System Center DPM 2016 [2. kumulatív](https://support.microsoft.com/en-us/help/3209593) van a legújabb frissítést.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-protect-on-premises-applicationvm-workloads-to-azure"></a>Telepítettem az Azure Backup ügynököt a fájljaim és mappáim megvédéséhez. Telepítheti a System Center DPM védelme érdekében a helyszíni alkalmazás/virtuális gépek számítási feladatait az Azure-bA?
Igen. Az Azure Backup használatához a System Center Data Protection Manager (DPM), azonban először a DPM telepítése, és telepítse az Azure Backup-ügynök. Az Azure Backup ügynök és a DPM együttműködését úgy biztosíthatja, ha az Azure Backup-összetevőket ebben a sorrendben telepíti. Nem javasolt és nem támogatott az Azure Backup ügynök telepítése a DPM telepítése előtt.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>A DPM használatával biztonsági másolatot készíteni az alkalmazásokat az Azure Stack?
Nem. Bár az Azure Backup segítségével megvédheti az Azure Stack, az Azure Backup jelenleg nem támogatja a DPM biztonsági mentése alkalmazások az Azure Stack használatával.

## <a name="how-azure-backup-works"></a>Az Azure Backup működése
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik? <br/>
Nem. A biztonsági mentési feladat megszakításának pillanata előtt a tárolóba már átvitt minden adat a tárolóban marad. Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához. Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét. A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Korlátozva van, hogy mikorra vagy hányszor ütemezhető egy biztonsági mentési feladat?<br/>
Igen. Windows Server- vagy Windows-munkaállomásokon a biztonsági mentési feladatok naponta legfeljebb háromszor futtathatók. A System Center DPM biztonsági mentési feladatok naponta kétszer legfeljebb futtathatja. Az infrastruktúra-szolgáltatás virtuális gépei esetén a biztonsági mentési feladat naponta legfeljebb egyszer futtatható. A Windows Server vagy Windows-munkaállomás ütemezési szabályzatával használatával adja meg, napi vagy heti ütemezéseket. A System Center dpm-mel napi, heti, havi és évi ütemezéseket is megadhat.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Miért kisebb a Recovery Services-tárolóba átvitt adatok mérete a biztonsági mentéskor létrehozott adatméretnél?<br/>
 Minden, az Azure Backup ügynökétől, az SCDPM-ből vagy az Azure Backup Serverről származó adatot, amelyről biztonsági mentés készül, a rendszer tömörít és titkosít az átvitel előtt. A tömörítés és titkosítás alkalmazása után a Recovery Services-tároló adatai 30 – 40 százalékkal kisebbek.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vaultbr"></a>Törölhetem-e az egyes fájlok a tárban lévő helyreállítási pontból?<br/>
Nem, az Azure Backup nem támogatja a törlését vagy végleges törlése az egyes elemek tárolt biztonsági másolatokból.

## <a name="what-can-i-back-up"></a>Miről tudok biztonsági mentést készíteni?
### <a name="which-operating-systems-does-azure-backup-support-br"></a>Mely operációs rendszerek nem támogatják az Azure Backup? <br/>
Az Azure Backup a következő operációs rendszerek biztonsági mentését támogatja: az Azure Backup Server és a System Center Data Protection Manager (DPM) használatával védett fájlok és mappák, valamint számítási feladatokat végző alkalmazások.

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| Windows 8 és a legújabb szervizcsomagok |64 bit |Enterprise, Pro |
| Windows 7 és a legújabb szervizcsomagok |64 bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 és a legújabb szervizcsomagok |64 bit |Enterprise, Pro |
| Windows 10 |64 bit |Enterprise, Pro, Home |
| Windows Server 2016 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 és a legújabb szervizcsomagok |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Essential |
| Windows Server 2008 R2 SP1 |64 bit |Standard, Enterprise, Datacenter, Foundation |

**Azure VM Backup esetében:**

* **Linux**: Az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md) támogatja, a Core OS Linux kivételével.  Egyéb saját Linux-disztribúciók is működtethetők, ha a virtuális gép ügynöke elérhető a virtuális gépen, és a rendszer támogatja a Pythont.
* **Windows Server**: A Windows Server 2008 R2-nél régebbi verziók nem támogatottak.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Létezik méretkorlátozás a biztonsági mentésre kijelölt adatforrásokra vonatkozóan? <br/>
Az Azure Backup kényszerít egy adatforrás maximális mérete, azonban a korlátok a forrás nagyok. A 2015. augusztusi állapot szerint a támogatott operációs rendszerek esetén az adatforrás maximális mérete a következő:

| Sorszám | Operációs rendszer | Adatforrás maximális mérete |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 vagy újabb |54 400 GB |
| 2 |Windows 8 vagy újabb |54 400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

| Adatforrás | Részletek |
|:---:|:--- |
| Kötet |Egy kiszolgáló vagy ügyfélgép egyetlen kötetéről biztonsági mentés alatt álló adatok mennyisége |
| Hyper-V virtuális gép |A biztonsági mentés alatt álló virtuális gép összes virtuális merevlemezének összes adata |
| Microsoft SQL Server-adatbázis |A biztonsági mentés alatt álló egyetlen SQL-adatbázis mérete |
| Microsoft SharePoint |Egy biztonsági mentés alatt álló SharePoint-farm tartalmának és konfigurációs adatbázisainak összessége |
| Microsoft Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisa |
| BMR/Rendszerállapot |A biztonsági mentés alatt álló gép BMR-ének vagy rendszerállapotának minden egyes másolata |

Az Azure IaaS virtuális gépek biztonsági mentését minden virtuális gép legfeljebb 32 adatlemezt is rendelkezik, és az egyes adatlemezek legfeljebb 4095 GB is lehet.

### <a name="is-there-a-limit-on-the-amount-of-data-held-in-a-recovery-services-vault"></a>A Recovery Services-tárolóban tárolt adatok mennyisége korlátozva van?
Nincs, képes biztonsági mentést egy Recovery Services-tároló adatok mennyisége nincs korlátozva.

## <a name="retention-policy-and-recovery-points"></a>Adatmegőrzési szabály és helyreállítási pontok
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Van különbség a DPM és a Windows Server vagy Windows-ügyfél (pl. Windows Server kiszolgálón DPM nélkül) esetén a megtartási házirendben?<br/>
Nem, a DPM és a Windows Server vagy Windows-ügyfél is rendelkezik napi, heti, havi és évi megtartási házirendekkel.

### <a name="can-i-configure-my-retention-policies-selectively--that-is-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Konfigurálása saját megtartási házirendeket szelektíven – Ez azt jelenti, konfigurálja a heti és napi, de nem évente és havonta?<br/>
Igen, az Azure Backup megtartási struktúrája az igényeihez igazodva teljes rugalmasságot tesz lehetővé a megtartási házirend meghatározásában.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>„Ütemezhetek egy biztonsági mentést” este 6 órára, és megadhatok „megtartási házirendeket” egy másik időpontra?<br/>
Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. A következő képen a megtartási házirend 0 és 18 órakor készült biztonsági mentésekre van meghatározva. <br/>

![Biztonsági mentés és megőrzés ütemezése](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Ha egy biztonsági másolat hosszú ideig van megtartva, több időbe telik egy régebbi adatpont helyreállítása? <br/>
Nem – a legrégebbi és a legújabb pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?<br/>
A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják. A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók. A növekményes másolatok tárolás szempontjából *hatékonyak* de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt. Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez az adattárolási módszer biztosítja, hogy a bemenő és kimenő sávszélesség is hatékonyan van felhasználva. Mind az adattárolási hely, mind az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva. Megtudhatja, hogy miért hatékonyak a [növekményes biztonsági mentések](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>A létrehozható helyreállítási pontok száma korlátozott?<br/>
Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy számítási feladatok, amelyek úgy vannak konfigurálva, hogy biztonsági mentést végezzenek az Azure-ba. További információkért lásd a magyarázatokat a [biztonsági mentést és megőrzést](./backup-introduction-to-azure-backup.md#backup-and-retention), illetve a [védett példányokat](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance) ismertető szakaszban.

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Hány helyreállítást végezhetek el az Azure-ban biztonsági másolattal rendelkező adatokból?<br/>
Az Azure Backupból történő helyreállítások száma korlátlan.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért? <br/>
Nem. A helyreállítások ingyenesek, és nem kell fizetni a kimenő forgalomért.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot?
Új szabályzat alkalmazásakor, ütemezése és megőrzése az új házirendet kell követni. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="azure-backup-encryption"></a>Azure Backup-titkosítás
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Az Azure-ba küldött adatok titkosítottak? <br/>
Igen. Az adatokat a rendszer az AES256 használatával a helyszíni kiszolgálón/ügyfélen/SCDPM-gépen titkosítja, és egy biztonságos HTTPS-kapcsolaton keresztül küldi el.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?<br/>
Igen. Az Azure-ba küldött (aktívan nem használt) adatok titkosítva maradnak. A Microsoft soha nem fejt vissza adatokat. Azure virtuális gép biztonsági mentése esetén az Azure Backup a virtuális gép titkosítására támaszkodik. Ha például a virtuális gépe az Azure Disk Encryption vagy más titkosítási technológiával van titkosítva, az Azure Backup ezzel védi az adatait.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Mi az adatok biztonsági másolatának titkosításához használt titkosítási kulcs minimális hossza? <br/>
Az Azure Backup-ügynök használatakor a titkosítási kulcsnak legalább 16 karakterből kell állnia. Az Azure-beli virtuális gépek esetében nincs korlátozva az Azure KeyVault által használt kulcsok hossza.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat (vagy) a Microsoft helyre tudja állítani az adatokat? <br/>
Az adatok biztonsági másolatának titkosításához használt kulcs csak az ügyfél telephelyén van jelen. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.
