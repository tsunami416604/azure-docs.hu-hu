---
title: Az Azure Import/Export szolgáltatás – gyakori kérdések |} A Microsoft Docs
description: Olvasási Azure importálási exportálása szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: edaff86531a9c40064d25a046bbbb70f48b75c84
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027129"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Az Azure Import/Export szolgáltatás: gyakori kérdések 
Az alábbiakban a kérdések és válaszok, amelyek az adatok átviteléhez az Azure storage-bA az Azure Import/Export szolgáltatás használatakor előfordulhat, hogy rendelkezik. A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Import/Export szolgáltatásról
- A lemezek előkészítése importálási és exportálási szolgáltatáshoz
- Importálási/exportálási feladat
- A szállítási címhez tartozó lemezek
- Egyéb rendelkezések 

## <a name="about-importexport-service"></a>Import/Export szolgáltatásról

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Másolhatja az Azure Import/Export szolgáltatás használata az Azure File storage?

Igen. Az Azure Import/Export szolgáltatás támogatja az Azure File Storage-bA importálja. Az Azure-fájlok exportálása jelenleg nem támogatja.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Az Azure Import/Export szolgáltatás érhető el a CSP-előfizetésekben?

Igen. Az Azure Import/Export szolgáltatás támogatja a Cloud Solution Providers (CSP) előfizetések.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Az Azure Import/Export szolgáltatás használata PST postaládákhoz és SharePoint-adatok másolása az Office 365?

Igen. További információért ugorjon [importálás PST fájlok vagy az Office 365 SharePoint-adatok](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Az Azure Import/Export szolgáltatás használata a biztonsági mentések offline másolása az Azure Backup szolgáltatással?

Igen. További információért ugorjon [Offline Backup munkafolyamat az Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Vásárolhatok meghajtók importálási és exportálási feladatokhoz a Microsoft?

Nem. Szeretne saját meghajtók importáláshoz szállíthat, és a feladatok exportálása.


## <a name="preparing-disks-for-importexport"></a>Az importálási/exportálási lemezek előkészítése

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kihagyhatja a meghajtó-előkészítési lépésként importálási feladatokhoz? Képes a meghajtó is előkészítése másolása nélkül?

Nem. Bármely meghajtó segítségével az adatok importálása az Azure WAImportExport eszközzel kell készíteni. Az eszköz használatával is az adatok másolása a meghajtóra.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Kell minden olyan lemezt előkészítési hajtható végre, ha a exportálási feladat létrehozása?

Nem. Néhány Eszközfrissítések használata ajánlott. Ellenőrizze a lemezek szükséges számát, használja a WAImportExport eszköz PreviewExport parancsot. További információkért lásd: [meghajtó használata előnézet-exportálás feladat](https://msdn.microsoft.com/library/azure/dn722414.aspx). A parancs segítségével megtekintheti a kiválasztott blobokat, a meghajtók használni kívánja a mérete alapján meghajtóinak használatát. Emellett ellenőrizze, hogy a merevlemezre, amely tartalmazza a szükséges az exportálási feladat írnak és onnan olvasnak.

## <a name="importexport-jobs"></a>Importálási/exportálási feladat

### <a name="can-i-cancel-my-job"></a>A feladat lemondható?
Igen. Amikor állapotú-e egy feladat megszakítható **létrehozása** vagy **szállítási**. Ezen szakaszokban túli feladat nem szakítható meg, és továbbra is fennáll, amíg az utolsó fáziséit.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Mennyi ideig lehet a befejezett feladatok állapotának megtekintése az Azure Portalon?
A befejezett feladatok állapotát megtekintheti a 90 napig. Befejezett feladatok 90 nap után törlődnek.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Ha el szeretne importálni vagy exportálni a több mint 10 meghajtók, mit kell tennem?
Egy importálási vagy exportálási feladatot egyetlen feladatban csak 10 meghajtók hivatkozhat. Több mint 10 meghajtók szállításra, több feladatot kell létrehoznia. Ugyanazon feladathoz hozzárendelt meghajtók együtt kell Önnek egy csomagban található. További információkért és útmutatóért adatkapacitás több lemezre kiterjedő Amikor importálja a feladatok, forduljon a Microsoft bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>A szállítási címhez tartozó lemezek

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Mi az a HDD maximális számát egy a szállítmány?
A szállítmány egy HDD-k száma nincs korlátozva van. Ha több feladat tartozik a lemezeket, azt javasoljuk, hogy Ön: 
- a lemezek megfelelő feladat nevű címkét.
- Frissítse a feladatok utótaggal -1, követési számnak-2 stb.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>E tartalmaznia kell a HDD csakis a csomag?
A szállítási csomagban csak rögzített meghajtók kiszállítása. Például a tápkábelek ellátási vagy USB-kábelt nem tartalmazzák.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Rendelkezik a meghajtók FedEx vagy DHL szállításra?
Az Azure-adatközpontban semmilyen ismert szolgáltatója FedEx, DHL, UPS vagy USA postai például meghajtókon küldhet el. Azonban a meghajtó Önnek az adatközpontból visszaszállításhoz kell megadnia:

- Az USA és Európa, a fiók FedEx számnak vagy
- Az ázsiai és Ausztrália régiókban DHL fiók szám.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Vannak az Egyesült Államokon kívül is a meghajtó szállítási korlátozások?
Vegye figyelembe, hogy valóban a fizikai adathordozón kell adatbázisközi nemzetközi határokon. Ön felelős azért, hogy a fizikai adathordozók és az adatok vannak importálva és/vagy a hatályos törvényeknek megfelelően exportált. A fizikai adathordozó hordozás előtt ellenőrizze a tanácsadók, győződjön meg arról, hogy az adathordozó és az adatok is jogilag szállítása az azonosított adatközpont. Ez segít annak érdekében, hogy a Microsoft időben éri el.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Egy feladat létrehozásakor, a szállítási cím egy olyan hely, a tárfiók helye eltérő. Mit tegyek?

A szállítási címhez tartozó másodlagos helyek bizonyos tárolási fiók helyek vannak leképezve. Korábban elérhető szállítási helyek is ideiglenesen rendelhetők más helyekre. Mindig ellenőrizze a szállítási cím, a meghajtók, mielőtt a feladat létrehozása során megadott.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Szállítási a meghajtó, amikor a szolgáltató a data center címét és telefonszámát a telefonszám kér. Mit kell biztosítania?

A telefonszám és a tartományvezérlő-cím van feladat létrehozásának részeként.


## <a name="miscellaneous"></a>Egyéb rendelkezések

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Mi történik, ha véletlenül küld egy HDD, amely nem felel meg a támogatott követelményeknek?

Az Azure-adatközpontban visszaküldi a meghajtó, amely nem felel meg a támogatott követelményeket is. Ha csak néhány, a csomagot a meghajtók felel meg a támogatási követelmények, a program feldolgozza az adott meghajtókat, és a meghajtókat, amelyek nem felelnek meg a követelményeknek, visszakerül.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Nem a szolgáltatás formázása előtt, hogy azok a meghajtók?

Nem. Az összes meghajtó van titkosítva, a BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hogyan érhetem el a szolgáltatás által importált adatoknak?

Az Azure Portal vagy [Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) férhet hozzá az adatokhoz az Azure storage-fiókban.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Az importálás befejezése után, mi nem saját adatok néz ki a storage-fiókban? Megőrződik a könyvtár-hierarchia?

Ha a merevlemez-meghajtó előkészítése importálási feladatokhoz, a cél az adatkészlet CSV DstBlobPathOrPrefix mező szerint van megadva. Ez az a cél tároló storage-fiókban, amelyhez a merevlemez származó adatokat másolja. A cél-tárolón belül a virtuális könyvtárak mappákat a merevlemez-meghajtóról jön létre, és blobok fájlok jönnek létre. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Ha egy meghajtó fájl már létezik a storage-fiókom van, nem a szolgáltatás felülírja a meglévő blobok és fájlok?

Függ. Amikor előkészíti a meghajtót, megadhatja a cél fájlok felülírható akár Disposition figyelmen kívül hagyott mező használatával adatkészlet CSV-fájl neve: < átnevezése |} nem írja felül |} felülírása >. Alapértelmezés szerint a szolgáltatás az új fájlok átnevezése helyett meglévő blobok és fájlok felülírása.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Az a WAImportExport eszköz kompatibilis az operációs rendszerek 32 bites?
Nem. A WAImportExport eszköze csak 64 bites Windows operációs rendszerekkel kompatibilis. Támogatott operációs rendszer teljes listájának megtekintéséhez keresse fel [támogatott operációs rendszerek](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Mi az a maximális Blokkblobok és a támogatott az Azure Import/Export Lapblob méretét?

Blokkblob maximális mérete körülbelül 4.768 TB-os vagy 5,000,000 MB.
Lapblob maximális mérete 1 TB-ot.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Támogatja az Azure Import/Export AES-256 titkosítással?
Az Azure Import/Export szolgáltatás alapértelmezés szerint AES-128 bitlocker-titkosítást használ. Módosíthatja ezt az AES-256 manuálisan titkosítása a bitlocker előtt a rendszer az adatokat másolja. 

- Ha használ [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), az alábbi, egy mintául szolgáló parancs
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Ha használ [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) "AlreadyEncrypted" adja meg, és adja meg a fürt megosztott kötetei szolgáltatás driveset a kulcsot.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>További lépések

* [Mi az Azure Import/Export?](storage-import-export-service.md)


