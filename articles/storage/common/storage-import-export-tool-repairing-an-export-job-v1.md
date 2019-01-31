---
title: Az Azure Import/Export exportálási feladat - v1 javítása |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozott és az Azure Import/Export szolgáltatás használatával futtassa exportálási feladat javítása.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 395ff654bcacf1a4f70a9032492deb2a9d5202f3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454759"
---
# <a name="repairing-an-export-job"></a>Exportálási feladat javítása
Exportálási feladat befejezését követően a Microsoft Azure Import/Export eszköz a helyszíni futtathatja:  
  
1.  Töltse le azokat a fájlokat, az Azure Import/Export szolgáltatás nem tudta exportálni.  
  
2.  Ellenőrizze, hogy a meghajtó a fájlok megfelelően lettek-e exportálva.  
  
Kapcsolat az Azure Storage-e funkció használatához rendelkeznie kell.  
  
A parancs az importálási feladat javítása a **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport paraméterek

A következő paraméterek adható **RepairExport**:  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|**r: < RepairFile\>**|Kötelező. A javítási fájlt, amely nyomon követi a folyamatot a javítás, és lehetővé teszi, hogy folytatni az megszakított javítását elérési útja. Minden olyan meghajtó csak egy javítási fájlnak kell lennie. Amikor egy adott meghajtó javítás, fog adja át az elérési út egy javítási fájlba, amely még nem létezik. Az megszakított javítását folytatásához be egy meglévő helyreállítási fájl nevét kell átadnia. A javítási fájlt a cél meghajtó megfelelő mindig meg kell adni.|  
|**/ logdir: < LogDirectory\>**|Választható. A naplózási könyvtár. Ez a könyvtár részletes naplófájlok lesz írva. Ha nincs naplókönyvtár van megadva, a naplózási könyvtár az aktuális könyvtárban lesz.|  
|**/ d: < TargetDirectory\>**|Kötelező. A könyvtár ellenőrzése és javítása. Ez általában a az export-meghajtó gyökérkönyvtárát, de sikerült is kell egy hálózati fájlmegosztásra tartalmazó egy másolatot az exportált fájlokat.|  
|**/bk:<BitLockerKey\>**|Választható. A BitLocker-kulcsot kell megadnia, ha azt szeretné, hogy az eszköz zárolásának feloldásához egy titkosított az exportált fájlok tárolására.|  
|**/sn:<StorageAccountName\>**|Kötelező. A storage-fiók neve az exportálási feladatot.|  
|**/sk:<StorageAccountKey\>**|**Szükséges** csak, ha nincs megadva egy SAS-tárolót. A fiókkulcs a storage-fiókját az exportálási feladatot.|  
|**/csas:<ContainerSas\>**|**Szükséges** csak, ha nincs megadva a tárfiók-kulcsot. A tároló SAS a blobok az exportálási feladatot társított eléréséhez.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Kötelező. A meghajtó másolása naplófájl elérési útja. A fájlt a Windows Azure Import/Export szolgáltatás által létrehozott, és a feladathoz hozzárendelt blob storage-ból letölthető. A log fájl másolása sikertelen blobok vagy fájlokat, amelyek javításra szorul kapcsolatos információkat tartalmazza.|  
|**/ ManifestFile: < DriveManifestFile\>**|Választható. Az exportálási meghajtó Alkalmazásjegyzék-fájl elérési útja. Ezt a fájlt a Windows Azure Import/Export szolgáltatás által létrehozott és tárolt, az Exportálás meghajtón és opcionálisan a feladathoz társított storage-fiókban található blob.<br /><br /> Az MD5-kivonatát a fájlban található a rendszer ellenőrzi a exportálási meghajtón található fájlok tartalmát. Azokat a fájlokat, valószínűleg sérült határozza meg a rendszer letölti és a cél könyvtárak átírása.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Javítsa ki a hibás exportálások RepairExport mód használata  
Az Azure Import/Export eszköz segítségével, amely nem sikerült exportálni a fájlok letöltése. A másolási naplófájlt fogja tartalmazni, amely nem sikerült exportálni a fájlok listáját.  
  
Az exportálási hibák okai a következő lehetőségek állnak rendelkezésére:  
  
-   Sérült meghajtók  
  
-   A tárfiók-kulcsot az átvitel során megváltozott  
  
Az eszköz futtatásához **RepairExport** mód, először csatlakoznia kell a számítógépre az exportált fájlokat tartalmazó meghajtót. Ezután futtassa az Azure Import/Export eszköz, a meghajtó elérési útját adja meg a `/d` paraméter. Meg kell adja meg a naplófájl elérési útja a meghajtó másolása letöltött is. A következő parancssori példa az alábbi futtatja az eszközt nem sikerült exportálni a fájlok helyreállításához:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Az alábbiakban látható egy példa, amely egy kódrészleten jeleníti meg a blob másolási naplófájl nem sikerült exportálni:  
  
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
  
A másolási naplófájl azt jelzi, hogy hiba történt a Windows Azure Import/Export szolgáltatás lett letöltése a blob blokkok egyikét a fájl exportálása a meghajtón. A többi összetevő, a fájl letöltése sikerült, és a fájl hosszát megfelelően be lett állítva. Ebben az esetben az eszköz fog nyissa meg a fájlt a meghajtón, a blokkblob letöltése a storage-fiókból, és azt írni a fájl tartomány eltolása 65536 hosszúságú 65536-től.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Meghajtó tartalmát érvényesíteni RepairExport használatával  
Az Azure Import/Export is használhatja a **RepairExport** helyesek-e a meghajtó tartalmát érvényesíteni a beállítást. Az Alkalmazásjegyzék-fájl minden kiviteli meghajtót a meghajtó tartalmát MD5s tartalmazza.  
  
Az Azure Import/Export szolgáltatás mentheti is a fájlok egy storage-fiókba az exportálási folyamat során. A fájlok helyét keresztül érhető el a [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet, amikor a feladat befejeződött. Lásd: [Import/Export service Manifest fájl formátuma](storage-import-export-file-format-metadata-and-properties.md) meghajtó jegyzékfájl formátuma további információt.  
  
Az alábbi példa bemutatja, hogyan futtathat az Azure Import/Export eszközzel rendelkező a **/ManifestFile** és **/CopyLogFile** paraméterek:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Az alábbiakban látható egy példa a jegyzékfájlt:  
  
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
  
A javítási folyamat befejezése, miután az eszköz olvassa végig a jegyzékfájlban hivatkozott fájlok, és ellenőrizze az MD5-kivonatát a fájl integritását. A jegyzékfájl felett akkor fog áthaladni a következő összetevőket.  

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

Minden olyan összetevő, az ellenőrzés sikertelen lesz az eszköz által letölthető, és ugyanazt a fájlt a meghajtón átírása.  
  
## <a name="next-steps"></a>További lépések
 
* [Az Azure Import/Export eszköz telepítése](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
