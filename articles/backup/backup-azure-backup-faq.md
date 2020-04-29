---
title: Gyakori kérdésekre adott válaszok
description: 'Gyakori kérdésekre adott válaszok a következő témakörökben: az Azure Backup szolgáltatásai, mint például a Recovery Services-tárolók, a biztonsági mentés lehetséges elemei, működése, titkosítás és korlátozások. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: 039666c4b54da6ac5444f5aa5acda5f1bb438782
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80156055"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Gyakori kérdések

Ez a cikk az Azure Backup szolgáltatással kapcsolatban gyakran feltett kérdésekre ad választ.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott?

Igen. Előfizetésenként az Azure Backup minden támogatott régiójához legfeljebb 500 Recovery Services-tároló hozható létre. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott?

Tárolónként maximum 1000 Azure-beli virtuális gépet regisztrálhat. Ha a Microsoft Azure Backup-ügynököt használja, legfeljebb 50 MARS-ügynököt regisztrálhat. Továbbá regisztrálhat 50 MABS-kiszolgálókat/DPM-kiszolgálókat egy tárba.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Egy tároló hány adatforrás/elem védelmére képes?

Összesen maximum 2000 adatforrást/elemet helyezhet védelem alá egy tárolóban. Ez a szám az összes számítási feladatra (IaaS VM, SQL, AFS stb.) egyszerre vonatkozik.
Ha például egy tárolóban már védelem alá helyezett 500 virtuális gépet és 400 Azure Files-megosztást, legfeljebb további 1100 SQL-adatbázist helyezhet ugyanott védelem alá.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hány szabályzatot lehet létrehozni tárolónként?

Egy tárolóhoz legfeljebb 200 szabályzat rendelhető hozzá.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Ha a cég vagy szervezet egyetlen tárolóval rendelkezik, hogyan tudom a különböző kiszolgálóktól származó adatokat elszigetelni egymástól az adatok visszaállításakor?

Az együtt helyreállítani kívánt kiszolgálóadatoknak ugyanazt a jelszót kell használniuk, amelyet a biztonsági mentés beállításakor adhat meg. Ha el szeretné különíteni egy adott kiszolgáló vagy kiszolgálók helyreállítását, adjon meg egy jelszót, amelyet csak az adott kiszolgáló(k)hoz használ. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Át lehet helyezni egy tárolót az előfizetések között?

Igen. Recovery Services-tároló áthelyezéséhez tekintse meg ezt a [cikket](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Áthelyezhetők a biztonsági másolati adatok egy másik tárolóba?

Nem. A tárolókban tárolt biztonságimásolat-adatok nem helyezhetők át más tárolóba.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Biztonsági mentés után lehetséges GRS-ről LRS-re váltani?

Nem. A Recovery Services-tárolók tárolási beállításait csak a biztonsági másolatok tárolása előtt lehet módosítani.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Végrehajtható elemszintű visszaállítás (Item Level Restore, ILR) olyan virtuális gépeken, amelyeknek Recovery Services-tárolóban található a biztonsági másolata?

- Az ILR az olyan Azure-beli virtuális gépeknél támogatott, amelyeknek a biztonsági másolata az Azure VM Backup használatával készült. További információkért tekintse meg ezt a [cikket](backup-azure-restore-files-from-vm.md).
- Az ILR nem támogatott az olyan helyszíni virtuális gépek online helyreállítási pontjainál, amelyeknek a biztonsági másolata az Azure Backup Server vagy a System Center DPM használatával készült.

## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Hol találhatók az Azure VM Backup Azure Backup-ügynökére vonatkozó gyakori kérdések?

- Az Azure-beli virtuális gépeken futó ügynökkel kapcsolatban tekintse át ezeket a [gyakori kérdéseket](backup-azure-vm-backup-faq.md).
- Az Azure-fájlmappák biztonsági másolatainak létrehozásához használt ügynökkel kapcsolatban tekintse át ezeket a [gyakori kérdéseket](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Általános biztonsági mentés

### <a name="are-there-limits-on-backup-scheduling"></a>Vonatkoznak korlátozások a biztonsági mentések ütemezésére?

Igen.

- A Windows Server vagy Windows rendszerű gépekről naponta legfeljebb három alkalommal lehet biztonsági másolatot készíteni. A napi vagy heti ütemezéseket az ütemezési szabályzattal adhatja meg.
- A DPM biztonsági mentését naponta a legfeljebb kétszer lehet elvégezni. Az ütemezési szabályzatban napi, heti, havi vagy éves gyakoriság állítható be.
- Az Azure-beli virtuális gépekről naponta egyszer készíthet biztonsági másolatot.

### <a name="what-operating-systems-are-supported-for-backup"></a>Mely operációs rendszerekről lehet biztonsági mentést készíteni?

Az Azure Backup a következő operációs rendszerek fájljainak és mappáinak biztonsági mentését támogatja, az Azure Backup Server és a DPM által védett alkalmazások mellett.

**OS** | **SKU** | **Részletek**
--- | --- | ---
Munkaállomás | |
Windows 10, 64 bites | Enterprise, Pro, Home | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 8.1, 64 bites | Enterprise, Pro | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 8, 64 bites | Enterprise, Pro | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Windows 7, 64 bites | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | A gépekre telepítve kell lenniük a legújabb szervizcsomagoknak és frissítéseknek.
Kiszolgáló | |
Windows Server 2019, 64 bites | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2016, 64 bites | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012 R2, 64 bites | Standard, Datacenter, Foundation | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012, 64 bites | Datacenter, Foundation, Standard | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2016, 64 bites | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012 R2, 64 bites | Standard, Workgroup, Essential | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012, 64 bites | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2008 R2 SP1, 64 bites | Standard, Enterprise, Datacenter, Foundation | A legújabb frissítésekkel.
Windows Server 2008, 64 bites | Standard, Enterprise, Datacenter | A legújabb frissítésekkel.

Az Azure Backup nem támogatja a 32 bites operációs rendszereket.

A Linux rendszerű Azure-beli virtuális gépek biztonsági mentése esetén az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md) támogatja, a Core OS Linux és a 32 bites operációs rendszerek kivételével. Egyéb saját Linux-disztribúciók is működtethetők, ha a virtuális gép ügynöke elérhető a virtuális gépen, és a rendszer támogatja a Pythont.

### <a name="are-there-size-limits-for-data-backup"></a>Vannak méretkorlátai az adatok biztonsági mentésének?

A következő méretkorlátok vannak érvényben:

Operációs rendszer/számítógép | Adatforrás méretkorlátja
--- | ---
Windows 8 vagy újabb | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 vagy újabb | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 adatlemez<br/> Ha regisztrálni szeretne a 16-nál több (legfeljebb 32) adatlemezzel rendelkező virtuális gépek privát előzetes verziójára, írjon nekünk a következő címre: AskAzureBackupTeam@microsoft.com <br><br> Akár 32 TB-os adatlemez

### <a name="how-is-the-data-source-size-determined"></a>Adatforrás méretének meghatározása

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

**Adatforrás** | **Részletek**
--- | ---
Kötet |Azon adatok mennyisége, amelyekről biztonsági másolat készül a biztonsági mentés alatt álló, egyetlen kötetből álló virtuális gépről.
SQL Server-adatbázis |A biztonsági mentés alatt álló egyetlen SQL-adatbázis mérete.
SharePoint | Egy biztonsági mentés alatt álló SharePoint-farm tartalmának és konfigurációs adatbázisainak összessége.
Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisa.
BMR/rendszerállapot |A biztonsági mentés alatt álló gép BMR-ének vagy rendszerállapotának minden egyes másolata.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Vonatkozik méretkorlátozás a Recovery Services-tárolóval biztonsági másolatokba mentett adatok mennyiségére?

Recovery Services-tároló használatával nem korlátozható a biztonsági mentéshez használható teljes adatmennyiség. Az egyes adatforrások (az Azure-beli virtuális gépeken kívül) legfeljebb 54 400 GB méretűek lehetnek. A korlátokkal kapcsolatos további információkért tekintse [meg a tároló korlátai szakaszt a támogatási mátrixban](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Miért kisebb a Recovery Services-tárolóba átvitt adatok mérete a biztonsági mentéshez kiválasztott adatok méreténél?

Az Azure Backup ügynökétől, a DPM-ből és az Azure Backup Serverről származó adatokat, amelyekről biztonsági mentés készül, a rendszer tömöríti és titkosítja az átvitel előtt. A tömörítés és a titkosítás alkalmazása után a tárolóban lévő adatok 30–40 százalékkal kisebbek lesznek.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Törölhetek egyes fájlokat a tároló helyreállítási pontjáról?

Nem, az Azure Backup nem támogatja az egyes elemek törlését vagy eltávolítását a tárolt biztonsági másolatokból.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik?

Nem. A biztonsági mentési feladat megszakításának pillanata előtt a tárolóba már átvitt minden adat a tárolóban marad.

- Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához.
- Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét.
- A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

## <a name="retention-and-recovery"></a>Megőrzés és helyreállítás

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>A DPM-es és a DPM nélküli Windows gépek adatmegőrzési szabályai ugyanazok?

Igen, mindkettőnek napi, heti, havi és évi adatmegőrzési szabályai vannak.

### <a name="can-i-customize-retention-policies"></a>Testre lehet szabni az adatmegőrzési szabályokat?

Igen, a szabályokat testre lehet szabni. Dönthet például úgy, hogy konfigurál heti és napi adatmegőrzési követelményeket, de évi és havi szabályokat nem.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Használhatok különböző időpontokat a biztonsági mentések ütemezéséhez és az adatmegőrzési szabályokhoz?

Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. Ezen a képen például a 12:00-kor és 18:00-kor készített biztonsági mentések adatmegőrzési szabálya látható.

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Ha egy biztonsági másolatot a rendszer hosszú ideig őriz, több időbe telik egy régebbi adatpont helyreállítása?

Nem. A legrégebbi és a legutóbbi pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?

A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják.

- A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók.
- A növekményes másolatok tárolás szempontjából *hatékonyak*, de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt.

Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez a módszer gondoskodik róla, hogy a bemenő és kimenő sávszélesség is hatékonyan legyen felhasználva. Az adattárolási hely és az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva. További információk a [növekményes biztonsági másolatokról](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>A létrehozható helyreállítási pontok száma korlátozott?

Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példányok olyan számítógépek, (fizikai vagy virtuális) kiszolgálók vagy számítási feladatok, amelyeknek a biztonsági másolatai az Azure-ba kerülnek.

- További információk a [biztonsági mentésről és a megőrzésről](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hányszor állíthatom helyre az Azure-beli biztonsági másolattal rendelkező adatokat?

Az Azure Backupból történő helyreállítások száma korlátlan.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért?

Nem. A helyreállítás ingyenes, és nem kell fizetni a kimenő forgalomért.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatomat?

Amikor új szabályzatot léptet érvénybe, a rendszer annak az ütemezését és megőrzési szabályait fogja követni.

- Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak.
- Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="encryption"></a>Titkosítás

### <a name="is-the-data-sent-to-azure-encrypted"></a>Az Azure-ba küldött adatok titkosítottak?

Igen. Az adatokat a rendszer az AES256 használatával titkosítja a helyszíni gépen. Az adatokat a rendszer egy biztonságos HTTPS-kapcsolaton keresztül küldi el. A felhőben továbbított adatokat csak a HTTPS-kapcsolat védi a tároló és a helyreállítási szolgáltatás között. A helyreállítási szolgáltatás és a felhasználó gépe közötti adatátvitelt iSCSI protokoll védi. Az iSCSI-csatorna védelméről Secure Tunneling (biztonságos protokollbújtatás) gondoskodik.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?

Igen. Az Azure-ban az inaktív állapotú adatok titkosítva vannak.

- Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval.
- Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat.

A Microsoft soha nem fejt vissza adatokat.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Mi az adatok biztonsági másolatának titkosításához használt titkosítási kulcs minimális hossza?

Az Azure Backup-ügynök használatakor a titkosítási kulcsnak legalább 16 karakterből kell állnia. Az Azure-beli virtuális gépek esetében nincs korlátozva az Azure KeyVault által használt kulcsok hossza.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat? A Microsoft helyre tudja állítani az adatokat?

Az adatok biztonsági másolatának titkosításához használt kulcs csak Önnek van meg. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.

## <a name="next-steps"></a>További lépések

További gyakori kérdéseket is áttekinthet:

- [Gyakori kérdések](backup-azure-vm-backup-faq.md) az Azure-beli virtuális gépek biztonsági mentéséről.
- [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről
