---
title: Titkosítás inaktív állapotban, az Azure Cosmos DB-ben
description: Ismerje meg, hogyan nyújt az Azure Cosmos DB a inaktív adatok titkosítását, és hogyan van megvalósítva.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f406f008e2c377b39deb8d151855ce7315616701
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616856"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Adattitkosítás a Azure Cosmos DBban 

Titkosítás inaktív állapotban, egy kifejezés, amely gyakran felejtő tárolóeszközök, az adatok titkosítása vonatkozik, például a tartós állapotú meghajtókkal (SSD-kkel) és a merevlemezes (HDD) meghajtók. A cosmos DB az elsődleges adatbázisok SSD meghajtókon tárolja. A media mellékletek és a biztonsági mentések általánosan biztonsági másolatot készít a HDD-k, az Azure Blob storage vannak tárolva. A Cosmos DB-hez készült inaktív adatok titkosítását, kezdve az adatbázisok, media mellékletek és biztonsági mentések titkosítottak. Az adatok most már (a hálózaton kívül) titkosított átvitel és inaktív (felejtő tárolás), így teljes körű titkosítást.

Mivel egy PaaS szolgáltatás, a Cosmos DB használata egyszerű. Cosmos DB-ben tárolt összes felhasználói adat titkosítva van, inaktív és átvitel, mert nincs teendője. Ez helyezni egy másik módja, hogy "on", alapértelmezés szerint a titkosítás inaktív állapotban van. Nincsenek ki- vagy kikapcsolni. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut. Ez a funkció kínálunk, miközben továbbra is, hogy megfeleljen a [rendelkezésre állását és teljesítményét az SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Titkosítás inaktív állapotban, az Azure Cosmos DB megvalósítása

Inaktív adatok titkosítását számos biztonsági technológia, többek között a biztonságos kulcs tárolása rendszerek, a titkosított hálózatokat és a titkosítási API-k segítségével van megvalósítva. Adatok feldolgozása és visszafejtésére rendszerek kell kommunikálni a rendszereket, a kulcsok kezelése. Az ábra bemutatja, hogyan titkosított adatok és a kulcsok kezelését választja el egymástól. 

![Tervezési diagramja](./media/database-encryption-at-rest/design-diagram.png)

Egy felhasználói kérelem használt alapvető folyamat a következőképpen történik:
- Történik, hogy a felhasználói adatbázis-fiók készen áll, és a tárkulcsok olvassa be a felügyeleti szolgáltatás erőforrás-szolgáltató kérést keresztül.
- Egy felhasználó kapcsolatot hoz létre a Cosmos DB-hez HTTPS/biztonságos átvitel keresztül. (Az SDK-k absztrakt részletei).
- A felhasználó küld egy JSON-dokumentum a korábban létrehozott biztonságos kapcsolaton keresztül kell tárolni.
- A JSON-dokumentum indexelve van, kivéve, ha a felhasználó kikapcsolta az indexelés.
- A JSON dokumentum és index adatainak, mind a biztonságos tárolás készültek.
- Az adatok rendszeres időközönként, olvassa el a biztonságos tárból és biztonsági mentése az Azure titkosított Blob Store.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>K: Mennyibe kerül az Azure Storage szolgáltatás, ha Storage Service Encryption engedélyezve van?
V: Nincs további díj.

### <a name="q-who-manages-the-encryption-keys"></a>K: Kik kezelik a titkosítási kulcsokat?
V: A kulcsokat a Microsoft felügyeli.

### <a name="q-how-often-are-encryption-keys-rotated"></a>K: Milyen gyakran vannak elforgatva a titkosítási kulcsok?
V: A Microsoft a titkosítási kulcs elforgatására vonatkozó belső irányelveket tartalmaz, amelyek Cosmos DB következnek. A megadott irányelveket nem tesszük közzé. A Microsoft közzététele a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx), amely belső útmutató egy részét, látható, és fejlesztők számára hasznos ajánlott eljárásokat tartalmaz.

### <a name="q-can-i-use-my-own-encryption-keys"></a>K: Használhatom a saját titkosítási kulcsokat?
V: Cosmos DB egy Pásti szolgáltatás, és a szolgáltatás könnyen használható marad. Azt észleltük, hogy ezt a kérdést gyakran feltesznek, például a PCI DSS megfelelőségi követelménynek proxy kérdést. Ez a szolgáltatás létrehozásának részeként működtünk együtt, győződjön meg arról, hogy ügyfeleink, akik Cosmos DB követelményeinek saját maguk kulcsok kezelése nélkül a megfelelőségi auditorok.

### <a name="q-what-regions-have-encryption-turned-on"></a>K: Milyen régiókban van engedélyezve a titkosítás?
V: Az összes Azure Cosmos DB-régió titkosítása be van kapcsolva minden felhasználói adattal.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>K: Befolyásolja a titkosítás a teljesítmény késését és az átviteli sebességet?
V: A teljesítményre vonatkozó SLA-kat jelenleg nem befolyásolja vagy nem változtatja meg, hogy az összes meglévő és új fiók esetében engedélyezve van a titkosítás a REST-ben. Tudjon meg többet a a [a Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) oldalon tekintheti meg a legújabb garanciákat.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>K: Támogatja a helyi emulátor a titkosítást a REST-ben?
V: Az emulátor egy önálló fejlesztési/tesztelési eszköz, és nem használja a felügyelt Cosmos DB szolgáltatás által használt kulcskezelő szolgáltatásokat. Azt javasoljuk, hogy hol tárolja bizalmas emulátor Tesztadatok meghajtók engedélyezheti a Bitlockert. A [emulátor támogatja az alapértelmezett adatkönyvtárát módosítása](local-emulator.md) valamint a jól ismert hely használatát.

## <a name="next-steps"></a>További lépések

A Cosmos DB biztonság és a legújabb Újdonságok áttekintését lásd: az [Azure Cosmos Database biztonsága](database-security.md).
Microsoft-minősítésekkel kapcsolatos további információkért lásd: a [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/).
