---
title: Azure Backup – Gyakori kérdések
description: 'Gyakori kérdésekre adott válaszok: Azure Backup-funkciók többek között a Recovery Services tárolók, biztonsági, hogyan működik, titkosítás és korlátozások. '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 4e3a79c28fc0e67fbf22e4d0fde3de9528d3edf4
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382635"
---
# <a name="azure-backup---frequently-asked-questions"></a>Az Azure Backup – gyakori kérdések
Ez a cikk az Azure Backup szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott?
Igen. Legfeljebb 500 Recovery Services-tárolók száma előfizetésenként az Azure Backup szolgáltatás, a támogatott régióban hozhat létre. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott?
Legfeljebb 1000 Azure-beli virtuális gép tárolónként regisztrálhat. Ha a Microsoft Azure Backup ügynököt használ, tárolónként 50 MAB-ügynökök is regisztrálhatja. És regisztrálhatja az 50 MAB kiszolgálót/DPM-kiszolgálók egy tárolóba.


### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Ha a szervezetem egy tárolóval rendelkezik, hogyan tudom elszigetelni adat a tárolóban lévő különböző kiszolgálókról származó adatok visszaállításakor?

Együtt a helyreállítani kívánt kiszolgálót adatok ugyanazt a jelszót használja, ha a biztonsági mentés beállítása. Ha el szeretné különíteni a helyreállítás az adott kiszolgálón vagy kiszolgálókon, vagy a kiszolgáló csak használni egy hozzáférési kódot. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Is saját tárolót is áthelyezése előfizetések között?

Nem. A tároló előfizetési szinten jön létre, és nem lehet hozzárendelni egy másik előfizetésbe.

### <a name="can-i-move-backup-data-to-another-vault"></a>Biztonsági mentési adatok átvitele egy másik tárban?

Nem. A tárolókban tárolt biztonsági mentési adatok nem helyezhető át egy másik tárban.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Módosíthatom a GRS az LRS egy biztonsági mentés után?

Nem. Recovery Services-tárolót is csak tárolási beállításainak módosítása előtt minden biztonsági eltárolta

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Használhatom-e egy elem szintű visszaállítása (ILR) a virtuális gépek biztonsági mentése Recovery Services-tárolóra?
Nem, ILR nem támogatott.


## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Hol találok az Azure Backup-ügynök az Azure virtuális gép biztonsági mentésével kapcsolatos gyakori kérdéseket?

- Az ügynököt futtató Azure virtuális gépeken, olvassa el ezt [– gyakori kérdések](backup-azure-vm-backup-faq.md).
- Az ügynök használt Azure-beli mappák biztonsági mentése, olvassa el ezt [– gyakori kérdések](backup-azure-file-folder-backup-faq.md).


## <a name="vmware-and-hyper-v-backup"></a>A VMware és Hyper-V biztonsági mentés

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?

Igen. Az Azure Backup Server használatával biztonsági másolatot készíteni VMware vCenter-kiszolgáló és az ESXi-gazdagépek Azure-bA.

- [További](backup-mabs-protection-matrix.md) kapcsolatos támogatott verziója.
- [Az alábbi lépésekkel](backup-azure-backup-server-vmware.md) egy VMware-kiszolgálók biztonsági mentéséhez.

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>Kell helyreállítani egy teljes helyi VMware vagy Hyper-V fürt különálló licenc?

Nem különálló van szüksége a licencelés VMware vagy Hyper-V-védelem.

- Ha egy System Center-előfizető, VMware virtuális gépek védelme a System Center Data Protection Manager (DPM) használatával.
- Ha egy System Center-ügyfél nem, az Azure Backup Server (használatalapú fizetés) használhatja a VMware virtuális gépek védelméhez.

## <a name="dpm-and-azure-backup-server-backup"></a>A DPM és az Azure Backup Server biztonsági másolat

### <a name="which-dpm-versions-are-supported"></a>Melyik DPM-verziók támogatottak?

Támogatott DPM-verziók foglalja össze a [támogatási mátrix](backup-azure-dpm-introduction.md#prerequisites-and-limitations). Javasoljuk, hogy a DPM legújabb frissítéseinek a telepítéséhez, és futtassa a [legújabb verzió](https://aka.ms/azurebackup_agent) , az Azure Backup ügynököt a DPM-kiszolgálón.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Regisztrálhatom a kiszolgálómat több tárolóhoz?

Nem. A DPM vagy az Azure Backup server csak egy tárolóhoz regisztrálhatók.



### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>A DPM használatával biztonsági másolatot készíteni az alkalmazásokat az Azure Stack?
Nem. Az Azure Backup segítségével megvédheti az Azure Stack, az Azure Backup nem támogatja a DPM biztonsági mentése az Azure Stackben alkalmazások használatával.


### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Telepítettem az Azure Backup ügynököt a fájljaim és Mappáim védelme, ha telepíthető System Center DPM biztonsági mentése a helyszíni számítási feladatokat az Azure-bA?
Igen. De kell először állítsa be a DPM, és ezután telepítheti az Azure Backup ügynököt.  Ebben a sorrendben összetevők telepítése biztosítja, hogy az Azure Backup ügynök működik a dpm-mel. Az ügynök telepítése a DPM telepítése előtt nem javasolt vagy támogatott.



## <a name="general-backup"></a>Általános biztonsági mentés

### <a name="are-there-limits-on-backup-scheduling"></a>Korlátozva van, a biztonsági mentés ütemezését?
Igen.
- Képes biztonsági másolatot készíteni a Windows Server vagy Windows legfeljebb napi háromszori gépek. Az ütemezési házirendben napi vagy heti ütemezéseket is beállíthatja.
- Naponta legfeljebb kétszer is biztonsági másolatot készíteni a DPM. Az ütemezési házirendben és napi, heti, havi és éves megadható.
- Ön az Azure virtuális gépek biztonsági mentése naponta egyszer.

## <a name="what-operating-systems-are-supported-for-backup"></a>Milyen operációs rendszereket támogat a biztonsági mentés?

Az Azure Backup biztonsági mentését a fájlok és mappák és az Azure Backup Server és a DPM által védett alkalmazások ezeket az operációs rendszereket támogatja.

**OS**| **Termékváltozat** |**Részletek**
--- | --- | ---
Munkaállomás | |
Windows 10 64 bit | Enterprise, Pro, Home | Gépek a legújabb szolgáltatások csomagokat és frissítéseket kell futtatnia.
Windows 8.1 64 bites | Enterprise, Pro | Gépek a legújabb szolgáltatások csomagokat és frissítéseket kell futtatnia.
Windows 8 64 bites | Enterprise, Pro | Gépek a legújabb szolgáltatások csomagokat és frissítéseket kell futtatnia.
Windows 7 64 bites | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Gépek a legújabb szolgáltatások csomagokat és frissítéseket kell futtatnia.
Kiszolgáló | |
A Windows Server 2016 64 bites | Standard, Datacenter, Essentials | Rogramtelepítés a legújabb szolgáltatási csomagokat és frissítéseket.
A Windows Server 2012 R2 64 bites | Standard, Datacenter, Foundation | A legújabb szolgáltatási csomagokat és frissítésekkel.
A Windows Server 2012 64 bites | Datacenter, Foundation, Standard | A legújabb szolgáltatási csomagokat és frissítésekkel.
A Windows Storage Server 2016 64 bites | Standard, Workgroup | A legújabb szolgáltatási csomagokat és frissítésekkel.
A Windows Storage Server 2012 R2 64 bites | Standard, Workgroup, Essential | A legújabb szolgáltatási csomagokat és frissítésekkel.
A Windows Storage Server 2012 64 bites | Standard, Workgroup | A legújabb szolgáltatási csomagokat és frissítésekkel.
A Windows Server 2008 R2 SP1, 64 bites | Standard, Enterprise, Datacenter, Foundation | A legújabb frissítésekkel.
A Windows Server 2008 64 bites | Standard, Enterprise, Datacenter | Legújabb frissítéseit.

Linux-alapú Azure virtuális gép biztonsági mentése, az Azure Backup támogatja a [Azure által támogatott disztribúciók listáját](../virtual-machines/linux/endorsed-distros.md), 32 bites operációs rendszer és a Core OS Linux kivételével. Más bring-your-own Linux-disztribúciók működnek, amíg a Virtuálisgép-ügynök nem érhető el a virtuális gépen, és támogatja a pythont.


## <a name="are-there-size-limits-for-data-backup"></a>Az adatok biztonsági mentésének mérete korlátozva van?

Méretek korlátok a következők:


Az operációs rendszer/gép | Adatforrás maximális mérete
--- | --- | ---
Windows 8 vagy újabb | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 vagy újabb | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 adatlemez<br/><br/> Adatlemez legfeljebb 4095 GB-ig

## <a name="how-is-the-data-source-size-determined"></a>Hogyan történik az adatforrás mérete határozza meg?

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

**Adatforrás** | **Részletek**
--- | ---
Kötet |Adatok mennyisége egyetlen kötetéről biztonsági mentés alatt álló virtuális gép biztonsági mentése.
SQL Server-adatbázis |Biztonsági mentés alatt álló egyetlen SQL adatbázis méretétől mérete.
SharePoint | A SharePoint-farm biztonsági mentés alatt álló tartalmakat és konfigurációs adatbázisainak összegét.
Exchange |A biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisok összegét.
BMR/rendszerállapot |A biztonsági mentés alatt álló gép BMR vagy rendszerállapot minden egyes másolata.


### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Az adatok biztonsági mentése Recovery Services-tároló használatával mennyisége korlátozva van?

Készíthet biztonsági mentést egy Recovery Services-tároló használatával adatok mennyisége nincs korlátozva van.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted"></a>Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik?
Nem. A biztonsági mentési feladat megszakításának pillanata előtt a tárolóba már átvitt minden adat a tárolóban marad. Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához. Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét. A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

## <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Miért átkerülnek az adatok mérete kisebb, mint a biztonsági mentésre kijelölt adatokat a Recovery Services-tároló?

 Adatok biztonsági mentése az Azure Backup-ügynök, DPM, és az Azure Backup Server van tömörítve és titkosítva kerül az átvitel előtt. A tömörítés és a rendszer titkosítást alkalmaz, a tárolóban lévő adatok 30 – 40 százalékkal kisebbek.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Törölhetem-e az egyes fájlok a tárban lévő helyreállítási pontból?
Nem, az Azure Backup nem támogatja a törlését vagy végleges törlése az egyes elemek tárolt biztonsági másolatokból.


### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Ha megszakítom biztonsági mentési feladatot, miután elindult, az átküldött adatok biztonsági másolata törlődik?

Nem. A biztonsági mentési feladat előtt a tárolóba átvitt minden adat a tárolóban marad megszakítva.
- Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához.
- Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét.
- A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.




## <a name="retention-and-recovery"></a>Megőrzés és helyreállítás

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Megegyeznek az adatmegőrzési szabályokról a DPM és a Windows-gépek DPM nélkül?
Igen, mindkettő rendelkezik napi, heti, havi és éves adatmegőrzési szabályoknál.

### <a name="can-i-customize-retention-policies"></a>Adatmegőrzési házirendek testre?
Igen, testre szabályzatokkal rendelkezik. Konfigurálhatja például hetente és naponta adatmegőrzési követelmények, de nem éves és havi.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Használható a különböző időpontokban készült biztonsági mentés ütemezését és adatmegőrzési szabályzatok?
Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. Például a képek készült biztonsági mentésekre 0 és 18: 00 adatmegőrzési jeleníti meg.

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>A biztonsági másolatok hosszú ideje, ha nem, több időt vesz igénybe egy régebbi adatpont helyreállítása? <br/>
Nem – a legrégebbi és a legújabb pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?

A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják.
    - A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók.
    - Növekményes másolatok tárolás szempontjából *hatékony* , de egy adatlánc, amely befolyásolja a helyreállítási időt visszaállítását

Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez biztosítja, hogy a bejövő és kimenő sávszélesség hatékonyan van felhasználva. Az adattárolás és az adatok helyreállításához szükséges idő mennyisége minimális másolatok. Tudjon meg többet [növekményes biztonsági mentések](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>A létrehozható helyreállítási pontok száma korlátozott?

Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példány egy számítógép, kiszolgáló (fizikai vagy virtuális) vagy munkaterhelést futtatják, amelyről biztonsági másolatot készít az Azure-bA.

- Tudjon meg többet [biztonsági mentés és megőrzés](./backup-introduction-to-azure-backup.md#backup-and-retention).
- Ismerje meg [védett példányok](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>Hány alkalommal lehetőségeket az adatok helyreállítását az Azure-bA biztonsági mentésének?
Az Azure Backupból történő helyreállítások száma korlátlan.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért?
Nem. Helyreállítás az ingyenes és a kimenő forgalom nem kell fizetnie.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot?

Új szabályzat alkalmazásakor, ütemezése és megőrzése az új házirendet kell követni.

- Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak.
- - Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="encryption"></a>Titkosítás

### <a name="is-the-data-sent-to-azure-encrypted"></a>Az Azure-ba küldött adatok titkosítottak?

Igen. Adatok titkosítása az AES256 használatával a helyszíni gépen. Az adatokat a rendszer egy biztonságos HTTPS-kapcsolaton keresztül küldi el. Az adatok átvitele a felhőben csak a tárolási és helyreállítási szolgáltatás közötti HTTPS-kapcsolaton védi. az iSCSI protokoll biztonságossá teszi a helyreállítási szolgáltatás és a felhasználó gép között továbbított adatokat. Az iSCSI-csatorna védelmére Secure tunneling használják.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?

Igen. Az adatok, az Azure-ban a titkosított inaktív.
- A helyszíni biztonsági mentés inaktív titkosítási biztosított Azure-ba való biztonsági mentéshez adjon meg a jelszót használja.
- Az Azure virtuális gépek az adatok titkosítása inaktív Storage Service Encryption (SSE) használatával el.

A Microsoft soha nem fejt vissza adatokat.


### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Mi az, hogy a titkosítás az adatok biztonsági másolatának titkosításához használt kulcs minimális hossza?

Az Azure Backup-ügynök használatakor a titkosítási kulcsnak legalább 16 karakterből kell állnia. Az Azure-beli virtuális gépek esetében nincs korlátozva az Azure KeyVault által használt kulcsok hossza.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Helyreállíthatja az adatokat? A Microsoft helyreállíthatja az adatokat?
Az adatok biztonsági másolatának titkosításához használt kulcs csak a webhelyen megtalálható. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha a kulcs akkor feljegyezte, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

## <a name="next-steps"></a>További lépések

Olvassa el a gyakori kérdések:

- [Gyakori kérdések](backup-azure-vm-backup-faq.md) Azure virtuális gép biztonsági mentéseinek kapcsolatban.
- [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) tudnivalók az Azure Backup-ügynök
