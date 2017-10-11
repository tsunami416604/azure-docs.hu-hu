---
title: "Merevlemez-meghajtók előkészítése az Azure Import/Export importálási feladat |} Microsoft Docs"
description: "Ismerje meg, hogyan készíti elő az Azure Import/Export szolgáltatás importálási feladat létrehozása a WAImportExport eszközzel merevlemez-meghajtókat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 23f2640bc71bc4eba0f3fc76014cce4a298bfcfa
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Merevlemez-meghajtók előkészítése az importálási feladat

A WAImportExport eszköz a meghajtó előkészítése és a javítás eszköz, amely használható a [Microsoft Azure Import/Export szolgáltatás](../storage-import-export-service.md). Az eszköz segítségével történő egy Azure-adatközpontban fog merevlemezek adatok másolása. Az importálási feladat befejezése után az eszköz segítségével javítsa ki, hogy megsérült, hiányoztak vagy ütköző bármely blobok a más BLOB. Miután a meghajtók kapott egy befejezett exportálási feladat, az eszköz segítségével javítsa ki azokat a fájlokat, hibás vagy hiányzik a meghajtó a volt. Ez a cikk azt ismerteti az eszköz használatát is.

## <a name="prerequisites"></a>Előfeltételek

### <a name="requirements-for-waimportexportexe"></a>WAImportExport.exe követelményei

- **Számítógép-konfiguráció**
  - Windows 7, Windows Server 2008 R2 vagy újabb Windows operációs rendszert
  - .NET framework 4 telepítve kell lennie. Lásd: [gyakran ismételt kérdések](#faq) való annak ellenőrzése, hogy a .net Framework telepítve a számítógépen.
- **Tárfiók kulcsa** -a kulcsait legalább egyike szükséges a tárfiók.

### <a name="preparing-disk-for-import-job"></a>Lemez előkészítése az importálási feladat

- **A BitLocker -** Bitlockernek engedélyezve kell lennie azon a számítógépen, amelyen a WAImportExport eszköz. Tekintse meg a [gyakran ismételt kérdések](#faq) arról, hogyan engedélyezheti a Bitlockert.
- **Lemezek** érhető el a gépen, amelyen WAImportExport eszközt futtatja. Lásd: [gyakran ismételt kérdések](#faq) lemez megadását.
- **Forrásfájlokat** -a fájlok importálását tervezi a másolási számítógépről elérhetőnek kell lennie, hogy egy hálózati megosztásra vagy egy helyi merevlemezen lévő.

### <a name="repairing-a-partially-failed-import-job"></a>A részlegesen sikertelen importálási feladat javítása

- **Másolás naplófájl** , amely jön létre, amikor az Azure Import/Export szolgáltatás közötti Tárfiók és a lemez adatainak másolása. A céloldali tárfiók található.

### <a name="repairing-a-partially-failed-export-job"></a>A részlegesen sikertelen exportálási feladat javítása

- **Másolás naplófájl** , amely jön létre, amikor az Azure Import/Export szolgáltatás közötti Tárfiók és a lemez adatainak másolása. A forrás tárolási fiókjában található.
- **A jegyzékfájlnak** -exportált meghajtón, a Microsoft által visszaadott [opcionális] Located.

## <a name="download-and-install-waimportexport"></a>Töltse le és telepítse a WAImportExport

Töltse le a [WAImportExport.exe legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=55280). Bontsa ki a tömörített tartalmat, hogy egy könyvtárat a számítógépen.

A következő feladata a CSV-fájlok létrehozása.

## <a name="prepare-the-dataset-csv-file"></a>A dataset CSV-fájl előkészítése

### <a name="what-is-dataset-csv"></a>Mi az az adatkészlet CSV

A DataSet CSV-fájl /dataset jelző értéke könyvtárak listája és/vagy a cél-meghajtók másolandó fájlok listáját tartalmazó CSV-fájl. Az importálási feladat létrehozásának első lépése, hogy határozzák meg, mely könyvtárak és fájlok importálni kívánja. Ez lehet könyvtárak listája, egyedi fájlok listáját, vagy ezeket a kettő kombinációja. Része egy könyvtárat, ha minden fájl a könyvtárban és annak alkönyvtáraiban lesz az importálási feladat részeként.

Minden könyvtárat vagy fájlt, importálandók meg kell adni a cél virtuális könyvtárat vagy a blob az Azure Blob szolgáltatásban. Ezeken a célokon fogja használni a WAImportExport eszköz bemeneteként. Könyvtárak kell tagolt, a perjel karakter a "/".

A következő táblázatban néhány példa a blob-tárolók:

| Forrás-fájl vagy könyvtár | Cél blob vagy virtuális könyvtár |
| --- | --- |
| H:\Video | https://mystorageaccount.BLOB.Core.Windows.NET/video |
| H:\Photo | https://mystorageaccount.BLOB.Core.Windows.NET/Photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.BLOB.Core.Windows.NET/Music |

### <a name="sample-datasetcsv"></a>A minta dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Adatkészlet CSV-fájl mezők

| Mező | Leírás |
| --- | --- |
| BasePath | **[Szükséges]**<br/>Ez a paraméter értékének a forrás, ahol az adatokat, importálandók jelöli. Az eszköz fogja rekurzív módon másolása az elérési út alatt található összes adatot.<br><br/>**Megengedett értékek**: Ez egy érvényes elérési utat a helyi számítógépen vagy egy érvényes elérési utat, és elérhető-e a felhasználó kell. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie. Ha az elérési út végződik "\\", a befejezési nélkül elérési utat képvisel más könyvtár"\\" fájlt jelöli.<br/>Ez a mező nem regex engedélyezett. Ha az elérési út szóközöket tartalmaz, tegye "".<br><br/>**Példa**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Szükséges]**<br/> A Windows Azure-tárfiókban lévő a cél virtuális könyvtár elérési útja. Előfordulhat, hogy a virtuális könyvtár, vagy előfordulhat, hogy már nem létezik. Ha még nem létezik, Import/Export szolgáltatás egyet fog létrehozni.<br/><br/>Ne feledje el érvényes tároló-neveket használja a cél virtuális címtárak vagy blobot megadásakor. Ne feledje, hogy a tároló nevének kisbetűnek kell lennie. A tároló elnevezési szabályait, lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Ha csak a legfelső szintű meg van adva, a forrás könyvtárszerkezete replikálja a rendszer a cél blob a tárolóban. Ha egy másik könyvtárstruktúrát van szükség a forrás, leképezés CSV-ben több sort<br/><br/>Megadhat egy tárolót, vagy egy blob előtagja például zene/70s /. A célkönyvtár a tároló neve követ perjellel kell kezdődnie. "/", és előfordulhat, hogy választhatóan blob virtuális könyvtár nem végződik "/".<br/><br/>Ha a céltárolója a legfelső szintű tárolója, meg kell adnia a legfelső szintű tárolót, mint a $root a perjelet is beleértve /. Mivel a gyökérszintű tárolóban blobok nem tartalmazhatnak "/" a név a forráskönyvtárban alkönyvtáraiban nem kerülnek a legfelső szintű tároló esetén a célkönyvtárat.<br/><br/>**Példa**<br/>A cél blob elérési út https://mystorageaccount.blob.core.windows.net/video, ha ez a mező értékének videó lehet /  |
| BlobType | **[Választható]**  blokk &#124; a lap<br/>Jelenleg Import/Export szolgáltatás használatát támogatja 2 blobokat. Blobok és blokk BlobsBy alapértelmezett összes fájlokat importálni szeretné blokkblobként lapon. És \*.vhd és \*.vhdx importálódnak, mert a lap BlobsThere korlátozást a blokk-blob és lapblobterület megengedett méretet. Lásd: [Storage méretezhetőségi célok](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files) további információt.  |
| Törlése | **[Választható]**  átnevezése &#124; nem írható felül &#124; felülírása <br/> Ebben a mezőben az a másolási viselkedés importálási Egytényezős során Ha adatok töltenek fel a tárfiók a lemezről. Lehetőségek a következők: átnevezése &#124; felülírja &#124; nem írható felül. Alapértelmezés szerint az "átnevezése", ha nincs megadva. <br/><br/>**Nevezze át**: Ha azonos névvel rendelkező objektum, a rendszer létrehoz egy másolatot a cél.<br/>Felülírása: újabb fájl felülírja a fájlt. A fájl utolsó módosításának WINS.<br/>**Nem írható felül**: átugrása, ha már jelen van a fájl írása.|
| MetadataFile | **[Választható]** <br/>Ez a mező értéke a metaadatfájl, amelyen megadható, ha az meg kell őrizni az objektumok metaadatait, vagy adjon meg egyéni metaadat. A cél blobokat a metaadatok fájl elérési útja. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információ |
| PropertiesFile | **[Választható]** <br/>A fájl elérési útját tulajdonság a cél BLOB objektumokhoz. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Készítse elő a InitialDriveSet vagy AdditionalDriveSet CSV-fájl

### <a name="what-is-driveset-csv"></a>Mi az a fürt megosztott kötetei szolgáltatás driveset

A /InitialDriveSet vagy /AdditionalDriveSet jelző értéke, amelyhez a meghajtó-betűjelek van leképezve, úgy, hogy az eszköz megfelelően ki tudja választani készüljön lemezek listáját lemezek listáját tartalmazó CSV-fájlból. Ha az adatok mérete nagyobb, mint a egyetlen lemez méretét, a WAImportExport eszköz kiosztja az adatok több lemezre bejegyezve a CSV-fájlban található optimalizált módon.

Az is lehet adatokat írni az egyetlen munkamenetben lemezek száma korlátozva van. Az eszköz fogja kiosztani a lemez mérete és a mappa mérete alapján. A lemezen, amely a legtöbb kiválaszt a objektumméret optimalizálva. Az adatok feltöltése a tárfiókba vissza a könyvtárstruktúra dataset fájlban megadott konvergált. A fürt megosztott kötetei szolgáltatás driveset létrehozásához kövesse az alábbi lépéseket.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Egyszerű kötet létrehozása és a meghajtóbetűjelet rendelje

Egyszerű kötet létrehozásához, és rendeljen meghajtóbetűjelet, a "Egyszerűbb partíció létrehozása" regisztrációkor cikk utasításait követve [Lemezkezelés áttekintése](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>A minta InitialDriveSet és AdditionalDriveSet CSV-fájl

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Driveset CSV-fájl mezők

| Mezők | Érték |
| --- | --- |
| Meghajtóbetűjel | **[Szükséges]**<br/> Minden olyan meghajtó, amely a alatt az eszköz, a cél egy egyszerű NTFS-kötetre, és a hozzá rendelt meghajtóbetűjelet kell rendelkeznie.<br/> <br/>**Példa**: R vagy r |
| FormatOption | **[Szükséges]**  Formátum &#124; AlreadyFormatted<br/><br/> **Formátum**: Adja meg a formázza a lemezen található összes adatot. <br/>**AlreadyFormatted**: az eszköz kihagyja, ha ezt az értéket. |
| SilentOrPromptOnFormat | **[Szükséges]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: Ez az érték megadása lehetővé teszi a felhasználó az eszköz futtatásához csendes módban. <br/>**PromptOnFormat**: az eszköz fogja kérni a felhasználót, hogy erősítse meg, hogy a művelet minden formátum valóban irányul.<br/><br/>Ha nincs megadva, parancs fog megszakítása és hibaüzenet megjelenítése: "beállítás értéke helytelen SilentOrPromptOnFormat: nincs" |
| Titkosítás | **[Szükséges]**  Titkosítása &#124; AlreadyEncrypted<br/> Ez a mező értékének úgy dönt, mely lemez titkosításához, és amelyek nem arra. <br/><br/>**Titkosítani**: eszköz formázza a meghajtót. Ha a "FormatOption" mező értéke "Formátum" majd ennek az értéknek kell lennie "Titkosítás". "AlreadyEncrypted" meg van adva ebben az esetben, ha okoz hiba történt "Formátum megadása esetén titkosítása is meg kell adni".<br/>**AlreadyEncrypted**: eszköz lesz vissza a meghajtót, a "ExistingBitLockerKey" mezőben megadott BitLockerKey használatával. Ha a "FormatOption" mező értéke "AlreadyFormatted", majd ez az érték lehet "Titkosítás" vagy "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Szükséges]**  Ha "Titkosítás" mező értéke "AlreadyEncrypted"<br/> Ez a mező értéke az adott lemezt társított a BitLocker-kulcsot. <br/><br/>Ezt a mezőt üresen, ha a "Titkosítás" mező értéke "Titkosítás" kell hagyni.  A BitLocker kulcs van megadva ebben az esetben, ha okoz hiba történt "Bitlocker kulcs nem adható meg".<br/>  **Példa**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Lemez előkészítése az importálási feladat

Meghajtók előkészítése az importálási feladat, hívja meg a WAImportExport eszközben a **PrepImport** parancsot. Mely paraméterek is függ, hogy ez-e az első másolás munkamenet, vagy egy későbbi másolási munkamenet.

### <a name="first-session"></a>Első munkamenet

Első másolás munkamenet Single/Multiple könyvtár átmásolása egy egyetlen vagy több lemez (attól függően, hogy mi a CSV-fájlban van megadva) WAImportExport eszköz az első másolás munkamenet könyvtárak és/vagy egy új példány munkamenet-fájlok másolása PrepImport parancs:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adatok hozzáadása a következő munkamenet

A következő másolási-munkamenetekben nem kell megadnia a kezdeti paramétereket. Kell használnia az eszköz a napló fájl megjegyezhető, ahol korábban az előző munkamenetben. A másolási munkamenet állapota a napló fájl írása. Ez a szintaxis és a további címtárakat, vagy fájlok másolása későbbi másolási munkamenet:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Adja hozzá meghajtók legújabb-munkamenethez

Ha az adatok nem fértek InitialDriveset a meghajtók, egy a eszköz segítségével adja hozzá további meghajtók azonos másolási munkamenethez. 

>[!NOTE] 
>A munkamenet-azonosítót meg kell felelnie az előző munkamenet-azonosító. Naplófájl meg kell felelnie az előző munkamenet megadottal.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Példa**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>A legutóbbi munkamenet megszakítása:

Ha egy másolás munkamenet megszakad, és nincs lehetőség (Ha például forráskönyvtárat bizonyult nem érhető el) folytatni, kell megszakítás a jelenlegi munkamenet, hogy azt vissza lesz vonva, és új másolási munkamenetek indíthatók el:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Csak az utolsó másolás munkamenethez, ha a rendellenesen, is megszakítja. Vegye figyelembe, hogy megszakítása nem lehetséges az első másolás munkamenet meghajtóhoz. Ehelyett újra kell indítani a a másolási munkamenet egy új napló-fájllal.

### <a name="resume-a-latest-interrupted-session"></a>A legújabb megszakított munkamenet folytatása

Ha a Másolás munkamenet bármilyen okból megszakad, folytathatja az eszköz csak megadott napló fájl futtatásával:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> A másolási munkamenet visszatértekor ne módosítsa a forrás adatok fájlok és könyvtárak hozzáadásával vagy eltávolításával a fájlokat.

## <a name="waimportexport-parameters"></a>WAImportExport paraméterek

| Paraméterek | Leírás |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Szükséges**<br/> A naplófájl elérési útja. A naplófájl nyomon követi a meghajtók készlete, és előkészítése során ezek a meghajtók állapotát rögzíti. A naplófájl mindig meg kell adni.  |
|     / logdir:&lt;LogDirectory&gt;  | **Választható**. A naplózási könyvtár.<br/> Részletes naplófájlok, valamint bizonyos fájlokat a könyvtárba lesz írva. Ha nem a megadott, a jelenlegi könyvtárat fogja használni, mint a naplózási könyvtár. A naplózási könyvtár napló ugyanazt a fájlt csak egyszer adható meg.  |
|     /ID:&lt;munkamenet-azonosító&gt;  | **Szükséges**<br/> A munkamenet-azonosítót használja a Másolás-munkamenet. A pontos egy megszakított másolási munkamenet-helyreállítás szolgál.  |
|     / ResumeSession  | Választható. Ha az utolsó másolás munkamenet rendellenesen megszakadt, ezzel a paraméterrel adható meg folytatni a munkamenetet.   |
|     / AbortSession  | Választható. Ha az utolsó másolás munkamenet rendellenesen megszakadt, ezzel a paraméterrel adható meg a munkamenet megszakítása.  |
|     /sn:&lt;StorageAccountName&gt;  | **Szükséges**<br/> Csak a RepairImport és RepairExport alkalmazható. A tárfiók neve.  |
|     /SK:&lt;StorageAccountKey&gt;  | **Szükséges**<br/> A tárfiók kulcsa. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Szükséges** amikor fut az első másolás munkamenet<br/> A CSV-fájl előkészítése meghajtók listáját tartalmazza.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Szükséges**. Meghajtó másolása aktuális munkamenetre hozzáadásakor. <br/> A CSV-fájl fel kell venni a további meghajtók listáját tartalmazza.  |
|      r:&lt;RepairFile&gt; | **Szükséges** RepairImport és RepairExport csak érvényes.<br/> A javítási folyamat nyomkövetési fájl elérési útja. Minden olyan meghajtó meg kell adni egy javítási fájlt.  |
|     / d:&lt;TargetDirectories&gt; | **Szükséges**. Csak a RepairImport és RepairExport alkalmazható. A RepairImport legalább egy pontosvesszővel elválasztott könyvtárak kijavításához; RepairExport, egy címtárban javítására, pl. legfelső az a meghajtó.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Szükséges** RepairImport és RepairExport csak érvényes. A meghajtó másolása naplófájl elérési útja (részletes vagy hiba).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Szükséges** RepairExport csak alkalmazható.<br/> A meghajtó Alkalmazásjegyzék-fájl elérési útja.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Választható**. Csak a RepairImport alkalmazható.<br/> A fájl elérési útját tartalmazó fájl elérési utak helyekre tényleges fájlok (tabulátorral tagolt) a meghajtó gyökeréhez viszonyítva. Először adja meg, ha azt tölti fel elérési utat a üres célkitűzések, ami azt jelenti, vagy nem található TargetDirectories, a hozzáférés megtagadva a érvénytelen a neve, vagy több könyvtárban vannak. Az elérési út térkép fájl manuálisan módosítani őket a megfelelő cél elérési útját és megadott újra az eszköz megfelelően feloldani a fájlelérési út.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Szükséges**. Csak a PreviewExport alkalmazható.<br/> Az XML-fájl elérési útja blob elérési utak listája tartalmazó fájlt, vagy a blob-elérési út-előtagokat a blobok exportálható. A fájl formátuma megegyezik az Import/Export szolgáltatás REST API-t a feladat Put műveletben a blob blob formátuma.  |
|     / DriveSize:&lt;DriveSize&gt; | **Szükséges**. Csak a PreviewExport alkalmazható.<br/>  Az exportált használható meghajtók mérete. Ha például 500 GB 1,5 TB. Megjegyzés: 1 GB 1 000 hívás = 000 000 bytes1 TB 1,000,000,000,000 bájt =  |
|     / Adatkészlet:&lt;dataset.csv&gt; | **Szükséges**<br/> Könyvtárak listája és/vagy a cél-meghajtók másolandó fájlok listáját tartalmazó CSV-fájl.  |
|     /silentmode  | **Választható**.<br/> Ha nincs megadva, az a követelmény a meghajtók figyelmeztetni, illetve hányat kell folytatja a megerősítés.  |

## <a name="tool-output"></a>Eszköz kimeneti

### <a name="sample-drive-manifest-file"></a>Minta meghajtó jegyzékfájl

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Minden olyan meghajtó minta napló fájl (XML)

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Minta munkamenetet, amely rögzíti az eljárást a munkamenetek napló fájlt (JRN)

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>GYIK

### <a name="general"></a>Általános kérdések

#### <a name="what-is-waimportexport-tool"></a>Mi az WAImportExport eszköz?

A WAImportExport eszköze a meghajtó előkészítése és a javítás eszköz, amely a Microsoft Azure Import/Export szolgáltatás használata. Az eszköz segítségével történő egy Azure-adatközpontban fog merevlemezek adatok másolása. Az importálási feladat befejezése után az eszköz segítségével javítsa ki, hogy megsérült, hiányoztak vagy ütköző bármely blobok a más BLOB. Miután a meghajtók kapott egy befejezett exportálási feladat, az eszköz segítségével javítsa ki azokat a fájlokat, hibás vagy hiányzik a meghajtó a volt.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Hogyan működik a WAImportExport eszköz több forrás dir és lemezek?

Ha az adatok mérete a lemez mérete nagyobb, a WAImportExport eszköz optimalizált módon fogja kiosztani az adatokat a lemezeken. Az adatok másolását több lemezére párhuzamosan vagy egymás után végezhető. Az is lehet adatokat írni egy időben lemezek száma korlátozva van. Az eszköz fogja kiosztani a lemez mérete és a mappa mérete alapján. A lemezen, amely a legtöbb kiválaszt a objektumméret optimalizálva. Az adatok feltöltése a tárfiókba vissza a megadott könyvtárstruktúrát konvergált.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Hol találok WAImportExport eszköz korábbi verzióját?

WAImportExport az eszköz összes funkciói láthatók, amelyekről WAImportExport V1 eszköz rendelkezik. WAImportExport eszköz lehetővé teszi a felhasználóknak adjon meg több és több meghajtó írni. Emellett egy, könnyedén kezelhető, amelyből az adatoknak kell másolni egy CSV-fájl több forráshelyet. Azonban esetben meg kell SAS segítségét, vagy az egy forrásból másolása egy lemezt, akkor [letöltheti WAImportExport V1-es eszköz] szeretné (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid = 0x409), majd tekintse át [WAImportExport V1 hivatkozás](storage-import-export-tool-how-to-v1.md) WAImportExport V1 felhasználású segítségét.

#### <a name="what-is-a-session-id"></a>Mi az a munkamenet-azonosító?

Az eszköz a másolási munkamenet vár (/ azonosítója) paraméter azonosnak kell lennie, ha a szándéka az, hogy az adatok több lemezre terjednek. A Másolás-munkamenet neve azonos karbantartása lehetővé teszi a felhasználói adatok másolása egy vagy több adatforrás helyének be egy vagy több cél lemezek vagy könyvtárakat. Azonos munkamenet-azonosító karbantartása lehetővé teszi az eszköz, ahol legutóbbi hagyták-fájlok a Másolás átvételéhez.

Egy példány munkamenet azonban nem használható eltérő tárfiókokból importálja az adatokat.

Amikor másolatot-munkamenet neve nem azonos több frissítési kísérletei során az eszköz, a naplófájl (/ logdir) és a tárfiók kulcsa (/ sk) is várt azonosnak kell lennie.

Munkamenet-azonosító tartalmazhat betűket, 0 ~ 9, understore (\_), kötőjelet (-) vagy a kivonatoló (#), és a hossza lehet 3 ~ 30.

például a munkamenet-1 vagy a munkamenet #1 vagy a munkamenet\_1

#### <a name="what-is-a-journal-file"></a>Mi az, hogy a napló-fájl?

Minden alkalommal, amikor a fájlok másolása a merevlemez-meghajtóról, WAImportExport eszközt futtatja az eszköz létrehoz egy másolatot munkamenet. A másolási munkamenet állapota a napló fájl írása. Ha a Másolás munkamenet megszakad (például mert a rendszer az áramellátás megszakadása miatt), akkor az eszköz újra és megadhatja a napló fájlt a parancssorból folytathatók.

Az egyes merevlemez-meghajtóról, amely előkészítheti az Azure Import/Export eszközzel, az eszköz létrehoz egy egyetlen napló fájlt nevű "&lt;meghajtót&gt;.xml" meghajtó azonosítója helyére társított meghajtót, a lemez beolvassa az eszköz sorozatszámát. Az összes, a meghajtók, az importálási feladat létrehozásához szüksége lesz az adatbázisnapló-fájlok. A naplófájl meghajtó előkészítése folytatni, ha az eszköz megszakad is használható.

#### <a name="what-is-a-log-directory"></a>Mi az az naplókönyvtár?

A naplózási könyvtár határozza meg azt, hogy részletes naplókat, valamint az ideiglenes fájlok tárolására. Ha nincs megadva, a naplózási könyvtár az aktuális könyvtárban lesz. A részletes naplókat feldolgozásra.

### <a name="prerequisites"></a>Előfeltételek

#### <a name="what-are-the-specifications-of-my-disk"></a>Mik a lemez specifikációk?

Egy vagy több üres 2.5-es vagy 3,5 hüvelykes SATAII vagy III vagy SSD meghajtók a másolási géphez csatlakoztatva.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Hogyan engedélyezhetem a BitLocker a gépen?

Ellenőrizze legegyszerűbb kattintson a jobb gombbal a rendszermeghajtón. Megjelenik a BitLocker beállítások Ha a funkció be van kapcsolva. Ha ki van kapcsolva, akkor nem látható.

![A BitLocker ellenőrzése](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Íme egy cikk a [BitLocker engedélyezése](https://technet.microsoft.com/library/cc766295.aspx)

Akkor lehet, hogy a számítógép nem rendelkezik TPM lapka használata. Ha nem jelenik meg egy kimeneti TPM.msc parancsot használja, tekintse meg a következő gyakran ismételt kérdések.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Hogyan lehet letiltani a platformmegbízhatósági modul (TPM) a BitLocker?
> [!NOTE]
> Csak akkor, ha nincs TPM szerepel a kiszolgálókra, tiltsa le a TPM házirend szeretné. Nincs szükség a TPM letiltása, ha a felhasználó server megbízható TPM-mel. 
> 

Ahhoz, hogy letiltja a BitLocker a TPM, végrehajtania az alábbi lépéseket:<br/>
1. Indítsa el **Helyicsoportházirend-szerkesztő** gpedit.msc parancsot a parancssorba beírásával. Ha **Helyicsoportházirend-szerkesztő** úgy tűnik, hogy nem érhető el, a BitLocker engedélyezéséhez először. Lásd az előző gyakran ismételt kérdések.
2. Nyissa meg **helyi számítógép-házirend &gt; számítógép konfigurációja &gt; felügyeleti sablonok &gt; Windows-összetevők&gt; a BitLocker meghajtótitkosítás &gt; operációsrendszer-meghajtók**.
3. Szerkesztés **indításkor további hitelesítést** házirend.
4. A házirend beállítása **engedélyezve** , és győződjön meg arról, hogy **BitLocker engedélyezése kompatibilis TPM nélküli** be van jelölve.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Hogyan lehet .NET 4-es vagy újabb verziójú meglétének ellenőrzése a számítógépen?

A következő könyvtár telepített összes Microsoft .NET-keretrendszer verziója: %windir%\Microsoft.NET\Framework\

Nyissa meg a fent említett része a célszámítógépen, amelyen az eszközt kell futtatni. Keresse meg "v4" kezdetű mappa nevét. Nincs ilyen könyvtár azt jelenti, hogy a .NET 4 nincs telepítve a számítógépre. A gép használatával is letöltheti a .net-4 [Microsoft .NET-keretrendszer 4 (webes telepítő)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Korlátok

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Hány meghajtókat is szeretnék előkészítése vagy küldése egy időben?

Nincs korlát, amely előkészítheti az eszköz lemezek számát. Azonban az eszköz meghajtóbetűjelek bemeneteként vár. Amely korlátozza a 25 lemez egyidejű előkészítése. Egyetlen feladat egyszerre legfeljebb 10 lemezre kezelésére képes. Ha több mint 10 lemezek célzó ugyanazt a tárfiókot, a lemezek között több feladat is terjeszthetők.

#### <a name="can-i-target-more-than-one-storage-account"></a>Célozhatok egynél több tárfiókot?

Csak egy tárfiók küldheti el feladat és egy eredeti munkamenetben.

### <a name="capabilities"></a>Funkciók

#### <a name="does-waimportexportexe-encrypt-my-data"></a>Nem WAImportExport.exe az adatok titkosítása?

Igen. A bitlockernek engedélyezve van, és ez a folyamat szükséges.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>A hierarchia adat mi lesz, amikor az megjelenik a tárfiókban lévő?

Bár az adatok lemezek között van elosztva, az adatok feltöltése a tárfiókba lesz átszervezett vissza a dataset CSV-fájlban megadott könyvtárstruktúrát a.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>A bemeneti lemezek aktív IO párhuzamosan, akkor lesz másolása folyamatban van?

Az eszköz adatainak elosztja a bemeneti lemezeket a bemeneti fájlok mérete alapján. Említett, a bemeneti adatok jellegétől teljesen delends a párhuzamos aktív lemezek számát. Attól függően, hogy a bemeneti adatkészlet egyes fájlok méretét egy vagy több lemezt is megjelenítése a aktív IO párhuzamosan. Következő kérdés további részletekért tekintse meg.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Hogyan nem elosztani az eszköz a fájlokat a lemezek között?

WAImportExport eszköz beolvassa és kötegelt által kötegelt fájlok egy kötegelt maximális értékének 100000 fájlokat tartalmazza. Ez azt jelenti, hogy maximális 100000 fájlok írhatók párhuzamos. Több lemez kerülnek egyidejűleg Ha ezeket a fájlokat 100000 továbbítja őket a többszörös meghajtókat. Azonban e az eszköz ír több lemez is egyszerre, vagy egyetlen lemez függ a köteg összesített mérete. Például esetén a kisebb fájlok 10,0000 fájlok összes tudja, hogy elférjen egyetlen meghajtóval, ha eszköz ír csak egy lemezre a feldolgozása során.

### <a name="waimportexport-output"></a>WAImportExport kimeneti

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Két napló fájlt, melyiket kell tölthetők fel az Azure-portálhoz?

**.xml** -az egyes merevlemez-meghajtóról, amely előkészítheti a WAImportExport eszközzel, az eszköz létrehoz egy egyetlen napló fájlt nevű `<DriveID>.xml` meghajtó esetén a társított meghajtót, a lemez beolvassa az eszköz sorozatszámát. Az összes, az importálási feladat létrehozása az Azure portálon meghajtó szüksége lesz az adatbázisnapló-fájlok. Ez a napló fájl is használható meghajtó előkészítése folytatni, ha az eszköz megszakad.

**.jrn** -utótag napló fájl `.jrn` merevlemez-meghajtóra vonatkozó összes másolási munkamenet állapotát tartalmazza. Az importálási feladat létrehozásához szükséges információkat is tartalmaz. Mindig meg kell adni a napló fájlt, ha futtatja a WAImportExport eszköz, valamint egy másolatot munkamenet-azonosítót.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Import/Export eszköz beállítása](storage-import-export-tool-setup.md)
* [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import.md)
* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [A gyakran használt parancsok rövid összefoglalása](storage-import-export-tool-quick-reference.md) 
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
