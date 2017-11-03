---
title: "Egy Azure Import/Export importálási feladat - v1 javítására |} Microsoft Docs"
description: "Megtudhatja, hogyan javítsa az importálási feladat létrehozott és az Azure Import/Export szolgáltatás használatával fusson."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="repairing-an-import-job"></a>Importálási feladat javítása
A Microsoft Azure Import/Export szolgáltatás lehetséges, hogy nem másolja a fájlok vagy a fájl tartalmát egy része a Windows Azure Blob szolgáltatásban. Néhány ok, amiért hibák a következők:  
  
-   A sérült fájlok  
  
-   Sérült meghajtók  
  
-   A fiók kulcsot módosítható, amíg a fájl átvitel során.  
  
Futtathatja a Microsoft Azure Import/Export eszköz az importálás feladat másolási naplófájlokat, és az eszköz feltölti a hiányzó fájlokat (vagy a fájl tartalmát) a Windows Azure storage-fiókot az importálási feladat befejeződik.  
  
## <a name="repairimport-parameters"></a>RepairImport paraméterek

A következő paraméterekkel rendelkező adható meg **RepairImport**: 
  
|||  
|-|-|  
|**r:**< RepairFile\>|**Szükséges.** A fájl elérési útját javítása, amely nyomon követi a folyamatot, a javítási, és lehetővé teszi az megszakított javítását folytatása. Minden olyan meghajtó meg kell adni egy javítási fájlt. Amikor elindít egy adott meghajtó javítása, adja át az elérési út javítási fájlban, amely még nem létezik. Az megszakított javítását folytatásához meglévő javítási fájl nevében kell átadni. A célmeghajtó megfelelő javítási fájlt mindig meg kell adni.|  
|**/ logdir:**< LogDirectory\>|**Nem kötelező.** A naplózási könyvtár. Ez a könyvtár részletes naplófájlok kerülnek. Ha nincs naplókönyvtár meg van adva, az aktuális könyvtárban lesz a naplózási könyvtár.|  
|**/ d:**< TargetDirectories\>|**Szükséges.** Legalább egy pontosvesszővel elválasztott könyvtárak importált eredeti fájlokat tartalmazó. Az importálás meghajtó is használható, de nincs szükség esetén érhetők el az eredeti fájlokat más helyekre.|  
|**/BK:**< BitLockerKey\>|**Nem kötelező.** A BitLocker kulcsot kell megadnia, ha azt szeretné, hogy az eszköz zárolásának feloldásához egy titkosított meghajtó, ahol az eredeti fájl áll rendelkezésre.|  
|**/sn:**< StorageAccountName\>|**Szükséges.** Az importálási feladatnak a tárfiók neve.|  
|**/SK:**< StorageAccountKey\>|**Szükséges** csak, ha a tároló SAS nincs megadva. A fiók az importálási feladatnak a tárfiók kulcsa.|  
|**/csas:**< ContainerSas\>|**Szükséges** csak, ha nincs megadva a tárfiók kulcsára. A tároló SAS az importálási feladattal társított BLOB eléréséhez.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Szükséges.** Naplófájl elérési útja a meghajtó másolása (vagy részletes naplózás vagy hiba napló). A fájlt a Windows Azure Import/Export szolgáltatás által generált és tölthető le: a feladathoz társított blob-tároló. A napló fájl másolása sikertelen blobokkal vagy a fájlokat, amelyek javítani kell információkat tartalmaz.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Nem kötelező.** Elérési útját egy szövegfájlt, amely segítségével kétértelműséget feloldása, ha több fájl, amely ugyanazt a feladatot importálna ugyanazzal a névvel rendelkezik. Az eszköz fut, először azt töltheti fel az összes, a nem egyértelmű nevek a fájl. Az eszköz utólagosan a fájl segítségével hárítsa el a kétértelműséget.|  
  
## <a name="using-the-repairimport-command"></a>A RepairImport paranccsal  
Adatok importálása a hálózaton keresztül az adatok folyamatos javításához importálna, használja az eredeti fájlokat tartalmazó könyvtárak kell megadnia a `/d` paraméter. A letöltött importáljon a tárfiókba másolása naplófájl is meg kell adnia. Tipikus parancssor használatával javítsa ki az importálási feladat részleges kapcsolatos problémákkal néz ki:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
A másolási naplófájl a következő példában egy fájl egy 64-K adat megsérült teljesített az importálási feladatnak a meghajtón. Mivel ez a csak hibát jelzett, a blobot, amely a feladatot a többi az importálás sikeres volt.  
  
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
  
Ha ezt a naplót másolása az Azure Import/Export eszköz számára, az eszköz próbálja meg a hiányzó tartalmának másolása a hálózaton keresztül az adott fájlt az importálási befejezéséhez. A fenti példában követően az eszköz megkeresi az eredeti fájl `\animals\koala.jpg` belül a két címtár `C:\Users\bob\Pictures` és `X:\BobBackup\photos`. Ha a fájl `C:\Users\bob\Pictures\animals\koala.jpg` létezik, az Azure Import/Export eszköz a hiányzó adattartomány másolja a megfelelő blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Az ütközések feloldása RepairImport használatakor  
Bizonyos esetekben az eszköz nem lehet tudni található, vagy nyissa meg a szükséges fájl a következő okok miatt: a fájl nem található, a fájl nem érhető el, a fájl neve nem egyértelmű, vagy a fájl tartalma nem megfelelő.  
  
Nem egyértelmű hiba akkor fordulhat elő, ha az eszköz megkeresésére tett kísérlet `\animals\koala.jpg` , és mindkettő adott nevű fájl `C:\Users\bob\pictures` és `X:\BobBackup\photos`. Ez azt jelenti, hogy mindkét `C:\Users\bob\pictures\animals\koala.jpg` és `X:\BobBackup\photos\animals\koala.jpg` az importálási feladat meghajtón található.  
  
A `/PathMapFile` lehetővé teszi a hibák elhárításához. Megadhatja, hogy a fájl, amely tartalmazza a fájlokat, az eszköz nem tudta helyesen azonosítani nevét. A következő parancssori példa tölti fel `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Az eszköz majd fog kiírni, a hibás fájlt elérési útjait `9WM35C2V_pathmap.txt`, soronként egy. Azt jelzi, például a fájl tartalmazhat-e a parancs futtatása után az alábbi bejegyzéseket:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 A listában lévő összes fájlhoz meg kell próbálni keresse meg, és nyissa meg a fájlt annak érdekében, hogy az eszköz a rendelkezésére áll. Ha kívánja, hogy az eszköz explicit módon a fájl helyét, módosítsa az elérési út térkép fájlt, és vegye fel az elérési útját minden egyes fájl egy sorban, karakterrel elválasztó karakterláncként lapon:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Elérhetővé teszi azokat a fájlokat az eszköz, vagy az elérési út leképezési fájl frissítése után az eszköz az importálási folyamat befejezéséhez futtassa újra.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Az Azure Import/Export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
