---
title: Az Azure importálási/exportálási szolgáltatásának követelményei | Microsoft dokumentumok
description: Ismerje meg az Azure importálási/exportálási szolgáltatásszoftver- és hardverkövetelményeit.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255313"
---
# <a name="azure-importexport-system-requirements"></a>Az Azure Import/Export rendszerkövetelményei

Ez a cikk ismerteti az Azure importálási/exportálási szolgáltatás fontos követelményeit. Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt használná az Importálás/exportálás szolgáltatást, majd szükség szerint hivatkozzon rá a művelet során.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A merevlemezek WAImportExport eszközzel történő előkészítéséhez a következő 64 bites operációs rendszer támogatja a **BitLocker meghajtótitkosítást.**


|Platform |Verzió |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Egyéb szükséges szoftverek a Windows-ügyfélhez

|Platform |Verzió |
|---------|---------|
|.NET-keretrendszer    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Az Azure import/export szolgáltatás a következő típusú tárfiókokat támogatja:

- Normál általános célú v2 tárfiókok (a legtöbb esetben ajánlott)
- Blob Storage-fiókok
- Általános célú v1 tárfiókok (klasszikus vagy Azure Resource Manager-telepítések egyaránt),

A tárfiókokról az [Azure storage-fiókok áttekintése című témakörben olvashat bővebben.](storage-account-overview.md)

Minden feladat használható adatok átvitele vagy csak egy tárfiókból. Más szóval egyetlen importálási/exportálási feladat nem terjedhet ki több tárfiókra. Az új tárfiók létrehozásáról a [Tárfiók létrehozása című témakörben talál](storage-account-create.md)további információt.

> [!IMPORTANT]
> Az Azure Import Export szolgáltatás nem támogatja azokat a tárfiókokat, amelyeknél a [virtuális hálózati szolgáltatás végpontjai](../../virtual-network/virtual-network-service-endpoints-overview.md) szolgáltatás engedélyezve van. 

## <a name="supported-storage-types"></a>Támogatott tárolótípusok

A következő lista a tárolási típusok támogatja az Azure import/export szolgáltatás.


|Feladat  |Tárolási szolgáltatás |Támogatott  |Nem támogatott  |
|---------|---------|---------|---------|
|Importálás     |  Azure Blob Storage <br><br> Azure File Storage       | Támogatott blobok és lapblobok blokkolása <br><br> Támogatott fájlok          |
|Exportálás     |   Azure Blob Storage       | Támogatott blokkblobok, lapblobok és hozzáfűző blobok         | Az Azure-fájlok nem támogatottak


## <a name="supported-hardware"></a>Támogatott hardver

Az Azure import/export szolgáltatáshoz támogatott lemezekre van szükség az adatok másolásához.

### <a name="supported-disks"></a>Támogatott lemezek

Az importálási/exportálási szolgáltatással való használatra a lemezek alábbi listája támogatott.


|Lemeztípus  |Méret  |Támogatott |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

A következő lemeztípusok nem támogatottak:
- Usbs.
- Külső HDD beépített USB adapterrel.
- Külső HDD-burkolaton belüli lemezek.

Egyetlen importálási/exportálási feladat nak lehet:
- Legfeljebb 10 HDD/SSD.
- Bármilyen méretű HDD/SSD keveréke.

A meghajtók nagy száma több feladat között is elosztható, és nincs korlátozva a létrehozható feladatok száma. Importálási feladatok esetén csak a meghajtó első adatkötete kerül feldolgozásra. Az adatkötetet NTFS rendszerrel kell formázni.

A merevlemezek előkészítésekor és az adatok WAImportExport eszközzel történő másolásakor külső USB-adaptereket is használhat. A legtöbb kész USB 3.0 vagy újabb adapternek működnie kell.


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](storage-use-azcopy.md)
* [Azure Import Export REST API-minta](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
