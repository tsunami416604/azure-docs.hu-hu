---
title: Horizontális felskálázás az Azure SQL Database |} A Microsoft Docs
description: A felhőben, ezek az eszközök az Saas-fejlesztők számára nyújtanak a szoftverek rugalmas, skálázható adatbázisok könnyedén hozhat létre
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9f61748a489987bf6c3f38e8ebfdab660198e10a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463021"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)
Könnyedén horizontális felskálázása az Azure SQL-adatbázisok használatával a **Elastic Database** eszközök. Az eszközök és a szolgáltatások lehetővé teszik az adatbázis-erőforrások használata **Azure SQL Database** megoldásokat a tranzakciós munkaterhelések kezelésére, és különösen a szoftverek, mint a szoftverszolgáltatások (SaaS) alkalmazások. Rugalmas adatbázis-szolgáltatások alkotják a:

* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md): Az ügyféloldali kódtár funkciója, amely lehetővé teszi, szilánkokra osztott adatbázisok létrehozásához és kezeléséhez.  Lásd: [Ismerkedés az Elastic Database-eszközök](sql-database-elastic-scale-get-started.md).
* [Elastic Database felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md): szilánkokra osztott adatbázisok között helyez át adatokat. Ez az eszköz számára, amely adatokat helyez át egy több-bérlős adatbázis egy egybérlős adatbázis (vagy fordítva) hasznos. Lásd: [Elastic database felosztási-egyesítési eszközzel oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastic Database-feladatok](sql-database-elastic-jobs-overview.md) (előzetes verzió): Feladatok használata kezelheti az Azure SQL Database-adatbázisok nagy számú. Könnyen elvégezhet olyan adminisztratív műveleteket, például sémamódosításokat, hitelesítő adatok kezelése, referenciaadatok frissítése, teljesítményadat-gyűjtés vagy bérlő (ügyfél) eszköztelemetria-gyűjtést feladatok használatával.
* [Elastic Database-lekérdezés](sql-database-elastic-query-overview.md) (előzetes verzió): Lehetővé teszi a több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatásához. Ez lehetővé teszi a jelentéskészítő eszközökkel, például az Excel, a Power BI, Tableau kapcsolatot.
* [Rugalmas tranzakciók](sql-database-elastic-transactions-overview.md): Ez a funkció lehetővé teszi a tranzakciók, amelyek az Azure SQL Database adatbázisok futtatását. Rugalmas adatbázis-tranzakciók forrásoszlopokat használó .NET-alkalmazások érhetők el, valamint integrálhatja a szolgáltatást a megszokott programozási felület használatával a [System.Transaction osztályok](https://msdn.microsoft.com/library/system.transactions.aspx).

A következő ábrán látható egy olyan architektúra, amely tartalmazza a **rugalmas adatbázis-funkciók** egy adatbázis-gyűjtemény viszonyítva.

Az ábrán az adatbázis színek sémák képviseli. Megegyező színre az adatbázisok közösen használják a ugyanazzal a sémával.

1. Egy **Azure SQL Database-adatbázisok** horizontális skálázási architektúra használatával Azure-ban üzemel.
2. A **Elastic Database-ügyfélkódtár** szegmens kezelésére szolgál.
3. Az adatbázisok egy részét a rendszer elhelyezi egy **rugalmas készlet**. (Lásd: [Mi a készlet?](sql-database-elastic-pool.md)).
4. Egy **rugalmas adatbázis-feladatok** ütemezett és alkalmi T-SQL parancsfájlt futtat minden adatbázisokhoz.
5. A **felosztási-egyesítési eszközének** adatok áthelyezése egyik adatszilánkba író másik szolgál.
6. A **Elastic Database-lekérdezés** írhat egy lekérdezést, amely átnyúlik a szilánkleképezés-készlet összes adatbázisa.
7. **Rugalmas tranzakciók** lehetővé teszik a számos adatbázist átfogó tranzakciókat futtatni. 

![Rugalmasadatbázis-eszközök][1]

## <a name="why-use-the-tools"></a>Miért érdemes használni az eszközök?
Virtuális gépek és a blob storage egyszerű jövedelmezőbb munkát tesznek lehetővé a rugalmasság és a méretezési csoport a felhőalapú alkalmazásokhoz lett – egyszerűen hozzáadása vagy kivonása egységek, vagy növelje power. De a relációs adatbázisok állapot-nyilvántartó adatkezelési kihívást maradt. Kihívások kiderült, a következő használati helyzetekben:

* Növekvő, és zsugorításának a relációs adatbázis része a számítási kapacitást.
* Előfordulhat, hogy ez hatással lenne az adatok – például egy foglalt végfelhasználói (bérlő) egy adott részét felmerülő elérési pontokhoz történő kezelése.

Hagyományosan az alábbiakhoz hasonló forgatókönyveket rendelkezik emlegetett processzorsebezhetőségről infrastruktúrán végezhetjük nagyobb méretű adatbázist az alkalmazása támogatásához. Azonban ez a beállítás korlátozza a felhőben, ahol az összes feldolgozása történik, előre meghatározott hardvereken. Ehelyett adatok terjesztése, és számos azonos módon strukturált adatbázis között (a horizontális méretezési módszer "horizontális skálázási" néven ismert) egy költséget és rugalmasságára épülő osztályé, mind a hagyományos vertikális felskálázás megközelítések alternatívát kínál.

## <a name="horizontal-and-vertical-scaling"></a>Vízszintes és függőleges skálázás
A következő ábrán látható, a vízszintes és függőleges dimenziók méretezését, amely az alapvető módon, a rugalmas adatbázisok skálázhatók.

![Vízszintes és függőleges horizontális felskálázás][2]

Horizontális skálázás hozzáadásával vagy eltávolításával adatbázisok annak érdekében, hogy módosítsa a kapacitás vagy a teljes teljesítményt, más néven a "méretezés" hivatkozik. Horizontális skálázási, amelyben adatok particionálása több azonos módon strukturált adatbázisok gyűjteménye gyakori módja a horizontális skálázás végrehajtására.  

Vertikális skálázás hivatkozik növelésével vagy csökkentésével az egyes adatbázisok számítási mérete, más néven "vertikális felskálázása."

A legtöbb felhőalapú adatbázis-alkalmazások ezen két stratégia szerint kombinációját használják. Például egy szoftver alkalmazás használatával új végfelhasználókat kiépítése horizontális skálázást, és a vertikális skálázás lehetővé teszi minden végfelhasználói adatbázisnak megnöveljék vagy csökkentsék a erőforrások által a számítási feladatok igény szerint.

* Horizontális skálázás kezelhetők a [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md).
* Függőleges méretezés valósítható meg a szolgáltatásszint módosítása Azure PowerShell-parancsmagok használatával vagy a rugalmas készletben található adatbázisok való elhelyezésével.

## <a name="sharding"></a>Sharding
*A horizontális skálázás* technika, azonos módon strukturált adatok nagy mennyiségű szét egy önálló adatbázisok száma. Általában különösen a fejlesztők számára hoz létre szoftverek, a szolgáltatás (SAAS-) ajánlatok kisvállalkozások vagy a végfelhasználók számára. Ezek a végfelhasználókat gyakran "bérlőkkel" nevezik. A horizontális skálázás szükséges bármely számos oka lehet:  

* Adatok teljes mennyisége túl nagy ahhoz, hogy az egyes adatbázisok megkötései belül
* A teljes számítási feladatnak tranzakciós átviteli kapacitást meghaladja az egyes adatbázisok képességeiről
* Bérlők lehet szükség a fizikai elkülönítését egymástól, így külön adatbázis szükséges ahhoz, hogy minden bérlő
* Adatbázis különböző szakaszaiban található különböző földrajzi megfelelőség, a teljesítmény vagy a geopolitikai okokból szükségessé.

Más esetekben az Adatbetöltési elosztott eszközökről származó adatok, például a horizontális skálázás segítségével töltse ki az adatbázis, amely ideiglenesen vannak rendszerezve. Például egy különálló adatbázist dedikálhatja minden nap vagy hét folyamán. Ebben az esetben a horizontális skálázási kulcs (a horizontálisan particionált tábla összes sora szerepel) dátumot jelölő egész szám lehet, és a egy dátumtartomány adatainak lekérése lekérdezéseket át kell irányítani a következő részhalmazt a szóban forgó tartomány lefedő adatbázisok az alkalmazás által.

A horizontális skálázás akkor működik a legjobban, ha az alkalmazások minden egyes tranzakciót korlátozható egy horizontális skálázási kulcs egyetlen értéket. Amely biztosítja, hogy az összes tranzakció helyi egy adott adatbázisban.

## <a name="multi-tenant-and-single-tenant"></a>Több-bérlős és egybérlős
Egyes alkalmazások használata a legegyszerűbb megközelítés, egy önálló adatbázis létrehozása az egyes bérlők számára. Ez a megközelítés a **egybérlős horizontális skálázási minta** elkülönítés, biztonsági mentési és visszaállítási lehetőség és a bérlő részletességgel méretezése resource biztosít. Egyetlen bérlő horizontális skálázási minden adatbázisnak társítva egy adott bérlővel azonosítóérték (vagy a kulcs értéket), de a kulcs nem minden esetben kell magát az adatok tartalmazzák. Az alkalmazás feladata, hogy egyes kérelmek átirányítása a megfelelő adatbázis - és az ügyféloldali kódtár Ez egyszerűbbé teheti.

![Egybérlős és több-bérlős][4]

Más forgatókönyvek csomag több bérlő együtt az adatbázisok ahelyett, hogy az önálló adatbázisok elszigetelése őket. Ez a minta akkor tipikus **több-bérlős horizontális skálázási minta** - és vezethetik azt a tényt, hogy egy alkalmazás kezeli a nagy számú kis méretű bérlővel. A több-bérlős horizontális skálázás a sorok az adatbázistáblák összes tervezték, hogy egy kulcs, amely azonosítja a bérlő Azonosítóját vagy a szegmenskulcs megfelelő megválasztása. Ismét az alkalmazásrétegek felelős a bérlőkéréssel útválasztás a megfelelő adatbázishoz, és ez az elastic database-ügyfélkódtár támogatnak. Emellett a sorszintű biztonság használható-e a szűrő mely sorai minden bérlő hozzáférhet – részletekért lásd: [az elastic database-eszközökkel és a sorszintű biztonság több-bérlős alkalmazások](sql-database-elastic-tools-multi-tenant-row-level-security.md). Verziójának terjesztése adatbázisok között olyan adatokat is szükség lehet a több-bérlős horizontális skálázási minta a, és az elastic database felosztási-egyesítési eszközének megkönnyíthető. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Adatok áthelyezése több single-bérlős adatbázis
Egy SaaS-alkalmazás létrehozása, esetén általában az ajánlat leendő ügyfelek a szoftver próbaverzióját. Ebben az esetben egy több-bérlős adatbázis használata az adatok költséghatékony. Azonban amikor egy potenciális vevő ügyfél, egy egybérlős adatbázis célszerűbb óta jobb teljesítményt biztosít. Ha az ügyfél adatainak hozta létre a próbaidőszak alatt, használja a [felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md) a több-bérlős az adatok áthelyezése az új egybérlős adatbázis.

## <a name="next-steps"></a>További lépések
Egy mintaalkalmazást, amely az ügyféloldali kódtár, lásd: [Ismerkedés az Elastic Database-eszközök](sql-database-elastic-scale-get-started.md).

Eszközök használata a meglévő adatbázisok konvertálása, lásd: [horizontális felskálázási meglévő adatbázisok Migrálása](sql-database-elastic-convert-to-use-elastic-tools.md).

A rugalmas készlet tulajdonságairól, olvassa el [rugalmas készletek ára és teljesítménye szempontok](sql-database-elastic-pool.md), vagy hozzon létre egy új készletet a [rugalmas készletek](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

