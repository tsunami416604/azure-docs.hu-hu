---
title: Azure importálási/exportálási importálási feladatának javítása - v1 | Microsoft dokumentumok
description: Ismerje meg, hogyan javíthatja ki az Azure importálási/exportálási szolgáltatásával létrehozott és futtatott importálási feladatot.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973883"
---
# <a name="repairing-an-import-job"></a>Importálási feladat javítása
Előfordulhat, hogy a Microsoft Azure importálási/exportálási szolgáltatása nem képes átmásolni a fájlok egy részét vagy egy fájlrészét a Windows Azure Blob szolgáltatásba. A hibák néhány oka a következő:  
  
-   Sérült fájlok  
  
-   Sérült meghajtók  
  
-   A tárfiók kulcsa megváltozott a fájl átvitele közben.  
  
Futtathatja a Microsoft Azure importálási/exportálási eszközt az importálási feladat másolási naplófájljaival, és az eszköz feltölti a hiányzó fájlokat (vagy egy fájl részeit) a Windows Azure tárfiókjába az importálási feladat befejezéséhez.  
  
## <a name="repairimport-parameters"></a>RepairImport paraméterek

A **repairimport**következő paraméterei adhatók meg: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Szükséges.** A javítási fájl elérési útja, amely nyomon követi a javítás előrehaladását, és lehetővé teszi a megszakított javítás folytatását. Minden meghajtónak egy és csak egy javítófájllal kell rendelkeznie. Amikor elkezd egy javítást egy adott meghajtóhoz, adja át az elérési utat egy javítási fájlhoz, amely még nem létezik. A megszakított javítás folytatásához adja át egy meglévő javítási fájl nevét. A célmeghajtónak megfelelő javítási fájlt mindig meg kell adni.|  
|**/logdir:**<LogDirectory\>|**Választható.** A naplókönyvtár. A részletes naplófájlok ebbe a könyvtárba kerülnek. Ha nincs megadva naplókönyvtár, a rendszer az aktuális könyvtárat használja naplókönyvtárként.|  
|**/d:**<TargetDirectories\>|**Szükséges.** Egy vagy több pontosvesszővel elválasztott könyvtár, amely az importált eredeti fájlokat tartalmazza. Az importálási meghajtó is használható, de nem szükséges, ha az eredeti fájlok alternatív helyei rendelkezésre állnak.|  
|**/bk:**<BitLockerKey\>|**Választható.** Ha azt szeretné, hogy az eszköz feloldjon egy titkosított meghajtót, ahol az eredeti fájlok elérhetők, adja meg a BitLocker-kulcsot.|  
|**/sn:**<StorageAccountName\>|**Szükséges.** Az importálási feladat tárfiókjának neve.|  
|**/sk:**<StorageAccountKey\>|Akkor és csak akkor **szükséges,** ha nincs megadva tárolóSAS. Az importálási feladat tárfiókjának fiókkulcsa.|  
|**/csas:**<ContainerSas\>|**Szükség akkor** és csak akkor, ha a tárfiók kulcsa nincs megadva. A tároló SAS az importálási feladathoz társított blobok eléréséhez.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Szükséges.** A meghajtó másolási naplófájljának elérési útja (részletes napló vagy hibanapló). A fájlt a Windows Azure importálási/exportálási szolgáltatás hozza létre, és letölthető a feladathoz társított blob storage-ból. A másolási naplófájl a hibás blobokról vagy fájlokról tartalmaz információkat, amelyeket meg kell javítani.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Választható.** A kétértelműségek feloldására használható szövegfájl elérési útja, ha több fájlja van ugyanazzal a feladattal, és ugyanazt a nevet használja. Az eszköz első futtatásakor feltöltheti ezt a fájlt az összes kétértelmű névvel. Az eszköz későbbi futtatásai ezt a fájlt használják a kétértelműség feloldásához.|  
  
## <a name="using-the-repairimport-command"></a>Az Importálás javítása parancs használata  
Az adatok hálózaton keresztüli streamelésével történő importálásának javításához meg kell adnia `/d` azokat a könyvtárakat, amelyek a paraméter rel importált eredeti fájlokat tartalmazzák. Meg kell adnia a tárfiókból letöltött másolási naplófájlt is. A részleges hibákat leváltó importálási feladat javításához egy tipikus parancssor a következőképpen néz ki:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
A következő példában egy másolati naplófájl, egy 64 K darab fájl sérült a meghajtón, hogy a szállított az importálási feladat. Mivel ez az egyetlen jelzett hiba, a feladat többi blobja sikeresen importálásra került.  
  
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
  
Amikor ezt a másolási naplót átadja az Azure Importálási/exportálási eszköznek, az eszköz megpróbálja befejezni a fájl importálását a hiányzó tartalom másolásával a hálózaton keresztül. A fenti példát követve az `\animals\koala.jpg` eszköz megkeresi `C:\Users\bob\Pictures` az `X:\BobBackup\photos`eredeti fájlt a két könyvtárban, és . Ha a `C:\Users\bob\Pictures\animals\koala.jpg` fájl létezik, az Azure importálási/exportálási eszköz `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`a hiányzó adattartományt másolja a megfelelő blobba.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Ütközések feloldása a RepairImport használatakor  
Bizonyos esetekben előfordulhat, hogy az eszköz a következő okok valamelyike miatt nem találja vagy nyitja meg a szükséges fájlt: a fájl nem található, a fájl nem érhető el, a fájl neve nem egyértelmű, vagy a fájl tartalma már nem helyes.  
  
Kétértelmű hiba léphet fel, ha az `\animals\koala.jpg` eszköz megpróbálja megtalálni, és `C:\Users\bob\pictures` van `X:\BobBackup\photos`egy fájl, amelynek neve a és a. Ez azt, `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` hogy mindkettő, és létezik az import feladat meghajtók.  
  
A `/PathMapFile` beállítás lehetővé teszi a hibák elhárítását. Megadhatja a fájl nevét, amely az eszköz által nem megfelelően azonosítható fájlok listáját tartalmazza. A következő parancssori példa `9WM35C2V_pathmap.txt`felpezsdül:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Az eszköz ezután írja a `9WM35C2V_pathmap.txt`problémás fájl elérési útjait a sorba. A parancs futtatása után például a következő bejegyzéseket tartalmazhatják:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 A lista minden egyes fájlja esetében meg kell kísérelnie a fájl megkeresését és megnyitását, hogy az biztosan elérhető legyen az eszköz számára. Ha egyértelműen meg szeretné mondani az eszköznek, hogy hol találja a fájlt, módosíthatja az elérési úttérkép-fájlt, és hozzáadhatja az elérési utat az azonos sorban lévő minden fájlhoz, tabulátorkaraktersel elválasztva:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Miután elérhetővé tette a szükséges fájlokat az eszköz számára, vagy frissítette az elérési úttérkép-fájlt, újra futtathatja az eszközt az importálási folyamat befejezéséhez.  
  
## <a name="next-steps"></a>További lépések
 
* [Az Azure importálási/exportálási eszközbeállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
