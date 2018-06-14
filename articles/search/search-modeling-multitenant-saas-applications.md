---
title: Több vállalat kiszolgálása az Azure Search modellezési |} Microsoft Docs
description: További információk a közös tervminták több-bérlős SaaS-alkalmazásokhoz az Azure Search használata során.
manager: jlembicz
author: ashmaka
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 765f9c4600f762efdd7d57681529751e99c13894
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797175"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>A kialakítási minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Search
Egy több-bérlős alkalmazás egyike, amellyel a bérlők számára nem látható, vagy semmilyen más bérlővel adatok megosztására tetszőleges számú szolgáltatások és képességei azonosak. Ez a dokumentum ismerteti a bérlő elkülönítési stratégiák több-bérlős alkalmazásokhoz az Azure Search beépített.

## <a name="azure-search-concepts"></a>Az Azure Search fogalmak
Keresési,--szolgáltatás megoldásként az Azure Search lehetővé teszi a fejlesztők számára a hatékony keresési lép adhat hozzá az alkalmazásokhoz bármilyen infrastruktúra kezelése és az adatok beolvasása szakértő váljon nélkül. Adatok feltöltődtek a szolgáltatásra, és a felhőben tárolt majd. Az Azure Search API egyszerű kérelmek használatával, az adatok majd módosíthatók és keres. A szolgáltatás áttekintése itt található: [Ez a cikk](http://aka.ms/whatisazsearch). Ismertetése kialakítási minta, előtt fontos tudni, hogy néhány olyan fogalmat, az Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Keresési szolgáltatások, az indexek, a mezők és a dokumentumok
Azure Search használata esetén egy előfizet egy *keresési szolgáltatás*. Mivel adatokat tölt fel az Azure Search, tárolása pedig egy *index* belül a keresési szolgáltatást. Egy önálló szolgáltatásként az indexek száma lehet. Az ismerős fogalmakat adatbázisok használatához a közben a szolgáltatás az indexek is likened, adatbázisban lévő táblák adatbázishoz a keresési szolgáltatás is likened.

A keresési szolgáltatás belüli egyes index saját séma, testreszabható számos által definiált van *mezők*. Adatok kerülnek az Azure Search-index egyéni formájában *dokumentumok*. Minden egyes dokumentum egy adott index fel kell tölteni, és bele kell férnie a sémát, hogy indexeli. Azure Search használatával adatokat keres, ha a teljes szöveges keresési lekérdezések alapján egy adott index adják ki.  Hasonlítsa össze ezeket a fogalmakat, az adatbázis, mezőket is likened, a tábla oszlopainak, és dokumentumokat is likened, sorokra.

### <a name="scalability"></a>Méretezhetőség
Bármely Azure Search szolgáltatást, a standard [tarifacsomag](https://azure.microsoft.com/pricing/details/search/) méretezheti a két dimenziókban: tárolási és rendelkezésre állás.

* *Partíciók* felveheti a search szolgáltatás a tároló növelése érdekében.
* *Replikák* adhatók hozzá egy szolgáltatás, amelyet egy keresési szolgáltatás kezelni tud a kapacitásának növelése érdekében.

Hozzáadása és eltávolítása a partíció, és a replikákról lehetővé teszi a kapacitás, a keresési szolgáltatást együtt adatok mennyiségét, és a forgalom az alkalmazás iránti igények kielégítése érdekében. Ahhoz, hogy a keresési szolgáltatás eléréséhez, az olvasási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), két replika van szükség. Ahhoz, hogy a szolgáltatás eléréséhez, az olvasási és írási [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), három replikák van szükség.

### <a name="service-and-index-limits-in-azure-search"></a>Az Azure Search szolgáltatás és az index korlátok
Nincsenek különböző néhány [tarifacsomagok](https://azure.microsoft.com/pricing/details/search/) az Azure Search, hogy a rétegek mindegyiknek különböző [korlátozásai és a kvóták](search-limits-quotas-capacity.md). Ezek a korlátozások némelyike a szolgáltatás szintjén, a index szintjén vannak, és a partíció szintjén vannak.

|  | Alapszintű | Standard1 | Standard (2) | Standard (3) | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximális replikák-szolgáltatás |3 |12 |12 |12 |12 |
| Maximális partícióból szolgáltatás |1 |12 |12 |12 |3 |
| Maximális keresési egységek (replikák * partíciók) szolgáltatás |3 |36 |36 |36 |36 (legfeljebb 3 partíció) |
| Maximális dokumentumok-szolgáltatás |1 millió |180 millió |720 millió |1.4 milliárd |600 millió |
| Maximális tárolási szolgáltatás |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximális dokumentumok partíciónként |1 millió |15 millió |60 millió |120 millió |200 millió |
| Maximális tárolási partíciónként |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximális indexek-szolgáltatás |5 |50 |200 |200 |3000 (legfeljebb 1000 indexek/partíció) |

#### <a name="s3-high-density"></a>S3 Nagy sűrűségű "
Az Azure Search S3 tarifacsomag van egy beállítást a kifejezetten a több-bérlős forgatókönyvek készült nagy sűrűségű (HD) mód. Sok esetben az egyetlen szolgáltatásban történő elérése az egyszerűség és a költséghatékonyság hatékonyságát előnyei kisebb bérlők nagy számú támogatásához szükséges.

S3 HD lehetővé teszi a sok kisméretű indexeit, hogy amelyet indexek lehetővé teszi a partíciók használatával további indexek egyetlen szolgáltatás futtatására, a szolgáltatás kereskedelmi egyetlen keresési szolgáltatás felügyelet alá kell csomagolni.

S3 szolgáltatásnak konkrétan, 1 és 200 indexek üzemeltető együtt sikerült legfeljebb 1.4 milliárd dokumentumok között lehet. Egy S3 HD egyrészről lehetővé teszi az egyedi indexek csak go legfeljebb 1 millió dokumentumot, de legfeljebb 1000 indexek 200 millió partíciónként azoknak a teljes dokumentum számával (legfeljebb 3000 szolgáltatásonként) partíciónként is képes kezelni (legfeljebb 600 millió-szolgáltatás esetében).

## <a name="considerations-for-multitenant-applications"></a>Több-bérlős alkalmazásokhoz szempontjai
Több-bérlős alkalmazásokhoz hatékonyan kell juttatnia erőforrások szétosztását a bérlők között a különböző bérlők adatvédelmi bizonyos fokú megőrzése mellett. Ilyen alkalmazás architektúrája tervezésekor, van néhány szempontok:

* *Bérlők elkülönítésének:* alkalmazásfejlesztők kell annak érdekében, hogy nem a bérlők számára nem engedélyezett vagy nemkívánatos férhetnek hozzá az adatokhoz, a többi bérlő megfelelő intézkedéseket. Az adatvédelem szempontjából, túl bérlői elkülönítési stratégiák a megosztott erőforrások és a védelmet a zajos szomszédok hatékony kezelése szükséges.
* *A felhő erőforrás költség:* módon és bármely alkalmazás, szoftveres megoldások költség versenyképes egy több-bérlős alkalmazás összetevőjeként kell maradnia.
* *Műveletek a könnyű:* a több-bérlős architektúrák fejleszt, amikor az alkalmazás műveleteket és összetettsége szempontjából fontos szempont. Az Azure Search rendelkezik egy [99,9 %-os SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globális erőforrásigényét:* több-Bérlős alkalmazások esetleg hatékonyan a bérlők számára, amely a világ különböző pontjain kiszolgálásához.
* *Méretezhetőség:* alkalmazásfejlesztők figyelembe kell vennie, hogyan azok egyeztetése közötti alkalmazás összetettségi elég alacsony szintű karbantartásáért, valamint a bérlők számának és a bérlők adatok méretét a skálázandó alkalmazás tervezése és munkaterhelés.

Az Azure Search kínál néhány határok bérlők adatok és a terhelést elkülönítéséhez használható.

## <a name="modeling-multitenancy-with-azure-search"></a>Több vállalat kiszolgálása az Azure Search modellezési
Egy több-bérlős forgatókönyv esetén az alkalmazás fejlesztőjének egy vagy több keresési szolgáltatást használ, és osztani a bérlők között a szolgáltatások, indexeket, vagy mindkettőt. Az Azure Search rendelkezik néhány gyakori minták a modellezési egy több-bérlős forgatókönyv:

1. *Bérlőnként index:* mindegyik bérlő rendelkezik egy keresési szolgáltatáson belül, amely más bérlők meg van osztva a saját index.
2. *Bérlőnként szolgáltatás:* mindegyik bérlő rendelkezik saját dedikált Azure Search szolgáltatással, adatokat és a munkaterhelések elkülönítése legmagasabb szintű kínál.
3. *Mindkét vegyesen:* nagyobb, a több aktív bérlők rendelt dedikált szolgáltatások, amíg kisebb bérlők megosztott szolgáltatások az egyedi indexek vannak rendelve.

## <a name="1-index-per-tenant"></a>1. Bérlőnként index
![Az index egyes bérlői modell egy portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Az index egyes bérlői modell több bérlő helyezkednek el egyetlen Azure Search szolgáltatás ahol mindegyik bérlő rendelkezik saját index.

Bérlők adatok elkülönítési érhető el, mert minden keresési kérelmeket, és az Azure Search index szinten kiállított dokumentum műveletek. Az alkalmazási rétegre van a szükséges tájékoztatási át tudja irányítani a forgalmat a különböző bérlők a megfelelő indexek miközben is erőforrásokat érhet el a szolgáltatási szint összes bérlők között.

Az index egyes bérlői modell kulcsattribútum azt a képességet az alkalmazás fejlesztő oversubscribe a kapacitás, a search szolgáltatás, az alkalmazás bérlők között. Ha a bérlők munkaterhelési eloszlása egyenletlen rendelkezik, a bérlők optimális kombinációja be legyen-e elosztva a keresési szolgáltatás indexek nagyon aktív, az erőforrás-igényes bérlők számos olyan közben egyszerre egy hosszú utóhívás kisebb aktív bérlők. A kompromisszum a modell nem kezeli az olyan helyzetekben, ahol mindegyik bérlő egyidejű nagyon aktív.

Az index-/-bérlős modell alapjául szolgáló változó költség modell, ahol az egész Azure Search szolgáltatást vásárolt kezdeti és majd ezt követően feltölti a bérlők számára. Ez lehetővé teszi a nem használt kapacitás kísérletek és ingyenes fiókot kell kijelölni.

Globális tárhely-alkalmazások esetében az index-/-bérlős modell nem lehet a leghatékonyabb. Ha egy alkalmazás bérlők a világ különböző pontjain, akkor egy külön szolgáltatási mindegyik régióhoz, amely előfordulhat, hogy duplikálja költségek azok szükség lehet.

Az Azure Search lehetővé teszi, hogy a skála az egyedi indexek és indexek száma nő. Ha egy megfelelő árképzési szint van kiválasztva, partíciókat és a replikák is hozzáadhatók a teljes keresési szolgáltatást, ha a szolgáltatáson belül egyedi index növekedésének storage vagy a forgalom túl nagy.

Indexek száma túl nagy az egyetlen szolgáltatás növekszik, ha egy másik szolgáltatást ki kell létrehozni, hogy megfeleljen az új bérlők számára. Ha indexek, az új szolgáltatással bővül keresési szolgáltatások közötti áthelyezésének, az adatokat az indexből kézzel átmásolni egy index a másikra, az Azure Search nem teszi lehetővé egy olyan indexnél, az áthelyezendő rendelkezik.

## <a name="2-service-per-tenant"></a>2. Bérlőnként szolgáltatás
![A szolgáltatás egy bérlő modell egy portrayal](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

A szolgáltatás-/-bérlős architektúrák mindegyik bérlő saját keresési szolgáltatás rendelkezik.

Ebben a modellben az alkalmazás eléri-e a legnagyobb szintű a bérlők elszigetelését. Minden szolgáltatás van dedikált, tárolási és átviteli sebesség kezelésére keresési kérelmet, valamint a külön API-kulcsokat.

Ahol mindegyik bérlő rendelkezik egy nagy méretű tárhely, vagy a munkaterhelés kevés variancia bérlő bérlői alkalmazások a szolgáltatás egy bérlő modell tényleges választási esetén, mert erőforrások megosztása nem különböző bérlők munkaterhelések között.

A szolgáltatás egyes bérlői modellek egy előre jelezhető, rögzített költség modell előnyeit is biztosít. Nincs teljes keresési szolgáltatásnak a kezdeti beruházást van addig, amíg nincs egy bérlőt, töltse ki, azonban a költség-/-bérlő értéke magasabb, mint az index egyes bérlői modell.

A szolgáltatás egy bérlő modell esetén egy globális tárhely alkalmazások hatékony választani. Földrajzilag elosztott bérlők egyszerűen mindegyik bérlő szolgáltatás a megfelelő régióban.

Ebben a mintában skálázás kihívás merülhetnek fel, ha az egyes bérlők elszaporodó a szolgáltatás. Az Azure Search jelenleg nem támogatja a tarifacsomagot a keresési szolgáltatás frissítésével összes adatot kell kézzel átmásolni egy új szolgáltatás.

## <a name="3-mixing-both-models"></a>3. Mindkét modellt keverése
Több vállalat kiszolgálása modellezési egy másik mintát index / bérlői és a szolgáltatás egy bérlő stratégiák van keverése.

A két mintát úgy, egy alkalmazás legnagyobb bérlők lefoglalhatja dedikált szolgáltatások közben kevésbé aktív, kisebb bérlők hosszú végének indexek lefoglalhatja a megosztott szolgáltatás. Ez a modell biztosítja, hogy a legnagyobb bérlők következetesen nagy teljesítményű szolgáltatásból elősegítik a kisebb bérlők bármely zajos szomszédok elleni védelméhez.

Azonban ezt a stratégiát végrehajtási támaszkodik előrelátás előrejelzésére, mely a bérlők egy dedikált szolgáltatás vagy egy megosztott szolgáltatás indexe van szükség. Alkalmazás összetettségét növeli a kell a több-bérlős modell kezeléséhez.

## <a name="achieving-even-finer-granularity"></a>Még nagyobb részletességgel elérése
A fenti kialakítási minta a következő modellre: több-bérlős forgatókönyvek az Azure Search azt feltételezik, hogy az alkalmazás teljes példánya esetén az egyes bérlők egységes hatókör. Alkalmazások azonban néha kezelik a sok kisebb hatókörét.

Ha a szolgáltatás-/-bérlők és az index / bérlői modellek nem elég hatókörök, egy granularitási még nagyobb fokú eléréséhez index modellezésére.

Ahhoz, hogy egy egyetlen index a különböző ügyfél-végpontok eltérően viselkednek, egy mezőt az indexbe, amely a megadott érték közötti minden lehetséges ügyfél is hozzáadhatók. Minden alkalommal, amikor az ügyfél meghívja az Azure Search lekérdezni, vagy módosíthatja az indexet, a kódot az ügyfélalkalmazás határozza meg a megfelelő értéket a Azure keresési mező [szűrő](https://msdn.microsoft.com/library/azure/dn798921.aspx) időben funkció.

Ez a módszer külön felhasználói fiókok, külön jogosultsági szintek funkció eléréséhez használható, és akár teljesen különálló alkalmazások.

> [!NOTE]
> A fent leírt megközelítéssel több bérlő kiszolgálására egyetlen index konfigurálása befolyásolja az keresési eredmények. Keresési relevanciájának pontszámok arra az esetre vonatkoznak egy index szintű hatókörben, nem bérlői szintű hatókör, így egyetlen bérlő számára adatokat a relevanciájának pontszámok alapul szolgáló statisztikákról, például a kifejezés gyakoriság van beépítve.
> 
> 

## <a name="next-steps"></a>További lépések
Az Azure Search esetén számos alkalmazás kényszerítő választani [a szolgáltatás robusztus képességeivel kapcsolatos további](http://aka.ms/whatisazsearch). A különböző tervminták több-bérlős alkalmazások kiértékelését végzi, vegye figyelembe a [különböző árképzési szinteket](https://azure.microsoft.com/pricing/details/search/) és a megfelelő [szolgáltatási korlátait](search-limits-quotas-capacity.md) legjobb személyessé tétele az Azure Search alkalmazás megfelelően munkaterhelések és különböző méretű architektúráinak megfelelően.

Bármely kérdések az Azure Search és a több-bérlős forgatókönyvek irányítható azuresearch_contact@microsoft.com.

