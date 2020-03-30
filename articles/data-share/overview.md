---
title: Mi az az Azure Data Share?
description: Ismerje meg, hogyan oszthatja meg az adatokat egyszerűen és biztonságosan több ügyféllel és partnerrel az Azure Data Share használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621856"
---
# <a name="what-is-azure-data-share"></a>Mi az az Azure Data Share?

A mai világban az adatokat kulcsfontosságú stratégiai értéknek tekintik, amelyet sok szervezetnek egyszerűen és biztonságosan meg kell osztania ügyfeleivel és partnereivel. Sok módja van, hogy az ügyfelek ezt ma, többek között FTP, e-mail, API-k, hogy csak néhányat említsünk. A szervezetek könnyen elveszíthetik a nyomukat amiatt, hogy kivel osztották meg az adataikat. Az adatok FTP-n keresztüli megosztása vagy saját API-infrastruktúrájuk felállása gyakran költséges az üzembe helyezése és felügyelete. A nagy megosztási módszerek használatával felügyeleti többletterhelés áll fenn. 

Sok szervezetnek elszámoltathatónak kell lennie a megosztott adatokért. Az elszámoltathatóság mellett számos szervezet szeretné, hogy az összes adatmegosztást egyszerű módon vezérelhessék, kezelhessék és figyelhessék. A mai világban, ahol az adatok várhatóan továbbra is exponenciális ütemben növekednek, a szervezeteknek egyszerű módon kell megosztaniuk a big data-adatokat. Az ügyfelek a legfrissebb adatokat igénylik annak érdekében, hogy képesek legyenek időben megadatfolyamokat létrehozni.

Az Azure Data Share lehetővé teszi a szervezetek számára, hogy egyszerűen és biztonságosan osszák meg az adatokat több ügyféllel és partnerrel. Néhány kattintással új adatmegosztási fiókot hozhat létre, adatkészleteket adhat hozzá, és meghívhatja ügyfeleit és partnereit az adatmegosztásba. Az adatszolgáltatók mindig kézben tartják a megosztott adatokat. Az Azure Data Share segítségével egyszerűen kezelheti és figyelheti, hogy milyen adatokat osztottak meg, mikor és ki. 

Az adatszolgáltatók az adatmegosztás használati feltételeinek megadásával továbbra is szabályozhatják az adataik kezelését. Az adatfelhasználónak el kell fogadnia ezeket a feltételeket ahhoz, hogy adatokat fogadhasson. Az adatszolgáltatók meghatározhatják, hogy az adatfelhasználók milyen gyakorisággal kapjanak frissítéseket. Az új frissítésekhez való hozzáférést az adatszolgáltató bármikor visszavonhatja. 

Az Azure Data Share segít az elemzésekben azáltal, hogy megkönnyíti a harmadik felektől származó adatok kombinálását az elemzések és a ai-forgatókönyvek bővítéséhez. Az Azure-elemzési eszközök segítségével egyszerűen előkészítheti, feldolgozhatja és elemezheti az Azure Data Share használatával megosztott adatokat. 

Mind az adatszolgáltatónak, mind az adatfogyasztónak Rendelkeznie kell egy Azure-előfizetéssel az adatok megosztásához és fogadásához. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

## <a name="scenarios-for-azure-data-share"></a>Az Azure-adatmegosztás forgatókönyvei

Az Azure Data Share számos különböző iparágban használható. Előfordulhat például, hogy egy kiskereskedő meg szeretné osztani a legutóbbi értékesítési adatokat a szállítóival. Az Azure Data Share használatával a kiskereskedő beállíthat egy adatmegosztást, amely az összes szállítója értékesítési adatait tartalmazza, és óránként vagy naponta megoszthatja az értékesítéseket. 

Az Azure Data Share egy adott iparág adatpiacterének létrehozásához is használható. Például egy olyan kormány vagy kutatóintézet, amely rendszeresen megosztja a népességnövekedésre vonatkozó anonimizált adatokat harmadik felekkel. 

Az Azure Data Share egy másik használati esete egy adatkonzorcium létrehozása. Például számos különböző kutatóintézet megoszthatja az adatokat egyetlen megbízható szervvel. Az adatok elemzése, összesítése vagy feldolgozása az Azure-elemzési eszközök használatával történik, majd megosztjuk az érdekelt felekkel. 

## <a name="how-it-works"></a>Működés

Az Azure Data Share jelenleg pillanatkép-alapú megosztást és helybeni megosztást kínál. 

A pillanatkép-alapú megosztásban az adatok az adatszolgáltató Azure-előfizetéséből mozognak, és az adatfogyasztó Azure-előfizetésébe kerülnek. Adatszolgáltatóként adatmegosztást létesít, és meghívja a címzetteket az adatmegosztásra. Az adatfogyasztók e-mailben kapnak meghívót az Ön adatmegosztására. Miután egy adatfogyasztó elfogadja a meghívást, teljes pillanatképet indíthat a velük megosztott adatokról. Ezek az adatok az adatfogyasztók tárfiókjába érkeznek. Az adatfogyasztók rendszeres, növekményes frissítéseket kaphatnak a velük megosztott adatokhoz, hogy mindig az adatok legújabb verziójával rendelkezzenek. 

Az adatszolgáltatók növekményes frissítéseket kínálhatnak az adatfogyasztóknak a velük megosztott adatokhoz egy pillanatkép-ütemezés en keresztül. A pillanatképek ütemezése óránként vagy naponta kínál. Amikor egy adatfogyasztó elfogadja és konfigurálja az adatmegosztást, előfizethet egy pillanatkép-ütemezésre. Ez előnyös olyan esetekben, amikor a megosztott adatok rendszeresen frissülnek, és az adatfogyasztónak a legfrissebb adatokra van szüksége. 

![adatmegosztási folyamat](media/data-share-flow.png)

Ha egy adatfogyasztó elfogadja az adatmegosztást, az adatokat az általa választott adattárban fogadhatja. Ha például az adatszolgáltató megosztja az adatokat az Azure Blob Storage használatával, az adatfogyasztó ezeket az adatokat az Azure Data Lake Store-ban is megkaphatja. Hasonlóképpen, ha az adatszolgáltató megosztja az adatokat egy Azure SQL Data Warehouse, az adatfogyasztó eldöntheti, hogy szeretné-e fogadni az adatokat egy Azure Data Lake Store, egy Azure SQL Database vagy egy Azure SQL Data Warehouse. Az SQL-alapú forrásokból történő megosztás esetén az adatfogyasztó azt is eldöntheti, hogy parketta vagy csv formában kap-e adatokat. 

A helyen történő megosztással az adatszolgáltatók az adatok másolása nélkül megoszthatják az adatokat. Miután a megosztási folyamat on keresztül létrejön, szimbolikus kapcsolat jön létre az adatszolgáltató forrás-adattára és az adatfogyasztó céladattára között. Az adatfogyasztó valós időben olvashatja és kérdezheti le az adatokat a saját adattárhasználatával. A forrásadattár módosításai azonnal elérhetők az adatfogyasztó számára. A helyszíni megosztás jelenleg előzetes verzióban érhető el az Azure Data Explorer számára.

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Data Share lehetővé teszi az adatszolgáltatók számára, hogy:

* A [támogatott adattárak](supported-data-stores.md) listájából származó adatok megosztása a szervezeten kívüli ügyfelekkel és partnerekkel

* Annak nyomon követése, hogy kivel osztotta meg az adatait

* A pillanatfelvétel vagy a helyen történő megosztás megválasztása

* Milyen gyakran kapnak az adatfogyasztók az adatok frissítéseit?

* Annak engedélyezése, hogy az ügyfelek szükség szerint lekérik az adatok legújabb verzióját, vagy lehetővé tegyék számukra, hogy automatikusan megkapják az adatok növekményes módosításait az Ön által meghatározott időközönként

Az Azure Data Share lehetővé teszi az adatfogyasztók számára, hogy: 

* A megosztott adatok típusának leírásának megtekintése

* Az adatok használati feltételeinek megtekintése

* Azure-adatmegosztási meghívás elfogadása vagy elutasítása

* Fogadja el az Önnel megosztott adatokat egy [támogatott adattárba.](supported-data-stores.md)

* Teljes vagy növekményes pillanatkép aktiválása egy olyan adatmegosztásról, amelyet egy szervezet megosztott Önnel

* Feliratkozás egy adatmegosztásra, hogy megkapja az adatok legújabb példányát növekményes pillanatképen keresztül

A fent felsorolt összes kulcsképesség az Azure Portalon vagy a REST API-kon keresztül támogatott. Az Azure Data Share REST API-kon keresztüli használatával kapcsolatos további részletekért tekintse meg a referenciadokumentációt. 

## <a name="security"></a>Biztonság

Az Azure Data Share kihasználja az Azure által az inaktív és az átvitel közbeni adatok védelmére szolgáló mögöttes biztonságot. Az adatok titkosítva vannak inkamerre, ahol az alapul szolgáló adattár támogatja. Az adatok az átvitel során is titkosítva vannak. Az adatmegosztás metaadatai is titkosítva vannak az inaktív és az átvitel során. 

A hozzáférés-vezérlések az Azure Data Share erőforrás szintjén állíthatók be annak biztosítására, hogy az engedélyezett ek hozzáférjenek hozzá. 

Az Azure Data Share az Azure-erőforrások (korábbi nevén MSI-k) felügyelt identitásait használja az Azure Active Directory automatikus identitáskezeléséhez. Az Azure Resources felügyelt identitásai az adatmegosztáshoz használt adattárakhoz való hozzáféréshez használatosak. Az adatszolgáltató és az adatfogyasztó között nem történik hitelesítő adatok cseréje. További információt az [Azure Resources felügyelt identitások lapján](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)talál. 


## <a name="supported-regions"></a>Támogatott régiók

Az Azure-adatok megosztását elérhetővé tévő Azure-régiók listáját tekintse meg a [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) oldalon, és keresse meg az Azure Data Share-t. 

Az Azure Data Share nem tárolja az adatok másolatát. Az adatok a megosztott alapul szolgáló adattárban tárolódnak. Ha például egy adattermelő az adatokat az USA nyugati részén található Azure Data Lake Store-fiókban tárolja, akkor az adatok tárolása itt történik. Ha az adatokmegosztása egy Nyugat-Európában található Azure Storage-fiókkal pillanatkép segítségével történik, általában az adatok közvetlenül a Nyugat-Európában található Azure Storage-fiókba kerülnek.

Az Azure Data Share szolgáltatás nem kell elérhető a régióban a szolgáltatás kihasználásához. Ha például egy Olyan régióban található Azure Storage-fiókban tárolt adatokat, ahol az Azure Data Share még nem érhető el, továbbra is használhatja a szolgáltatást az adatok megosztásához. 

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md)
