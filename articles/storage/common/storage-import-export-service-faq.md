---
title: Gyakran feltett kérdések az Azure importálási/exportálási szolgáltatással kapcsolatban | Microsoft dokumentumok
description: Olvassa el az Azure Import Export szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 904f93aa705a4e327f29bbec109bdf3b937f6c70
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519483"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure importálási/exportálási szolgáltatás: gyakori kérdések

Az alábbiakban olyan kérdéseket és válaszokat, amelyek az Azure import/export szolgáltatás használatával adatokat továbbítanak az Azure storage-ba. A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Az Importálási/Exportálás szolgáltatás
- A lemezek előkészítése importálásra/exportálásra
- Feladatok importálása/exportálása
- Lemezek szállítása
- Egyéb

## <a name="about-importexport-service"></a>Az Importálási/Exportálás szolgáltatás

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Másolhatom az Azure File storage-ot az Azure importálási/exportálási szolgáltatással?

Igen. Az Azure importálási/exportálási szolgáltatás támogatja az Azure File Storage-ba történő importálást. Jelenleg nem támogatja az Azure Files exportálását.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Elérhető az Azure importálási/exportálási szolgáltatás a CSP-előfizetésekhez?

Igen. Az Azure Import/Export szolgáltatás támogatja a felhőszolgáltató (CSP) előfizetéseket.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Használhatom az Azure import/export szolgáltatást PST-postaládák és SharePoint-adatok másolására az O365-be?

Igen. További információt a [PST-fájlok vagy SharePoint-adatok importálása az Office 365-be](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)című részben talál.

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Használhatom az Azure import/export szolgáltatás átmásolni a biztonsági mentések offline az Azure backup szolgáltatás?

Igen. További információ: [Offline Backup-munkafolyamat az Azure Backup ban.](../../backup/backup-azure-backup-import-export.md)

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Vásárolhatok meghajtókat a Microsofttól az importálási/exportálási feladatokhoz?

Nem. Az importálási és exportálási feladatokhoz saját meghajtókat kell szállítania.

## <a name="preparing-disks-for-importexport"></a>Lemezek előkészítése importálásra/exportálásra

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kihagyhatom az importálási feladat előkészítésének lépéseit? Előkészíthetek egy meghajtót másolás nélkül?

Nem. Az adatok importálásához használt meghajtót az Azure WAImportExport eszközzel kell elkészíteni. Az eszközzel adatokat is másolhat a meghajtóra.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Az exportálási feladat létrehozásakor lemezelőkészítést kell végrehajtanom?

Nem. Néhány előellenőrzés ajánlott. A szükséges lemezek számának ellenőrzéséhez használja a WAImportExport eszköz PreviewExport parancsát. További információ: [A meghajtóhasználat megtekintése exportálási feladathoz című témakörben talál.](https://msdn.microsoft.com/library/azure/dn722414.aspx) A parancs segít a kijelölt blobok meghajtóhasználatának előnézetének megtekintésében, a használni kívánt meghajtók mérete alapján. Ellenőrizze azt is, hogy tud-e olvasni és írni az exportálási feladathoz szállított merevlemezről.

## <a name="importexport-jobs"></a>Feladatok importálása/exportálása

### <a name="can-i-cancel-my-job"></a>Lemondhatom a munkámat?

Igen. A feladatot akkor szakíthatja meg, ha állapota **Létrehozás** vagy **Szállítás.** Ezeken a szakaszokon túl a feladat nem szakítható meg, és az utolsó szakaszig folytatódik.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Mennyi ideig tekinthetem meg a befejezett feladatok állapotát az Azure Portalon?

A befejezett feladatok állapotát legfeljebb 90 napig tekintheti meg. A befejezett feladatok 90 nap elteltével törlődnek.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Ha 10-nél több meghajtót szeretnék importálni vagy exportálni, mit tegyek?

Egy importálási vagy exportálási feladat egyetlen feladatban csak 10 meghajtóra hivatkozhat. 10-nél több meghajtó szállításához több feladatot kell létrehoznia. Az ugyanahhoz a feladathoz társított meghajtókat ugyanabban a csomagban kell szállítani.
Ha további információt és útmutatást szeretne arról, hogy az adatkapacitás haon több lemezimportálási feladat ra terjed ki, forduljon a Microsoft támogatási szolgálatához.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>A feltöltött blob "Lejárt bérlet" állapotot jelenít meg. Mit tegyek?

A "Lejárt bérlet" mezőt figyelmen kívül hagyhatja. Az importálás/exportálás a feltöltés során bérletet kap a blobhoz, hogy megbizonyosodjon arról, hogy egyetlen más folyamat sem tudja párhuzamosan frissíteni a blobot. A lejárt bérlet azt jelenti, hogy az importálás/exportálás már nem töltődik fel, és a blob elérhető az Ön számára.

## <a name="shipping-disks"></a>Lemezek szállítása

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Mi a hdd maximális száma egy szállítmányban?

Egy szállítmányban nincs korlátozva a HDD-k száma. Ha a lemezek több feladathoz tartoznak, a következőket javasoljuk:

- címkével ellátni a lemezeket a megfelelő feladatnevekkel.
- a feladatok frissítése -1, -2 stb.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>A csomagomban szerepeljen a HDD-n kívül más is?

Csak a merevlemezeket szállítsa a szállítási csomagban. Ne tartalmazzon olyan elemeket, mint a tápkábel ek vagy usb-kábelek.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>FedEx vagy DHL segítségével kell szállítanom a meghajtóimat?

A meghajtókat bármely ismert szolgáltató, például a FedEx, a DHL, a UPS vagy az US Postal Service használatával szállíthatja az Azure-adatközpontba. Az adatközpontból történő meghajtók visszaküldéséhez azonban a következőket kell megadnia:

- FedEx számlaszám az Egyesült Államokban és az EU-ban, vagy
- DHL-számlaszám az ázsiai és ausztráliai régiókban.

> [!NOTE]
> Az adatközpontok Indiában szükség egy nyilatkozatot levelet a fejléces (szállítási challan) a meghajtók visszaküldéséhez. A szükséges belépési igazolvány megszervezéséhez le kell foglalnia a felvételeket a kiválasztott szolgáltatónál, és meg kell osztania a részleteket az adatközponttal.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Vannak-e korlátozások a szállítás és a visszatérő meghajtóm nemzetközileg?

Kérjük, vegye figyelembe, hogy a szállított fizikai adathordozónak át kell lépnie a nemzetközi határokat. Ön felelős annak biztosításáért, hogy fizikai adathordozóit és adatait a vonatkozó jogszabályoknak megfelelően importálja és/vagy exportálja. A fizikai adathordozó kontinamán, érdeklődjön a tanácsadóknál, hogy a média- és adatfájlok jogszerűen szállíthatók-e az azonosított adatközpontba. Ez segít annak biztosításában, hogy időben eljusson a Microsofthoz.

A feltöltés befejezése után a meghajtó(k) nemzetközi címre való visszaküldésének folyamata hosszabb időt vehet igénybe, mint a helyi szállításhoz szükséges tipikus 2-3 nap. Az Azure Portalon csomagolásként felsorolt szakaszban a Data Box csapata biztosítja, hogy a megfelelő dokumentáció álljon rendelkezésre annak biztosítására, hogy a szállítmány megfeleljen a különböző nemzetközi import és export követelményeknek.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Vannak-e speciális követelmények a lemezek adatközpontba való kézbesítéséhez?

A követelmények az Azure-adatközpont-korlátozásoktól függenek.

- Vannak néhány helyek, például Ausztrália, Németország és az Egyesült Királyság déli, amelyek megkövetelik a Microsoft adatközpont bejövő azonosító számot kell írni a csomagot biztonsági okokból. Mielőtt a meghajtók vagy lemezek az adatközpontba szállít, lépjenadbops@microsoft.comkapcsolatba az Azure DataBox Operations ( ) a szám lekérődése. E szám nélkül a csomag elutasításra kerül.
- The datacenters in India require the personal details of the driver, such as the Government ID Card or Proof No. (például PAN, AADHAR, DL), név, kapcsolat és az autó rendszáma a kapubelépő áttételéhez. A szállítási késedelmek elkerülése érdekében tájékoztassa a szolgáltatót ezekről a követelményekről.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Feladat létrehozásakor a szállítási cím olyan hely, amely eltér a tárfiókom helyétől. Mit tegyek?

Egyes tárfiókhelyek más szállítási helyekre vannak leképezve. A korábban rendelkezésre álló szállítási helyek ideiglenesen más helyekre is leképezhetők. A meghajtók szállítása előtt mindig ellenőrizze a munkahelyteremtés során megadott szállítási címet.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>A meghajtó szállításakor a szolgáltató kéri az adatközpont telefonszámát és telefonszámát. Mit kell nyújtanom?

A telefonszám és a tartományvezérlő-cím a munkahelyteremtés részeként van megadva.

## <a name="miscellaneous"></a>Egyéb

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Mi történik, ha véletlenül olyan HDD-t küldök, amely nem felel meg a támogatott követelményeknek?

Az Azure adatközpont visszaadja önnek a támogatott követelményeknek meg nem felelő meghajtót. Ha a csomagban lévő meghajtók közül csak néhány felel meg a támogatási követelményeknek, akkor a rendszer feldolgozza ezeket a meghajtókat, és a követelményeknek nem megfelelő meghajtókat visszaküldi Önnek.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>A szolgáltatás formázza a meghajtókat, mielőtt visszaküldi őket?

Nem. Minden meghajtó a BitLocker titkosításával van titkosítva.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hogyan érhetem el a szolgáltatás által importált adatokat?

Az Azure Portalon vagy a [Storage Explorerben](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) keresztül érheti el az Azure storage-fiókjában tárolt adatokat.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Az importálás befejezése után hogyan néznek ki az adataim a tárfiókban? Megmarad a könyvtárhierarchia?

Amikor merevlemezt készít egy importálási feladathoz, a célt a CSV adatkészlet DstBlobPathOrPrefix mezője határozza meg. Ez az a céltároló a tárfiókban, amelyre a merevlemezről származó adatokat másolja. Ebben a céltárolóban virtuális könyvtárak jönnek létre a merevlemezről származó mappákhoz, és blobok jönnek létre a fájlokhoz.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Ha egy meghajtó olyan fájlokat tartalmaz, amelyek már léteznek a tárfiókban, a szolgáltatás felülírja a meglévő blobokat vagy fájlokat?

Függ. A meghajtó előkészítésekor megadhatja, hogy a célfájlokat felül írja-e felül vagy figyelmen kívül hagyja-e az Adathalmaz CSV-fájlban található mező használatával, amelynek neve Adatsor:<átnevezi|nincs felülírás|felülírja>. Alapértelmezés szerint a szolgáltatás átnevezi az új fájlokat ahelyett, hogy felülírja a meglévő blobokat vagy fájlokat.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Kompatibilis a WAImportExport eszköz a 32 bites operációs rendszerekkel?

Nem. A WAImportExport eszköz csak 64 bites Windows operációs rendszerekkel kompatibilis. A támogatott operációs rendszer teljes listáját a Támogatott operációs rendszerek című [lapban](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)lehet megkeresni.

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Mi az Azure importálása/exportálása által támogatott maximális blokkblob- és lapblob-méret?

- A maximális blokkblob mérete körülbelül 4,768 TB vagy 5 000 000 MB.
- A lapblob maximális mérete 8 TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Támogatja az Azure importálási/exportálási támogatása az AES-256 titkosítást?

Nem. Az Azure Import/Export szolgáltatás AES-128 BitLocker titkosítást használ.

## <a name="next-steps"></a>További lépések

* [Mi az Azure Import/Export?](storage-import-export-service.md)
