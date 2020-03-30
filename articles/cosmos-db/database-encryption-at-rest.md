---
title: Titkosítás inaktív módon az Azure Cosmos DB-ben
description: Ismerje meg, hogy az Azure Cosmos DB hogyan biztosítja az inaktív adatok titkosítását és megvalósításának módját.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: db20388b5277e000ffe7055e9840742d6af7788e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062606"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Adatok titkosítása az Azure Cosmos DB-ben 

Az inaktív titkosítás olyan kifejezés, amely általában a nem felejtő tárolóeszközökön, például a szilárdtest-meghajtókon (SSD) és a merevlemez-meghajtókon (HDD-k) lévő adatok titkosítására utal. A Cosmos DB az elsődleges adatbázisokat SSD-ken tárolja. A médiamellékletek és biztonsági mentések az Azure Blob storage tárolja, amely általában hdd-k által. A Cosmos DB nyugalmi titkosításának kiadásával az összes adatbázis, médiamelléklet és biztonsági másolat titkosítva van. Az adatok most már titkosítva vannak az átvitel során (a hálózaton keresztül) és inaktív (nem felejtő tárolás) alatt, így végpontok közötti titkosítást biztosít.

PaaS-szolgáltatásként a Cosmos DB nagyon könnyen használható. Mivel a Cosmos DB-ben tárolt összes felhasználói adat titkosítva van inkóban és átvitelkor, nem kell semmilyen műveletet végrehajtania. Egy másik módja annak, hogy ez az, hogy a titkosítás inaktív "on" alapértelmezés szerint. Nincsenek ki- és bekapcsolási vezérlők. Az Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut. Ezt a funkciót biztosítjuk, miközben továbbra is teljesítjük [a rendelkezésre állási és teljesítmény-SLA-kat.](https://azure.microsoft.com/support/legal/sla/cosmos-db)

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Az Azure Cosmos DB inaktív titkosításának megvalósítása

Az inaktív titkosítás számos biztonsági technológiával valósul meg, beleértve a biztonságos kulcstároló rendszereket, a titkosított hálózatokat és a kriptográfiai API-kat. Az adatokat visszafejtő és feldolgozó rendszereknek kommunikálniuk kell a kulcsokat kezelő rendszerekkel. Az ábra bemutatja, hogyan oszlik el a titkosított adatok tárolása és a kulcsok kezelése. 

![Tervezési diagram](./media/database-encryption-at-rest/design-diagram.png)

A felhasználói kérelmek alapfolyamata a következő:
- A felhasználói adatbázisfiók készen áll, a tárolási kulcsok at pedig a felügyeleti szolgáltatás-szolgáltatóhoz intézett kéréssel olvassa be.
- A felhasználó https/biztonságos átvitelen keresztül hoz létre kapcsolatot a Cosmos DB-vel. (Az SDK-k absztrakt a részleteket.)
- A felhasználó json-dokumentumot küld a korábban létrehozott biztonságos kapcsolaton keresztül tárolandó JSON-dokumentumnak.
- A JSON-dokumentum indexelve van, kivéve, ha a felhasználó kikapcsolta az indexelést.
- Mind a JSON-dokumentum, mind az indexadatok biztonságos tárolásra vannak írva.
- Rendszeres időközönként az adatok at a biztonságos tárolóból olvassa be, és az Azure titkosított blob store biztonsági mentést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>K: Mennyibe kerül még az Azure Storage, ha a storage-szolgáltatás titkosítása engedélyezve van?
A: Nincs további költség.

### <a name="q-who-manages-the-encryption-keys"></a>K: Ki kezeli a titkosítási kulcsokat?
Megjegyzés: A kulcsokat a Microsoft kezeli.

### <a name="q-how-often-are-encryption-keys-rotated"></a>K: Milyen gyakran forgatják el a titkosítási kulcsokat?
A: A Microsoft rendelkezik egy sor belső irányelvek titkosítási kulcs forgatás, amely Cosmos DB következik. Az egyedi iránymutatásokat nem teszik közzé. A Microsoft közzéteszi a [Biztonsági fejlesztési életciklust (SDL),](https://www.microsoft.com/sdl/default.aspx)amely a belső útmutatás egy részhalmazának tekinthető, és hasznos gyakorlati tanácsokkal rendelkezik a fejlesztők számára.

### <a name="q-can-i-use-my-own-encryption-keys"></a>K: Használhatom a saját titkosítási kulcsaimat?
A: Igen, most ez a funkció elérhető az új cosmos-fiókok, és ezt meg kell tenni a fiók létrehozásakor. További információkért olvassa el [az Ügyfél által kezelt kulcsok](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) dokumentumát.

### <a name="q-what-regions-have-encryption-turned-on"></a>K: Milyen régiókban van bekapcsolva a titkosítás?
A: Minden Azure Cosmos DB-régió titkosításbe van kapcsolva az összes felhasználói adat.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>K: A titkosítás befolyásolja a teljesítmény késését és az átviteli SLA-k?
A: Nincs hatása, vagy a teljesítmény SL-ek most, hogy az inaktív titkosítás engedélyezve van az összes meglévő és új fiókok. A [cosmos-adatbázishoz](https://azure.microsoft.com/support/legal/sla/cosmos-db) felelős adatbázis oldalon további információa legfrissebb garanciák megtekintéséhez.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>K: Támogatja a helyi emulátor a titkosítást inaktív helyen?
V: Az emulátor egy önálló fejlesztési és tesztelési eszköz, és nem használja a fő felügyeleti szolgáltatásokat, amelyek et a felügyelt Cosmos DB szolgáltatás használ. Javasoljuk, hogy engedélyezze a BitLocker-t azokon a meghajtókon, ahol érzékeny emulátorteszt-adatokat tárol. Az [emulátor támogatja az alapértelmezett adatkönyvtár módosítását,](local-emulator.md) valamint egy jól ismert hely használatát.

## <a name="next-steps"></a>További lépések

A Cosmos DB biztonságának áttekintését és a legújabb fejlesztéseket az [Azure Cosmos adatbázis-biztonság című](database-security.md)témakörben találja.
A Microsoft-tanúsítványokról az [Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/)talál további információt.
