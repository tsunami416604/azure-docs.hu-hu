---
title: Javítása Azure Import/Export exportálási feladat - 1-es verzió |} Microsoft Docs
description: Megtudhatja, hogyan javítsa ki, hogy létrejött, de az Azure Import/Export szolgáltatás használatával futtassa exportálási feladat.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873876"
---
# <a name="repairing-an-export-job"></a>Exportálási feladat javítása
Miután exportálási feladat befejeződött, a Microsoft Azure Import/Export eszköz helyszíni is futtathatja:  
  
1.  Töltse le azokat a fájlokat, az Azure Import/Export szolgáltatás nem tudta exportálni.  
  
2.  Ellenőrizze, hogy a meghajtón lévő fájlokat helyesen lettek-e exportálva.  
  
A funkció használatához Azure-tárhellyel létesített kapcsolat kell rendelkeznie.  
  
A parancs az importálási feladat javítási **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport paraméterek

A következő paraméterekkel rendelkező adható meg **RepairExport**:  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|**r: < RepairFile\>**|Kötelező. A fájl elérési útját javítása, amely nyomon követi a folyamatot, a javítási, és lehetővé teszi az megszakított javítását folytatása. Minden olyan meghajtó meg kell adni egy javítási fájlt. Egy adott meghajtó javítás indításakor meg fogja továbbítani az elérési út egy javítási fájlt, amely még nem létezik. Az megszakított javítását folytatásához meglévő javítási fájl nevében kell átadni. A célmeghajtó megfelelő javítási fájlt mindig meg kell adni.|  
|**/ logdir: < LogDirectory\>**|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok fog szerepelni. Ha nincs naplókönyvtár meg van adva, a naplózási könyvtár az aktuális könyvtár lesz.|  
|**/ d: < TargetDirectory\>**|Kötelező. Ellenőrizze és javítsa ki a könyvtárat. Ez általában az Exportálás meghajtó gyökérkönyvtárában, de sikerült is kell egy hálózati fájlmegosztásra tartalmazó az exportált fájlokat egy példányát.|  
|**/BK: < BitLockerKey\>**|Választható. A BitLocker kulcsot kell megadnia, ha azt szeretné, hogy az eszköz zárolásának feloldásához egy titkosított az exportált fájlok tárolására.|  
|**/sn: < StorageAccountName\>**|Kötelező. Az exportálási feladat a tárfiók nevét.|  
|**/SK: < StorageAccountKey\>**|**Szükséges** csak, ha a tároló SAS nincs megadva. A fiók az exportálási feladat a tárfiók kulcsa.|  
|**/csas: < ContainerSas\>**|**Szükséges** csak, ha nincs megadva a tárfiók kulcsára. A tároló SAS az exportálási feladat társított BLOB eléréséhez.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Kötelező. A naplófájl elérési útja a meghajtó másolása. A fájlt a Windows Azure Import/Export szolgáltatás által generált és tölthető le: a feladathoz társított blob-tároló. A napló fájl másolása sikertelen blobokkal vagy a fájlokat, amelyek javítani kell információkat tartalmaz.|  
|**/ ManifestFile: < DriveManifestFile\>**|Választható. Az Exportálás meghajtó Alkalmazásjegyzék-fájl elérési útja. Ezt a fájlt a Windows Azure Import/Export szolgáltatás által létrehozott és exportálás a meghajtón, és szükség esetén egy blobba a feladathoz társított tárfiókban tárolja.<br /><br /> Az Exportálás meghajtón a fájlok tartalmának fogja ellenőrizni a ebben a fájlban található MD5-kivonatok. Azokat a fájlokat, sérült határozza meg a rendszer letölti és a cél könyvtárak írni.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Javítsa ki a hibás kivitel RepairExport mód használata  
Az Azure Import/Export eszköz segítségével, amely nem sikerült exportálni a fájlok letöltése. A másolási naplófájlt fogja tartalmazni, amely nem sikerült exportálni a fájlok listáját.  
  
Exportálási hiba okai a következő lehetőségek állnak rendelkezésére:  
  
-   Sérült meghajtók  
  
-   A tárfiók hívóbetűjét módosult az átvitel során  
  
Az eszköz futtatásához **RepairExport** mód, először a meghajtót, amely tartalmazza az exportált fájlokat a számítógépre. Ezután futtassa az Azure Import/Export eszköz, az elérési út megadását, hogy a meghajtót, amelyen a `/d` paraméter. Meg kell adnia a naplófájl elérési útja a meghajtó másolása letöltött is. A következő parancsot az alábbi példa használatával javítsa ki azokat a fájlokat, nem sikerült exportálni az eszköz fut:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
A következő egy példa bemutatja, hogy egy blokk a BLOB másolási naplófájlt nem tudott exportálni:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
A másolási naplófájl azt jelzi, hogy hiba történt a Windows Azure Import/Export szolgáltatás lett letöltését a blob blokkok egyikét a fájlt az exportálási meghajtón. A többi összetevő a fájl letöltése sikeresen befejeződött, és a fájl hosszát helyesen be lett állítva. Ebben az esetben az eszköz nyissa meg a fájlt a meghajtón, a blokk letöltését a tárfiók, és írja a 65536 értékű eltolás hosszúságú 65536-től kezdődő fájl tartománynak.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>RepairExport segítségével ellenőrzi a meghajtó tartalmát  
Használhatja az Azure Import/Export a **RepairExport** lehetőséget a meghajtón a tartalmát érvényesíteni helyesek. A jegyzékfájl minden exportálási meghajtón MD5s a meghajtó tartalmát tartalmazza.  
  
Az Azure Import/Export szolgáltatás is mentheti a jegyzékfájlt tárfiókba az exportálási folyamat során. Fájlok helye keresztül érhető el a [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet, amikor a feladat befejeződött. Lásd: [Import/Export service Manifest fájlformátum](storage-import-export-file-format-metadata-and-properties.md) meghajtó jegyzékfájl formátuma további információt.  
  
A következő példa bemutatja, hogyan együtt az Azure Import/Export eszköz futtatásához a **/ManifestFile** és **/CopyLogFile** paraméterek:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
A következő egy példa a jegyzékfájlt:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
A javítási folyamat végén, az eszköz olvassa végig a jegyzékfájl hivatkozott minden fájl és a fájl integritását a MD5 kivonatok ellenőrzése. A jegyzékfájl felett, a hibaállapotba kerül, a következő összetevők keresztül.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Az ellenőrzés sikertelen összetevők közül bármelyik a eszköz által letöltődik és írni a meghajtón ugyanazt a fájlt.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Az Azure Import/Export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
