---
title: Gyakran ismételt kérdések az Azure Import/Export szolgáltatásról |} Microsoft Docs
description: Olvasási Azure importálási exportálása szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.openlocfilehash: ed928452946b871ee9192bda82fcbf205b96e6e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660886"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Az Azure Import/Export szolgáltatásról: gyakran ismételt kérdések 
Kérdések és válaszok előfordulhat, hogy az Azure storage az adatok átviteléhez az Azure Import/Export szolgáltatás használatakor a következők: Kérdések és válaszok a következő kategóriákba vannak rendezve:

- Import/Export szolgáltatásról
- Az importálási/exportálási a lemezek előkészítése
- Importálási/exportálási feladatok
- Szállítási lemezek
- Egyéb rendelkezések 

## <a name="about-importexport-service"></a>Import/Export szolgáltatásról

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Másolhatja az Azure Import/Export szolgáltatás használata Azure File storage?

Igen. Az Azure Import/Export szolgáltatás támogatja az Azure File Storage importálja. Azure-fájlok exportálása jelenleg nem támogatja.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Az Azure Import/Export szolgáltatás érhető el a CSP-előfizetések?

Igen. Az Azure Import/Export szolgáltatás támogatja a felhő megoldás szolgáltatók (CSP) előfizetések.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Az Azure Import/Export szolgáltatás segítségével csendes-óceáni TÉLI postaládákhoz és SharePoint-adatok másolása az o365-re?

Igen. További információkért látogasson el [importálási csendes-óceáni TÉLI fájlok vagy az Office 365 SharePoint-adatok](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Az Azure Import/Export szolgáltatás követve másolja át a biztonsági másolatok offline állapotba az Azure Backup szolgáltatás?

Igen. További információkért látogasson el [Offline biztonsági másolat munkafolyamat Azure backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Lehet az importálási/exportálási feladatok meghajtókat is vásárolni a Microsoft?

Nem. Küldje el saját meghajtók importálni és exportálni a feladatokat kell.


## <a name="preparing-disks-for-importexport"></a>Az importálási/exportálási lemezek előkészítése

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Ugorjon a meghajtó előkészítése lépés, hogy az importálás? Képes a meghajtó is előkészítése másolása nélkül?

Nem. Minden meghajtó használhatók, hogy adatokat importáljon elő kell készíteni az Azure WAImportExport eszközzel. Az eszközzel adatokat szeretne másolni a meghajtóra.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Van bármilyen lemez előkészítése exportálási feladat létrehozásakor elvégzéséhez?

Nem. Néhány prechecks használata ajánlott. Ellenőrizze a szükséges lemezek számát, használja a WAImportExport eszköz PreviewExport parancsot. További információkért lásd: [meghajtó használata Előnézet egy exportálása feladat](https://msdn.microsoft.com/library/azure/dn722414.aspx). A parancs segítségével megtekintheti a kijelölt blobokat, a használni kívánt meghajtók mérete alapján meghajtóinak használatát. Is ellenőrizheti, hogy olvasni és írni a merevlemez-meghajtóról, amelyek teljesítik a exportálási feladat.

## <a name="importexport-jobs"></a>Importálási/exportálási feladatok

### <a name="can-i-cancel-my-job"></a>Lehet megszakítani a feladatot?
Igen. Egy feladat megszakíthatja, ha az állapota **létrehozása** vagy **szállítási**. A szakaszok túl nem lehet megszakítani a feladatot, és továbbra is fennáll, addig az utolsó szakaszában.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Mennyi ideig lehet befejezett feladatok állapotának megtekintése az Azure-portálon?
Legfeljebb 90 napig megtekintheti a befejezett feladatokhoz állapotát. Befejezett feladatokhoz 90 nap után törlődnek.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Mi a teendő, ha szeretnék, importálása és exportálása a 10-nél több meghajtó?
Egy importálási vagy exportálási feladat egyetlen feladatban csak 10 meghajtók hivatkozhat. 10-nél több meghajtó szállítási, több feladatot kell létrehoznia. Ugyanazon feladathoz hozzárendelt meghajtók együtt kell szállítani egy csomagban található. További információt és útmutatást, ha az adatok kapacitás több lemezre kiterjedő importálási feladatok, forduljon a Microsoft bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Szállítási lemezek

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Mi az a maximális számát a HDD egy szállítási?
Nincs több szállításra a HDD-k száma nincs korlátozva. Ha több feladat tartozik a lemezeket, azt javasoljuk, hogy Ön: 
- címke a lemezek, amelyek megfelelő feladat nevét.
- Frissítse a feladatok követési számnak -1, a utótaggal-2 stb.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>I bele kell foglalni a HDD csakis a csomagot?
Küldje el a merevlemezen csak a szállítási csomagban. Ne adjon meg például a kiemelt tápkábelek vagy USB-kábelt.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Küldje el a meghajtókat FedEx vagy DHL kell?
Az Azure-adatközpontban használatával FedEx, DHL, UPS vagy Velünk postai bármely ismert vivőjel-meghajtóval elküldhet. Azonban a datacenter Önnek meghajtói visszatérési szállításra, meg kell adnia:

- Az Amerikai Egyesült Államokban és Európa, fiók FedEx számnak vagy
- Ázsia és a Ausztrália régiókban DHL fiók szám.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Vannak-e a nemzetközi a meghajtó szállítási korlátozások?
Vegye figyelembe, hogy valóban a fizikai adathordozókat esetleg kereszt-nemzetközi határokon. Biztos, hogy a fizikai adathordozó és adatok vannak importálva és/vagy az alkalmazandó jogszabályok betartása exportált biztosításáért felelős. Még a szállítás előtt a fizikai adathordozó egyeztetni a tanácsadók annak ellenőrzésére, hogy az adathordozót, és adatok jogilag szállítani a azonosított adatközpontba. Ez segít annak érdekében, hogy időben Microsoft eléri.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Feladat létrehozása a szállítási cím esetén egy helyet, amely eltér a tárfiókhely. Mit tegyek?

Néhány fiók tárolóhelyek szállítási másodlagos helyek vannak leképezve. Korábban elérhető szállítási helyről is ideiglenesen rendelhetők más helyekre. Mindig ellenőrizze a megadott feladat létrehozásakor, a meghajtók, mielőtt szállítási címe.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Ha a meghajtó szállítási, a szolgáltatói a data center címét és telefonszámát a telefonszám kér. Mit kell nyújtania?

A telefonszám és a tartományvezérlő cím valósul meg a projekt létrehozásának részeként.


## <a name="miscellaneous"></a>Egyéb rendelkezések

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Mi történik, ha véletlenül küldése egy HDD nem felel meg a támogatott követelmények?

Az Azure-adatközpont visszatér a meghajtó nem felel meg a támogatott követelményeinek Önnek. Ha csak néhány a meghajtót a csomag megfelel a támogatási követelmények, ezek a meghajtók dolgoz fel, és Önnek visszaadott a meghajtókat, amelyek nem felelnek meg a követelményeknek.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Nem a szolgáltatás formázása előtt azok a meghajtók?

Nem. Az összes meghajtó bitlockerrel titkosított.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hogyan hozzáférhet a szolgáltatás által importált adatoknak?

Az Azure portálon vagy [Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) az Azure storage-fiókjában az adatok eléréséhez.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Az importálás után mi nem saját adatok megjelenését, például a tárfiókban lévő? A könyvtár-hierarchia megőrzi?

Ha a merevlemez-meghajtó előkészítése az importálási feladat, a cél a fürt megosztott kötetei szolgáltatás adatkészlet DstBlobPathOrPrefix mező szerint van megadva. Ez az a cél tároló a storage-fiókot, amelyhez a merevlemez-meghajtóról az adatok másolásakor. A cél tárolóban virtuális könyvtárak mappák a merevlemez-meghajtóról jön létre, és blobok fájlok jönnek létre. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Ha a meghajtó már megtalálható a tárfiókban lévő fájlok, nem a szolgáltatás felülírja a meglévő blobokkal vagy a fájlokat?

Függ. Amikor előkészíti a meghajtót, vagy adhat meg hogy felülírható-e a cél fájlok törlése figyelmen kívül hagyott használatát a mező dataset CSV-fájl neve: < átnevezése |} nem írható felül |} felülírása >. Alapértelmezés szerint a szolgáltatás az új fájlok átnevezése helyett felülírja a meglévő blobokkal vagy a fájlokat.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Egy 32 bites operációs rendszerekkel kompatibilis a WAImportExport eszköz?
Nem. A WAImportExport eszköze csak 64 bites Windows operációs rendszerekkel kompatibilis. A támogatott operációs rendszer teljes listáját lásd [támogatott operációs rendszerek](). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Mi az a maximális Blokkblob és támogatott Azure Import/Export Blob oldalméret?

Maximális Blokkblob mérete körülbelül 4.768TB vagy 5,000,000 MB.
Az oldalakra vonatkozó Blob maximális mérete 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Támogatja az Azure Import/Export AES-256 titkosítás?
Az Azure Import/Export szolgáltatás alapértelmezés szerint AES-128 bitlocker-titkosítást használ. Módosíthatja a AES-256 manuálisan titkosítása a bitlocker előtt a rendszer átmásolja az adatokat. 

- Ha használ [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), az alábbiakban van egy minta parancs
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Ha használ [WAImportExport V2](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) "AlreadyEncrypted" adja meg, és adja meg a fürt megosztott kötetei szolgáltatás driveset a kulcsot.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>További lépések

* [Mi az Azure Import/Export?](storage-import-export-service.md)


