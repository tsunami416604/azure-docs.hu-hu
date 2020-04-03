---
title: Adatbázis-biztonság – Azure Cosmos DB
description: Ismerje meg, hogy az Azure Cosmos DB hogyan biztosít adatbázis-védelmet és adatbiztonságot az adatok számára.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 345fc329df1c57cab7dd66c609bf3701fa3a6124
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619134"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Biztonság az Azure Cosmos DB-ben – Áttekintés

Ez a cikk az Azure Cosmos DB által az adatbázis feltörésének megakadályozása, észlelése és kezelése érdekében biztosított adatbázis-biztonsági ajánlott eljárásokat és fő funkciókat írja le.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Az Azure Cosmos DB biztonsági szolgáltatás újdonságai

Az inaktív titkosítás már elérhető az Azure Cosmos DB-ben tárolt dokumentumok és biztonsági mentések számára az összes Azure-régióban. Az inaktív titkosítás automatikusan érvénybe lép mind az új, mind a meglévő ügyfelek rekedve ezekben a régiókban. Nincs szükség konfigurálni semmit; és ugyanazt a nagy késleltetést, átviteli, rendelkezésre állást és funkcionalitást kapja, mint korábban, azzal az előnnyel, hogy tudja, az adatok biztonságosak és biztonságosak az inaktív titkosítással.

## <a name="how-do-i-secure-my-database"></a>Hogyan biztosíthatom az adatbázisomat?

Az adatbiztonság megosztott felelősség Ön, az ügyfél és az adatbázis-szolgáltató között. A választott adatbázis-szolgáltatótól függően a felelősség mértéke eltérő lehet. Ha helyszíni megoldást választ, a végpontvédelemtől kezdve a hardver fizikai biztonságáig mindent meg kell adnia , ami nem könnyű feladat. Ha egy PaaS-felhő-adatbázis-szolgáltatót, például az Azure Cosmos DB-t választja, az aggodalomra okot adó terület jelentősen csökken. Az alábbi kép, amely a Microsoft [megosztott felelősségei a felhőalapú számítástechnikával](https://aka.ms/sharedresponsibility) kapcsolatos tanulmányából származik, bemutatja, hogyan csökken a felelőssége egy PaaS-szolgáltatóval, például az Azure Cosmos DB-vel.

![Az ügyfél- és adatbázis-szolgáltató felelőssége](./media/database-security/nosql-database-security-responsibilities.png)

Az előző ábrán magas szintű felhőbiztonsági összetevők láthatók, de milyen elemek miatt kell aggódnia kifejezetten az adatbázis-megoldáshoz? És hogyan lehet összehasonlítani a megoldásokat egymással?

Az adatbázisrendszerek összehasonlítására vonatkozó követelmények alábbi ellenőrzőlistáját javasoljuk:

- Hálózati biztonság és tűzfal beállításai
- Felhasználói hitelesítés és részletes felhasználói vezérlők
- A regionális hibák globális replikálásának képessége
- Feladatátvétel egyik adatközpontból a másikba
- Helyi adatreplikáció adatközponton belül
- Automatikus adatmentés
- Törölt adatok visszaállítása biztonsági mentésekből
- Érzékeny adatok védelme és elkülönítése
- Támadások figyelése
- Reagálás a támadásokra
- Az adatok adatgyűjtés-irányítási korlátozások betartásának földrajzi kerítésezése
- A védett adatközpontokban lévő kiszolgálók fizikai védelme
- Tanúsítványok

És bár nyilvánvalónak tűnhet, a legutóbbi [nagyszabású adatbázis-jogsértések](https://thehackernews.com/2017/01/mongodb-database-security.html) emlékeztetnek minket a következő követelmények egyszerű, de kritikus fontosságára:

- Naprakészen tartott javított kiszolgálók
- HTTPS alapértelmezés szerint/TLS-titkosítás
- Rendszergazdai fiókok erős jelszóval

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hogyan biztosítja az Azure Cosmos DB az adatbázisomat?

Nézzük vissza az előző listát – hány ilyen biztonsági követelményeket biztosít az Azure Cosmos DB? Minden egyes darabot.

Ássuk bele magunkat részletesen.

|Biztonsági követelmény|Az Azure Cosmos DB biztonsági megközelítése|
|---|---|
|Hálózati biztonság|Az adatbázis védelmének első védelmi rétege az IP-tűzfal használata. Az Azure Cosmos DB támogatja a házirend-alapú IP-alapú hozzáférés-vezérlés a bejövő tűzfal támogatása. Az IP-alapú hozzáférés-vezérlések hasonlóak a hagyományos adatbázis-rendszerek által használt tűzfalszabályokhoz, de azok ki vannak bontva, így az Azure Cosmos-adatbázisfiók csak jóváhagyott gépek vagy felhőszolgáltatások készletéről érhető el. További információ az [Azure Cosmos DB tűzfal támogatási](firewall-support.md) cikkében.<br><br>Az Azure Cosmos DB lehetővé teszi egy adott IP-cím (168.61.48.0), egy IP-tartomány (168.61.48.0/8) és IP-cím és tartományok kombinációjának engedélyezését. <br><br>Az engedélyezett listán kívüli gépekről származó összes kérelmet az Azure Cosmos DB blokkolja. A jóváhagyott gépektől és felhőszolgáltatásoktól érkező kérelmeknek ezután be kell fejezniük a hitelesítési folyamatot, hogy hozzáférés-vezérlést kapjanak az erőforrásokhoz.<br><br> [A virtuális hálózati szolgáltatás címkék](../virtual-network/service-tags-overview.md) segítségével hálózati elkülönítést, és az Azure Cosmos DB-erőforrások védelme az általános internetről. Biztonsági szabályok létrehozásakor a szolgáltatáscímkéket használjon adott IP-címek helyett. A szolgáltatáscímke nevének (például az AzureCosmosDB) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.|
|Engedélyezés|Az Azure Cosmos DB kivonat-alapú üzenethitelesítési kódot (HMAC) használ az engedélyezéshez. <br><br>Minden kérelem kivonatolt a titkos fiók kulcshasználatával, és a következő base-64 kódolt kivonatküldi minden egyes hívás az Azure Cosmos DB. A kérelem érvényesítéséhez az Azure Cosmos DB szolgáltatás a megfelelő titkos kulcsot és tulajdonságokat használ egy kivonat létrehozásához, majd összehasonlítja az értéket a kérelemben szereplővel. Ha a két érték egyezik, a művelet sikeresen engedélyezett, és a kérelem feldolgozása, ellenkező esetben van egy engedélyezési hiba, és a kérelem elutasításra kerül.<br><br>Használhat egy [főkulcsot](secure-access-to-data.md#master-keys), vagy egy [erőforrás-jogkivonatot,](secure-access-to-data.md#resource-tokens) amely lehetővé teszi a részletes hozzáférést egy erőforráshoz, például egy dokumentumhoz.<br><br>További információ az [Azure Cosmos DB-erőforrásokhoz való hozzáférés biztosítása.](secure-access-to-data.md)|
|Felhasználók és engedélyek|A fiók főkulcsával adatbázisonként felhasználói erőforrásokat és engedély-erőforrásokat hozhat létre. Az erőforrás-jogkivonat egy adatbázis engedélyéhez van társítva, és meghatározza, hogy a felhasználó rendelkezik-e hozzáféréssel (írási és írási, írásvédett vagy nincs hozzáférés) az adatbázis ban lévő alkalmazás-erőforrásokhoz. Az alkalmazás-erőforrások közé tartoznak a tárolók, a dokumentumok, a mellékletek, a tárolt eljárások, az eseményindítók és az UDF-ek. Az erőforrás-jogkivonat ezután a hitelesítés során az erőforráshoz való hozzáférés biztosításához vagy megtagadásához használatos.<br><br>További információ az [Azure Cosmos DB-erőforrásokhoz való hozzáférés biztosítása.](secure-access-to-data.md)|
|Active directory-integráció (RBAC)| A Cosmos-fiókhoz, az adatbázishoz, a tárolóhoz és az ajánlatokhoz (átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és átviteli- és elérési- hozzáféréshez) való hozzáférést is biztosíthatja vagy korlátozhatja az Azure Portalon. Az IAM szerepköralapú hozzáférés-vezérlést biztosít, és integrálható az Active Directoryval. Beépített szerepköröket vagy egyéni szerepköröket használhat egyének és csoportok számára. További információt az [Active Directory integrációs](role-based-access-control.md) cikkében talál.|
|Globális replikáció|Az Azure Cosmos DB kulcsrakész globális terjesztést kínál, amely lehetővé teszi, hogy egyetlen kattintással replikálja az adatokat az Azure bármelyik világméretű adatközpontjára. A globális replikáció lehetővé teszi, hogy globálisan skálázjon, és alacsony késleltetésű hozzáférést biztosítson az adatokhoz világszerte.<br><br>A biztonság összefüggésében a globális replikáció biztosítja az adatvédelmet a regionális hibák ellen.<br><br>További információ: [Globális adatterjesztés](distribute-data-globally.md).|
|Régiónkénti feladatátvétel|Ha az adatokat több adatközpontban replikálta, az Azure Cosmos DB automatikusan átgördül a műveleteken, ha egy regionális adatközpont offline állapotba kerül. Létrehozhat egy rangsorolt feladatátvételi régiók listáját azadatok replikálásának régiójával. <br><br>További információ az [Azure Cosmos DB regionális feladatátvételeiről.](high-availability.md)|
|Helyi replikáció|Az Azure Cosmos DB még egyetlen adatközponton belül is automatikusan replikálja az adatokat a magas rendelkezésre állás érdekében, így választhat a [konzisztenciaszintek közül.](consistency-levels.md) Ez a replikáció garantálja a 99,99%-os [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) az összes egyrégiós fiókok és az összes több régiós fiókok nyugodt konzisztencia és 99,999%-os olvasási rendelkezésre állás az összes többrégiós adatbázis-fiókok.|
|Automatizált online biztonsági mentések|Az Azure Cosmos-adatbázisok rendszeresen biztonsági mentése és tárolása georedundáns tárolóban történik. <br><br>További információ az [Automatikus online biztonsági mentés és visszaállítás az Azure Cosmos DB-vel.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)|
|Törölt adatok visszaállítása|Az automatikus online biztonsági mentések segítségével helyreállíthatja azokat az adatokat, amelyeket véletlenül az esemény után akár ~ 30 nappal törölt. <br><br>További információ az [Automatikus online biztonsági mentés és visszaállítás az Azure Cosmos DB-vel](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)|
|Érzékeny adatok védelme és elkülönítése|Az Összes adat a régiókban felsorolt Újdonságok? most már titkosítva van.<br><br>A személyes adatok és egyéb bizalmas adatok elkülöníthetők egy adott tárolóra és írásra és írásra, vagy az írásvédett hozzáférés csak meghatározott felhasználókra korlátozható.|
|Támadások figyelése|A [naplózás és a tevékenységnaplók](logging.md)használatával figyelheti a fiókját a normál és rendellenes tevékenységekre. Megtekintheti, hogy milyen műveleteket hajtottak végre az erőforrásokon, ki kezdeményezte a műveletet, mikor történt a művelet, a művelet állapota, és még sok más, amint az a táblázatot követő képernyőképen látható.|
|Reagálás a támadásokra|Miután kapcsolatba lépett az Azure-támogatással egy esetleges támadás bejelentéséhez, egy 5 lépéses incidens-reagálási folyamat elindul. Az 5 lépéses folyamat célja, hogy a probléma észlelése és a vizsgálat megkezdése után a lehető leggyorsabban helyreállítsa a normál szolgáltatásbiztonságot és műveleteket.<br><br>További információ a [Microsoft Azure biztonsági válasza a felhőben](https://aka.ms/securityresponsepaper)című részben.|
|Geo-kerítés|Az Azure Cosmos DB biztosítja a szuverén régiók (például Németország, Kína, USA-beli kormány) adatirányítását.|
|Védett létesítmények|Az Azure Cosmos DB-ben tárolt adatok az Azure védett adatközpontjaiban található SSD-ken tárolódnak.<br><br>További információ a [Microsoft globális adatközpontjaiban](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS titkosítás|Az Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. Az Azure Cosmos DB a TLS 1.2-t is támogatja.<br>Lehetőség van egy minimális TLS verziókiszolgálói oldal kényszerítésére. Ehhez vegye fel [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)a kapcsolatot.|
|Titkosítás inaktív állapotban|Az Azure Cosmos DB-ben tárolt összes adat inkultálta az inkett. További információ az [Azure Cosmos DB titkosítása inaktív módon](./database-encryption-at-rest.md)|
|Javított szerverek|Felügyelt adatbázisként az Azure Cosmos DB szükségtelenné teszi a kiszolgálók kezelését és javítását, amely automatikusan elvégezhető.|
|Rendszergazdai fiókok erős jelszóval|Nehéz elhinni, hogy még ezt a követelményt is meg kell említenünk, de néhány versenytársunkkal ellentétben lehetetlen jelszó nélkül rendelkezni az Azure Cosmos DB-ben.<br><br> A TLS- és HMAC titkos alapú hitelesítésen keresztüli biztonság alapértelmezés szerint bevan sülve.|
|Biztonsági és adatvédelmi tanúsítványok| A tanúsítványok legfrissebb listáját lásd a teljes [Azure Compliance-webhelyen,](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) valamint a legújabb [Azure Megfelelőségi dokumentumot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) az összes tanúsítványsal (keresés a Cosmos-ra). 2018. április 25-i posta (Azure #CosmosDB: Secure, private, compliant, amely tartalmazza a SOCS 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High és még sok más tartalmat) tartalmazó, célzottabb olvasd el.

A következő képernyőkép bemutatja, hogyan használhatja a naplózási és tevékenységnaplókat a fiók figyelésére: ![Az Azure Cosmos DB tevékenységnaplói](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>További lépések

A főkulcsokról és az erőforrás-jogkivonatokról az [Azure Cosmos DB-adatokhoz való hozzáférés védelme](secure-access-to-data.md)című témakörben talál további információt.

A naplózásról az [Azure Cosmos DB diagnosztikai naplózása](logging.md)című témakörben talál további információt.

A Microsoft-tanúsítványokról az [Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/)talál további információt.