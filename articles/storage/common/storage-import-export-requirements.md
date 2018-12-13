---
title: Az Azure Import/Export szolgáltatás követelményei |} A Microsoft Docs
description: Ismerje meg az Azure Import/Export szolgáltatás szoftveres és hardveres követelményekkel.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 5f6908a80a467e1a8a12d61be2f67cbe70cce8a0
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275637"
---
# <a name="azure-importexport-system-requirements"></a>Az Azure Import/Export rendszerre vonatkozó követelmények

Ez a cikk ismerteti az Azure Import/Export szolgáltatás a fontos követelmények. Azt javasoljuk, hogy mielőtt, tekintse át az információkat gondosan meg az Import/Export szolgáltatás használata, majd vissza, szükség esetén a művelet során.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A merevlemez-meghajtókat a WAImportExport eszközzel, a következő előkészítése **64 bites operációs rendszer, amely támogatja a BitLocker meghajtótitkosítás** támogatottak.


|Platform |Verzió |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> A Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows-ügyfél szükséges szoftverek

|Platform |Verzió |
|---------|---------|
|.NET-keretrendszer    | 4.5.1       |
| BitLocker        |  \_          |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Az Azure Import/Export szolgáltatás támogatja a következő típusú storage-fiókok:

- Általános célú v2 storage-fiókok (a legtöbb forgatókönyvek esetén ajánlott)
- Blob Storage-fiókok
- Általános célú v1-tárfiókok (klasszikus és a Azure Resource Manager üzembe helyezések), 

Storage-fiókokkal kapcsolatos további információkért lásd: [az Azure storage-fiókok áttekintése](storage-account-overview.md).

Minden egyes feladat előfordulhat, hogy használható-adatok áthelyezésénél csak egy storage-fiókból. Más szóval egy egyetlen importálási/exportálási feladat terjedhetnek ki több tárfiókon keresztül. Új tárfiók létrehozásával kapcsolatos információkért lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md).

> [!IMPORTANT] 
> Az Azure importálási exportálása szolgáltatás nem támogatja a storage-fiókok, a [virtuális hálózati Szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) funkció engedélyezve van. 

## <a name="supported-storage-types"></a>Támogatott tárolási típus

A tárolási típusok a következők közül az Azure Import/Export szolgáltatás használata támogatott.


|Feladat  |Storage szolgáltatás |Támogatott  |Nem támogatott  |
|---------|---------|---------|---------|
|Importálás     |  Azure Blob Storage <br><br> Az Azure File storage       | Támogatott Blokkblobok és lapblobok <br><br> Támogatott fájlok          |
|Exportálás     |   Azure Blob Storage       | A blokkblobok, lapblobok és hozzáfűző blobok támogatott         | Az Azure Files nem támogatott


## <a name="supported-hardware"></a>Támogatott hardveres 

Az Azure Import/Export szolgáltatás az adatok másolása támogatott lemez szükséges.

### <a name="supported-disks"></a>Támogatott lemezek

Lemezek a következők közül az Import/Export szolgáltatás használata támogatott.


|Lemez típusa  |Méret  |Támogatott |Nem támogatott  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |         |         |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III.         |Beépített USB-adapterrel rendelkező külső HDD <br> A kis-és a egy külső HDD belül a lemez         |


Egy egyetlen importálási/exportálási feladat veheti fel:
- Legfeljebb 10 HDD és SSD-kkel.
- HDD és SSD és méretű vegyesen.

Nagy számú meghajtók oszlik el több feladat között, és ott nem hozható létre a feladatok száma korlátlan. Az importálási feladatokhoz csak az első adatmennyiség a meghajtón dolgoz fel. Az adatmennyiség NTFS fájlrendszerrel kell formázni.

Ha a merevlemez-meghajtók előkészítése, és a WAImportExport eszköz használata az adatok másolásának, külső USB-adaptereket is használhat. Legtöbb megoldásszolgáltatóknál USB 3.0-s vagy újabb adaptereket működnie kell. 


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Az Azure importálási exportálása REST API-minta](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

