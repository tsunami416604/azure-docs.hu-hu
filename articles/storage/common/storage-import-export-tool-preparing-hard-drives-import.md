---
title: Merevlemezek előkészítése importálási feladatokhoz az Azure Import/Export |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő a merevlemezek importálási feladatokhoz az Azure Import/Export szolgáltatás létrehozása a WAImportExport eszközzel.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 9d8509e97ad83dd636f0a1b1892a2fa67c69e0b7
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521795"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Merevlemezek előkészítése importálási feladatokhoz

A WAImportExport eszköz egy a meghajtó-előkészítési és -javítás eszköz, amely használható a [a Microsoft Azure Import/Export szolgáltatás](../storage-import-export-service.md). Ez az eszköz segítségével másolhat adatokat a merevlemez-meghajtók küldje el a egy Azure-adatközpontban fog. Importálási feladat befejezését követően az eszköz használatával javítsa ki azokat a blobokat, hogy sérültek voltak, lett(ek), vagy ütköző más blobok használata. A meghajtók, kap egy befejezett exportálási feladatot, miután az eszköz használatával javítsa ki azokat a fájlokat, sérült vagy hiányzik a meghajtókon is. Ebben a cikkben azt felszámolunk az eszköz használatát is.

## <a name="prerequisites"></a>Előfeltételek

### <a name="requirements-for-waimportexportexe"></a>WAImportExport.exe követelményei

- **Számítógép-konfiguráció**
  - Windows 7, Windows Server 2008 R2 vagy újabb Windows operációs rendszer
  - .NET-keretrendszer 4 telepítve kell lennie. Lásd: [– gyakori kérdések](#faq) való ellenőrizze, hogy .net keretrendszer van telepítve a számítógépen.
- **Tárfiók kulcsa** – legalább egy, a fiók-kulcsokat a tárfiók szükséges.

### <a name="preparing-disk-for-import-job"></a>Lemez előkészítése importálási feladatokhoz

- **A BitLocker -** Bitlockernek engedélyezve kell lennie a gépen a WAImportExport eszközt futtatja. Tekintse meg a [– gyakori kérdések](#faq) a BitLocker engedélyezésének módja.
- **Lemezek** elérhető, amelyre WAImportExport eszközt futtató gépet. Lásd: [– gyakori kérdések](#faq) lemez megadását.
- **Forrásfájlokat** -a fájlok importálását tervezi a másolási gép elérhetőknek kell lenniük egy hálózati megosztásra vagy a helyi merevlemezen vannak-e.

### <a name="repairing-a-partially-failed-import-job"></a>Egy részben nem sikerült importálási feladat javítása

- **Log fájl másolása** , amely akkor jön létre, amikor az Azure Import/Export szolgáltatás átmásolja a Storage-fiók és a lemez adatait. A célként megadott tárfiók található.

### <a name="repairing-a-partially-failed-export-job"></a>Egy részben nem sikerült exportálási feladat javítása

- **Log fájl másolása** , amely akkor jön létre, amikor az Azure Import/Export szolgáltatás átmásolja a Storage-fiók és a lemez adatait. A forrás tárfiókban található.
- **Jegyzékfájl** -Located [opcionális] a Microsoft által visszaadott exportált meghajtón.

## <a name="download-and-install-waimportexport"></a>Töltse le és telepítse a WAImportExport

Töltse le a [WAImportExport.exe legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=55280). Bontsa ki a tömörített tartalmat, a számítógép könyvtárba.

A következő feladata CSV-fájlok létrehozása.

## <a name="prepare-the-dataset-csv-file"></a>Az adatkészlet CSV-fájl előkészítése

### <a name="what-is-dataset-csv"></a>Mi a fürt megosztott kötetei szolgáltatás adatkészlet

Adatkészlet CSV-fájl /dataset jelző értéke egy CSV-fájl, amely a címtárak listájának és/vagy a cél-meghajtók másolandó fájlok listáját tartalmazza. Az első lépés az importálási feladat létrehozása feladata annak megállapítása, mely könyvtárak és fájlok importálni kívánja. Ez lehet könyvtárainak listáját, egyedi fájlok listáját, vagy ezeket a kettő kombinációját. Ha egy könyvtárat tartalmaz, a címtárat és annak alkönyvtáraiban található összes fájl lesz az importálási feladat részeként.

Minden egyes könyvtárat vagy fájlt importálni meg kell adni a cél virtuális könyvtárat vagy a blob az Azure Blob service-ben. Ezeken a célokon bemeneteként a WAImportExport eszközt használhat. Könyvtárak kell tagolt, a perjel karakter a "/".

Az alábbi táblázat néhány példa a blob-tárolók:

| Forrás-fájl vagy könyvtár | Cél blob vagy virtuális könyvtár |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Minta dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Adatkészlet CSV-fájl mezők

| Mező | Leírás |
| --- | --- |
| BasePath | **[Kötelező]**<br/>Ez a paraméter értékét a forrás, ahol megtalálható az importálandó adatokat jelöli. Az eszköz fog rekurzív módon másolási az elérési út alatt található összes adatot.<br><br/>**Megengedett értékek**: Ez kell lennie a helyi számítógépen érvényes elérési utat vagy egy érvényes elérési utat, és a felhasználó által elérhetőnek kell lennie. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie. Ha az elérési út végződik "\\", azt a könyvtárat, más egy elérési út nélküli záró "\\" egy fájlt jelöl.<br/>Ez a mező nincs reguláris kifejezés használata engedélyezett. Ha az elérési út szóközöket tartalmaz, helyezze "".<br><br/>**Példa**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Kötelező]**<br/> A Windows Azure storage-fiókban a cél virtuális könyvtár elérési útja. Előfordulhat, hogy a virtuális könyvtár, vagy már nem létezik. Ha még nem létezik, Import/Export szolgáltatás létrehoz egyet.<br/><br/>Ügyeljen arra, érvényes a tároló nevének megadása a cél virtuális könyvtárak és blobok esetén. Ne feledje, hogy a tároló neve csak kisbetűket tartalmazhatnak. Tároló elnevezési szabályait, lásd: [elnevezése és a hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Ha csak a legfelső szintű meg van adva, a rendszer replikálja a könyvtárstruktúra, a forrás-cél blob-tárolóban. Ha egy másik könyvtárstruktúrát van szükség, mint a forrás, leképezés CSV-ben több sort<br/><br/>Megadhat egy tárolót, vagy egy hasonló music/70s blob előtagot /. A célkönyvtárban duplikátum a tároló neve, amelyet által előre dőlő perjellel kell kezdődnie. a "/", és szükség esetén belefoglalhatja a előfordulhat, hogy a blob virtuális könyvtár nem végződik "/".<br/><br/>Ha a cél tároló a legfelső szintű tárolót, meg kell adnia a legfelső szintű tárolót, mint $root a perjelet is beleértve /. Mivel a gyökérszintű tárolóban található blobok nem szerepelhet "/" a nevében szerepel a forráskönyvtárban alkönyvtáraiban nem lesznek másolva a célkönyvtárban duplikátum esetén a gyökérszintű tárolóban.<br/><br/>**Példa**<br/>Ha a célblob elérési https://mystorageaccount.blob.core.windows.net/video, lehet, hogy ez a mező értékének videó /  |
| BlobType | **[Opcionális]**  blokk &#124; lap<br/>Import/Export szolgáltatás jelenleg támogatja a Blobok 2 típusú. Blobok és a blokk BlobsBy alapértelmezett összes fájlt importálja blokkblobként lapon. És \*.vhd és \*.vhdx importálja, a lap BlobsThere a blokkblob és lapblobok engedélyezett mérete korlátozva. Lásd: [Storage skálázhatósági célértékét](storage-scalability-targets.md) további információt.  |
| Törlése | **[Opcionális]**  átnevezése &#124; nem írja felül &#124; felülírása <br/> Ebben a mezőben adja meg a másolási viselkedés azaz importálás során Amikor adatokat töltenek fel a tárfiók a lemezről. Elérhető lehetőségek: átnevezése&#124;felülírja&#124;nem írja felül. Alapértelmezés szerint a "átnevezése", ha nincs megadva érték. <br/><br/>**Nevezze át**: ugyanazzal a névvel rendelkező objektum jelen, ha létrehoz egy másolatot a célhelyen.<br/>Felülírása: újabb fájl felülírja a fájlt. A fájl utolsó módosításának WINS.<br/>**Nem írja felül**: kihagyja a fájl írása, ha már van ilyen.|
| Metaadatfájl | **[Opcionális]** <br/>Ez a mező értéke a metaadatok fájlt, amely képes biztosítani, ha azt meg kell őrizni az objektumok metaadatait, vagy adjon meg egyéni metaadatok. Az a cél blobok metaadatait tartalmazó fájl elérési útja. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információ |
| PropertiesFile | **[Opcionális]** <br/>A fájl elérési útját a tulajdonság a cél blobok számára. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>InitialDriveSet vagy AdditionalDriveSet CSV-fájl előkészítése

### <a name="what-is-driveset-csv"></a>Mi az fürt megosztott kötetei szolgáltatás driveset

A /InitialDriveSet vagy /AdditionalDriveSet jelző értéke egy CSV-fájl, amely tartalmazza azokat a lemezeket, amelyhez a meghajtó betűjelei vannak leképezve, úgy, hogy az eszköz megfelelően válassza ki a készüljön fel a lemezek listája. Az adatok mérete nagyobb, mint egy egyetlen lemez mérete, ha a WAImportExport eszközt elosztja az adatokat optimalizált módon bejegyezve a CSV-fájl több lemezre kiterjedő.

Az adatok csak írható egyetlen munkamenetben lemezek száma nincs korlátozva van. Az eszközt elosztja a lemez méretét és a mappa mérete alapján. A lemez, amely a legtöbb ki lesz a objektumméret optimalizálva. Az adatok a storage-fiókban való feltöltés vissza a megadott adatkészlet fájlnevével könyvtárstruktúrát konvergált. A fürt megosztott kötetei szolgáltatás driveset létrehozásához kövesse az alábbi lépéseket.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Egyszerű kötet létrehozása és hozzárendelése a meghajtó betűjele

Annak érdekében, hogy hozzon létre egy alapszintű kötetet, és rendeljen hozzá meghajtóbetűjelet, a "Egyszerűbb partíció létrehozása" megadott utasításokat követve [Lemezkezelés áttekintése](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Minta InitialDriveSet és AdditionalDriveSet CSV-fájl

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Driveset CSV-fájl mezők

| Mezők | Érték |
| --- | --- |
| Meghajtóbetűjel | **[Kötelező]**<br/> Minden olyan meghajtó, amely a folyamatban van az eszköz, a cél egy egyszerű NTFS-kötetre, és egy hozzá rendelt meghajtóbetűjelet kell rendelkeznie.<br/> <br/>**Példa**: R vagy az r |
| FormatOption | **[Kötelező]**  Formátum &#124; AlreadyFormatted<br/><br/> **Formátum**: Adja meg ezt fogja formázni a lemezen lévő összes adatot. <br/>**AlreadyFormatted**: az eszköz kihagyja a formázást, ha ez az érték meg van adva. |
| SilentOrPromptOnFormat | **[Kötelező]**  SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: Ez az érték megadása lehetővé teszi a felhasználó az eszköz futtatásához csendes módban. <br/>**PromptOnFormat**: az eszköz meg fogja kérni a felhasználót, hogy e a művelet nagyon irányul, minden formátum.<br/><br/>Ha nincs beállítva, parancs fog megszakítása és hibaüzenet megjelenítése: "beállítás értéke helytelen SilentOrPromptOnFormat: nincs" |
| Titkosítás | **[Kötelező]**  Titkosítása &#124; AlreadyEncrypted<br/> Ez a mező értékét úgy dönt, hogy mely lemez titkosításához, és amely nem az. <br/><br/>**Titkosítása**: eszköz lesznek formázva, a meghajtó. Ha az érték "FormatOption" mező "Formátum" majd ezt az értéket szükséges "Titkosítás" lehet. "AlreadyEncrypted" van megadva ebben az esetben, ha okoz hibába ütközött "Formátum van megadva, amikor titkosítása is meg kell adni".<br/>**AlreadyEncrypted**: eszköz fejti vissza a meghajtót a "ExistingBitLockerKey" mezőjében megadott BitLockerKey. Ha "FormatOption" mező értéke "AlreadyFormatted", majd ez az érték lehet "Titkosítás" vagy "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Kötelező]**  Ha "Titkosítás" mező értéke "AlreadyEncrypted"<br/> Ez a mező értéke a BitLocker-kulcs, amely az adott lemezt társítva van. <br/><br/>Ezt a mezőt üresen, ha az érték "Titkosítás" mező "Titkosítás" kell hagyni.  BitLocker-kulcs van megadva ebben az esetben, ha okoz hibába ütközött "Bitlocker-kulcs nem adható meg".<br/>  **Példa**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Lemez előkészítése importálási feladatokhoz

Meghajtó előkészítése importálási feladatokhoz, hívja meg a WAImportExport eszközben a **PrepImport** parancsot. Mely paraméterek is függ, hogy ez-e az első másolás munkamenet, vagy egy későbbi másolási munkamenet.

### <a name="first-session"></a>Első munkamenet

Első másolás munkamenet Single/Multiple könyvtár átmásolása egy egyetlen vagy több lemez (attól függően, a CSV-fájlban megadott) WAImportExport eszköz PrepImport parancsot az első másolás munkamenet könyvtárak és/vagy egy új példányt munkamenettel fájlok másolása:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adatok hozzáadása a következő munkamenetben

Az ezt követő másolási-munkamenetekben nem kell megadnia a kezdeti paramétereket. Ne felejtse el, ahol az előző munkamenet a bal oldali ugyanazt a napló fájlt ahhoz, hogy az eszköz használatával kell. A másolási munkamenet állapotának íródik a naplófájl. A szintaxist és a további címtárak, vagy fájlok másolása egy későbbi másolási munkamenet a következő:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>A legutóbbi munkamenet meghajtók hozzáadása

Az adatok nem fértek InitialDriveset a meghajtók, ha az egyik eszközzel a további meghajtók hozzáadása másolási ugyanazon munkamenet. 

>[!NOTE] 
>A munkamenet-azonosítót meg kell egyeznie az előző munkamenet-azonosító. Naplófájl meg kell egyeznie az előző munkamenet megadott.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Példa**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>A legutóbbi munkamenet megszakítása:

Ha egy példány munkamenet megszakad, és nem lehetséges (például, ha a forráskönyvtár bizonyult nem érhető el) folytatásához, a jelenlegi munkamenet, hogy azt vissza lesz állítva, és az új példány munkamenetek indíthatók el kell megszakít:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Csak az utolsó másolás munkamenet, ha rendellenesen, is megszakítja. Vegye figyelembe, hogy az első másolás munkamenet-meghajtó nem szakítható meg. Ehelyett újra kell indítania a másolási munkamenet együtt egy új naplófájl.

### <a name="resume-a-latest-interrupted-session"></a>A legújabb megszakított munkamenet folytatása

Ha a másolási munkamenet bármilyen okból megszakad, folytathatja az eszköz csak a megadott journal-fájl futtatásával:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Példa**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> A másolási munkamenet folytatásakor ne módosítsa a forrás adatok fájlok és könyvtárak hozzáadásával vagy eltávolításával a fájlokat.

## <a name="waimportexport-parameters"></a>WAImportExport paraméterek

| Paraméterek | Leírás |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Szükséges**<br/> A naplófájl elérési útja. A naplófájl meghajtók készletét követi, és ezek a meghajtók előkészítése a folyamat állapotát rögzíti. A naplófájl mindig meg kell adni.  |
|     / logdir:&lt;LogDirectory&gt;  | **Választható**. A naplózási könyvtár.<br/> Ez a könyvtár részletes naplófájlok, valamint az egyes ideiglenes fájlok lesz írva. Ha nem a megadott, a jelenlegi könyvtárat használja, a naplózási könyvtár. A naplózási könyvtár napló ugyanazt a fájlt csak egyszer adható meg.  |
|     / ID:&lt;munkamenet-azonosító&gt;  | **Szükséges**<br/> A munkamenet-azonosító egy másolási munkamenet azonosítására szolgál. Győződjön meg, hogy egy megszakított másolási munkamenet pontos helyreállítási szolgál.  |
|     / ResumeSession  | Választható. Ha az utolsó másolás munkamenet rendellenesen megszakadt, ez a paraméter adható meg a munkamenet folytatása.   |
|     / AbortSession  | Választható. Ha a legutóbbi másolási munkamenet rendellenesen megszakadt, ez a paraméter adható meg megszakíthatja a munkamenetet.  |
|     /sn:&lt;StorageAccountName&gt;  | **Szükséges**<br/> Csak a RepairImport és RepairExport alkalmazható. A tárfiók neve.  |
|     /SK:&lt;StorageAccountKey&gt;  | **Szükséges**<br/> A tárfiók kulcsa. |
|     / InitialDriveSet:&lt;driveset.csv&gt;  | **Szükséges** futtatásakor az első másolás munkamenet<br/> Egy CSV-fájl előkészítése meghajtók listáját tartalmazza.  |
|     / AdditionalDriveSet:&lt;driveset.csv&gt; | **Szükséges**. Amikor a meghajtók hozzáadása a jelenlegi példány munkamenet. <br/> Egy CSV-fájl, amely további meghajtók hozzáadandó listáját tartalmazza.  |
|      r:&lt;RepairFile&gt; | **Szükséges** RepairImport és RepairExport csak érvényes.<br/> A javítási folyamat nyomon követéséhez fájl elérési útját. Minden olyan meghajtó csak egy javítási fájlnak kell lennie.  |
|     / d:&lt;TargetDirectories&gt; | **Szükséges**. Csak a RepairImport és RepairExport alkalmazható. A RepairImport egy vagy több pontosvesszővel tagolt könyvtárak kijavításához; RepairExport, egy címtárban, javításához, például: gyökér a meghajtó könyvtár.  |
|     / CopyLogFile:&lt;DriveCopyLogFile&gt; | **Szükséges** RepairImport és RepairExport csak érvényes. A meghajtó másolása naplófájl elérési útja (részletes vagy hiba).  |
|     / ManifestFile:&lt;DriveManifestFile&gt; | **Szükséges** RepairExport csak alkalmazható.<br/> A meghajtó Alkalmazásjegyzék-fájl elérési útja.  |
|     / PathMapFile:&lt;DrivePathMapFile&gt; | **Választható**. Csak a RepairImport alkalmazható.<br/> A Fájlelérési utak helyekre tényleges fájlok (tabulátorral tagolt) a meghajtó gyökeréhez viszonyítva leképezéseit tartalmazó fájl elérési útját. Először adja meg, amikor azt tölti fel üres példányokkal rendelkező Fájlelérési utak ami azt jelenti, vagy nem található TargetDirectories, a hozzáférés megtagadva, a neve érvénytelen, vagy több könyvtárban találhatók. Az elérési út leképezési fájl manuális tartalmazza a megfelelő céloldali elérési utak szerkeszthető és megadott újra az eszközt, hogy a Fájlelérési utak feloldása megfelelően.  |
|     / ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Szükséges**. Csak a PreviewExport alkalmazható.<br/> Az XML-fájl elérési útja blob elérési útjának listáját tartalmazó fájl, vagy a blob elérési útja előtagok exportálható a blobok számára. A fájl formátuma megegyezik az Import/Export szolgáltatás REST API Put feladat működése során a blob blob formátuma.  |
|     / DriveSize:&lt;DriveSize&gt; | **Szükséges**. Csak a PreviewExport alkalmazható.<br/>  Az Exportálás használható meghajtók mérete. Ha például 500 GB, 1,5 TB. Megjegyzés: 1 GB-os 1 000 hívás = 000 000 bytes1 TB = 1,000,000,000,000 bájt  |
|     / Adatkészlet:&lt;dataset.csv&gt; | **Szükséges**<br/> Egy CSV-fájl, amely a címtárak listájának és/vagy a cél-meghajtók másolandó fájlok listáját tartalmazza.  |
|     /silentmode  | **Választható**.<br/> Ha nincs megadva, ez lesz emlékeztesse a követelmény a meghajtók, és a megerősítés gombra kell.  |

## <a name="tool-output"></a>Eszköz kimeneti

### <a name="sample-drive-manifest-file"></a>Minta meghajtó Alkalmazásjegyzék-fájl

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

### <a name="sample-journal-file-xml-for-each-drive"></a>Minta journal-fájl (XML) minden meghajtó

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

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Minta naplófájl (JRN), amely rögzíti az eljáráshoz a munkamenetek-munkamenethez

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

A WAImportExport eszköze a meghajtó-előkészítési és -javítás eszköz, amely a Microsoft Azure Import/Export szolgáltatás használata. Ez az eszköz segítségével másolhat adatokat a merevlemez-meghajtók küldje el a az Azure-adatközpontban fog. Importálási feladat befejezését követően az eszköz használatával javítsa ki azokat a blobokat, hogy sérültek voltak, lett(ek), vagy ütköző más blobok használata. A meghajtók, kap egy befejezett exportálási feladatot, miután az eszköz használatával javítsa ki azokat a fájlokat, sérült vagy hiányzik a meghajtókon is.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Hogyan működik a WAImportExport eszköz több forrás dir és lemezek?

Az adatok mérete a lemez mérete nagyobb, ha a WAImportExport eszközt elosztja az adatok a lemezeken optimalizált módon. Az adatok másolását, több egymás után vagy a párhuzamos teheti meg. Az is lehet adatokat írni egy időben lemezek száma nincs korlátozva van. Az eszközt elosztja a lemez méretét és a mappa mérete alapján. A lemez, amely a legtöbb ki lesz a objektumméret optimalizálva. Az adatok a storage-fiókban való feltöltés vissza a megadott könyvtárstruktúrát konvergált.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Hol találhatok WAImportExport tool korábbi verziójával?

WAImportExport az eszköz összes elavuló WAImportExport V1 eszközzel kellett rendelkezik. WAImportExport eszköz lehetővé teszi a felhasználók számára adja meg a több forrásból, és több meghajtó írni. Ezenkívül egy könnyedén felügyelheti a több forráshelyet, amelyről az adatokat egy CSV-fájl másolni kell. Azonban az esetben meg kell szeretne egyetlen forrás átmásolása [töltheti WAImportExport V1 eszköz], egyetlen lemez vagy SAS támogatási (http://go.microsoft.com/fwlink/?LinkID=301900&amp; clcid = 0x409), majd tekintse át [WAImportExport V1 referencia](storage-import-export-tool-how-to-v1.md) WAImportExport V1 segítség használat.

#### <a name="what-is-a-session-id"></a>Mi az a munkamenet-azonosító?

Az eszköz a másolási munkamenet vár (/ id) paraméternek azonosnak kell lennie, ha a célja a helyezkednek el az adatokat több lemezen. A másolási munkamenet neve azonos karbantartása lehetővé teszi a felhasználói adatokat másol be egy vagy több cél lemezek/címtárakat egy vagy több forráshelyet. Ugyanazon munkamenet-azonosító karbantartása lehetővé teszi az eszköz folytattuk a munkát, ahol a legutóbbi alkalommal maradt másolására.

Azonban ugyanazon másolási munkamenet adatok importálása különböző storage-fiókok nem használható.

Ha a munkamenet-példány a név nem azonos az eszközt, a naplófájl több futtatás során (/ logdir) és a tárfiók-kulcsot (/ sk) is várt azonosnak kell lennie.

Munkamenet-azonosító tartalmazhat betűket, 0 ~ 9, understore (\_), kötőjelet (-) vagy az ujjlenyomat (#), és a hosszának 3 meg kell ~ 30.

például a munkamenet-1 vagy a munkamenet 1 vagy a munkamenet\_1

#### <a name="what-is-a-journal-file"></a>Mi az, hogy a napló-fájl?

Fájlok másolása a merevlemezen a WAImportExport eszköz minden egyes futtatásakor az eszköz létrehoz egy másolási munkamenet. A másolási munkamenet állapotának íródik a naplófájl. Ha a másolási munkamenet megszakad (például miatt a rendszer az áramellátás megszakadása), azt újra futtatni az eszközt, és a naplófájl a parancssorban megadásával folytathatók.

Mindegyik merevlemez, amely előkészíti az Azure Import/Export eszközzel, az eszköz létre fog hozni egy egyetlen naplófájl nevű "&lt;meghajtót&gt;.xml" azonosítójú meghajtó helyére társított arra a meghajtóra, a lemez beolvassa az eszköz sorozatszámát. Az összes meghajtó az importálási feladat létrehozásához szüksége lesz az adatbázisnapló-fájlok. A naplófájl is használható meghajtó előkészítés folytatásához, ha az eszköz megszakad.

#### <a name="what-is-a-log-directory"></a>Mi az, hogy egy könyvtárat?

A naplózási könyvtár részletes naplók, valamint az ideiglenes fájlok tárolására szolgáló könyvtár megadása Ha nincs megadva, a naplózási könyvtár az aktuális könyvtárban lesz. A naplókban a részletes naplók.

### <a name="prerequisites"></a>Előfeltételek

#### <a name="what-are-the-specifications-of-my-disk"></a>Mik azok a saját lemez előírásoknak?

Egy vagy több üres 2,5-es vagy 3,5 hüvelykes SATAII vagy III vagy SSD meghajtók másolási géphez csatlakoztatva.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Hogyan lehet engedélyezni a BitLocker a gépemen?

Egyszerű akkor kattintson a jobb gombbal a rendszermeghajtón. Megjelenik a beállítások a BitLocker, ha a funkció be van kapcsolva. Ha ki van kapcsolva, nem jelenik meg.

![Ellenőrizze a BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Íme egy cikk a [BitLocker engedélyezésének módja](https://technet.microsoft.com/library/cc766295.aspx)

Akkor lehet, hogy a gép nem rendelkezik TPM-lapka. Ha nem kap egy kimeneti TPM.msc parancsot használja, tekintse meg a következő gyakori kérdések.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Hogyan tilthatja le a platformmegbízhatósági modul (TPM) a BitLocker?
> [!NOTE]
> Csak akkor, ha nincs TPM nem található a kiszolgálók, tiltsa le a TPM-szabályzatot szeretne. Nem kell a TPM letiltása, ha a felhasználó server megbízható TPM. 
> 

Annak érdekében, hogy letiltja a BitLocker a TPM, nyissa meg az alábbi lépéseket:<br/>
1. Indítsa el a **Helyicsoportházirend-szerkesztő** gpedit.msc beírásával a parancssorba. Ha **Helyicsoportházirend-szerkesztő** úgy tűnik, hogy nem érhető el, a BitLocker engedélyezéséhez először. Tekintse meg az előző – gyakori kérdések.
2. Nyissa meg **helyi számítógép-házirend &gt; számítógép konfigurációja &gt; felügyeleti sablonok &gt; Windows-összetevők&gt; a BitLocker meghajtótitkosítás &gt; operációsrendszer-meghajtók**.
3. Szerkesztés **további hitelesítés indításkor szükséges** házirend.
4. Állítsa be a házirendet **engedélyezve** , és győződjön meg arról, hogy **engedélyezi a BitLocker nem kompatibilis TPM** be van jelölve.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Hogyan ellenőrizheti, ha .NET 4-es vagy újabb verziója van telepítve a gépemen?

Az összes Microsoft .NET-keretrendszer-verziókat települnek a következő könyvtárban: %windir%\Microsoft.NET\Framework\

Keresse meg a fent említett részére, amelyben a célgépen, ahol az eszköz futtatásához szükséges. Keressen a "v4" kezdetű mappa neve. Hiányában egy ilyen directory azt jelenti, hogy a .NET-4 nincs telepítve a gépén. Letöltheti a Microsoft .net-4 használatával [a Microsoft .NET-keretrendszer 4 (webes telepítő)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Korlátok

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>Hány meghajtókat is szeretnék előkészítése/küldés egyszerre?

Nincs megszabva, hogy felkészülhessen az eszköz lemezek számát. Az eszköz azonban meghajtóbetűjelek vár bemenetként. Amely korlátozza a 25 egyidejű lemez előkészítése. Egyszerre csak egy feladat legfeljebb 10 lemez kezelésére is alkalmas. Ha több mint 10 lemez ugyanazt a tárfiókot céloz meg, a lemezek több feladat szét lehetnek osztva.

#### <a name="can-i-target-more-than-one-storage-account"></a>Használhatom-e egynél több tárfiókot?

Csak egy storage-fiók is lehet beküldeni, feladatonként és a egy eredeti munkamenetben.

### <a name="capabilities"></a>Funkciók

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe titkosítja az adataimat?

Igen. A bitlockernek engedélyezve van, és ehhez a folyamathoz szükséges.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Mi lesz adat a hierarchiában, amikor az megjelenik a storage-fiókban?

Bár a lemezek között oszlanak meg adatokat, az adatok a storage-fiókban való feltöltés fog összevont térjen vissza a könyvtárstruktúra, az adatkészlet CSV-fájlban megadott.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>Lemezek számát a bemeneti lesz aktív i/o párhuzamosan, ha folyamatban van a Másolás?

Az eszközt elosztja az adatokat a bemeneti lemezeken, a bemeneti fájlok mérete alapján. Mindemellett a bemeneti adatok jellegétől teljesen delends egyidejűleg aktív lemezek számát. A bemeneti adatkészlet az egyes fájlok méretétől függően egy vagy több lemez aktív IO jelenhetnek meg a párhuzamos. Tekintse meg a következő kérdésben talál további részleteket.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Hogyan nem terjesztheti az eszközt a fájlok a lemezek között?

WAImportExport eszköz olvassa be, és írja a batch által kötegelt fájlokat egy kötegben legfeljebb 100 000-es fájlokat tartalmaz. Ez azt jelenti, hogy legfeljebb 100 000-es fájlok írhatók párhuzamosan. Több lemez írt egyszerre, ha ezeket a fájlokat 100 000-es oszlanak meg több meghajtókra. Azonban-e az eszköz ír több lemez egy időben, vagy egyetlen attól függ, a batch összesített mérete. Például kisebb fájlok esetén ha 10,0000 fájlok mindegyike képes ahhoz, hogy elférjen egyetlen meghajtóval, eszköz lesz írási csak egy lemezre a köteg feldolgozása közben.

### <a name="waimportexport-output"></a>WAImportExport kimenet

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Két journal-fájl, melyiket kell feltöltött az Azure Portal webhelyen?

**.xml** -mindegyik merevlemez, amely előkészíti a WAImportExport eszközzel, az eszköz létre fog hozni egy egyetlen naplófájl nevű `<DriveID>.xml` meghajtó esetén társított arra a meghajtóra, a lemez beolvassa az eszköz sorozatszámát. A naplófájl minden meghajtó az importálási feladat létrehozása az Azure Portalon kell. Ez a napló-fájl is használható meghajtó előkészítés folytatásához, ha az eszköz megszakad.

**.jrn** – a naplófájl utótaggal rendelkező `.jrn` merevlemez-meghajtóra vonatkozó összes másolási munkamenetet állapotát tartalmazza. Az importálási feladat létrehozásához szükséges információkat is tartalmaz. Mindig meg kell adnia egy journal-fájlt, ha futtatja a WAImportExport eszköz, valamint egy másolási munkamenet-azonosítót.

## <a name="next-steps"></a>További lépések

* [Az Azure Import/Export eszköz telepítése](storage-import-export-tool-setup.md)
* [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import.md)
* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Használt gyakori parancsok gyors áttekintése](storage-import-export-tool-quick-reference.md) 
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
