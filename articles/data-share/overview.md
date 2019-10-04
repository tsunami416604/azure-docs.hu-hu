---
title: Mi az Azure-beli adatmegosztás előzetes verziója?
description: Az Azure-beli adatmegosztás előzetes verziójának áttekintése
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 11f32b1f1349ef0f9826f95832648e6949cc2f8c
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421402"
---
# <a name="what-is-azure-data-share-preview"></a>Mi az az Azure Data Share előzetes verzió?

Napjaink világában az adatgyűjtés kulcsfontosságú stratégiai eszköznek tekinthető, amelyet számos szervezetnek egyszerűen és biztonságosan kell megosztania ügyfeleivel és partnereivel. Manapság számos módon teheti ezt meg, többek között FTP-n, e-mailben, API-kon keresztül, hogy csak néhányat említsünk. A szervezetek könnyen elveszítik a nyomon követését, hogy kik osztják meg az adatvédelmet a segítségével. Az adatmegosztás FTP-n keresztül vagy a saját API-infrastruktúrájának kiépítésével gyakran költséges a kiépítése és felügyelete. A megosztások ezen módszereinek nagy léptékű használata esetén a felügyelet terhelése is fennáll. 

Számos szervezetnek a megosztott adatmennyiségért kell felvennie a fiókot. Az elszámoltathatóság mellett számos szervezet egyszerűen vezérelheti, kezelheti és figyelheti az összes adatmegosztást. A mai világban, ahol az adatgyűjtés várhatóan exponenciálisan növekszik, a szervezeteknek egy egyszerű módszert kell megosztaniuk big data. Az ügyfeleknek a legfrissebb adatokra van szükségük annak biztosításához, hogy képesek legyenek időben betekinteni a bepillantást.

Az Azure-beli adatmegosztás előzetes verziója lehetővé teszi, hogy a szervezetek egyszerűen és biztonságosan osszák meg az adatmegosztást több ügyféllel és partnerrel. Néhány kattintással kiépítheti az új adatmegosztási fiókot, adatkészleteket adhat hozzá, és meghívja ügyfeleit és partnereit az adatmegosztásra. Az adatszolgáltatók mindig szabályozzák a megosztott adatmennyiséget. Az Azure-beli adatmegosztás révén egyszerűen kezelheti és figyelheti az adatmegosztást, amikor és kivel. 

Az adatszolgáltatók az adatmegosztásra vonatkozó használati feltételek megadásával szabályozhatják az adatkezelés módját. Az adatfogyasztónak el kell fogadnia ezeket a feltételeket, mielőtt az adatfogadásra képes lenne. Az adatszolgáltatók meghatározhatják, hogy az adatkezelők milyen gyakorisággal kapják meg a frissítéseket. Az adatszolgáltató bármikor visszavonhatja az új frissítések elérését. 

Az Azure-adatmegosztás segít az elemzések fejlesztésében azáltal, hogy megkönnyíti a harmadik féltől származó adatok egyesítését az elemzési és AI-forgatókönyvekhez. Az Azure-adatmegosztás használatával könnyedén felkészítheti, feldolgozhatja és elemezheti az Azure-beli adatmegosztással megosztott adatkezelési eszközöket. 

Az adatszolgáltatónak és az adatfogyasztónak is rendelkeznie kell egy Azure-előfizetéssel az adatmegosztáshoz és az adatfogadáshoz. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Azure-adatmegosztási forgatókönyvek

Az Azure-adatmegosztás több különböző iparágban is felhasználható. Előfordulhat például, hogy egy kiskereskedő szeretné megosztani a legutóbbi értékesítési adatpontot a szállítókkal. Az Azure-adatmegosztás használatával a kiskereskedelmi adatmegosztást állíthat be, amely tartalmazza az értékesítési adatpontot az összes szállítónál, és megoszthatja az értékesítést óránként vagy naponta. 

Az Azure-adatmegosztás használatával egy adott iparág számára is létrehozhat egy adatpiacot. Például egy olyan kormány vagy kutatóintézet, amely rendszeresen oszt meg anonim információkat a népesség növekedésével kapcsolatban harmadik felekkel. 

Az Azure-adatmegosztás egy másik használati esete egy adatkonzorcium létrehozása. Például számos különböző kutatóintézet egyetlen megbízható törzstel oszthatja meg az adatmegosztást. Az adatokat elemezze, összesíti vagy dolgozza fel az Azure Analytics-eszközök használatával, majd megoszthatja őket az érdekelt felekkel. 

## <a name="how-it-works"></a>Működés

Az Azure-beli adatmegosztás egy pillanatkép-alapú megosztási megközelítést használ, ahol az adatok az adatszolgáltató Azure-előfizetéséről és az adatfogyasztó Azure-előfizetésében található földekről származnak. Adatszolgáltatóként egy adatmegosztást kell kiépíteni, és meg kell hívnia a címzetteket az adatmegosztásra. Az adatfogyasztók e-mailben kapják meg az adatmegosztásra vonatkozó meghívót. Ha egy adatfogyasztó elfogadja a meghívót, elindíthatják a megosztott adatok teljes pillanatképét. Ezek az adathozzáférések az adatfogyasztók Storage-fiókba érkeznek. Az adatfogyasztók rendszeres, növekményes frissítéseket kaphatnak a velük megosztott adatmegosztáshoz, így mindig a legújabb verzióval rendelkeznek. 

Az adatszolgáltatók az adatfogyasztók növekményes frissítését is lehetővé tehetik a velük megosztott adatokhoz egy pillanatkép-ütemterv alapján. A pillanatkép-ütemtervek óránkénti vagy napi rendszerességgel is elérhetők. Amikor az adatfogyasztó elfogadja és konfigurálja az adatmegosztást, előfizethet egy pillanatkép-ütemtervre. Ez olyan esetekben hasznos, amikor a megosztott adatok rendszeresen frissülnek, és az adatok fogyasztójának a legfrissebb adatokra van szüksége. 

![adatmegosztási folyamat](media/data-share-flow.png)

Amikor az adatfogyasztó elfogad egy adatmegosztást, a kiválasztott Storage-fiókban is megkaphatja az adatmennyiséget. Ha például az adatszolgáltató az Azure Blob Storage használatával oszt meg adatmegosztást, az adatfogyasztó ezeket az adatAzure Data Lake Storet is megkapja. 

## <a name="key-capabilities"></a>Főbb képességek

Az Azure-adatmegosztás lehetővé teszi az adatszolgáltatók számára a következőket:

* Adatok megosztása az Azure Storage-ból és Azure Data Lake Store a szervezeten kívüli ügyfelekkel és partnerekkel

* Nyomon követheti, hogy ki osztotta meg az adatait a

* Az adatfogyasztók milyen gyakran kapnak frissítéseket az adataihoz

* Lehetővé teheti ügyfelei számára, hogy igény szerint lekérje az adataik legújabb verzióját, vagy az Ön által meghatározott időközönként automatikusan megkapják az adataik növekményes módosításait

Az Azure-adatmegosztás lehetővé teszi az adatfogyasztók számára a következőket: 

* A megosztva tárolt adattípusok leírásának megtekintése

* Az adathasználati feltételek megtekintése

* Azure-beli adatmegosztási meghívás elfogadása vagy elutasítása

* A szervezet által Önnel megosztott adatmegosztás teljes vagy növekményes pillanatképének elindítása

* Előfizetés egy adatmegosztásra, hogy megkapja az adatok legújabb másolatát a növekményes Pillanatképek másolásával

* Az Önnel megosztott adathozzáférés elfogadása Azure Blob Storage vagy Azure Data Lake Gen2-fiókkal

A fent felsorolt legfontosabb képességek az Azure-ban vagy a REST API-kon keresztül támogatottak. További információ az Azure-beli adatmegosztás REST API-kkal való használatáról: dokumentáció. 

## <a name="security"></a>Biztonság

Az Azure-adatmegosztás kihasználja az Azure által az inaktív és az átvitel alatt álló adatok védelméhez használt mögöttes biztonsági adatokat. Az adatok titkosítva vannak, ahol a mögöttes tárolási mechanizmus támogatja. Az adatátvitelt is titkosítja a rendszer. Az adatmegosztások metaadatait a rendszer a REST és az átvitel során is titkosítja. 

A hozzáférés-vezérlés beállítható az Azure-beli adatmegosztási erőforrás szintjén, így biztosítva, hogy azok elérhetők legyenek a jogosultak számára. 

Az Azure-adatmegosztás felügyelt identitásokat használ az Azure-erőforrások (korábbi nevén rendszercsomagok) számára a Azure Active Directory automatikus Identitáskezelés kezeléséhez. Az Azure-erőforrások felügyelt identitásait a rendszer az adatmegosztáshoz használt Storage-fiókok elérésére használja ki. Az adatszolgáltató és az adatfogyasztó között nincs hitelesítő adatok cseréje. További információkért tekintse meg az [Azure-erőforrások felügyelt identitások oldalát](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Támogatott régiók

Az Azure-beli adatmegosztást használó Azure-régiók listáját az Azure-beli adatmegosztást [ismertető oldalon](https://azure.microsoft.com/global-infrastructure/services/) tekintheti meg és keresheti meg. 

Az Azure-beli adatmegosztások nem tárolnak semmilyen adattárat. Az adatokat a rendszer az alapul szolgáló Storage-fiókokban tárolja. Ha például egy adatelőállító az USA nyugati régiójában található Azure Data Lake Store-fiókban tárolja az adattárat, akkor az adattárolási hely. Ha a Nyugat-Európában található Azure Storage-fiókkal osztanak meg adatmegosztást, a rendszer közvetlenül a Nyugat-Európában található Azure Storage-fiókba továbbítja az adatátvitelt. 

Az Azure-beli adatmegosztási szolgáltatásnak nem kell elérhetőnek lennie a régióban a szolgáltatás kihasználása érdekében. Ha például egy olyan régióban található Azure Storage-fiókban tárolt adatai vannak, amelyben az Azure-adatmegosztás még nem érhető el, akkor továbbra is használhatja a szolgáltatást az adatmegosztáshoz. 

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
