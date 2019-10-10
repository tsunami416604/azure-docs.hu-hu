---
title: Az Azure-beli adatmegosztás előzetes verziójának terminológiája
description: Ismerje meg az Azure-beli adatmegosztások előzetes verziójában használt erőforrások (adatszolgáltató, adatfogyasztó, adatmegosztás, megosztási előfizetés, pillanatkép, meghívó, címzett) leírására használt általános kifejezéseket.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: dd3235b7d87d8e02f44a440741b90f675263d147
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166279"
---
# <a name="azure-data-share-preview-concepts"></a>Az Azure-beli adatmegosztás előzetes verziójának alapfogalmai 

Az Azure-beli adatmegosztás előzetes verziója az adatmegosztással kapcsolatos új terminológiát ismerteti. Ez a cikk néhány olyan gyakran használt kifejezést ismertet, amelyet láthat a szolgáltatás során. 

## <a name="data-provider"></a>Adatszolgáltató

Az adatszolgáltató az a szervezet, amelyik az adatmegosztást a fogyasztókkal. Általában az adatszolgáltató lehet a tulajdonos vagy az adatkezelő. Az adatszolgáltatók különböző típusú adattípusokat oszthatnak meg. Néhány példa arra, hogy egy adatszolgáltató szeretne-e megosztani nyers adatmennyiséget, például az értékesítési pontot vagy az idősorozat-adatmennyiséget. Előfordulhat, hogy egy adatszolgáltató meg szeretné osztani az előre feldolgozható, az elemzéseket és az elemzéseket is tartalmazó, korábban feldolgozatlan, megtekintett adatgyűjtési 

## <a name="data-consumer"></a>Adatfogyasztó 

Az adatfogyasztó az a szervezet, amely adatszolgáltatótól fogadja az adatforrást. Előfordulhat, hogy az adatfogyasztó a saját adattal szeretné csatlakoztatni a megosztott adatforrásokat az elemzések kinyeréséhez. Bizonyos esetekben előfordulhat, hogy az adatfogyasztó olyan, már feldolgozott adatkérést kap, amely már fel van dolgozva. 

## <a name="data-share"></a>Adatmegosztás

Az adatmegosztások olyan adatkészletek csoportja, amelyek egyetlen entitásként vannak megosztva. Az adatkészletek több Azure-adatforrásból is származhatnak, amelyeket az Azure-adatmegosztás támogat. Az Azure-beli adatmegosztás jelenleg az Azure Blob Storage és Azure Data Lake Store használatát is támogatja. 

## <a name="share-subscription"></a>Előfizetés megosztása 

A megosztási előfizetések akkor jönnek létre, amikor az adatfogyasztó elfogad egy adatmegosztási meghívást egy adatszolgáltatótól. Az adatszolgáltatók megtekinthetik az aktív megosztás-előfizetéseket **, ha az**Azure-beli adatmegosztási fiókjában navigálnak az **Elküldett megosztásokhoz**

Egy adatfogyasztó megtekintheti, hogy rendelkezik-e aktív megosztási előfizetéssel, ha a **kapott megosztásokra** navigál, és megtekinti a kapott megosztások állapotát. 

## <a name="snapshot"></a>Pillanatkép

Adatmegosztási meghívást elfogadó adatfogyasztó létrehozhat pillanatképet. Ha elfogadják a meghívót, elindíthatják a velük megosztott adatok teljes pillanatképét. A pillanatkép az adatok másolata azon a ponton, amikor az adatfogyasztó létrehozta a pillanatképet. 

A pillanatképek két típusa létezik – teljes és növekményes. A teljes pillanatkép az adatmegosztáson belüli összes adatát tartalmazza. A növekményes Pillanatképek a legutóbbi pillanatkép elindítása óta frissített/hozzáadott összes adatértéket tartalmazzák. 

## <a name="snapshot-settings-in-azure-data-share"></a>Pillanatkép-beállítások az Azure-beli adatmegosztásban
 
Egy adatszolgáltató engedélyezheti az adatmegosztás pillanatkép-beállítását. Ez a beállítás lehetővé teszi, hogy az adatfogyasztók megkapják a növekményes frissítéseket. Ezt a beállítást engedélyezni kell, ha az adatszolgáltató szeretné, hogy az adatkezelők a megosztott adatkezelési szolgáltatásban is megkapják a frissítéseket. 

Ha egy adatszolgáltató engedélyezi ezt a beállítást, egy ismétlődési időköz is kiválasztható. Az ismétlődési időköz lehet óránként vagy naponta. 

Egy adatfogyasztó lehetősége van arra, hogy ezt a pillanatkép-ütemtervet olyan növekményes frissítések fogadására használja, amelyek az új pillanatkép létrehozása óta megváltoztatott adatokkal rendelkeznek. 

## <a name="invitation"></a>Meghívás

Egy adatszolgáltató több címzettet is meghívhat az adatmegosztásra. Ezt úgy teheti meg, hogy a címzetteket hozzáadja az adatmegosztáshoz. A meghívókat egy adatmegosztás létrehozása után is fel lehet venni. 

Egy adatszolgáltató törölhet egy meghívót, miután elküldték, ha még nem fogadták el. Ha az adatszolgáltató töröl egy meghívót, és még nem fogadta el, az adatfogyasztó nem fogja tudni elfogadni. 

A meghívások naponta akár ötször is elküldhetők. 

## <a name="recipient"></a>Címzett

A címzett olyan személy, aki meghívót kap egy adatmegosztásra. Egy adatszolgáltató jellemzően a létrehozott adatmegosztáshoz adja hozzá a címzetteket. Ha egy meghívás címzettjei elfogadják a meghívót, az adatfogyasztónak lesznek.  

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.

