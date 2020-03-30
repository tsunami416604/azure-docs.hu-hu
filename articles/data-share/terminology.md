---
title: Az Azure Data Share terminológiája
description: Ismerje meg az Azure Data Share-ben használt erőforrások (adatszolgáltató, adatfogyasztó, adatmegosztás, megosztási előfizetés, pillanatkép, meghívás, címzett) leírására használt általános kifejezéseket.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468554"
---
# <a name="azure-data-share-concepts"></a>Az Azure-adatmegosztási fogalmak 

Az Azure Data Share bevezet néhány új, az adatmegosztással kapcsolatos terminológiát. Ez a cikk ismerteti a szolgáltatás során használt, gyakran használt kifejezéseket. 

## <a name="data-provider"></a>Adatszolgáltató

Az adatszolgáltató az a szervezet, amely adatokat oszt meg a fogyasztókkal. Az adatszolgáltató általában lehet az adatok tulajdonosa vagy kurátora. Az adatszolgáltatók különböző típusú adatokat szeretnének megosztani. Néhány példa az adatszolgáltatók által megosztani kívánt adatokra, például az értékesítési pont vagy az idősorozat-adatok. Előfordulhat, hogy egy adatszolgáltató olyan előre feldolgozott, válogatott adatokat is szeretne megosztani, amelyek már tartalmaznak elemzéseket és elemzéseket. 

## <a name="data-consumer"></a>Adatfogyasztó 

Az adatfogyasztó az a szervezet, amely adatokat fogad egy adatszolgáltatótól. Előfordulhat, hogy az adatfogyasztó a megosztott adatokat saját adataival szeretné összeilleszteni az elemzések lenyerése érdekében. Bizonyos esetekben előfordulhat, hogy az adatfogyasztó már feldolgozott adatokat kap. 

## <a name="data-share"></a>Data Share

Az adatmegosztás olyan adatkészletek csoportja, amelyek egyetlen entitásként vannak megosztva. Az adatkészletek számos Azure-adatforrásból származhatnak, amelyeket az Azure Data Share támogat. Jelenleg az Azure Data Share támogatja az Azure Blob Storage-ot és az Azure Data Lake Store-t. 

## <a name="share-subscription"></a>Előfizetés megosztása 

A megosztási előfizetés akkor jön létre, amikor egy adatfogyasztó elfogadja az adatszolgáltatótól származó adatmegosztási meghívót. Az adatszolgáltatók úgy tekinthetik meg az aktív megosztási előfizetéseket, hogy az Azure-adatmegosztási fiókjukban **az Elküldött megosztásokra** navigálnak, és kiválasztják **az Előfizetések megosztása**lehetőséget.

Az adatfogyasztó ellenőrizheti, hogy rendelkezik-e aktív megosztás-előfizetéssel, ha a **Fogadott megosztások** elemre navigál, és megtekinti a kapott megosztások állapotát. 

## <a name="snapshot"></a>Pillanatkép

Pillanatképet hozhat létre egy adatfogyasztó, amikor elfogadja az adatmegosztási meghívót. Amikor elfogadnak egy meghívást, teljes pillanatképet indíthatnak a velük megosztott adatokról. A pillanatkép az adatok másolata abban az időpontban, amikor az adatfogyasztó létrehozta a pillanatképet. 

Kétféle pillanatképek - teljes és növekményes. A teljes pillanatkép az adatmegosztáson belüli összes adatot tartalmazza. A növekményes pillanatkép tartalmazza az összes olyan adatot, amely az utolsó pillanatkép aktiválása óta frissült/hozzáadva. 

## <a name="snapshot-settings-in-azure-data-share"></a>Pillanatkép-beállítások az Azure Data Share-ben
 
Az adatszolgáltató engedélyezheti az adatmegosztás pillanatkép-beállítását. Ez a beállítás lehetővé teszi az adatfogyasztók számára, hogy a növekményes frissítéseket megkapják, amint azok bekövetkeznek. Ezt a beállítást engedélyezni kell, ha az adatszolgáltató azt szeretné, hogy az adatfogyasztók megkapják a megosztott adatok frissítéseit. 

Ha egy adatszolgáltató engedélyezi ezt a beállítást, ismétlődési időköz választható ki. Az ismétlődési időköz lehet óránként vagy naponta. 

Az adatfogyasztó nak lehetősége van arra, hogy engedélyezze ezt a pillanatkép-ütemezést, hogy növekményes frissítéseket kapjon, amely tartalmazza az új pillanatkép első létrehozása óta megváltozott adatokat. 

## <a name="invitation"></a>Meghívás

Az adatszolgáltatók több címzettet is meghívhatnak az adatmegosztásra. Ezt úgy tehetik meg, hogy hozzáadják a címzetteket az adatmegosztáshoz. Az adatmegosztás létrehozása után is felveheti a meghívókat. 

Ha az adatszolgáltató elküldés után törölheti a felkérést, törölheti azt. Ha az adatszolgáltató töröl egy meghívót, és még nem fogadták el, az adatfogyasztó nem fogja tudni elfogadni azt. 

A meghívók naponta legfeljebb ötször küldhetők újra. 

## <a name="recipient"></a>Címzett

A címzett olyan személy, aki meghívást kap egy adatmegosztásra. Az adatszolgáltatók általában hozzáadják a címzetteket az általuk létrehozott adatmegosztáshoz. Miután a meghívó címzettje elfogadta a meghívást, adatfogyasztóvá válik.  

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md)
