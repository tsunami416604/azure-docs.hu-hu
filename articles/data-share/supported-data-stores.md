---
title: Támogatott adattárak az Azure-adatmegosztásban
description: Ismerje meg az Azure-adatmegosztás használatát támogató adattárakat.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e0daa2b02c16d8d5a65b5e7e0f983a4f47181d40
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635968"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Támogatott adattárak az Azure-adatmegosztásban

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
| Azure Data Explorer | |Nyilvános előzetes verzió |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure-beli adatmegosztás rugalmasságot biztosít az adattáraknak az adattárakban való elfogadásához. Előfordulhat például, hogy az Azure SQL Database által megosztott adatok Azure Data Lake Store Gen2, Azure SQL Database vagy az Azure szinapszis Analyticsbe érkeznek. Az ügyfelek kiválaszthatják, hogy a fogadott adatmegosztás konfigurálásakor milyen formátumú adatfogadásra van lehetőség. 

Az alábbi táblázat a különböző kombinációkat és választásokat ismerteti, amelyeket az adatfogyasztók az adatmegosztás elfogadásakor és konfigurálásakor tartalmaznak. Az adatkészlet-hozzárendelések konfigurálásával kapcsolatos további információkért lásd: [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md).

|  | Azure Blob Storage | 1. generációs Azure Data Lake Storage | 2. generációs Azure Data Lake Storage | Azure SQL Database | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| 1. generációs Azure Data Lake Storage | ✓ | | ✓ ||
| 2. generációs Azure Data Lake Storage | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure szinapszis Analytics (korábban Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Megosztás egy Storage-fiókból
Az Azure-beli adatmegosztás támogatja a fájlok, mappák és fájlrendszerek megosztását Azure Data Lake Gen1 és Azure Data Lake Gen2. Emellett támogatja a Blobok, mappák és tárolók megosztását az Azure Blob Storage-ból. Jelenleg csak a Block blob támogatott. Ha a fájlrendszer, a tárolók vagy a mappák a pillanatkép-alapú megosztásban vannak megosztva, az adatfogyasztó dönthet úgy, hogy teljes másolatot készít a megosztási adatokról, vagy a növekményes pillanatkép-képességgel csak az új vagy frissített fájlokat másolja. A növekményes pillanatkép a fájlok utolsó módosításának időpontján alapul. A rendszer felülírja az azonos nevű meglévő fájlokat.

## <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az Azure-beli adatmegosztás támogatja a táblák és nézetek megosztását a Azure SQL Database és az Azure szinapszis Analytics (korábban Azure SQL DW) használatával. Az adatfogyasztók dönthetnek úgy, hogy elfogadják az adatAzure Data Lake Store Gen2 vagy az Azure Blob Storage CSV-vagy Parque-fájlként. Vegye figyelembe, hogy alapértelmezés szerint a fájlformátumok CSV-fájlok. Az adatfogyasztó dönthet úgy, hogy a kívánt módon a parketta formátumban fogadja az adott adatkérést. Ez az adatkészlet-leképezési beállításokban végezhető el az adat fogadásakor. 

Az Azure Data Lake Store Gen2 vagy az Azure Blob Storageba való befogadásakor a teljes Pillanatképek felülírják a célfájl tartalmát. 

Az adatfogyasztók dönthetnek úgy, hogy az Ön által választott táblázatba fogadják az adatmennyiséget. Ebben az esetben, ha a céltábla még nem létezik, az Azure-adatmegosztás létrehozza az SQL-táblázatot a forrás sémával. Ha egy céltábla már létezik ugyanazzal a névvel, a rendszer elveti és felülírja a legújabb teljes pillanatképtel. A céltábla leképezése esetén megadhat egy alternatív sémát és egy Táblanév nevet is. A növekményes Pillanatképek jelenleg nem támogatottak. 

Az SQL-alapú forrásokból való megosztáshoz a tűzfalszabályok és az engedélyek előfeltételei vonatkoznak. A részletekért tekintse meg az [adatok megosztása](share-your-data.md) oktatóanyag előfeltételek című szakaszát.

## <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Adatkezelő
Az Azure-beli adatmegosztás lehetővé teszi az adatbázisok helyben történő megosztását az Azure Adatkezelő-fürtökön. Az adatszolgáltató megoszthatja az adatbázist vagy a fürt szintjét. Az adatbázis szintjén megosztva az adatfogyasztó csak az adatszolgáltató által megosztott adatbázis (oka) t fogja tudni elérni. A fürt szintjén megosztott adatfogyasztók a szolgáltató fürtjéből származó összes adatbázishoz hozzáférhetnek, beleértve az adatszolgáltató által létrehozott jövőbeli adatbázisokat is.

A megosztott adatbázisok eléréséhez az adatfogyasztónak saját Azure Adatkezelő-fürtöt kell használnia. Az adatfogyasztó Azure Adatkezelő fürtjének ugyanabban az Azure-adatközpontban kell megkeresnie, mint az adatszolgáltató Azure Adatkezelő-fürtje. A megosztási kapcsolat létrehozásakor az Azure-beli adatmegosztás szimbolikus hivatkozást hoz létre a szolgáltató és a fogyasztó Azure Adatkezelő-fürtök között.

Az Azure Adatkezelő kétféle adatfeldolgozási módot támogat: a Batch és a streaming. A megosztott adatbázis batch szolgáltatásból kapott adatok néhány másodperc és néhány perc között az adatfogyasztó oldalon is megjelennek. A streamingtől kapott adatok akár 24 órát is igénybe vehetnek, hogy megjelenjenek az adatfeldolgozó oldalon. 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
