---
title: Támogatott adattárolók az Azure Data Share-ben
description: Ismerje meg az Azure Data Share használatához támogatott adattárakat.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501809"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Támogatott adattárolók az Azure Data Share-ben

Az Azure Data Share nyílt és rugalmas adatmegosztást biztosít, beleértve a különböző adattárakból és adattárakból való megosztást is. Az adatszolgáltatók megoszthatják az adatokat egy adattárból, és adatfogyasztóik kiválaszthatják, hogy melyik adattárba fogadják az adatokat. 

Ebben a cikkben megismerheti az Azure Data Share által támogatott Azure-adattárak gazdag készletét. Az adatszolgáltatók és az adatfogyasztók által kihasználható adattárak kombinációiról is találhat információkat. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Milyen adattárak at támogat az Azure Data Share? 

Az alábbi táblázat részletezi az Azure Data Share támogatott adatforrásait. 

| Adattár | Pillanatkép-alapú megosztás | Helyen történő megosztás 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| 1. generációs Azure Data Lake Storage |✓ | |
| 2. generációs Azure Data Lake Storage |✓ ||
| Azure SQL Database |Nyilvános előzetes verzió | |
| Azure Synapse Analytics (korábban Azure SQL DW) |Nyilvános előzetes verzió | |
| Azure Data Explorer | |Nyilvános előzetes verzió |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure Data Share rugalmasságot biztosít az adatfogyasztók számára, amikor úgy döntenek, hogy egy adattárban fogadja nak adatokat. Például az Azure SQL Database-ből megosztott adatok az Azure Data Lake Store Gen2, az Azure SQL Database vagy az Azure Synapse Analytics szolgáltatásba is beérkezhetnek. Az ügyfelek kiválaszthatják, hogy milyen formátumban fogadják az adatokat a fogadott adatmegosztás konfigurálásakor. 

Az alábbi táblázat részletezi az adatmegosztás elfogadásakor és konfigurálásakor az adatfogyasztók által nyújtott különböző kombinációkat és választási lehetőségeket. Az adatkészlet-leképezések konfigurálásáról az [adatkészlet-leképezések konfigurálása című](how-to-configure-mapping.md)témakörben talál további információt.

|  | Azure Blob Storage | 1. generációs Azure Data Lake Storage | 2. generációs Azure Data Lake Storage | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| 1. generációs Azure Data Lake Storage | ✓ | | ✓|
| 2. generációs Azure Data Lake Storage | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (korábban Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Megosztás tárfiókból
Az Azure Data Share támogatja a fájlok, mappák és fájlrendszerek megosztását az Azure Data Lake Gen1 és az Azure Data Lake Gen2 szolgáltatásból. Azt is támogatja a blobok, mappák és tárolók megosztását az Azure Blob Storage-ból. Jelenleg csak a blokkblob támogatott. Ha a mappák pillanatkép-alapú megosztásban vannak megosztva, az adatfogyasztó dönthet úgy, hogy teljes másolatot készít a megosztási adatokról, vagy a növekményes pillanatkép-képességet kihasználva csak az új vagy frissített fájlokat másolja. Az azonos nevű meglévő fájlok felülíródnak.

## <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az Azure Data Share támogatja az Azure SQL Database és az Azure Synapse Analytics (korábban Azure SQL DW) tábláinak és nézeteinek megosztását. Az adatfogyasztók választhatnak, hogy az adatokat az Azure Data Lake Store Gen2 vagy az Azure Blob Storage-ba csv vagy parkettafájlként fogadják el. Ne feledje, hogy a fájlformátumok alapértelmezés szerint csv.Note that by default, file formats are csv. Az adatfogyasztó dönthet úgy, hogy az adatokat parketta formátumban kapja meg, ha szükséges. Ez az adatfogadáskor az adatkészlet-leképezési beállításokban végezhető el. 

Amikor adatokat fogad az Azure Data Lake Store Gen2 vagy az Azure Blob Storage, teljes pillanatképek felülírja a célfájl tartalmát. 

Az adatfogyasztó dönthet úgy, hogy az adatokat az általa választott táblába fogadja. Ebben a forgatókönyvben, ha a céltábla még nem létezik, az Azure Data Share létrehozza az SQL-táblát a forrássémával. Ha egy céltábla már létezik ugyanazzal a névvel, a rendszer elejti és felülírja a legújabb teljes pillanatképpel. A céltábla leképezésekénül alternatív séma és táblanév adható meg. Növekményes pillanatképek jelenleg nem támogatottak. 

Az SQL-alapú forrásokból történő megosztás előfeltételekkel rendelkezik a tűzfalszabályokhoz és engedélyekhez kapcsolódóan. Kérjük, olvassa el az adatok [megosztásának](share-your-data.md) előzményeit.

## <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Data Explorerből
Az Azure Data Share támogatja az adatbázisok helyben történő megosztását az Azure Data Explorer-fürtökből. Az adatszolgáltató adatbázis- vagy fürtszinten is megoszthat. Az adatbázis szintjén megosztott adatfogyasztó csak az adatszolgáltató által megosztott adatbázis(ok)hoz férhet hozzá. Fürtszinten megosztva az adatfogyasztó hozzáférhet a szolgáltató fürtjének összes adatbázisához, beleértve az adatszolgáltató által létrehozott jövőbeli adatbázisokat is.

A megosztott adatbázisok eléréséhez az adatfogyasztónak saját Azure Data Explorer-fürttel kell rendelkeznie. Az adatfogyasztó Azure Data Explorer-fürtjének ugyanabban az Azure-adatközpontban kell megtelepednie, mint az adatszolgáltató Azure Data Explorer-fürtjének. Megosztási kapcsolat létrehozásakor az Azure Data Share szimbolikus kapcsolatot hoz létre a szolgáltató és a fogyasztó Azure Data Explorer-fürtjei között.

Az Azure Data Explorer két adatbetöltési módot támogat: a kötegelt és a streamelést. A megosztott adatbázisban a kötegből kapott adatok néhány másodperc től néhány percig jelennek meg az adatfogyasztói oldalon. A streamelésből származó adatok akár 24 órát is igénybe vehetnek, hogy megjelenjenek az adatfogyasztói oldalon. 

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md)
