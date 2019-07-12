---
title: Az Azure Data megosztás előzetes terminológiája
description: Az Azure Data megosztás előzetes terminológiája
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789227"
---
# <a name="azure-data-share-preview-concepts"></a>Az Azure Data megosztás előzetes fogalmak 

Az Azure Data megosztás előzetes verzióban bemutatkozik néhány kapcsolódó adatok megosztásának új fogalmakkal ismerkedik. Ez a cikk ismerteti, hogy néhány gyakran használt kifejezéseinek is láthatja, amelyeket a szolgáltatás. 

## <a name="data-provider"></a>Adatszolgáltató

Adatszolgáltató a szervezet, amely adatokat oszt meg márkák. Az adatszolgáltató általában tulajdonosa és a egy gondnoka az adatok is lehetnek. Adatszolgáltatók szeretné megosztani adatokat különböző típusú. Adatszolgáltató előfordulhat, hogy szeretne megosztani adatokat, például a nyers adatokat, például a sales vagy idősorozat-adatok. Adatszolgáltató is érdemes lehet Előfeldolgozott, összeválogatott adatokat, amely már tartalmazza az elemzéseket és információkat megosztani. 

## <a name="data-consumer"></a>Adatfogyasztó 

Adatfogyasztó a szervezet, amely kap adatokat egy adatszolgáltató. Az adatfogyasztó lehet térni a csatlakozás a megosztott adatok a saját adatokkal is használható elemzéseket. Bizonyos esetekben az adatfogyasztó előfordulhat, hogy kap a feldolgozott adatokat. 

## <a name="data-share"></a>Az adatok megosztása

Egy az adatok megosztása az egy csoport egyetlen egységként megosztott adatkészletek. Az adatkészletek az adatok megosztása az Azure által támogatott Azure adatforrások számos közötti lehet. Jelenleg az Azure az adatok megosztása az Azure Blob Storage és az Azure Data Lake Store támogatja. 

## <a name="share-subscription"></a>Előfizetés megosztása 

Amikor egy adatfogyasztó elfogadja az adatszolgáltató data megosztás felkérését megosztása előfizetés jön létre. Adatszolgáltatók tekintheti meg az aktív megosztás előfizetések **küldött megosztások** Azure adatai megosztásához, majd válassza ki **megosztása előfizetések**.

Adatfogyasztó ellenőrizheti, ha van egy aktív megosztás előfizetés lépve **kapott megosztások** és a kapott megosztások állapotának megtekintése. 

## <a name="snapshot"></a>Pillanatkép

Adatfogyasztó pillanatkép hozható létre egy adatok megosztás meghívó elfogadásakor. Meghívó elfogadja őket, amikor azok is indíthat egy teljes pillanatképet a velük megosztott adatokról. A pillanatkép az adatok az időponthoz kötött, hogy az adatfogyasztó hozza létre a pillanatkép másolatát. 

A pillanatképek – teljes és növekményes két típusa van. Egy teljes pillanatképet tartalmaz az adatok megosztása található minden adat. Egy növekményes pillanatképet tartalmaz minden olyan adat, amelyet frissítve vagy hozzáadva, mivel a legutolsó pillanatfelvétel lett elindítva. 

## <a name="snapshot-settings-in-azure-data-share"></a>Pillanatkép-beállítások az Azure az adatok megosztása
 
Adatszolgáltató adatok megosztás egy pillanatkép-beállítás engedélyezésével. Ezzel a beállítással lehetővé teszi, hogy az adatfelhasználók fogadásához növekményes frissítéseket, azok bekövetkezésekor. Ezt a beállítást engedélyezni kell, ha szeretné, hogy az adatszolgáltató az adatfelhasználók a megosztott adatok a frissítések fogadásához. 

Adatszolgáltató lehetővé teszi, hogy ezt a beállítást, ha egy ismétlődési időköz lehet kiválasztani. Az ismétlődési időköz óránkénti vagy napi lehet. 

Adatfogyasztó jóváhagyja a Pillanatkép ütemezés fogadni a növekményes frissítéseket, amely tartalmazza az adatokat, amelyek módosultak, mivel az előbb létrehozott új pillanatkép lehetősége van. 

## <a name="invitation"></a>Meghívás

Adatszolgáltató meghívhatja, hogy az adatok megosztása több címzettnek. Ezek az adatok megosztása a címzettek hozzáadásával teheti meg. Meghívók adatok megosztás létrehozása után is hozzáadhatók. 

Adatszolgáltató meghívó törölheti, miután a rendszer elküldte. Vegye figyelembe, hogy ha egy adatszolgáltató törli a meghívót, elfogadását követően, az adatfogyasztó továbbra is egy aktív megosztás előfizetéssel rendelkezhet. Ha az adatszolgáltató törli a meghívást, és még nem fogadták, az adatfogyasztó nem lesz képes fogadni, azt. 

## <a name="recipient"></a>Címzett

A címzett olyan személy, amely egy adatmegosztáshoz meghívót kap. Általában egy adatszolgáltató címzettek adnak hozzá, amelyek az általuk létrehozott adatok megosztása. A meghívó a címzett elfogadja a meghívást, miután egy adatfogyasztó válnak.  

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan indítsa el az adatok megosztása, folytassa a [az adatok megosztása](share-your-data.md) oktatóanyag.

