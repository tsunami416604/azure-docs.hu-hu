---
title: Válaszok a gyakori kérdésekre
description: 'Gyakori kérdésekre adott válaszok a következő témakörökben: az Azure Backup szolgáltatásai, mint például a Recovery Services-tárolók, a biztonsági mentés lehetséges elemei, működése, titkosítás és korlátozások. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: f95a4e7e9084c304838b01bdfaceee261d8151cf
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173229"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – gyakran ismételt kérdések

Ez a cikk a Azure Backup szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott?

Igen. Akár 500 Recovery Services-tárolót is létrehozhat, a Azure Backup támogatott régiójában, előfizetések szerint. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott?

Akár 1000 Azure-beli virtuális gépet is regisztrálhat. Ha a Microsoft Azure Backup-ügynököt használja, akár 50 Mohácsi-ügynököt is regisztrálhat. Továbbá regisztrálhat 50 Mohácsi-kiszolgálókat/DPM-kiszolgálókat egy tárba.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Hány adatforrást/elemet lehet védeni egy tárolóban?

A tárolóban akár 2000 adatforrást/elemet (IaaS VM, SQL, AFS stb.) is védetté tehet a tárolóban.
Ha például már védett a 500-es virtuális gépek és a 400 Azure Files a tárolóban lévő megosztások, akkor csak a 1100-os SQL-adatbázisok védelmét tudja biztosítani.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hány szabályzatot hozhatok létre a tárolóban?

A tár legfeljebb 200 házirendet tartalmazhat.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Ha a szervezetem egy tárolóval rendelkezik, hogyan lehet elkülöníteni a tárolóban lévő különböző kiszolgálók adatait az adatok visszaállításakor?

A helyreállítani kívánt kiszolgálói adatoknak ugyanazt a jelszót kell használniuk a biztonsági mentés beállításakor. Ha egy adott kiszolgálóra vagy kiszolgálókra szeretné elkülöníteni a helyreállítást, akkor csak az adott kiszolgálóhoz vagy kiszolgálókhoz tartozó hozzáférési kódot kell használnia. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Áthelyezhetem a tárolót az előfizetések között?

Igen. Recovery Services-tároló áthelyezéséhez tekintse meg ezt a [cikket](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Áthelyezhetem a biztonsági mentési adattárakat egy másik tárba?

Nem. A tárolóban tárolt biztonsági mentési adatkészletek nem helyezhetők át másik tárba.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Megváltoztathatom a GRS, hogy LRS a biztonsági mentés után?

Nem. A Recovery Services-tárolók csak a biztonsági másolatok tárolása előtt módosíthatják a tárolási beállításokat.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Használhatok elemszintű visszaállítást (ILR) a Recovery Services-tárolóba mentett virtuális gépek számára?

- A ILR az Azure-beli virtuális gépek biztonsági mentése által készített Azure-beli virtuális gépek esetében támogatott. További információ: [Article](backup-azure-restore-files-from-vm.md)
- A ILR nem támogatott az Azure Backup Server vagy a System Center DPM által készített helyszíni virtuális gépek online helyreállítási pontjain.

## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Hol találhatok gyakori kérdéseket az Azure-beli virtuális gépek biztonsági mentésének Azure Backup ügynökével kapcsolatban?

- Az Azure-beli virtuális gépeken futó ügynök esetén olvassa el ezt a [gyakori kérdéseket](backup-azure-vm-backup-faq.md).
- Az Azure file Folders biztonsági mentésére használt ügynök esetében olvassa el ezt a [gyakori kérdéseket](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Általános biztonsági mentés

### <a name="are-there-limits-on-backup-scheduling"></a>Korlátozzák a biztonsági mentés ütemezését?

Igen.

- A Windows Server vagy Windows rendszerű gépekről naponta legfeljebb háromszor készíthet biztonsági mentést. Az ütemezési házirendet beállíthatja napi vagy heti ütemezésre.
- Akár naponta kétszer is készíthet biztonsági mentést a DPM. Az ütemezési szabályzatot napi, heti, havi és éves szinten is beállíthatja.
- Naponta egyszer biztonsági mentést készíthet az Azure-beli virtuális gépekről.

### <a name="what-operating-systems-are-supported-for-backup"></a>Milyen operációs rendszereket támogat a biztonsági mentés?

Azure Backup támogatja ezeket az operációs rendszereket a fájlok és mappák, valamint a Azure Backup Server és a DPM által védett alkalmazások biztonsági mentéséhez.

**OS** | **Termékváltozat** | **Részletek**
--- | --- | ---
Munkaállomás | |
Windows 10 64 bit | Enterprise, Pro, Home | A gépeknek a legújabb szolgáltatási csomagokat és frissítéseket kell futtatniuk.
Windows 8,1 64 bit | Enterprise, Pro | A gépeknek a legújabb szolgáltatási csomagokat és frissítéseket kell futtatniuk.
Windows 8 64 bit | Enterprise, Pro | A gépeknek a legújabb szolgáltatási csomagokat és frissítéseket kell futtatniuk.
Windows 7 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | A gépeknek a legújabb szolgáltatási csomagokat és frissítéseket kell futtatniuk.
Kiszolgáló | |
Windows Server 2019 64 bit | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2016 64 bit | Standard, Datacenter, Essentials | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012 R2 64 bit | Standard, Datacenter, Foundation | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2012 64 bit | Datacenter, Foundation, Standard | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2016 64 bit | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Storage Server 2012 64 bit | Standard, Workgroup | A legújabb szervizcsomagokkal/frissítésekkel.
Windows Server 2008 R2 SP1 64 bites | Standard, Enterprise, Datacenter, Foundation | A legújabb frissítésekkel.
Windows Server 2008 64 bit | Standard, Enterprise, Datacenter | A legújabb frissítésekkel.

A Azure Backup nem támogatja a 32 bites operációs rendszereket.

Az Azure VM Linux biztonsági mentések esetén [a Azure Backup támogatja az Azure által támogatott disztribúciók listáját](../virtual-machines/linux/endorsed-distros.md), kivéve a Core os Linux és a 32 bites operációs rendszert. Más, saját linuxos disztribúciók is működhetnek, amíg a virtuálisgép-ügynök elérhető a virtuális gépen, és a Python támogatása is létezik.

### <a name="are-there-size-limits-for-data-backup"></a>Vannak-e az adatbiztonsági mentésre vonatkozó méretkorlát?

A méretek korlátai a következők:

Operációs rendszer/számítógép | Adatforrás maximális mérete
--- | ---
Windows 8 vagy újabb | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 vagy újabb | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 adatlemez<br/><br/> 4095 GB-ig elmentett adatlemez

### <a name="how-is-the-data-source-size-determined"></a>Hogyan határozható meg az adatforrás mérete?

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

**Adatforrás** | **Részletek**
--- | ---
Kötet |Azon adatok mennyisége, amelyekről biztonsági mentés készül egy köteten lévő virtuális gépről.
SQL Server-adatbázis |A biztonsági mentés alatt álló egypéldányos SQL-adatbázis mérete.
SharePoint | A biztonsági mentés alatt álló SharePoint-farmon belüli tartalom-és konfigurációs adatbázisok összege.
Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgálón lévő Exchange-adatbázisok összege.
BMR/rendszerállapot |A BMR vagy a számítógép rendszerállapotának minden egyes példánya, amelyről biztonsági mentés készül.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Korlátozva van-e a Recovery Services-tárolóval biztonsági mentésre kerülő adatmennyiség?

Recovery Services-tároló használatával nem korlátozható a biztonsági mentéshez használható adatmennyiség.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Miért kisebb a Recovery Services-tárolóba továbbított adatmennyiség, mint a biztonsági mentéshez kiválasztott adatforgalom?

A Azure Backup ügynökről, DPM és Azure Backup Serverról biztonsági másolat készítése az átvitel előtt tömörítve és titkosítva történik. A tömörítés és a titkosítás alkalmazása esetén a tárolóban lévő adatmennyiség 30-40%-kal kisebb.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Törölhetek egyes fájlokat egy helyreállítási pontról a tárolóban?

Nem, Azure Backup nem támogatja az egyes elemek törlését vagy kiürítését a tárolt biztonsági másolatokból.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Ha megszakítottam egy biztonsági mentési feladatot az indítás után, az átvitt biztonsági mentési adatok törlődnek?

Nem. A tárolóba a biztonsági mentési feladat megszakítása előtt továbbított összes adatok a tárolóban maradnak.

- Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához.
- Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét.
- A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

## <a name="retention-and-recovery"></a>Megőrzés és helyreállítás

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>A DPM és a Windows rendszerű gépek adatmegőrzési szabályzatai nem DPM?

Igen, mindkettő napi, heti, havi és éves adatmegőrzési szabályzattal rendelkezik.

### <a name="can-i-customize-retention-policies"></a>Testreszabhatók az adatmegőrzési szabályzatok?

Igen, testreszabhatja a szabályzatokat. Beállíthatja például a heti és a napi adatmegőrzési követelményeket, de nem évenként és havonta.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Használhatok különböző időpontokat a biztonsági mentési ütemezési és adatmegőrzési szabályzatokhoz?

Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. Ez a rendszerkép például egy adatmegőrzési szabályzatot jelenít meg a 12am és 18:00-kor készített biztonsági mentésekhez.

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Ha a biztonsági mentés hosszú ideig tart, hosszabb időt is igénybe kell venni a régebbi adatpontok helyreállítására?

Nem. A legrégebbi vagy a legújabb pont helyreállításának ideje ugyanaz. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?

A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják.

- A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók.
- A növekményes másolatok tárolási *hatékonyságot* igényelnek, de szükség van egy adatlánc visszaállítására, amely hatással van a helyreállítási időre

Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez biztosítja, hogy a bejövő és a kimenő forgalom hatékonyan használható legyen. Az adattárolás mennyiségét és az adatok helyreállításához szükséges időt a rendszer minimálisra tartja. További információ a [növekményes biztonsági mentésekről](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>A létrehozható helyreállítási pontok száma korlátozott?

Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy munkaterhelések, amelyek az Azure-ba készülnek.

- További információ a [biztonsági mentésről és a megőrzésről](./backup-overview.md#backup-and-retention).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hányszor lehet helyreállítani az Azure-ba mentett adatokat?

Az Azure Backupból történő helyreállítások száma korlátlan.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért?

Nem. A helyreállítás ingyenes, és nem számítunk fel díjat a kimenő forgalomért.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot?

Új házirend alkalmazása esetén a rendszer az új szabályzat ütemtervét és megőrzését követi.

- Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak.
- Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="encryption"></a>Titkosítás

### <a name="is-the-data-sent-to-azure-encrypted"></a>Az Azure-ba küldött adatok titkosítottak?

Igen. Az adattitkosítás a helyszíni gépen a AES256 használatával történik. Az adatokat a rendszer egy biztonságos HTTPS-kapcsolaton keresztül küldi el. A felhőben továbbított adatokat csak a Storage és a Recovery szolgáltatás között HTTPS-kapcsolat védi. az iSCSI protokoll biztosítja a helyreállítási szolgáltatás és a felhasználói gép közötti adatátvitel biztonságát. A biztonságos bújtatás az iSCSI-csatorna védelmére szolgál.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?

Igen. Az Azure-ban tárolt adatok titkosítva vannak.

- Helyszíni biztonsági mentés esetén a rendszer a titkosítást az Azure-ba történő biztonsági mentéshez megadott hozzáférési kód használatával biztosítja.
- Az Azure-beli virtuális gépek esetében az adatok a Storage Service Encryption (SSE) használatával titkosítva vannak.

A Microsoft soha nem fejt vissza adatokat.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Mi a minimális titkosítási érték a biztonsági másolatok titkosításához használt kulcs esetében?

Az Azure Backup-ügynök használatakor a titkosítási kulcsnak legalább 16 karakterből kell állnia. Az Azure-beli virtuális gépek esetében nincs korlátozva az Azure KeyVault által használt kulcsok hossza.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Tudom helyreállítani az adatokat? Helyre tudja állítani a Microsoft az adatokat?

A biztonsági másolatok titkosításához használt kulcs csak a webhelyen van. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha rossz helyre állítja a kulcsot, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

## <a name="next-steps"></a>Következő lépések

További gyakori kérdések:

- [Gyakori kérdések](backup-azure-vm-backup-faq.md) az Azure-beli virtuális gépek biztonsági mentéséről.
- Az Azure Backup-ügynökkel kapcsolatos [Gyakori kérdések](backup-azure-file-folder-backup-faq.md)
