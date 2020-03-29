---
title: Azure-importálási/exportálási exportálási feladat javítása - 1. Microsoft dokumentumok
description: Ismerje meg, hogyan javíthatja ki az Azure importálási/exportálási szolgáltatásával létrehozott és futtatott exportálási feladatot.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978015"
---
# <a name="repairing-an-export-job"></a>Exportálási feladat javítása
Miután egy exportálási feladat befejeződött, futtathatja a Microsoft Azure importálási/exportálási eszközt a helyszínen a következő helyekre:  
  
1.  Töltse le azokat a fájlokat, amelyeket az Azure Import/Export szolgáltatás nem tudott exportálni.  
  
2.  Ellenőrizze, hogy a meghajtón lévő fájlok megfelelően vannak-e exportálva.  
  
A funkció használatához kapcsolattal kell rendelkeznie az Azure Storage-hoz.  
  
Az importálási feladat javításának parancsa a **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport paraméterek

A **repairexport**segítségével a következő paraméterek adhatók meg:  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|**/r:<RepairFile fájl\>**|Kötelező. A javítási fájl elérési útja, amely nyomon követi a javítás előrehaladását, és lehetővé teszi a megszakított javítás folytatását. Minden meghajtónak egy és csak egy javítófájllal kell rendelkeznie. Amikor elkezd egy javítást egy adott meghajtóhoz, akkor átadja az elérési utat egy javítási fájlhoz, amely még nem létezik. A megszakított javítás folytatásához adja át egy meglévő javítási fájl nevét. A célmeghajtónak megfelelő javítási fájlt mindig meg kell adni.|  
|**/logdir:<LogDirectory\>**|Választható. A naplókönyvtár. A program részletes naplófájlokat ír ebbe a könyvtárba. Ha nincs megadva naplókönyvtár, a rendszer az aktuális könyvtárat használja naplókönyvtárként.|  
|**/d:<Célkönyvtár\>**|Kötelező. Az érvényesíthető és javítandó könyvtár. Ez általában az exportmeghajtó gyökérkönyvtára, de lehet olyan hálózati fájlmegosztás is, amely az exportált fájlok másolatát tartalmazza.|  
|**/bk:<BitLockerKey\>**|Választható. Ha azt szeretné, hogy az eszköz feloldjon egy titkosított fájlt, ahol az exportált fájlokat tárolja, adja meg a BitLocker-kulcsot.|  
|**/sn:<StorageAccountName\>**|Kötelező. Az exportálási feladat tárfiókjának neve.|  
|**/sk:<StorageAccountKey\>**|Akkor és csak akkor **szükséges,** ha nincs megadva tárolóSAS. Az exportálási feladat tárfiókjának fiókkulcsa.|  
|**/csas:<ContainerSas\>**|**Szükség akkor** és csak akkor, ha a tárfiók kulcsa nincs megadva. A tároló SAS az exportálási feladathoz társított blobok eléréséhez.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Kötelező. A meghajtó másolási naplófájljának elérési útja. A fájlt a Windows Azure importálási/exportálási szolgáltatás hozza létre, és letölthető a feladathoz társított blob storage-ból. A másolási naplófájl a hibás blobokról vagy a javítandó fájlokról tartalmaz információkat.|  
|**/ManifestFile:<DriveManifestFile\>**|Választható. Az exportmeghajtó jegyzékfájljának elérési útja. Ezt a fájlt a Windows Azure importálási/exportálási szolgáltatás hozza létre, és az exportálási meghajtón tárolja, és szükség esetén a feladathoz társított tárfiókblobjában tárolja.<br /><br /> Az exportmeghajtón lévő fájlok tartalmát a fájlban található MD5-kibések ellenőrzik. A sérültnek minősülő fájlok letöltése és újraírása a célkönyvtárakba lesz.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>A RepairExport mód használata a sikertelen exportálás okának javításához  
Az Azure importálási/exportálási eszközzel letöltheti azokat a fájlokat, amelyeket nem sikerült exportálni. A másolási naplófájl tartalmazza azoknak a fájloknak a listáját, amelyeket nem sikerült exportálni.  
  
Az exporthibák okai a következő lehetőségek:  
  
-   Sérült meghajtók  
  
-   A tárfiók kulcsa megváltozott az átviteli folyamat során  
  
Az eszköz **RepairExport** módban történő futtatásához először csatlakoztatnia kell az exportált fájlokat tartalmazó meghajtót a számítógéphez. Ezután futtassa az Azure Importálási/Exportálási eszközt, `/d` és adja meg a meghajtó elérési útját a paraméterrel. Meg kell adnia a meghajtó letöltött másolási naplófájljának elérési útját is. Az alábbi parancssori példa az eszközt futtatja az exportálást nem sikerült fájlok javításához:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Az alábbi példa egy másolati naplófájlt mutat be, amely azt mutatja, hogy a blob egyik blokkja nem sikerült exportálnia:  
  
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
  
A másolási naplófájl azt jelzi, hogy hiba történt, miközben a Windows Azure importálási/exportálási szolgáltatás a blob egyik blokkját töltötte le az exportmeghajtón lévő fájlba. A fájl többi összetevője sikeresen lelett töltve, és a fájl hossza megfelelően lett beállítva. Ebben az esetben az eszköz megnyitja a fájlt a meghajtón, letölti a blokkot a tárfiókból, és a 65536-os eltolástól kezdődően 65536-tól kezdődően 65536 hosszúságú fájltartományba írja.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>A meghajtó tartalmának ellenőrzése a RepairExport programmal  
Használhatja az Azure Import/Export a **RepairExport** opciót is a meghajtó tartalmának helyes ellenőrzéséhez. Az egyes exportmeghajtókon található jegyzékfájl MD5-öket tartalmaz a meghajtó tartalmához.  
  
Az Azure importálási/exportálási szolgáltatás is mentheti a jegyzékfájl fájlokat egy tárfiókba az exportálási folyamat során. A jegyzékfájlok helye a [Feladat betöltése](/rest/api/storageimportexport/jobs) műveleten keresztül érhető el, ha a feladat befejeződött. A meghajtójegyzékfájl formátumáról további információt a [Szolgáltatás jegyzékfájlformátumáról](storage-import-export-file-format-metadata-and-properties.md) talál.  
  
A következő példa bemutatja, hogyan futtatható az Azure importálási/exportálási eszköz a **/ManifestFile** és a **/CopyLogFile** paraméterekkel:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Az alábbi példa egy jegyzékfájlra mutat be:  
  
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
  
A javítási folyamat befejezése után az eszköz végigolvassa a jegyzékfájlban hivatkozott fájlokat, és ellenőrzi a fájl integritását az MD5 kimosókkal. A fenti jegyzékfájl esetében a következő összetevőkön megy keresztül.  

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

Az ellenőrzés sikertelen összetevőit az eszköz letölti, és a meghajtón lévő fájlba írja át.  
  
## <a name="next-steps"></a>További lépések
 
* [Az Azure importálási/exportálási eszközbeállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
