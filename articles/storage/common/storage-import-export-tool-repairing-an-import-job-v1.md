---
title: Az Azure Import/Export importálási feladat - v1 javítása |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozott és az Azure Import/Export szolgáltatás használatával futtassa importálási feladat javítása.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e2eb580df0a90e07e79c7f080ba31e5418fc5956
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523784"
---
# <a name="repairing-an-import-job"></a>Importálási feladat javítása
A Microsoft Azure Import/Export szolgáltatás néhány a fájlok és a egy fájl részeit átmásolása a Windows Azure Blob szolgáltatás sikertelen lehet. Bizonyos hibák okai a következők:  
  
-   A sérült fájlok  
  
-   Sérült meghajtók  
  
-   A tárfiókkulcs módosítva lett a fájl átvitele közben.  
  
Futtathatja a Microsoft Azure Import/Export eszköz az importálás feladat másolási naplófájlokat, és az eszköz feltölti a hiányzó fájlokat (vagy egy fájl részeit) a Windows Azure storage-fiókba az importálási feladat végrehajtásához.  
  
## <a name="repairimport-parameters"></a>RepairImport paraméterek

A következő paraméterek adható **RepairImport**: 
  
|||  
|-|-|  
|**r:**< RepairFile\>|**Szükséges.** A javítási fájlt, amely nyomon követi a folyamatot a javítás, és lehetővé teszi, hogy folytatni az megszakított javítását elérési útja. Minden olyan meghajtó csak egy javítási fájlnak kell lennie. Amikor egy adott meghajtó javítás, adja meg az elérési út egy javítási fájlt, amely még nem létezik. Az megszakított javítását folytatásához be egy meglévő helyreállítási fájl nevét kell átadnia. A javítási fájlt a cél meghajtó megfelelő mindig meg kell adni.|  
|**/ logdir:**< LogDirectory\>|**Nem kötelező.** A naplózási könyvtár. Ez a könyvtár részletes naplófájlok kerüljenek. Ha nincs naplókönyvtár van megadva, a naplózási könyvtár az aktuális könyvtárban lesz.|  
|**/ d:**< TargetDirectories\>|**Szükséges.** Egy vagy több pontosvesszővel tagolt könyvtárak, amelyek tartalmazzák az eredeti fájlokat a korábban importált. Az importálás meghajtó is használható, de nem kötelező, ha a másodlagos hely eredeti fájlok érhető el.|  
|**/BK:**< BitLockerKey\>|**Nem kötelező.** A BitLocker-kulcsot kell megadnia, ha azt szeretné, hogy az eszköz zárolásának feloldásához egy titkosított meghajtó, ahol az eredeti fájl áll rendelkezésre.|  
|**/sn:**< StorageAccountName\>|**Szükséges.** Az importálási feladatot a tárfiók neve.|  
|**/SK:**< StorageAccountKey\>|**Szükséges** csak, ha nincs megadva egy SAS-tárolót. A storage-fiókját az importálási feladat fiókkulcs.|  
|**/csas:**< ContainerSas\>|**Szükséges** csak, ha nincs megadva a tárfiók-kulcsot. A tároló SAS a blobok, az importálási feladathoz hozzárendelt eléréséhez.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Szükséges.** Naplófájl elérési útja a meghajtó másolása (vagy részletes naplózás vagy a hiba naplója). A fájlt a Windows Azure Import/Export szolgáltatás által létrehozott, és a feladathoz hozzárendelt blob storage-ból letölthető. A log fájl másolása sikertelen blobok vagy fájlokat, amelyek javításra szorul kapcsolatos információkat tartalmazza.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Nem kötelező.** Elérési út egy szövegfájlba feloldani a kétértelműséget, ha több fájlt, amely ugyanazt a feladatot importálna ugyanazzal a névvel rendelkezik használható. Az eszköz fut, először azt fel lehet tölteni a fájlt az összes, a nem egyértelmű nevek. Az eszköz későbbi futtatások a kétértelműséget feloldani ezt a fájlt használja.|  
  
## <a name="using-the-repairimport-command"></a>A RepairImport paranccsal  
Adatok importálása a hálózaton keresztül a streamadatok helyreállításához, meg kell adnia a importálna, használja az eredeti fájlokat tartalmazó könyvtárakat a `/d` paraméter. A log fájl másolása a tárfiókból letöltött is meg kell. Egy tipikus parancssori részleges hibákkal rendelkező importálási feladat javítása hasonlóan néz ki:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
A következő példában a másolási naplófájlok a meghajtón, amely az importálási feladat teljesített 64-K egy részét a fájl sérült. Mivel a csak hibát jelzett, a feladat a blobokat a többi címekkénti importálása sikeresen megtörtént.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Ha a másolási napló számára az Azure Import/Export eszköz van, az eszköz megkísérli a fájl az importálás befejezése a hiányzó tartalmának másolása a hálózaton keresztül. A fenti példát követve az eszköz figyeli a nem az eredeti fájl `\animals\koala.jpg` belül a két címtár `C:\Users\bob\Pictures` és `X:\BobBackup\photos`. Ha a fájl `C:\Users\bob\Pictures\animals\koala.jpg` létezik, az Azure Import/Export eszköz a megfelelő blobba másolja a hiányzó adattartományokat `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>RepairImport használata során az ütközések feloldása  
Bizonyos esetekben az eszköz nem lehet, vagy nyissa meg a szükséges fájlt az alábbi okok valamelyike: a fájl nem található, a fájl nem érhető el, a fájlnév nem egyértelmű vagy a fájl tartalma már nem megfelelő.  
  
Nem egyértelmű hiba akkor fordulhat elő, ha az eszköz van megkeresésére tett kísérlet `\animals\koala.jpg` , és mindkettő ilyen nevű fájl `C:\Users\bob\pictures` és `X:\BobBackup\photos`. Vagyis mindkét `C:\Users\bob\pictures\animals\koala.jpg` és `X:\BobBackup\photos\animals\koala.jpg` az importálási feladat meghajtókon található.  
  
A `/PathMapFile` beállítás lehetővé teszi a hibák elhárításához. Megadhatja, hogy a fájl, amely tartalmazza azokat a fájlokat, az eszköz nem tudta helyesen azonosítani a neve. A következő parancssori példa feltölti a `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Az eszköz ezután ír a problematikus fájl elérési útját `9WM35C2V_pathmap.txt`, soronként egy. Azt jelzi, például a fájl tartalmazhat-e a parancs futtatása után az alábbi bejegyzéseket:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 A lista minden fájl esetében meg kell próbálni keresse meg és nyissa meg a fájlt, hogy az eszköz számára elérhető. Ha szeretné, hogy az eszköz explicit módon, hogy hol található a fájl, módosítsa az elérési út térkép fájlt, és vegye fel a minden fájl egy sorban, tabulátor karakterrel elválasztva:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Elérhetővé teszi azokat a fájlokat az eszköz, vagy az elérési út leképezési fájl frissítése után futtathatja az eszközt, hogy az importálási művelet befejezéséhez.  
  
## <a name="next-steps"></a>További lépések
 
* [Az Azure Import/Export eszköz telepítése](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
