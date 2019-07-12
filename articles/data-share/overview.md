---
title: Mi az Azure Data megosztás előzetes verziója
description: Azure fájlmegosztás Adatelőnézet áttekintése
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7d4e51ec9564bfb123cf73d9fe89d040f42fe650
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807545"
---
# <a name="what-is-azure-data-share-preview"></a>Mi az az Azure Data Share előzetes verzió?

A mai világ adatok megtekintésének kulcs stratégiai eszközként, melyeket számos szervezetnek kell egyszerűen és biztonságosan megoszthatja az ügyfeleink és partnereink számára. Számos módon, hogy ügyfeleink tegye ezt még ma, így – FTP-, e-mail, API-kat, hogy néhányat említsünk. Szervezetek nyomon követése, aki hogy ők kivel osztották adataikat segítségével könnyedén elvesztése fogadható el. FTP vagy a saját API-t infrastruktúrájuk felállítását az adatok megosztásában drága gyakran történő kialakításához és felügyeletéhez. Nincs társított ezekkel a módszerekkel, nagy méretű megosztási munkaterhelést. 

Számos szervezet kell azokat a megosztott adatok elszámolással. Felelősség, mellett számos vállalat, ha tudni szabályozhatja, kezelheti és monitorozhatja a legegyszerűbben az adatmegosztás. A mai világ, ahol adatok várhatóan egyre nő a mennyisége exponenciálisan nő, a szervezetnek kell megosztása, big Data típusú adatok egyszerű módszert. Ügyfeleink a lehető legfrissebb adatokat, győződjön meg arról, hogy képesek időben is használható elemzéseket kereslet.

Az Azure Data megosztása előzetes verzió lehetővé teszi a szervezetek számára, hogy egyszerűen és biztonságosan megoszthatják az adatokat több ügyfeleink és partnereink számára. Mindössze néhány kattintással üzembe helyezése új adatok megosztás fiók, adja hozzá az adatkészletek és meghívni az ügyfelek és partnerek számára az adatok megosztása. Adatszolgáltatók mindig rendelkezik, amely rendelkezik a megosztott adatok felett. Az Azure az adatok megosztása egyszerűen felügyelheti és figyelheti, hogy milyen adatokat megosztották, ki és mikor lehet. 

Adatszolgáltató is is kézben adataikat kezelésmódját használati feltételeit, hogy az adatok megosztása megadásával. Az adatfogyasztó elfogadja ezeket a feltételeket, mielőtt fogadni az adatokat. Adatszolgáltatók megadhatja a gyakoriságot, amellyel az adatfelhasználók kapják a frissítéseket. Új frissítések a hozzáférést az adatszolgáltató által bármikor visszavonhatja. 

Az Azure az adatok megosztása segít insights javításához egyszerűvé analytics és a mesterséges intelligenciák feldúsítani harmadik felektől származó adatokat kombinálni. Könnyedén a power tezhet az Azure analytics eszközök segítségével előkészítése, feldolgozása és elemzése az Azure az adatok megosztása keresztül megosztott adatok. 

## <a name="scenarios-for-azure-data-share"></a>Az Azure az adatok megosztása forgatókönyvei

Az Azure az adatok megosztása a különböző iparágakban számos használható. Például egy közvetítő érdemes az értékesítési adatok a legutóbbi pont megosztása azok szállítói. Használja az Azure-adatok megosztása,-kereskedő is egy pont az értékesítési adatokat tartalmazó összes azok szállítói számára az adatok megosztása és megoszthatja értékesítési óránkénti vagy napi rendszerességgel. 

Az Azure az adatok megosztása a létesíteni egy adott iparági adatok marketplace is használható. Például egy közigazgatási vagy egy kutatás intézmény, amely rendszeresen oszt meg harmadik felekkel kapcsolatos népességnövekedés anonimizált adatok. 

Az Azure az adatok megosztása egy másik használati adatok konzorcium meghatározása. Ha például számos különböző kutatási intézményekkel megoszthatnak adatokat egy adott megbízható szervezet. Adatok elemez, összesített vagy feldolgozása Azure analitikai eszközeit és az érdekelt felek megosztott. 

## <a name="how-it-works"></a>Működés

Az Azure az adatok megosztása egy pillanatkép-alapú megosztási módszert használ, az adatokat helyez át az adatszolgáltató Azure-előfizetés és Azure-előfizetésében az adatfogyasztó hajtanak végre. Adatszolgáltató, mint egy adatmegosztáshoz kiépítése, és felhívja a címzettek számára az adatok megosztása. Adatfelhasználók az adatok megosztása e-mailben meghívást kapnak. Miután egy adatfogyasztó elfogadja a meghívást, azokat is indíthat egy teljes pillanatképet a közös adatok megosztott őket. Ezeket az adatokat a fogyasztói adatok tárfiókba történő érkezik. Az adatok velük megosztott úgy, hogy mindig a legújabb verzióra, az adatok rendszeres, a növekményes frissítések adatfelhasználók kapnak. 

Adatszolgáltatók kínálnak az adatokat a fogyasztók megosztanak velük egy pillanatkép ütemezése keresztül az adatok növekményes frissítéseit. Pillanatfelvétel-készítési ütemezéseket az óránkénti vagy napi szinten érhető el. Adatfogyasztó elfogadja, és konfigurálja az adatok megosztása, amikor azok előfizethetnek egy pillanatkép ütemezése. Ez a forgatókönyvekben, ahol a megosztott adatok rendszeresen frissül, és az adatfogyasztó kell a legújabb adatok szerepeljenek előnyös. 

![az adatfolyam-megosztás](media/data-share-flow.png)

Amikor egy adatfogyasztó elfogadja a egy adatmegosztáshoz, azok tudja fogadni az adatok a storage-fiókban hozhatna. Például ha az adatszolgáltató oszt meg adatokat az Azure Blob Storage, az adatfogyasztó kaphatnak ezeket az adatokat az Azure Data Lake Store. 

## <a name="key-capabilities"></a>Főbb képességek

Az Azure az adatok megosztása lehetővé teszi, hogy az adatok szolgáltatók számára:

* Megoszthatják az adatokat az Azure Storage-ból és az Azure Data Lake Store az ügyfelekkel és a szervezeten kívül

* Nyomon követheti, akik az adatok megosztott

* Milyen gyakran az adatfelhasználók azért küldtük Önnek, az adatok frissítései

* Az ügyfelek a legújabb verzióra, az adatok lekérése igény szerint, vagy lehetővé teszik számukra az adatok növekményes változásait automatikusan beolvashassa az Ön által meghatározott időközönként

Az Azure az adatok megosztása lehetővé teszi, hogy az adatfelhasználók: 

* Megosztott adatok leírásának megtekintése

* Az adatok a használati feltételek megtekintése

* Elfogadhatja vagy elutasíthatja az Azure Data megosztási meghívót

* Egy az adatok megosztása, amely egy szervezet megosztotta Önnel teljes vagy növekményes pillanatkép-trigger

* Fizessen elő a legfrissebb verzióját az adatok növekményes pillanatkép-másolás fogadásához az adatok megosztása

* Fogadja el az adatokat egy Azure Blob Storage vagy az Azure Data Lake Gen2-fiókba az Önnel megosztott

Minden főbb képességei fent felsorolt támogatottak az Azure-on keresztül vagy a REST API-kon keresztül. További információk az Azure az adatok megosztása REST API-k segítségével tekintse meg a dokumentációja. 

## <a name="security"></a>Biztonság

Az Azure az adatok megosztása kihasználja az alapul szolgáló biztonsági, Azure által kínált inaktív és úton lévő adatok védelme érdekében. Az inaktív adatok titkosítása, ha az alapul szolgáló tárolási mechanizmus támogatja. Az átvitel során is titkosítja az adatokat. Egy adatmegosztáshoz metaadatait is titkosítva van, inaktív és átvitel közben. 

Hozzáférés-vezérlést annak érdekében, amelyek jogosultak a érhető el az Azure az adatok megosztása erőforrásszintek simítása is beállíthat. 

Az Azure az adatok megosztása az automatikus identitástartomány-kezelés az Azure Active Directory felügyelt identitások kihasználja az Azure-erőforrások (korábbi nevén MSIs). Felügyelt identitások az Azure-erőforrások is használja az adatok megosztásához használt tárfiókok elérését. Nincs nem egy adatszolgáltató és a egy adatfogyasztó között hitelesítő adatok cseréjét. További információkért tekintse meg a [felügyelt identitások Azure-erőforrások lap](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 

## <a name="supported-regions"></a>Támogatott régiók

Elérhetővé teheti az Azure az adatok megosztása az Azure-régiók listájáért tekintse meg a [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) lapot, és keresse meg az Azure az adatok megosztása. 

Az Azure az adatok megosztása tárol adatokat magát. A mögöttes tárfiókokat, megosztott tárolja az adatokat. Például ha egy adat-előállító tárolja az adatokat egy Azure Data Lake Store-fiókban található, az USA nyugati RÉGIÓJA, ez az adatok tárolására. Ha azok megosztanak adatokat a Nyugat-Európában található Azure Storage-fiókot, az adatok közvetlenül a Nyugat-Európában található Azure Storage-fiók kerül. 

Az Azure az adatok megosztása szolgáltatás nem rendelkezik elérhető legyen a régiót használhatja a szolgáltatást. Például ha rendelkezik egy régióban, ahol az Azure-adatok megosztása még nem áll rendelkezésre az Azure Storage-fiókban tárolt adatok, továbbra is használhatja a szolgáltatást, hogy az adatok megosztása. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan indítsa el az adatok megosztása, folytassa a [az adatok megosztása](share-your-data.md) oktatóanyag.
