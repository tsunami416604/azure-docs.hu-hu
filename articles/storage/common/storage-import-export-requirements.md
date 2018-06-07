---
title: Azure Import/Export szolgáltatás követelményei |} Microsoft Docs
description: Ismerje meg az Azure Import/Export szolgáltatás szoftveres és hardveres követelményekkel.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/14/2018
ms.author: alkohli
ms.openlocfilehash: eb50846bd838597a6f1e9a0b6550595866e05edc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660879"
---
# <a name="azure-importexport-system-requirements"></a>Az Azure Import/Export rendszerkövetelményei

Ez a cikk ismerteti a fontos követelmények az Azure Import/Export szolgáltatás. Javasoljuk, hogy tekintse át az információkat gondosan majd hivatkozik vissza rá szükség esetén a művelet során és az Import/Export szolgáltatás használata előtt.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A merevlemez-meghajtókat a WAImportExport eszközzel, a következő előkészítéséhez **64 bites operációs rendszer, amely támogatja a BitLocker meghajtótitkosítás** támogatottak.


|Platform |Verzió |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>Támogatott storage-fiókok

Az Azure Import/Export szolgáltatás támogatja a következő Azure storage-fiókok.
- Klasszikus
- Blob Storage-fiókok
- Általános célú v1 tárfiókok. 

Minden feladat adatátvitel vagy a csak egy tárfiókot is használható. Más szóval egy egyetlen importálási/exportálási feladatok nem terjedhetnek ki több tárfiókok között. Új tárfiók létrehozásával kapcsolatos további információkért lásd: [a Storage-fiók létrehozása](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Az Azure importálási exportálása szolgáltatás nem támogatja a storage-fiókok ahol a [virtuális hálózati Szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) szolgáltatás engedélyezve van. 

## <a name="supported-storage-types"></a>Támogatott tárolási típusok

Az alábbi lista a tárolási típusok Azure Import/Export szolgáltatás használata támogatott.


|Feladat  |Storage  |Támogatott  |Nem támogatott  |
|---------|---------|---------|---------|
|Importálás     |  Az Azure Blob storage. <br>Blokkblobokat, lapblobokat támogatott. <br> Az Azure Files támogatott.       |         |
|Exportálás     |   Az Azure Blob storage. <br>Blokkblobokat, lapblobokat és hozzáfűző blobok támogatott.       | Az Azure Files nem támogatott.        |


## <a name="supported-hardware"></a>A támogatott hardverek 

Az Azure Import/Export szolgáltatás támogatott lemez szükséges, és támogatja az adatok másolása SATA-összekötőt.

### <a name="supported-disks"></a>Támogatott lemezek

Az alábbi listán szereplő lemezek az Import/Export szolgáltatás használata támogatott.


|Lemez típusa  |Méret  |Támogatott |Nem támogatott  |
|---------|---------|---------|---------|
|SSD    |   2.5"      |         |         |
|HDD     |  2.5"<br>3.5-ös "       |SATA II, SATA III.         |Beépített USB-adapterrel rendelkező külső HDD <br> A kis-és nagybetűhasználat egy külső HDD lemezt         |


Egy egyetlen importálási/exportálási feladatok veheti fel:
- Legfeljebb 10 HDD/SSD-k.
- A HDD/SSD bármilyen méretű kombinációját.

Nagy számú meghajtókat is elosztva több feladat és a nem hozható létre feladatok száma korlátozza. 

Az importálási feladatok csak az első adatmennyiség a meghajtón dolgoz fel. Az adatmennyiség NTFS fájlrendszerrel kell formázni.

### <a name="supported-external-usb-adaptors"></a>Támogatott külső USB-adapterek

Az alábbiakban az adatok másolása belső HDD használt külső USB-adapterek listáját. 
- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- 6628SUS3-C-fekete Orico (6628 sorozat)
- Thermaltake BlacX gyakran használt adatok-csere SATA külső merevlemez meghajtó rögzített állomás (USB 2.0-s & eSATA)


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Az Azure importálási exportálása REST API minta](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

