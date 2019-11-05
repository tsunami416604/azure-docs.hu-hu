---
title: Támogatott adattárak az Azure-adatmegosztásban
description: Ismerje meg az Azure-adatmegosztás használatát támogató adattárakat.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516395"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Támogatott adattárak az Azure-adatmegosztásban

Az Azure-beli adatmegosztás nyitott és rugalmas adatmegosztást biztosít, többek között a különböző adattárakból történő megosztási képességet is beleértve. Az adatszolgáltatók megoszthatnak egy adattárolóból származó adatmegosztást, és az adatok fogyasztóik kiválaszthatják, hogy melyik adattárba fogadják az adatok. 

Ebből a cikkből megismerheti az Azure-adatmegosztások által támogatott Azure-adattárak gazdag készletét. Emellett az adatszolgáltatók és az adatfogyasztók által kihasználható adattárak kombinációit is megtalálhatja. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Milyen adattárakat támogat az Azure-adatmegosztás? 

Az alábbi táblázat az Azure-adatmegosztás támogatott adatforrásait részletezi. 

| Adattár | Pillanatkép-alapú megosztás | Helyben történő megosztás 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| 1\. generációs Azure Data Lake Storage |✓ | |
| 2\. generációs Azure Data Lake Storage |✓ ||
| Azure SQL Database |Nyilvános előzetes verzió | |
| Azure SQL Data Warehouse |Nyilvános előzetes verzió | |
| Azure Data Explorer | |[Korlátozott előzetes verzió](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure-beli adatmegosztás rugalmasságot biztosít az adattáraknak az adattárakban való elfogadásához. Előfordulhat például, hogy az Azure SQL Database által megosztott adatok Azure Data Lake Store Gen2, Azure SQL Database vagy Azure SQL Data Warehouseba érkeznek. Az ügyfelek kiválaszthatják, hogy a fogadott adatmegosztás konfigurálásakor milyen formátumú adatfogadásra van lehetőség. 

Az alábbi táblázat a különböző kombinációkat és választásokat ismerteti, amelyeket az adatfogyasztók az adatmegosztás elfogadásakor és konfigurálásakor tartalmaznak. Az adatkészlet-hozzárendelések konfigurálásával kapcsolatos további információkért lásd: [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure SQL Data Warehouse 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| 1\. generációs Azure Data Lake Storage |✓ | |✓|
| 2\. generációs Azure Data Lake Storage |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure SQL Data Warehouse |✓ | |✓|✓|✓|

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
