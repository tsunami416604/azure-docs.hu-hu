---
title: Támogatott adattárak az Azure-adatmegosztásban
description: Ismerje meg az Azure-adatmegosztás használatát támogató adattárakat.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438689"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Támogatott adattárak az Azure-adatmegosztásban

Az Azure-beli adatmegosztás nyitott és rugalmas adatmegosztást biztosít, többek között a különböző adattárakból történő megosztási képességet is beleértve. Az adatszolgáltatók megoszthatnak egy adattárolóból származó adatmegosztást, és az adatok fogyasztóik kiválaszthatják, hogy melyik adattárba fogadják az adatok. 

Ebből a cikkből megismerheti az Azure-adatmegosztások által támogatott Azure-adattárak gazdag készletét. Emellett az adatszolgáltatók és az adatfogyasztók által kihasználható adattárak kombinációit is megtalálhatja. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Milyen adattárakat támogat az Azure-adatmegosztás? 

Az alábbi táblázat az Azure-adatmegosztás támogatott adatforrásait részletezi. 

| Adattár | Pillanatkép-alapú megosztás | Helyben történő megosztás 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |Nyilvános előzetes verzió | |
| Azure szinapszis Analytics (korábban Azure SQL DW) |Nyilvános előzetes verzió | |
| Azure Adatkezelő | |[Korlátozott előzetes verzió](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure-beli adatmegosztás rugalmasságot biztosít az adattáraknak az adattárakban való elfogadásához. Előfordulhat például, hogy az Azure SQL Database által megosztott adatok Azure Data Lake Store Gen2, Azure SQL Database vagy az Azure szinapszis Analyticsbe érkeznek. Az ügyfelek kiválaszthatják, hogy a fogadott adatmegosztás konfigurálásakor milyen formátumú adatfogadásra van lehetőség. 

Az alábbi táblázat a különböző kombinációkat és választásokat ismerteti, amelyeket az adatfogyasztók az adatmegosztás elfogadásakor és konfigurálásakor tartalmaznak. Az adatkészlet-hozzárendelések konfigurálásával kapcsolatos további információkért lásd: [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md).

|  | Azure Blob Storage | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
