---
title: Az Azure importálási/exportálási eszközének beállítása | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be az Azure importálási/exportálási szolgáltatás meghajtó-előkészítő és -javító eszközét.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979036"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Az Azure importálási/exportálási eszköz beállítása

A Microsoft Azure importálási/exportálási eszköz a Microsoft Azure importálási/exportálási szolgáltatással használható meghajtó-előkészítő és -javító eszköz. Az eszközt a következő funkciókhoz használhatja:

* Az importálási feladat létrehozása előtt ezzel az eszközzel adatokat másolhat az Azure-adatközpontba szállítandó merevlemezekre.
* Miután egy importálási feladat befejeződött, ezzel az eszközzel kijavíthatja a sérült, hiányzó vagy más blobokkal ütköző blobokat.
* Miután megkapta a meghajtókat egy befejezett exportálási feladatból, ezzel az eszközzel kijavíthatja a meghajtókon sérült vagy hiányzó fájlokat.

## <a name="prerequisites"></a>Előfeltételek

Ha **meghajtókat készít elő** egy importálási feladatra, az alábbi előfeltételeket kell teljesíteni:

* Aktív Azure-előfizetéssel kell rendelkeznie.
* Az előfizetésnek tartalmaznia kell egy tárfiókot, amely elegendő helyet biztosít az importálni kívánt fájlok tárolásához.
* Legalább egy tárfiók-hozzáférési kulcsra van szüksége.
* Windows 7, Windows Server 2008 R2 vagy újabb Windows operációs rendszert telepített számítógépre (a "fénymásológépre") van szükség.
* A .
* A BitLocker-t engedélyezni kell a fénymásológépen.
* Egy vagy több üres 2,5 hüvelykes vagy 3,5 hüvelykes SATAII vagy III vagy SSD merevlemezre van szükség, amelyek a fénymásológéphez csatlakoznak.
* Az importálni kívánt fájloknak elérhetőnek kell lenniük a fénymásológépről, függetlenül attól, hogy hálózati megosztáson vagy helyi merevlemezen vannak-e.

Ha egy részlegesen meghibásodott importálást próbál **kijavítani,** a következőkre van szükség:

* A naplófájlok másolása
* A tárfiók kulcsa

Ha olyan exportálást próbál **meg kijavítani,** amely részben meghibásodott, a következőkre van szükség:

* A naplófájlok másolása
* A jegyzékfájlok (nem kötelező)
* A tárfiók kulcsa

## <a name="installing-the-azure-importexport-tool"></a>Az Azure importálási/exportálási eszköz telepítése

Először [töltse le az Azure Import/Export eszközt,](https://www.microsoft.com/download/details.aspx?id=55280) és bontsa ki a számítógép egy könyvtárába, például `c:\WAImportExport`.

Az Azure importálási/exportálási eszköze a következő fájlokból áll:

* adatkészlet.csv
* meghajtó.csv
* hddid.dll
* Microsoft.Data.Services.Ügyfél.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Ezután nyisson meg egy parancssori ablakot **Rendszergazda módban,** és váltson a kibontott fájlokat tartalmazó könyvtárba.

A parancs súgójának futtatásához`WAImportExport.exe`futtassa az eszközt ( ) paraméterek nélkül:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>További lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Meghajtóhasználat előnézete exportálási feladatokhoz](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
