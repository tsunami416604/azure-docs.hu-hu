---
title: "Adatbázis-security - Azure Cosmos DB |} Microsoft Docs"
description: "Ismerje meg, hogy az Azure Cosmos DB biztosítja az adatok az adatbázis védelmét, és az adatok biztonsági."
keywords: "nosql-adatbázis biztonsági, információ-biztonság, adatok biztonsága, az adatbázis-titkosítás, az adatbázis védelme, biztonsági házirendeket, biztonsági tesztelése"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: mimig
ms.openlocfilehash: 73d5c33cec1ebec76ea738e03cd29e88d482b905
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-database-security"></a>Azure Cosmos DB adatbázis biztonsági

A cikk ismerteti az adatbázis ajánlott biztonsági eljárások és Azure Cosmos DB segítséget nyújtanak a megakadályozása, észlelésében és kezelésében az adatbázis behatolás által nyújtott főbb szolgáltatások.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB biztonsági újdonságai?

Titkosítását már elérhető a dokumentumok és Azure Cosmos DB minden Azure régióban található biztonsági másolataihoz. Aktívan titkosítása automatikusan alkalmazza az új és meglévő ügyfelek ezeken a területeken. Nincs szükség semmilyen; és az azonos nagy késleltetésű, teljesítményt, rendelkezésre állási kap, és funkciókat előtt azzal az előnnyel jár, hogy ismerjék az adatok titkosítását a biztonságos.

## <a name="how-do-i-secure-my-database"></a>Hogyan biztonságos a saját adatbázis? 

Adatok biztonsági megosztott feladata meg, az ügyfél és az adatbázis-szolgáltató között. Attól függően, hogy az adatbázis-szolgáltató úgy dönt hajt felelősségi mennyisége eltérőek lehetnek. Ha úgy dönt, hogy egy helyszíni megoldás, adja meg a hardver -, amely nem egyszerű feladat végpont fizikai biztonsági védelem szeretné. Ha úgy dönt, hogy a PaaS felhő adatbázis-szolgáltató például az Azure Cosmos DB, jelentősen zsugorítja probléma, amely az Ön lakóhelyén. A Microsoft az alábbi képen tárolt, közös [megosztott feladatkörök a felhőalapú informatika](https://aka.ms/sharedresponsibility) tanulmány, bemutatja, hogyan Ön felelőssége csökkenti, például Azure Cosmos DB PaaS szolgáltatóhoz.

![Ügyfél- és adatbázis-szolgáltató feladatkörei](./media/database-security/nosql-database-security-responsibilities.png)

Az előző ábrán látható magas szintű felhőalapú biztonsági összetevők, de milyen elemek van szüksége az adatbázis-megoldás a foglalkoznia? És hogyan lehet akkor megoldások összehasonlítása egymással? 

Azt javasoljuk, hogy az alábbi ellenőrzőlista a használandó adatbázis-rendszerek összehasonlítása követelmények:

- Hálózati biztonság és a tűzfal beállításai
- Felhasználó hitelesítése és részletes felhasználói vezérlők
- Replikálja globálisan a regionális meghibásodásokkal adatokat lehessen
- Feladatátvétel végrehajtott egyik adatközpontból a másikba
- Helyi adatreplikációját adatközponton belül
- Automatikus biztonsági mentések
- A biztonsági mentésekből törölt adatok helyreállítása
- Védeni, és a bizalmas adatok elkülönítése
- A támadások figyelése
- Válaszol a támadások
- Földrajzi-időkorlát adatok igazodnia kell az adatok adatszabályozás korlátozásokat lehessen
- Fizikai kiszolgálók védett adatközpontokban védelme

Bár nyilvánvaló, legutóbbi tűnhet és [nagyméretű adatbázis behatolás](http://thehackernews.com/2017/01/mongodb-database-security.html) emlékeztesse nekünk a egyszerű, de a kritikus fontosságú az alábbi követelményeket:
- Mindig naprakészek legyenek kiszolgálók lett
- Alapértelmezett/SSL-titkosítás által HTTPS
- Az erős jelszóval rendelkező rendszergazdai fiókok

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hogyan Azure Cosmos DB saját adatbázis védelme?

Nézzük vissza az előző listában - hány biztonsági követelményekről Azure Cosmos DB nyújt? Minden egyetlen egyes.

Most dig részletesen mindegyiknél be.

|Biztonsági követelmény|Azure Cosmos-adatbázis biztonsági módszer|
|---|---|---|
|Hálózati biztonság|Az IP-tűzfal használata az első védelmi réteg biztosítása az adatbázis védelmét. Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérléssel bejövő tűzfaltámogatás vezérelt házirend. Az IP-alapú hozzáférés-vezérlést a hagyományos adatbázis-rendszerek által használt tűzfalszabályok hasonló, de úgy, hogy az Azure Cosmos DB adatbázisfiók csak érhető el a gépek vagy felhőszolgáltatások jóváhagyott készlete bontva. <br><br>Azure Cosmos-adatbázis lehetővé teszi egy adott IP-cím (168.61.48.0), egy IP-címtartomány (168.61.48.0/8) és IP-címek és tartományok kombinációi engedélyezése. <br><br>Az engedélyezett bővítmények listájához kívül gépekről származó összes kérelem Azure Cosmos DB blokkolja. Kérések jóváhagyott gépek és felhőszolgáltatások, majd hajtsa végre a hitelesítési folyamat az erőforrásokhoz való hozzáférés-vezérlés kell megadni.<br><br>További információ: [Azure Cosmos DB tűzfaltámogatás](firewall-support.md).|
|Engedélyezés|Azure Cosmos-adatbázis kivonat-alapú üzenethitelesítési kóddal (HMAC) használ a hitelesítéshez. <br><br>Minden egyes kérelem kivonatolja a titkos kulcsa segítségével, és a későbbi base-64 kódolású kivonatoló jut Azure Cosmos DB minden egyes hívásakor. A kérés ellenőrzéséhez az Azure Cosmos DB szolgáltatás használja a megfelelő titkos kulcsot és a tulajdonságok a kivonat létrehozásához, és összehasonlítja az értéket a kérelemben egy. Ha a két érték egyezik, a művelet sikeresen engedélyezve van, és dolgozza fel a kérelmet, ellenkező esetben van egy engedélyezési hiba, és a kérelmet elutasították.<br><br>Választhat egy [főkulcs](secure-access-to-data.md#master-keys), vagy egy [erőforrás-jogkivonat](secure-access-to-data.md#resource-tokens) részletes hozzáférést egy erőforráshoz, például egy dokumentum.<br><br>További információ: [Azure Cosmos DB erőforrásokhoz való hozzáférés biztosítása](secure-access-to-data.md).|
|Felhasználók és engedélyek|Használja a [főkulcs](#master-key) a fiók felhasználói engedélyt erőforrásokat adatbázisonként és hozhat létre. A [erőforrás-jogkivonat](#resource-token) társított adatbázis engedélyt, és meghatározza, hogy a felhasználó rendelkezik-e az alkalmazás-erőforrást az adatbázisban (írható-olvasható, csak olvasható, vagy nincs hozzáférése) elérésére. Alkalmazás erőforrások közé tartoznak a gyűjtemények, dokumentumok, a mellékletek, tárolt eljárások, eseményindítók és felhasználó által megadott függvények. Az erőforrás-jogkivonat ezután történik a hitelesítés során vagy megtagadja a hozzáférést az erőforráshoz.<br><br>További információ: [Azure Cosmos DB erőforrásokhoz való hozzáférés biztosítása](secure-access-to-data.md).|
|Active directory-integráció (RBAC)| A hozzáférés-vezérlés (IAM) használatával az Azure portálon, adatbázis-fiók eléréséhez is megadhatja a táblázatot követő képernyőfelvételen látható módon. IAM szerepköralapú hozzáférés-vezérlés és jól integrálható az Active Directory. Használhatja a beépített szerepkörök vagy egyéni szerepkörök az egyes személyek és csoportok az alábbi ábrán látható módon.|
|Globális replikáció|Azure Cosmos DB kulcsrakész globális terjesztési, amely lehetővé teszi az adatok replikálása közül legalább egy Azure világszerte adatközpontok az egy kattintással kínál. Globális replikáció lehetővé teszi a globálisan méretezhető legyen, és az adatok a világ különböző alacsony késésű hozzáférést biztosítanak.<br><br>A biztonsági környezetében globális replikációs regionális meghibásodásokkal szemben az adatvédelem biztosítja.<br><br>További információ: [adatok globálisan terjesztése](distribute-data-globally.md).|
|Regionális feladatátvétel|Ha az adatok több adatközpont replikálása, Azure Cosmos DB automatikusan áthalad az operatív kell egy regionális adatközpont kapcsolat nélküli módba. A prioritási sorrend listájáról a régióban, amelyben a rendszer replikálja az adatokat használó feladatátvevő régiók hozhat létre. <br><br>További információ: [regionális feladatátvétel az Azure Cosmos Adatbázisba](regional-failover.md).|
|A helyi replikációt|Még belül egyetlen adatközpontba, Azure Cosmos DB automatikusan replikálja az adatokat a magas rendelkezésre állású, felkínálva a választott [konzisztenciaszintek](consistency-levels.md). Ez biztosítja, hogy egy [hasznos üzemidő rendelkezésre állás 99,99 % SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) és pénzügyi garanciális - valami más adatbázis-szolgáltatás által biztosított származnak.|
|Online biztonsági mentések automatikus|Az Azure Cosmos DB adatbázisok rendszeresen végeznek biztonsági mentést és egy georedundant tárolja. <br><br>További információ: [automatikus online biztonsági mentés és helyreállítás Azure Cosmos DB](online-backup-and-restore.md).|
|Törölt adatok helyreállításához|Az automatikus online biztonsági mentések helyreállítani az adatokat, lehetséges, hogy véletlenül törölt az esemény után legfeljebb kb. 30 napig használható. <br><br>További információ: [automatikus online biztonsági mentés és helyreállítás Azure Cosmos DB](online-backup-and-restore.md)|
|Védeni, és a bizalmas adatok elkülönítése|A felsorolt régiókban lévő összes adatot [Újdonságok?](#whats-new) most titkosítása.<br><br>PII-k és más bizalmas adatokat el lehet különíteni az adott gyűjteményekre és írható-olvasható, vagy csak olvasási hozzáféréssel az egyes felhasználók korlátozható.|
|A figyelő támadások|A [naplózás és a tevékenység a naplók](logging.md), figyelheti a normál és rendellenes tevékenységet-fiókjához. Tekintse meg az erőforrások, a művelet, ha a művelet történt, a művelet, és sokkal több, mint a táblázat utáni képernyőfelvételen látható állapotának elindító a végrehajtott műveleteket.|
|Válaszoljon a támadások által.|A potenciális támadási jelentést az Azure támogatási csatlakoztak, ha a program egy 5. lépés az incidensekre adott reakciók folyamat kezdődött el. Az 5. lépés folyamat célja normál szolgáltatás biztonsága és műveletei minél gyorsabban visszaállítása után a rendszer problémát észlelt, és a vizsgálat elindult.<br><br>További információ: [Microsoft Azure biztonsági válasz a felhőben](https://aka.ms/securityresponsepaper).|
|Geokerítések|Azure Cosmos DB biztosítja az adatok irányítási és a megfelelőségi szuverén régiókhoz (például Németország, Kína Velünk – (kormányzati)).|
|Védett létesítményekben|Az Azure Cosmos Adatbázisba tárolja az Azure által védett adatközpontokban SSD-k.<br><br>További információ: [Microsoft globális adatközpont](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS titkosítás|Minden ügyfél – szolgáltatás Azure Cosmos DB kapcsolati SSL/TLS 1.2 kényszerített. Minden helyen belüli datacenter és határokon datacenter replikációs is SSL/TLS 1.2 kényszerített.|
|Titkosítás inaktív állapotban|Az Azure Cosmos DB tárolt összes adat titkosítása. További információ: [Azure Cosmos DB titkosítását](.\database-encryption-at-rest.md)|
|Javított kiszolgálók|Egy felügyelt adatbázisként Azure Cosmos DB szükségtelenné teszi kezelése és a kiszolgálók, javítás, automatikusan elvégzett.|
|Az erős jelszóval rendelkező rendszergazdai fiókok|Már rögzített feltételezi, még akkor is kell említse meg ezt a követelményt, de eltérően néhány a versenytársak lehetetlen jelszó nélküli rendszergazdai fiókokkal rendelkeznek az Azure Cosmos-Adatbázisba.<br><br> Az SSL és HMAC titkos alapú hitelesítéssel biztonsági alapértelmezés szerint a sütik.|
|Biztonság és a data protection minősítései közül|Az Azure Cosmos DB rendelkezik [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [európai modell záradékok (EUKB)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses), és [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) minősítései közül. További minősítései közül jelenleg folyamatban vannak.|

Az alábbi képernyőfelvételen látható Active directory-integráció (RBAC) használata a hozzáférés-vezérlés (IAM) az Azure-portálon: ![az Azure portál – az adatbázis, amely tartalmazza a hozzáférés-vezérlés (IAM)](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Az alábbi képernyőfelvételen látható használatát naplózás és a tevékenység naplók fiókja figyelésére: ![tevékenység naplózza az Azure Cosmos DB rendszerhez](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Következő lépések

Főkulcsok és erőforrás-jogkivonatokat kapcsolatos további részletekért lásd: [Azure Cosmos DB adatokhoz való hozzáférés biztosítása](secure-access-to-data.md).

Naplózásra kapcsolatos további tudnivalókért lásd: [Azure Cosmos DB diagnosztikai naplózás](logging.md).

Microsoft minősítései közül kapcsolatos további tudnivalókért lásd: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).
