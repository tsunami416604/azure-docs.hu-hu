---
title: "Mi az az Azure rugalmas készlet? | Microsoft Docs"
description: "A készletek segítségével több száz vagy több ezer adatbázist kezelhet egyszerre. Azonos árért több teljesítményegységet kap, amelyek szétoszthatók a készleten belül. Az adatbázisok szabadon hozzáadhatók vagy eltávolíthatók."
keywords: "rugalmas adatbázis,sql-adatbázis"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.date: 12/06/2016
ms.author: CarlRabeler
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 829229542c05477d427b15a9d862f414d9c730d6


---
# <a name="what-is-an-azure-elastic-pool"></a>Mi az az Azure rugalmas készlet?
Az SQL DB rugalmas készletek egyszerű, költséghatékony megoldást kínálnak az olyan adatbázisok teljesítménybeli céljainak kezelésére, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul változik.

> [!NOTE]
> A rugalmas készletek minden Azure-régióban általánosan elérhetők, kivéve Nyugat-Indiát, ahol a szolgáltatás jelenleg előzetes verzióként érhető el.  A rugalmas készletek a lehető leghamarabb általánosan elérhetők lesznek ebben a régióban.
>
>

## <a name="how-it-works"></a>Működés
Gyakori SaaS-alkalmazási minta az egybérlős adatbázismodell: minden ügyfél saját adatbázist kap. Minden ügyfél (adatbázis) kiszámíthatatlan erőforrásigényekkel rendelkezik a memóriát, adatátvitelt és processzorteljesítményt illetően. Hogyan lehet hatékonyan és költséghatékonyan erőforrásokat lefoglalni az igényekben mutatkozó csúcsokhoz és völgyekhez igazodva? Ennek hagyományosan két módja volt: (1) a szükségesnél több erőforrás biztosítása a kihasználtsági csúcs alapján, valamint túlfizetés; vagy (2) a szükségesnél kevesebb erőforrás biztosítása a költségek megtakarítása céljából, a csúcsidőszakokban nyújtott teljesítmény és az ügyfélelégedettség rovására. A rugalmas készletek úgy oldják meg ezt a problémát, hogy akkor biztosítják az adatbázisok számára a szükséges nagy teljesítményű erőforrásokat, amikor azokra szükség van. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>
>

Az SQL Database-ban az adatbázisok erőforrásigény-kezelési képességének relatív mértékét az önálló adatbázisok esetében adatbázis-tranzakciós egységekkel (DTU), a rugalmas készletben található rugalmas adatbázisok esetében pedig rugalmas DTU-kkal (eDTU) fejezzük ki. További információk a DTU-król és eDTU-król: [Az SQL Database bemutatása](sql-database-technical-overview.md).

Egy készlethez egy adott számú eDTU tartozik, amelyek egy adott áron vehetők igénybe. A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. Nagy terhelés alatt az adatbázisok több eDTU-t használhatnak fel, hogy megfeleljenek az igényeknek. A kisebb terhelésű adatbázisok kevesebbet, a terhelés alatt nem álló adatbázisok pedig egyáltalán nem használnak fel eDTU-kat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Emellett a készlet költségei is kiszámíthatóak lesznek.

A létező készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a rugalmas készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a létező készletből.

Ezenfelül a készlethez adatbázisok adhatók hozzá vagy vonhatók ki belőle. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

## <a name="which-databases-go-in-a-pool"></a>Mely adatbázisok kerülnek egy készletbe?
![SQL-adatbázisok, amelyek eDTU-kon osztoznak egy rugalmas adatbáziskészletben.][1]

A rugalmas készletekhez azokat az adatbázisokat a leginkább érdemes hozzáadni, amelyeknek vannak tipikusan aktív és inaktív időszakaik. A fenti példában egy önálló adatbázis, 4 adatbázis, végül pedig egy 20 adatbázist tartalmazó rugalmas készlet aktivitása látható. Az ingadozó aktivitású adatbázisok kitűnő jelöltek a rugalmas készletekhez, mivel nem mind egyszerre aktívak, és képesek osztozni az eDTU-kon. Ebbe a mintázatba nem minden adatbázis illik bele. Az egyenletesebb erőforrás-igényű adatbázisok számára jobban megfelelőek az Alapszintű, Standard és Prémium szolgáltatási csomagok, amelyekben az erőforrások hozzárendelése különállóan történik.

[Rugalmas készletek ára és teljesítménye](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools-and-elastic-databases"></a>Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai

Az alábbi táblázat ismerteti az Alapszintű, Standard és Prémium rugalmas adatbáziskészletek jellemzőit.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához.  Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

## <a name="elastic-pool-and-elastic-database-properties"></a>Rugalmas készletek és rugalmas adatbázisok tulajdonságai

A következő táblázatok leírják a rugalmas készletekre és rugalmas adatbázisokra vonatkozó korlátokat.

### <a name="limits-for-elastic-pools"></a>Rugalmas készletek korlátai
| Tulajdonság | Leírás |
|:--- |:--- |
| Szolgáltatásszint |Alapszintű, Standard vagy Prémium. A szolgáltatásszint határozza meg a konfigurálható teljesítmény- és tárhelykorlát-tartományokat, valamint az üzletmenet-folytonossági lehetőségeket. Egy adott készleten belül minden adatbázis ugyanahhoz a szolgáltatásszinthez tartozik, mint a készlet. A szolgáltatásszinteket más néven kiadásoknak is hívjuk. |
| eDTU-k száma készletenként |A készletben található adatbázisok között megosztható eDTU-k maximális száma. A készletben található adatbázisok által egy időpontban használt eDTU-k teljes száma nem haladhatja meg ezt a korlátot. |
| Maximális tárterület készletenként (GB) |A készletben található adatbázisok között megosztható tárterület maximális mennyisége (GB-ban). A készletben található adatbázisok által használt tárterület teljes mennyisége nem haladhatja meg ezt a korlátot. Ezt a korlátot az eDTU-k készletenkénti száma határozza meg. Ha a felhasznált tárterület-mennyiség túllépi ez a korlátot, minden adatbázis csak olvashatóvá válik. |
| Adatbázisok maximális száma készletenként |A készletenként engedélyezett adatbázisok maximális száma. |
| Egyidejű feldolgozók maximális száma készletenként |Az egy készletben található adatbázisok számára egyidejűleg összesen elérhető feldolgozók (kérések) maximális száma. |
| Egyidejű bejelentkezések maximális száma készletenként |Az egy készletben található adatbázisok számára egyidejűleg összesen elérhető bejelentkezések maximális száma. |
| Egyidejű munkamenetek maximális száma készletenként |Az egy készletben található adatbázisok számára összesen elérhető munkamenetek maximális száma. |

### <a name="limits-for-elastic-databases"></a>Rugalmas adatbázisok korlátai
| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ).  Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára.  Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot.  Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma.  Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál.  Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Maximális tárterület adatbázisonként (GB) |A készletben található adatbázisok maximális tárterülete. A rugalmas adatbázisok osztoznak a készlet tárterületein, így az adatbázisok számára a készletek fennmaradó tárolókapacitása vagy az adatbázisonkénti engedélyezett tárterület közül a kisebbiknek megfelelő tárterület jut. |

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok
A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](sql-database-elastic-jobs-overview.md)** futtathatók. A rugalmas adatbázis-feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás. Az első lépésekért lásd: [Ismerkedés a rugalmas adatbázis-feladatokkal](sql-database-elastic-jobs-getting-started.md).

További információk a rugalmas adatbázisok további eszközeiről:[Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>A készletben található adatbázisok üzletmenet-folytonossági funkciói
A rugalmas adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](sql-database-business-continuity.md) támogatják, amelyek a V12-kiszolgálókon futó önálló adatbázisokhoz is elérhetők.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
Az időponthoz kötött visszaállítás az automatikus adatbázis-biztonságimentések használatával állít vissza egy készlethez tartozó adatbázist egy megadott időpontra. Lásd: [Időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Georedundáns helyreállítás
A georedundáns helyreállítás biztosítja az alapértelmezett helyreállítási lehetőséget arra az esetre, ha egy adatbázis elérhetetlenné válik valamilyen, az adatbázist futtató kiszolgáló régiójában bekövetkező esemény miatt. Lásd: [Az Azure SQL Database visszaállítása vagy feladatátvétel a másodlagos kiszolgálóra](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Aktív georeplikáció
A georedundáns helyreállítás által kínált lehetőségeknél agresszívabb helyreállítási követelményekkel bíró alkalmazásokhoz konfigurálja az aktív georeplikációt az [Azure Portal](sql-database-geo-replication-portal.md), a [PowerShell](sql-database-geo-replication-powershell.md) vagy a [Transact-SQL](sql-database-geo-replication-transact-sql.md) használatával.

## <a name="additional-resources"></a>További források
* [Microsoft Virtual Academy videotanfolyam a rugalmas adatbázisok képességeiről](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Dec16_HO1-->


