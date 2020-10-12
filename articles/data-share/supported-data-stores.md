---
title: Az Azure Data Share támogatott adattárai
description: Ismerje meg az Azure-adatmegosztás használatát támogató adattárakat.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: bb8b13e1141a8cb4610e15ed693e28042dd20d72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259013"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Az Azure Data Share támogatott adattárai

Az Azure-beli adatmegosztás nyitott és rugalmas adatmegosztást biztosít, többek között a különböző adattárakból történő megosztási képességet is beleértve. Az adatszolgáltatók megoszthatnak egy adattárolóból származó adatmegosztást, és az adatok fogyasztóik kiválaszthatják, hogy melyik adattárba fogadják az adatok. 

Ebből a cikkből megismerheti az Azure-adatmegosztások által támogatott Azure-adattárak gazdag készletét. Emellett az adatszolgáltatók és az adatfogyasztók által kihasználható adattárak kombinációit is megtalálhatja. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Milyen adattárakat támogat az Azure-adatmegosztás? 

Az alábbi táblázat az Azure-adatmegosztás támogatott adatforrásait részletezi. 

| Adattár | Pillanatkép-alapú megosztás | Helyi megosztás 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| 1. generációs Azure Data Lake Storage |✓ | |
| 2. generációs Azure Data Lake Storage |✓ ||
| Azure SQL Database |Nyilvános előzetes verzió | |
| Azure szinapszis Analytics (korábban Azure SQL DW) |Nyilvános előzetes verzió | |
| Azure Data Explorer | |✓ |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure-beli adatmegosztás rugalmasságot biztosít az adattáraknak az adattárakban való elfogadásához. Előfordulhat például, hogy az Azure SQL Database által megosztott adatok Azure Data Lake Store Gen2, Azure SQL Database vagy az Azure szinapszis Analyticsbe érkeznek. Az ügyfelek kiválaszthatják, hogy a fogadott adatmegosztás konfigurálásakor milyen formátumú adatfogadásra van lehetőség. 

Az alábbi táblázat a különböző kombinációkat és választásokat ismerteti, amelyeket az adatfogyasztók az adatmegosztás elfogadásakor és konfigurálásakor tartalmaznak. Az adatkészlet-hozzárendelések konfigurálásával kapcsolatos további információkért lásd: [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md).

| Adattár | Azure Blob Storage | 1. generációs Azure Data Lake Storage | 2. generációs Azure Data Lake Storage | Azure SQL Database | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| 1. generációs Azure Data Lake Storage | ✓ | | ✓ ||
| 2. generációs Azure Data Lake Storage | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure szinapszis Analytics (korábban Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Megosztás egy Storage-fiókból
Az Azure-beli adatmegosztás támogatja a fájlok, mappák és fájlrendszerek megosztását Azure Data Lake Gen1 és Azure Data Lake Gen2. Emellett támogatja a Blobok, mappák és tárolók megosztását az Azure Blob Storage-ból. Jelenleg csak a Block blob támogatott. Ha a fájlrendszer, a tárolók vagy a mappák a pillanatkép-alapú megosztásban vannak megosztva, az adatfogyasztó dönthet úgy, hogy teljes másolatot készít a megosztási adatokról, vagy a növekményes pillanatkép-képességgel csak az új vagy frissített fájlokat másolja. A növekményes pillanatkép a fájlok utolsó módosításának időpontján alapul. A rendszer felülírja az azonos nevű meglévő fájlokat.

A részletekért tekintse [meg az Azure-Blob Storage és Azure Data Lake Storage adatainak megosztása és fogadása](how-to-share-from-storage.md) című témakört.

## <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az Azure-beli adatmegosztás támogatja a táblák és nézetek megosztását a Azure SQL Database és az Azure szinapszis Analytics (korábban Azure SQL DW) használatával. Az adatfogyasztók dönthetnek úgy, hogy elfogadják az Azure Data Lake Storage Gen2 vagy az Azure Blob Storage CSV-vagy Parque-fájlként, valamint a Azure SQL Database és az Azure szinapszis Analytics táblázatként való elfogadását.

Amikor Azure Data Lake Store Gen2 vagy Azure Blob Storageba fogadja az adatfogadást, a teljes Pillanatképek felülírják a célfájl tartalmát, ha már létezik.
Amikor az Adatfogadás a táblába történik, és ha a céltábla még nem létezik, az Azure-beli adatmegosztás létrehozza az SQL-táblázatot a forrás sémával. Ha már létezik ilyen nevű céltábla, a rendszer elveti és felülírja a legújabb teljes pillanatképtel. A növekményes Pillanatképek jelenleg nem támogatottak.

A részletekért tekintse [meg a Azure SQL Database és az Azure szinapszis Analytics adatainak megosztásával és fogadásával](how-to-share-from-sql.md) kapcsolatos információkat.

## <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Data Explorerből
Az Azure-beli adatmegosztás lehetővé teszi az adatbázisok helyben történő megosztását az Azure Adatkezelő-fürtökön. Az adatszolgáltató megoszthatja az adatbázist vagy a fürt szintjét. Az adatbázis szintjén megosztva az adatfogyasztó csak az adatszolgáltató által megosztott adatbázis (oka) t fogja tudni elérni. A fürt szintjén megosztott adatfogyasztók a szolgáltató fürtjéből származó összes adatbázishoz hozzáférhetnek, beleértve az adatszolgáltató által létrehozott jövőbeli adatbázisokat is.

A megosztott adatbázisok eléréséhez az adatfogyasztónak saját Azure Adatkezelő-fürtöt kell használnia. Az adatfogyasztó Azure Adatkezelő fürtjének ugyanabban az Azure-adatközpontban kell megkeresnie, mint az adatszolgáltató Azure Adatkezelő-fürtje. A megosztási kapcsolat létrehozásakor az Azure-beli adatmegosztás szimbolikus hivatkozást hoz létre a szolgáltató és a fogyasztó Azure Adatkezelő-fürtök között. A Batch-módban betöltött, a forrás Azure Adatkezelő-fürtbe betöltött adatmennyiség néhány másodpercen belül, néhány percen belül megjelenik a célként megadott fürtön.

A részletekért tekintse [meg az Azure-adatkezelő adatainak megosztása és fogadása](/azure/data-explorer/data-share) című témakört. 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
