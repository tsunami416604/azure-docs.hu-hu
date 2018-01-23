---
title: "Az adatbázis-titkosítás aktívan: Azure Cosmos DB |} Microsoft Docs"
description: "Ismerje meg, hogyan nyújt az Azure Cosmos DB a alapértelmezett titkosítás az összes adatot."
services: cosmos-db
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: 193dbede0655376714c6f8f12c826ba3f9bc8933
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Az Azure Cosmos DB adatbázis titkosítását

Aktívan nem adattitkosítás egy kifejezést gyakran hivatkozik a titkosítás nem felejtő tárolóeszközök, adatok, például tartós állapotú meghajtók (SSD) és a merevlemezes (HDD) meghajtók. Cosmos DB az SSD-k az elsődleges adatbázis tárolja. A media mellékletek és a biztonsági mentések Azure Blob Storage tárolóban, amely általában a biztonsági HDD vannak tárolva. A Cosmos DB titkosítását kiadása az adatbázisok, media mellékletek és biztonsági mentések vannak titkosítva. A most adattitkosítás átvitel közben (a hálózaton kívül) és inaktív (nem felejtő tárolási), felkínálva végpontok közötti titkosítását.

Mivel a PaaS szolgáltatás Cosmos DB nagyon könnyen használható. Mert Cosmos DB tárolt összes felhasználói adatokat aktívan és átviteli titkosított, nincs teendője. Másképp fogalmazva úgy, hogy "a" alapértelmezés szerint van titkosítását. Nincsenek nem kapcsolható ki- vagy szabályozza. Ez a szolgáltatás nyújtunk közben továbbra is, hogy megfeleljen a [rendelkezésre állásának és teljesítményének SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Az Azure Cosmos DB titkosítását végrehajtása

Titkosítását számos biztonsági technológia, beleértve a biztonságos kulcs tárolása rendszerek, a titkosított hálózatokat és a kriptográfiai API-k segítségével történik. Adatok feldolgozása és visszafejtésére rendszerek kell kommunikálni a rendszerek, a kulcsok kezelése. Az ábra bemutatja, hogyan titkosított adatok tárolása és a kulcsok kezelése elválasztva egymástól. 

![Tervezési diagramja](./media/database-encryption-at-rest/design-diagram.png)

Egy felhasználói kérelem alapszintű folyamat a következőképpen történik:
- A felhasználói adatbázis fiók létesítése készen áll, és a tároló kulcsait a rendszer beolvassa a felügyeleti szolgáltatás erőforrás-szolgáltató kérelem keresztül.
- Egy felhasználó kapcsolatot hoz létre Cosmos DB HTTPS/secure átviteli keresztül. (Az SDK-k absztrakt részleteit.)
- A felhasználó elküldi egy JSON-dokumentum a korábban létrehozott biztonságos kapcsolaton keresztül kell tárolni.
- A JSON-dokumentum egy indexelt, kivéve, ha a felhasználó kikapcsolta az indexelés.
- Mindkét a JSON dokumentum és az index adatokat írni biztonságos tárolására.
- Rendszeres időközönként adatokat olvasni a biztonságos tárolására és biztonsági másolatot készít az Azure titkosított Blob tároló.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>K: hogyan sokkal költségeket a Azure Storage a Storage szolgáltatás titkosítási engedélyezésekor?
V: nincs további költség nélkül.

### <a name="q-who-manages-the-encryption-keys"></a>K: kezelő a titkosítási kulcsokat?
A: a Microsoft által felügyelt a kulcsokat.

### <a name="q-how-often-are-encryption-keys-rotated"></a>K: milyen gyakran elforgatott a titkosítási kulcsokat?
V: Microsoft tartozik egy belső iránymutatásokat titkosítási kulcs elforgatás, amely Cosmos DB követi. A megadott irányelveket nem kerülnek közzétételre. Microsoft közzététele a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx), amely belső útmutatást részhalmazának tekintendő, és rendelkezik a hasznos gyakorlati tanácsok a fejlesztők számára.

### <a name="q-can-i-use-my-own-encryption-keys"></a>K: használhatok saját titkosítási kulcsokat?
V: cosmos DB egy PaaS szolgáltatás, és ezért merevlemez előre tartani a szolgáltatás könnyen használható. Ez a kérdés gyakran ismételt egy megfelelőségi követelményt, például a PCI DSS kiválassza proxy kérdésként már észleltünk. Ez a szolgáltatás létrehozásának részeként ezért előre a megfelelőségi ellenőrök annak érdekében, hogy az ügyfelek, akik Cosmos-Adatbázist kíván használni megfelelnek-e a követelményeknek, nem kell maguknak kulcsok kezelése.
Ennek eredményeképpen azt jelenleg nem teszi lehetővé felhasználók terheljék magukat a kulcskezelést.

### <a name="q-what-regions-have-encryption-turned-on"></a>K: Mi memóriaterületnél engedélyezve van a titkosítás?
V: minden Azure Cosmos DB memóriaterületnél titkosítás az összes felhasználói adat-e kapcsolva.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>K: titkosítási hatással vannak, a teljesítmény késést és átviteli SLA-kat?
V: nincs hatása vagy módosításokat most, hogy aktívan engedélyezve van az összes meglévő és új fiók SLA-k teljesítménye. További a a [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) lapot, melyen megtekintheti a legfrissebb garanciát.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Kérdés: a helyi emulátor támogatja titkosítását?
V: az emulátor az önálló fejlesztési és tesztelési célú eszközzel, és nem használja a kulcskezelő szolgáltatások, a felügyelt Cosmos DB szolgáltatás által használt. Azt javasoljuk, ahol bizalmas emulátor Tesztadatok tároló meghajtókon a BitLocker engedélyezése érdekében. A [emulátor támogatja az alapértelmezett adatkönyvtár módosítása](local-emulator.md) továbbá használatával egy ismert helyre.

## <a name="next-steps"></a>További lépések

A Cosmos DB biztonság és a legújabb fejlesztései rendelkezésre megtudhatja, [Azure Cosmos DB adatbázis biztonsági](database-security.md).
További információ a Microsoft minősítései közül: a [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/en-us/support/trust-center/).
