---
title: Titkosítás inaktív állapotban, az Azure Cosmos DB-ben
description: Ismerje meg, hogyan nyújt az Azure Cosmos DB a inaktív adatok titkosítását, és hogyan van megvalósítva.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 8138a7dcae427d5f5f622170a7825306a7bf8dae
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140319"
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Az Azure Cosmos DB adatbázis-titkosítás inaktív állapotban

Titkosítás inaktív állapotban, egy kifejezés, amely gyakran felejtő tárolóeszközök, az adatok titkosítása vonatkozik, például a tartós állapotú meghajtókkal (SSD-kkel) és a merevlemezes (HDD) meghajtók. A cosmos DB az elsődleges adatbázisok SSD meghajtókon tárolja. A media mellékletek és a biztonsági mentések általánosan biztonsági másolatot készít a HDD-k, az Azure Blob storage vannak tárolva. A Cosmos DB-hez készült inaktív adatok titkosítását, kezdve az adatbázisok, media mellékletek és biztonsági mentések titkosítottak. Az adatok most már (a hálózaton kívül) titkosított átvitel és inaktív (felejtő tárolás), így teljes körű titkosítást.

Mivel egy PaaS szolgáltatás, a Cosmos DB használata egyszerű. Cosmos DB-ben tárolt összes felhasználói adat titkosítva van, inaktív és átvitel, mert nincs teendője. Ez helyezni egy másik módja, hogy "on", alapértelmezés szerint a titkosítás inaktív állapotban van. Nincsenek ki- vagy kikapcsolni. Ez a funkció kínálunk, miközben továbbra is, hogy megfeleljen a [rendelkezésre állását és teljesítményét az SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db).

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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>K: hogyan sok egyéb mellett az Azure Storage szolgáltatás Ha engedélyezve van a Storage Service Encryption szolgáltatással?
V: nincs további költség nélkül.

### <a name="q-who-manages-the-encryption-keys"></a>K: akik kezeli a titkosítási kulcsokat?
V: a kulcsokat a Microsoft felügyeli.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Kérdés: milyen gyakran elforgatott a titkosítási kulcsokat?
V: a Microsoft rendelkezik egy belső irányelveket rögzített a titkosítási kulcs kulcsrotálás, amely a Cosmos DB a következő. A megadott irányelveket nem tesszük közzé. A Microsoft közzététele a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx), amely belső útmutató egy részét, látható, és fejlesztők számára hasznos ajánlott eljárásokat tartalmaz.

### <a name="q-can-i-use-my-own-encryption-keys"></a>K: használhatok saját titkosítási kulcsokat?
V: a cosmos DB egy PaaS szolgáltatás, és azt keményen dolgozott, hogy a szolgáltatás egyszerűen használható. Azt észleltük, hogy ezt a kérdést gyakran feltesznek, például a PCI DSS megfelelőségi követelménynek proxy kérdést. Ez a szolgáltatás létrehozásának részeként működtünk együtt, győződjön meg arról, hogy ügyfeleink, akik Cosmos DB követelményeinek saját maguk kulcsok kezelése nélkül a megfelelőségi auditorok.

### <a name="q-what-regions-have-encryption-turned-on"></a>K: milyen régiók van kapcsolva a titkosítás?
V: összes az Azure Cosmos DB-régióban van titkosítás engedélyezve van a összes felhasználói adatot.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>K: titkosítási befolyásolja a teljesítményt késésével és átviteli SLA-kat?
V: ne legyen hatással vagy változások a teljesítmény most, hogy az összes meglévő és új fiók engedélyezve van a titkosítás inaktív állapotban SLA-kat. Tudjon meg többet a a [a Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) oldalon tekintheti meg a legújabb garanciákat.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>K: a helyi emulátor támogatja az inaktív adatok titkosítását?
V: az emulátor egy önálló fejlesztési-tesztelési eszköz, és nem használja a kulcskezelési szolgáltatások, a felügyelt Cosmos DB szolgáltatás által használt. Azt javasoljuk, hogy hol tárolja bizalmas emulátor Tesztadatok meghajtók engedélyezheti a Bitlockert. A [emulátor támogatja az alapértelmezett adatkönyvtárát módosítása](local-emulator.md) valamint a jól ismert hely használatát.

## <a name="next-steps"></a>További lépések

A Cosmos DB-biztonság és a legújabb fejlesztései áttekintéséhez lásd: [Azure Cosmos DB-adatbázis biztonsági](database-security.md).
Microsoft-minősítésekkel kapcsolatos további információkért lásd: a [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/).
