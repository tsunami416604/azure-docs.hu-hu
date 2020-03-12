---
title: Adatbázis-biztonság – Azure Cosmos DB-hez
description: Ismerje meg, hogyan nyújt az Azure Cosmos DB az adatbázis védelmét és az adatok biztonsági adataihoz.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0c97d70ea3e5c7fdd14b0f97c5e393359f2b948e
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087234"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Biztonság a Azure Cosmos DBban – áttekintés

Ez a cikk az Azure Cosmos DB által az adatbázis feltörésének megakadályozása, észlelése és kezelése érdekében biztosított adatbázis-biztonsági ajánlott eljárásokat és fő funkciókat írja le.

## <a name="whats-new-in-azure-cosmos-db-security"></a>A Azure Cosmos DB biztonság újdonságai

Titkosítás inaktív állapotban már elérhető a dokumentumok és az Azure Cosmos DB az összes Azure-régióban található biztonsági másolataihoz. Titkosítás inaktív állapotban automatikusan alkalmazza az új és meglévő ügyfelek ezekben a régiókban. Nem kell semmilyen; az azonos jelentős késés, átviteli sebesség, rendelkezésre állási kap, és funkciók, mielőtt az az előnye, hogy az adatok védelméről a titkosítás inaktív állapotban.

## <a name="how-do-i-secure-my-database"></a>Az adatbázis biztonságának Hogyan

Adatbiztonság egy, az ügyfél és az adatbázis-szolgáltató között megosztott felelősségre. Sokat felelőssége mennyisége az adatbázis-szolgáltatónál, attól függően eltérőek lehetnek. Ha úgy dönt, hogy egy helyszíni megoldás, meg kell adnia, hogy a hardver -, amely nem egyszerű feladat végponti fizikai biztonsági védelmet a. Ha úgy dönt, hogy egy PaaS felhőalapú adatbázis-szolgáltató például az Azure Cosmos DB, a terület hibaforrás jelentősen csökken. Az alábbi, a Microsoft által a [felhőalapú számítástechnikai feladatokkal kapcsolatos közös felelősséggel](https://aka.ms/sharedresponsibility) foglalkozó rendszerkép azt mutatja be, hogyan csökken a felelőssége egy olyan Pásti-szolgáltatóval, mint a Azure Cosmos db.

![Ügyfél- és adatbázis-szolgáltató feladatkörei](./media/database-security/nosql-database-security-responsibilities.png)

A fentebbi ábra bemutatja azokat magas szintű felhőbeli biztonsági összetevők, de milyen elemek kell aggódnia a kifejezetten a az adatbázis-megoldástól? És hogyan lehet összehasonlítja megoldásait egymáshoz?

Azt javasoljuk, hogy az alábbi ellenőrzőlista, amelyen adatbázis-rendszerek összehasonlítására követelmények:

- Hálózati biztonság és a tűzfal beállításai
- Felhasználói hitelesítés és a részletes felhasználói vezérlők
- Replikálhatók adatait globálisan regionális meghibásodások esetén
- Az egyik adatközpontból a másikba való feladatátvétel lehetősége
- Helyi adatok replikálása egy adatközponton belül
- Automatikus biztonsági mentések
- A biztonsági mentésekből törölt adatok helyreállítása
- Védelme és a bizalmas adatok elkülönítése
- A támadások figyelése
- Válaszadás a támadások
- Igazodnia kell irányítási adatkorlátozás teszi geo-időkorlát adatok
- Kiszolgálók a védett adatközpontok fizikai védelme
- Tanúsítványok

És bár nyilvánvalónak tűnhet, a közelmúltban [nagy léptékű adatbázis-megsértések](https://thehackernews.com/2017/01/mongodb-database-security.html) emlékeztetnek minket az alábbi követelmények egyszerű, de kritikus jelentőségére:

- A javított kiszolgálók naprakészen tartanak
- Alapértelmezett/SSL-titkosítás által HTTPS
- Erős jelszót a rendszergazdai fiókok

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hogyan Azure Cosmos DB az adatbázis biztonsága

Vizsgáljuk meg a fenti lista vissza - hány biztonsági követelményekről az Azure Cosmos DB biztosítja a? Minden egyes egyetlen olyan.

Tájékozódjon részletesebben mindegyikhez.

|Biztonsági követelmény|Az Azure Cosmos DB biztonsági módszer|
|---|---|
|Hálózati biztonság|Az IP-tűzfalak használata esetén az első adatbázis biztonságossá tétele védelmi réteget. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő vezérelt házirend. Az IP-alapú hozzáférés-vezérlések a hagyományos adatbázis-rendszerek által használt tűzfalszabályok hasonlóak, de kibővülnek, így az Azure Cosmos adatbázis-fiók csak jóváhagyott gépekből vagy felhőalapú szolgáltatásokból érhető el. További információt [Azure Cosmos db tűzfal támogatási](firewall-support.md) cikkében talál.<br><br>Az Azure Cosmos DB lehetővé teszi, hogy egy adott IP-cím (168.61.48.0), egy IP-címtartomány (168.61.48.0/8) és az IP-címek és tartományok kombinációi. <br><br>Azure Cosmos DB engedélyezési listán szereplő gépekről származó összes kérelem blokkolja. Jóváhagyott gépek és felhőszolgáltatások majd érkező kéréseket kell végeznie a hitelesítési folyamat az erőforrásokhoz való hozzáférés-vezérlés kell megadni.<br><br> A [virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md) a hálózati elkülönítés eléréséhez és a Azure Cosmos db-erőforrások általános internetről való ellátásához használhatók. A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. A szolgáltatási címke nevének (például AzureCosmosDB) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.|
|Engedélyezés|Az Azure Cosmos DB üzenetet kivonat-alapú hitelesítési kód (HMAC) használ a hitelesítéshez használ. <br><br>A titkos kulcsát minden kérés kivonatolt, és a későbbi base-64 kódolású kivonat küldi el az egyes Azure Cosmos DB-hívások. A kérelem érvényesítéséhez, az Azure Cosmos DB szolgáltatást használja a megfelelő titkos kulcs és a Tulajdonságok létrehozni a kivonatot, majd összehasonlítja az értéket egy, a kérelmet. Ha a két érték egyezik, a művelet sikeresen jogosult-e, és a kérés feldolgozása, ellenkező esetben van egy engedélyezési hiba, és a kérelmet elutasították.<br><br>Használhatja a [főkulcsot](secure-access-to-data.md#master-keys)vagy egy [erőforrás-tokent](secure-access-to-data.md#resource-tokens) is, amely lehetővé teszi a részletes hozzáférést egy erőforráshoz, például egy dokumentumhoz.<br><br>További információ a [Azure Cosmos db erőforrásaihoz való hozzáférés biztonságossá tételéről](secure-access-to-data.md).|
|Felhasználók és engedélyek|A fiók főkulcsának használatával létrehozhat egy adatbázison felhasználói erőforrásokat és engedélyek erőforrásait. Az erőforrás-token egy adatbázisban lévő engedélyhez van társítva, és meghatározza, hogy a felhasználó rendelkezik-e hozzáféréssel (írható-olvasható, írásvédett vagy nincs hozzáférés) az adatbázisban lévő alkalmazás-erőforráshoz. Alkalmazás-erőforrások közé tartoznak a tároló, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók és felhasználói függvények. Az erőforrás-jogkivonat-hitelesítés során majd használni a vagy megtagadja a hozzáférést az erőforráshoz.<br><br>További információ a [Azure Cosmos db erőforrásaihoz való hozzáférés biztonságossá tételéről](secure-access-to-data.md).|
|Active directory-integráció (RBAC)| Az Azure Portal hozzáférés-vezérlés (IAM) használatával is megadhatja vagy korlátozhatja a Cosmos-fiók, az adatbázis, a tároló és az ajánlatok elérését. Az IAM szerepköralapú hozzáférés-vezérlést biztosít, és együttműködik az Active Directoryval. Használhat beépített szerepköröket vagy egyéni szerepköröket egyéni felhasználókhoz és csoportokhoz. További információ: [Active Directory Integration](role-based-access-control.md) article.|
|Globális replikálás|Az Azure Cosmos DB kínál a kulcsrakész globális disztribúciót, amely lehetővé teszi, hogy az adatok replikálása egy gombra kattintva elküldjön világméretű az Azure-adatközpontok közül. Globális replikálás lehetővé teszi, hogy globálisan méretezhető legyen, és kis késésű hozzáférést az adataihoz, a világ különböző pontjain.<br><br>Biztonsági környezetében globális replikáció biztosítja az adatok védelmet élveznek a regionális meghibásodásokkal szemben.<br><br>További információ: [Globális adatterjesztés](distribute-data-globally.md).|
|Régiónkénti feladatátvétel|Ha egynél több adatközpontban az adatok replikálása, az Azure Cosmos DB automatikusan áthalad a műveletek kell egy regionális adatközpont kapcsolat nélküli módba. A régió, amelyben a rendszer replikálja az adatokat használó feladatátvevő régiók rangsorolt listáját is létrehozhat. <br><br>További információ a [regionális feladatátvételekről Azure Cosmos DBban](high-availability.md).|
|A helyi replikációt|A Azure Cosmos DB még egy adatközponton belül is automatikusan replikálja az adatok magas rendelkezésre állását, így biztosítva a [konzisztencia-szintek](consistency-levels.md)választékát. Ez a replikálás garantálja a 99,99%-os [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) -t az összes egyrégiós fiókhoz és az összes többrégiós fiókhoz, amely nyugodt konzisztenciát és 99,999%-os olvasási rendelkezésre állást biztosít az összes több régiós adatbázis|
|Automatikus online biztonsági másolatok|Az Azure Cosmos-adatbázisok rendszeresen készülnek, és egy geo redundáns tárolóban tárolódnak. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás Azure Cosmos DBsal](online-backup-and-restore.md).|
|Törölt adatok helyreállításához|Az automatikus online biztonsági másolatok helyreállítani az adatokat, előfordulhat, hogy véletlenül törölt az esemény után akár KB. 30 napig használható. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás Azure Cosmos db](online-backup-and-restore.md)|
|Védelme és a bizalmas adatok elkülönítése|Az Újdonságok listájában felsorolt régiók összes adatai mostantól titkosítva van.<br><br>Személyes adatok és egyéb bizalmas adatokat el lehet különíteni az adott tároló és az olvasási és írási, vagy csak olvasási hozzáférés meghatározott felhasználókra korlátozható.|
|A figyelő támadások|A naplózási [és a tevékenységi naplók](logging.md)használatával figyelheti a fiókját a normál és rendellenes tevékenységekhez. Milyen műveletek lettek végrehajtva az erőforrásokon, amikor a művelet történt, az állapot, a művelet, és sokkal amint az a táblázat alatti képernyőképet a műveletet kezdeményező tekintheti meg.|
|Elháríthatja a támadásokat|Azure-támogatási jelentését a potenciális támadási kell felvenni a kapcsolatot, miután egy 5-lépés incidensek megoldási folyamatának megkezdődik. Az 5. lépés – folyamat célja normál szolgáltatás biztonsága és műveletei a lehető leggyorsabban visszaállítása után problémát észlelt, és a vizsgálat elindult.<br><br>További információ: [Microsoft Azure biztonsági válasz a felhőben](https://aka.ms/securityresponsepaper).|
|Geokerítés-|Az Azure Cosmos DB biztosítja az adatszabályozást szuverén régiók (például Németország, Kína, US Gov).|
|Védett létesítményekben|Az Azure Cosmos DB adatokat az SSD-k az Azure által védett adatközpontokban tárolja.<br><br>További információ a [Microsoft globális adatközpontokban](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS encryption|A Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. A Azure Cosmos DB a TLS 1,2-et is támogatja.<br>Egy minimális TLS-verzió kiszolgálóoldali kikényszeríthető. Ehhez vegye fel a kapcsolatot [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Titkosítás inaktív állapotban|Az Azure Cosmos DB-be tárolt összes adat titkosítása. További információ a [Azure Cosmos db inaktív titkosításról](./database-encryption-at-rest.md)|
|Javított kiszolgálók|Egy felügyelt adatbázis Azure Cosmos DB szükségtelenné teszi a kezelése és a patch-kiszolgálók által elvégzett, automatikusan.|
|Erős jelszót a rendszergazdai fiókok|Hihetetlen, még akkor is meg kell ezt a követelményt említik, de néhány lemaradni a versenytársak eltérően nem lehet egy rendszergazdai fiók, jelszó nélküli rendelkezik az Azure Cosmos DB.<br><br> Alapértelmezés szerint a számlázásnak biztonsági SSL és HMAC-val titkos alapú hitelesítés használatával.|
|Biztonság és a data protection minősítések| A minősítések legnaprakészebb listája a teljes [Azure-megfelelőségi webhelyet](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , valamint a legújabb [Azure-megfelelőségi dokumentumot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) tartalmazza minden minősítéssel (a Cosmos kifejezés keresése). A koncentráltabb olvasásért tekintse meg 2018 az [Azure #CosmosDB: Secure, Private, kompatibilis, SOCS 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP magas és sok más.

A következő képernyőképen látható, hogyan használhatók a naplózási naplózási és a tevékenységi naplók a fiók figyeléséhez: ![-tevékenység naplófájljai Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Következő lépések

További információ a főkulcsokról és az erőforrás-jogkivonatokról: a [Azure Cosmos db adatokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md).

További információ a naplózásról: [Azure Cosmos db diagnosztikai naplózás](logging.md).

További információ a Microsoft-tanúsítványokról: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).