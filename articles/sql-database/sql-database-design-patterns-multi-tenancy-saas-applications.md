---
title: "A kialakítási minták a több-bérlős SaaS-alkalmazásokhoz és az Azure SQL Database |} Microsoft Docs"
description: "Ismerje meg a követelményeket és a közös adatok architektúra minták több-bérlős szoftver, egy szolgáltatott szoftverként (SaaS) adatbázis egy felhőalapú környezetben futó alkalmazások."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: eef48cfcbc7d6c241b5ece863df0be6ecad78ca7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>A kialakítási minták a több-bérlős SaaS-alkalmazásokhoz és az Azure SQL adatbázis
Ebből a cikkből megtudhatja a követelményeiről és a közös adatok architektúra minták több-bérlős szoftver, egy szolgáltatott szoftverként (SaaS) adatbázis egy felhőalapú környezetben futó alkalmazások. Azt is bemutatja, figyelembe kell vennie a tényezők és a különböző kialakítási minta a kompromisszumot alakítson ki. Rugalmas készletek és az Azure SQL Database rugalmas eszközök segíthetnek a igényeknek más célok veszélyeztetése nélkül.

A fejlesztők előfordulhat, hogy azok a több-bérlős alkalmazásokhoz adatok rétegek bérleti modellek tervezésekor a hosszú távú érdekében dolgozhat következő választási lehetőségeket. Kezdetben legalább egy fejlesztő előfordulhat, hogy alapvető változtatásokat hozott a könnyű fejlesztés és az alacsonyabb cloud service szolgáltató költségek fontosabb, mint a bérlők elszigetelésére, vagy a méretezhetőség, az alkalmazások. Ez a beállítás később ügyfelek elégedettségének maillel és költséges állomásokon javítási vezethet.

Egy több-bérlős alkalmazás üzemeltetett felhőalapú környezetek alkalmazás, és ugyanazokkal a több száz vagy ezer nem megosztása, vagy tekintse meg a másik fél adatok bérlő szolgáltatásokat biztosít. Példa: egy SaaS-alkalmazás szolgáltatásokat biztosít a bérlők számára a felhőben üzemeltetett környezetekben.

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazásokhoz
A több-bérlős alkalmazásokhoz adatokhoz és a munkaterhelés lehet könnyen particionálni. Akkor is partícióazonosító adatok és a munkaterhelés, például mentén bérlői határokat, mert a legtöbb kérelem fordulhat elő, a bérlő határain belül. Ez a tulajdonság rejlő az adatok és a munkaterhelés, és azt a cikkben szereplő alkalmazás minták előtérbe.

A fejlesztők a felhőalapú alkalmazások, beleértve a teljes skáláját Ez az alkalmazástípus használja:

* Partner adatbázis-alkalmazások, amelyek folyamatban érdekében átalakulnak a felhőben, SaaS-alkalmazásokhoz
* A felhőben az alapoktól létrehozott SaaS-alkalmazásokhoz
* Közvetlen, ügyfélkapcsolati alkalmazások
* Alkalmazott számára is elérhető a vállalati alkalmazások

A felhő- és gyökérrel rendelkező kifejlesztett SaaS-alkalmazásokhoz általában partneri adatbázis-alkalmazások során a rendszer több-bérlős alkalmazásokhoz. A SaaS-alkalmazások számára egy speciális alkalmazás szolgáltatásként biztosításához. Bérlők elérheti az alkalmazásszolgáltatást és társított az alkalmazásokban tárolt adatok teljes tulajdonjogát rendelkezik. De SaaS előnyeinek kihasználásához, a bérlők lemondást kell néhány szabályozhatják, hogy a saját adataikat. A Szolgáltatottszoftver-szolgáltató érdekében biztonságos és elkülönített más bérlők adatokból megbízhatóságát. A több-bérlős SaaS-alkalmazás ilyen többek között az MYOB SnelStart és a Salesforce.com. Mindkét alkalmazás bérlői határokat és az alkalmazás kialakítási minták arról lesz szó ebben a cikkben támogatás mentén lehet particionálni.

Alkalmazások, amelyek közvetlen szolgáltatást nyújtsanak az ügyfeleinek, vagy a dolgozók (más néven bérlők helyett a felhasználók) szervezeten belül egy másik kategóriát a több-bérlős alkalmazás pontszámot. Fizessen elő a service, és nem rendelkezik, amely a szolgáltató összegyűjti és tárolja az adatokat. Szolgáltatók a kormányzati megbízott adatvédelmi előírásokat túl egymástól különítve az ügyfelek adatainak kevésbé szigorú követelményekkel rendelkezik. Az ilyen ügyfélkapcsolati több-bérlős alkalmazás példák media tartalomszolgáltatók például Netflix, a Spotify és az Xbox LIVE. További példák könnyen particionálható alkalmazások ügyfélkapcsolati, Internet-skálázási alkalmazást, vagy az eszközök internetes hálózatát (IoT) alkalmazást, mely minden felhasználói vagy az eszköz lehet a partíció szolgál. Partícióhatárok elkülönítheti a felhasználók és eszközök számára.

Nem minden alkalmazások partíció könnyen mentén egyetlen tulajdonság például bérlői, felhasználói, felhasználó vagy eszköz. Egy összetett vállalati erőforrás-tervező (ERP) alkalmazás, például a termékek, rendeléseket és ügyfelek rendelkezik. Általában tartalmaz egy összetett séma több ezer magas egymáshoz kapcsolódó táblákat.

Nincs egyetlen partícióra stratégia az összes táblához alkalmazását és az alkalmazás munkaterhelés keresztül működnek. Ez a cikk foglalkozik a több-bérlős az alkalmazásokat, amelyek könnyen particionálható adatok és a munkaterhelések.

## <a name="multi-tenant-application-design-trade-offs"></a>Több-bérlős alkalmazás tervezési kompromisszumot
Egy több-bérlős alkalmazásfejlesztő általában úgy dönt, a kialakítási mintában veszi figyelembe az alábbi tényezők alapján:

* **Bérlők elkülönítésének**. A fejlesztőnek győződjön meg arról, hogy nincs bérlői van-e más bérlők adatok hozzáférésének nem kívánt. Az elkülönítési követelmény kiterjeszti egyéb tulajdonságait, például a védelem biztosítása a zajos szomszédok igényt a bérlő adatok helyreállítását és bérlői-specifikus testreszabásokat.
* **A felhő erőforrásköltségeket**. Egy SaaS-alkalmazáshoz kell költség-versenyképesség fenntartása érdekében. Egy több-bérlős alkalmazásfejlesztő felhőalapú erőforrásokat, például a tárolási felhasználásának alacsonyabb költségek optimalizálás, és a számítást költségek célszerű használni.
* **DevOps könnyű**. Egy több-bérlős alkalmazásfejlesztőnek elkülönítési védelmi tartalmaznia, karbantartása, és az alkalmazás és az adatbázis-séma állapotának figyelésére és a bérlői problémák elhárításához. Fokú összetettségével jár-alkalmazások fejlesztését és a művelet több közvetlenül nagyobb költség, és felkéri a bérlő számára.
* **Méretezhetőség**. Fokozatosan hozzáadható több bérlő és a kapacitás a bérlők számára, akiknek nincs szükségük egy sikeres Szolgáltatottszoftver-művelet feltétlenül szükséges.

Ezek a tényezők mindegyikén kompromisszumot alakítson ki egy másik képest. A legkisebb költségű felhőalapú ajánlat esetleg nem nyújtanak a legalkalmasabb fejlesztési felület. Fontos a fejlesztő ismerkednie ezeket a beállításokat és azok kompromisszumot alakítson ki az alkalmazás tervezési folyamat során.

A legnépszerűbb fejlesztési minta hoz több bérlő csomag egy vagy néhány adatbázisba. Ez a módszer előnyei nincsenek alacsonyabb költségű, mert néhány adatbázisok és az adatbázisok száma korlátozott relatív egyszerűség kell fizetnie. De idővel SaaS több-bérlős alkalmazásfejlesztő fog fontos megjegyezni, hogy ez a beállítás a bérlők elkülönítését és a méretezhetőség jelentős downsides. Ha a bérlők elszigetelésére válik a további elérhető megosztott tároló bérlői adatok védelme érdekében az illetéktelen hozzáféréstől és zajos szomszédok szükséges. Ebből a törekvésből jelentősen növeli a előfordulhat, hogy a fejlesztéshez és elkülönítési karbantartási költségek. Ehhez hasonlóan hozzáadása a bérlők szükség, ha ebben a kialakításban általában szükséges szakértelem újraterjesztése bérlői adatok az adatréteg az alkalmazások megfelelő méretezése az adatbázisok közötti.  

Gondoskodjon arról, hogy üzleteknek és szervezeteknek SaaS-több-bérlős alkalmazásokhoz a alapvető gyakran a bérlők elszigetelésére feltétele. A fejlesztők által észlelt előnyöket egyszerűség és a költségeket a bérlők elkülönítését és a méretezhetőség tempted előfordulhat, hogy lehet. A kompromisszum bizonyítja összetett és költséges, ha a szolgáltatás növekszik és a bérlői elkülönítési követelményei válik több fontos és az alkalmazás rétegben felügyelt. A több-bérlős alkalmazásokhoz, amelyek közvetlen, a felhasználók felé néző szolgáltatást biztosítani az ügyfél számára, azonban bérlők elszigetelésére lehet optimalizálja a felhő erőforrás költsége alacsonyabb prioritású.

## <a name="multi-tenant-data-models"></a>Több-bérlős adatmodelljeiről
Bérlői adatok elhelyezéséhez közös tervezési eljárásokat hajtsa végre a három különböző modellt, 1. ábrán látható.

![több-bérlős alkalmazás-adatmodellek](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

1. ábra: Több-bérlős adatmodellekben eljárások általános tervezési

* **Adatbázis-/-bérlő**. Mindegyik bérlő saját adatbázis van. Összes bérlői vonatkozó adatokat a bérlő adatbázis korlátozódik, és más bérlők és az adatokat nem.
* **A megosztott adatbázis-szilánkos**. Több bérlő több adatbázisok közül az egyik megosztani. Egy meghatározott készletét bérlők hozzá van rendelve az egyes adatbázisok például kivonat, a tartomány vagy a lista particionálás jó particionálási stratégia használatával. Az adatok terjesztési stratégia gyakran nevezik horizontális.
* **Adatbázis-egyetlen megosztott**. Egyetlen néha nagy adatbázisban adatokat tartalmaz, az összes-bérlőkkel, amelyek egy bérlői azonosító oszlop használatát is.

> [!NOTE]
> Az alkalmazásfejlesztő helyezze el a különböző bérlők különböző adatbázis sémákban, majd a sémanév elem egyértelműségének biztosításához a különböző bérlők célszerű használni. Nem javasoljuk ezt a módszert használja, mert általában szüksége van a dinamikus SQL használatát, és hatékonyan terv gyorsítótárazás nem lehet. Ez a cikk tovább azt összpontosítani a megosztott tábla megközelítés a kategória több-bérlős alkalmazás is.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Népszerű több-bérlős adatmodelljeiről
Fontos értékeli a különböző típusú több-bérlős adatmodellekben az alkalmazás tervezési kompromisszumot már felismertük tekintetében. Ezek a tényezők segítségével jól jellemezhető korábban leírt három leggyakoribb több-bérlős adatok modellek és az adatbázis-használat a 2. ábrán látható módon.

* **Elkülönítés**. A bérlők elkülönítését mértékét lehet egy mérték mennyi bérlői elkülönítési adatmodellt éri el.
* **A felhő erőforrásköltségeket**. A bérlők közötti erőforrás-megosztás mennyisége felhő erőforrás költség is optimalizálhatja. A számítási és tárolási költség erőforrás adható meg.
* **DevOps költség**. A könnyű alkalmazásfejlesztést, a központi telepítés és a kezelhetőségét csökkenti az általános SaaS működési költséget.  

A 2. ábrán az Y tengelyen jeleníti meg a bérlői elkülönítési szintjét. Az X tengely erőforrások megosztása szintjét mutatja. A szürke átlós a középső irányát nyíl DevOps költségek, általános szabályok növeléséhez vagy csökkentéséhez.

![Több-bérlős népszerű alkalmazás-tervezési minták](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

2. ábra: Több-bérlős népszerű adatok modellek

A 2. ábrán a jobb alsó részre osztott viselkedésmintát egy alkalmazás által használt egy potenciálisan nagy, megosztott egy adatbázist, és a megosztott tábla (vagy különálló séma) módszer. Az erőforrás-megosztás, mert egyetlen bérlő számára az ugyanazon adatbázis-erőforrások (CPU, a memória, a bemeneti/kimeneti.) használata egy önálló adatbázis jó. De a bérlők elszigetelésére korlátozva. Szükség lehet további lépésekkel védelme bérlők egymástól, az alkalmazás rétegben. Ezeket a további lépéseket jelentősen csökkenti a fejlesztésének és kezelésének az alkalmazás DevOps költségét. Méretezhetőség a hardvert, amelyen az adatbázis mérete korlátozza.

A bal alsó részre osztott a 2. ábra szemlélteti szilánkos több bérlő több adatbázis közötti (általában a más hardverekre skálázási egységek). Az egyes adatbázisok bérlők, egy részét, amely megoldja a méretezhetőség kapcsolatos azon aggodalmát, annak a más üzemelteti. Ha további kapacitás szükséges további bérlők, könnyen elhelyezheti a bérlők új hardver méretezési egységek rendelt új adatbázisokat. Azonban az erőforrás-megosztás mennyisége csökken. Az azonos méretezési egységeket erőforrások megosztása helyezett csak a bérlők számára. Ezt a módszert biztosít a bérlők elkülönítésének, mert több bérlő továbbra is automatikusan védett egymás műveletekből nélkül közös elhelyezésű kevés fokozása. Alkalmazás összetettsége továbbra is magas.

A bal felső részre osztott a 2. ábrán a harmadik módszer. Mindegyik bérlő adatok helyezi a saját adatbázisában. Ez a megközelítés jó bérlői-elkülönítési tulajdonságokkal rendelkezik, de korlátozza az erőforrás-megosztás, ha az egyes adatbázisok saját dedikált erőforrások. Ez a módszer akkor hasznos, ha minden bérlők előre jelezhető munkaterhelésekkel rendelkeznek. Ha a bérlői terhelések kevesebb előre jelezhető, a szolgáltató erőforrások megosztása nem lehet optimalizálni. Által esetében gyakori, az SaaS-alkalmazásokhoz. A szolgáltató kell túlzott kiépítés követelményeinek, vagy alacsonyabb erőforrásokat. Mindkét művelet vagy magasabb költségek, vagy alacsonyabb bérlői elégedettségének eredményez. Erőforrás-megosztás bérlők között nagyobb fokú kívánatos, hogy a megoldás költséghatékonyabb válik. DevOps költség telepíteni és fenntartani az alkalmazás is az adatbázisok számának növelésével növeli. Annak ellenére, hogy ezeket a problémákat Ez a módszer el vannak különítve a legjobb és legegyszerűbb a bérlők számára.

Ezek a tényezők is befolyásolják a kialakítási mintában egy ügyfél:

* **Bérlői adatok tulajdonjogát**. Egy alkalmazás bérlők tartsa meg a saját adatok tulajdonjogát előtérbe bérlőnként egy önálló adatbázis mintáját.
* **Méretezés**. Egy alkalmazás, amelynek célja a több ezer vagy a bérlők több millió több száz adatbázismegosztás módszerek, például a horizontális előtérbe. Elkülönítési követelményei továbbra is kihívást is jelent.
* **Érték és az üzleti modell**. Ha egy alkalmazás / bérlői bevétel Ha kis (kevesebb mint dollár), a kevésbé fontos válnak elkülönítési követelményei és a megosztott adatbázis teljesen logikus. Ha a bérlő bevétel néhány dollár vagy több, egy adatbázis-/-bérlős modell több valósítható meg. Akkor lehet, hogy fejlesztési a költségek csökkentése érdekében.

Megadott a Tervező kompromisszumot alakítson ki a 2. ábrán látható, egy ideális több-bérlős modellnek támogatnia kell a megfelelő bérlői elkülönítési tulajdonságok átfogó az optimális erőforrás-megosztás bérlők között. Ez a modell elfér a 2. ábra a jobb felső részre osztott ismertetett kategóriát.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Több-bérlős támogatás az Azure SQL-adatbázis
Az Azure SQL Database támogatja a 2. ábra leírt minden alkalmazás több-bérlős minták. A rugalmas készletek egy alkalmazás mintát, amely egyesíti a helyes erőforrás-megosztás is támogatja, és az adatbázis-/-bérlő elkülönítési előnyei közelítse (lásd a 3. ábra a jobb felső részre osztott). Rugalmas adatbáziseszközöket és az SQL-adatbázis képességeket fejleszthet, és egy alkalmazás, amely rendelkezik (az árnyékolt területre a 3. ábrán látható) számos más adatbázis üzemeltetéséhez költség csökkentése érdekében. Ezek az eszközök segítségével létrehozása és kezelése a többadatbázisos minták bármelyikét használó alkalmazásokat.

![Az Azure SQL-adatbázis minták](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

3. ábra: Több-bérlős alkalmazás mintákat az Azure SQL-adatbázis

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Adatbázis-/-bérlős modell rugalmas készletek és eszközök
Az SQL Database rugalmas készletek bérlők elszigetelésére egyesíthető erőforrás-megosztás között jobb támogatása érdekében bérlői adatbázisokat az adatbázis-/-bérlő módszerrel. SQL-adatbázis egy olyan adatok réteg megoldás a Szolgáltatottszoftver-szolgáltatók számára olyan több-bérlős alkalmazásokat hozhat létre. Erőforrás-megosztás bérlők között okozta terheket az alkalmazási rétegre a lesz az adatbázis szolgáltatásrétegben. Rugalmas feladat, rugalmas lekérdezési, rugalmas tranzakciók és a rugalmas adatbázis ügyféloldali kódtár egyszerűsödött, kezelése és az adatbázisok közötti léptékű lekérdezése összetettsége.

| Alkalmazáskövetelmények | SQL-adatbázis képességek |
| --- | --- |
| Bérlők elkülönítését és erőforrás-megosztás |[Rugalmas készletek](sql-database-elastic-pool.md): az SQL Database-erőforrásokat foglal le, és az erőforrások megosztása a különböző adatbázisok közötti. Az egyes adatbázisok is, a készlet kapacitásának igény szerinti igényeiben jelentkező bérlői munkaterheléseket érintő változások miatt befogadásához igény szerint is tárolt mértékű erőforrások. A rugalmas készlet maga is méretezhető felfelé vagy lefelé igény szerint. Rugalmas készletek is adja meg a könnyű kezelhetőség és figyelése és hibaelhárítása a készlet szinten. |
| Az adatbázisok közötti DevOps könnyű |[Rugalmas készletek](sql-database-elastic-pool.md): mivel korábban feljegyzett. |
| | [Rugalmas lekérdezési](sql-database-elastic-query-horizontal-partitioning.md): az adatbázisok közötti lekérdezés vagy a kereszt-bérlő elemzés. |
| | [Rugalmas feladat](sql-database-elastic-jobs-overview.md): csomag és megbízhatóan telepítése több adatbázis adatbázis-karbantartási műveletek vagy adatbázis-séma módosításait. |
| | [Rugalmas tranzakciók](sql-database-elastic-transactions-overview.md): folyamat több adatbázisok atomi és elkülönített módon változik. Rugalmas tranzakciók van szükség, amikor az alkalmazások több adatbázis-műveletek "mindent vagy semmit" garanciák van szükségük. |
| | [A rugalmas adatbázis ügyféloldali kódtár](sql-database-elastic-database-client-library.md): kezelése adatok azokat a terjesztéseket, és képezze le a bérlők adatbázisokhoz. |

## <a name="shared-models"></a>Megosztott modellek
Ismertetett módon, a legtöbb Szolgáltatottszoftver-szolgáltatók, a megosztott modell megközelítés bérlői elkülönítési kapcsolatos problémákkal rendelkező problémák és összetett szolgáltatásokkal, az alkalmazások fejlesztését és karbantartási nézve jelent. Előfordulhat azonban, több-bérlős alkalmazásokat, amelyek a szolgáltatás fogyasztóknak való, a bérlő elkülönítési követelményei nem egy minimalizálja a költség, a magas prioritású. Előfordulhat, hogy tudják bérlők csomag egy vagy több adatbázisaiban nagy sűrűségű költségek csökkentése érdekében. Egy egyetlen vagy több szilánkos adatbázis használ a megosztott adatbázis modellek az erőforrás-megosztás és a teljes költség további hatékonyság eredményezheti. Azure SQL Database is tartalmaz néhány funkció, amely segít a felhasználóknak a nagyobb biztonság és az adatszint a felügyelet elkülönítési build.

| Alkalmazáskövetelmények | SQL-adatbázis képességek |
| --- | --- |
| Biztonsági elkülönítés funkciói |[Biztonsági szint](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Adatbázis-séma](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Az adatbázisok közötti DevOps könnyű |[Rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) |
| | [Rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) |
| | [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md) |
| | [Rugalmas adatbázis felosztása és egyesítése](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Összefoglalás
Bérlői elkülönítési követelményei fontosak az többsége több-bérlős SaaS-alkalmazásokhoz. A legjobb lehetőség elkülönítést biztosítani leans fokozottan felé az adatbázis-/-bérlő módszerrel. A többi két megközelítés összetett alkalmazás rétegek gyakorlott fejlesztési csapattal elkülönítést, ami jelentősen növeli a költségeket és a kockázati biztosítani igénylő befektetések szükséges. Ha elkülönítési követelményei a számításból korai szakaszában a szolgáltatások fejlesztéséhez, még akkor is költségesebb, az első két modellek alkalmazásával őket lehet. A fő hátrányai az adatbázis-/-bérlő modellhez társított kapcsolódó megnövekedett felhő erőforrás költségek miatt csökkentett megosztása több adatbázis kezelésére és fenntartása. SaaS-alkalmazások fejlesztői gyakran ezek kompromisszumot elkészítésekor kihívást jelent.

Bár kompromisszumot lehet, hogy a legtöbb adatbázis felhőszolgáltatók jelentős korlátokat, az Azure SQL Database megszünteti a korlátok, a rugalmas készlet és a rugalmas adatbázis-képességeket. SaaS-fejlesztők egy adatbázis-/-bérlős modell elkülönítési jellemzői egyesítheti és erőforrás-megosztás és a kezelhetőségi fejlesztések a fő sok adatbázisok optimalizálása rugalmas készletek és a kapcsolódó eszközök segítségével.

Több-bérlős alkalmazás szolgáltatók rendelkező, nem bérlői elkülönítési követelményei, és egy adatbázisban nagy sűrűségű bérlők számára is csomag Előfordulhat, hogy a megosztott adatok modellek eredményt a további hatékonyságának erőforrás-megosztás, és csökkentheti a teljes költség szempontjából. Az Azure SQL Database rugalmas adatbáziseszközöket, horizontális szalagtárak és biztonsági szolgáltatások segítségével a Szolgáltatottszoftver-szolgáltatók létrehozása és kezelése a több-bérlős alkalmazásokhoz.

## <a name="next-steps"></a>Következő lépések
[Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md) azt mutatja be, az ügyféloldali kódtár minta alkalmazáshoz.

Hozzon létre egy [rugalmas készlet egyéni irányítópult szoftverszolgáltatások](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) rugalmas készletek adatbázis költséghatékony, méretezhető megoldást használó minta alkalmazáshoz.

Az Azure SQL Database eszközzel való [telepítse át a meglévő adatbázisok horizontális](sql-database-elastic-convert-to-use-elastic-tools.md).

Az Azure portál használatával rugalmas készletek létrehozásához lásd: [hozzon létre egy rugalmas készlet](sql-database-elastic-pool-manage-portal.md).  

Megtudhatja, hogyan [felügyeletéhez és kezeléséhez a rugalmas készletekben](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>További források

* [Központi telepítése, és vizsgálja meg a több-bérlős alkalmazás az Azure SQL Database - Wingtip SaaS használ](sql-database-saas-tutorial.md)
* [Mi az Azure rugalmas készletek?](sql-database-elastic-pool.md)
* [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)
* [több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonsággal](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [A több-bérlős alkalmazásokban az Azure Active Directory és az OpenID Connect hitelesítési](../guidance/guidance-multitenant-identity-authenticate.md)
* [A Tailspin Surveys-alkalmazás](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Kérdések és funkciókra vonatkozó kérések

A kérdésekhez megkeresés a a [SQL-adatbázis fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Adja hozzá a szolgáltatás kérést a [SQL adatbázis-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

