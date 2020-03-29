---
title: Tarifacsomag vagy termékváltozat kiválasztása
titleSuffix: Azure Cognitive Search
description: 'Az Azure Cognitive Search a következő termékkészletben építhető ki: ingyenes, alapszintű és standard és standard, valamint különböző erőforrás-konfigurációkban és kapacitásszinteken érhető el.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 35dbd064a09a96dae58e1b15a6d8889bda45ee0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899851"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Az Azure Cognitive Search díjszabási szintjének kiválasztása

Amikor létrehoz egy Azure Cognitive Search szolgáltatást, egy [erőforrás jön létre](search-create-service-portal.md) egy tarifaszinten (vagy termékváltozatban), amely a szolgáltatás élettartama alatt van rögzítve. A szintek közé tartozik az ingyenes, az alapszintű, a standard és a tárhelyre optimalizált. A standard és a tárhelyre optimalizált számos konfigurációval és kapacitással érhető el.

A legtöbb ügyfél az ingyenes szinttel kezdi, hogy kiértékelhessék a szolgáltatást. Utólagos kiértékelés, gyakori, hogy hozzon létre egy második szolgáltatást az egyik magasabb szintű fejlesztési és éles környezetben.

## <a name="feature-availability-by-tier"></a>A szolgáltatás elérhetősége réteg szerint

Szinte minden funkció elérhető minden rétegben, beleértve az ingyenes, de egy szolgáltatás vagy munkafolyamat, amely erőforrás-igényes előfordulhat, hogy nem működik jól, ha nem ad meg elegendő kapacitást. Például [a ai-dúsítás](cognitive-search-concept-intro.md) hosszú ideig futó készségek, amelyek időkiegy ingyenes szolgáltatás, kivéve, ha az adatkészlet kicsi.

Az alábbi táblázat a rétegekkel kapcsolatos szolgáltatáskorlátozásokat ismerteti.

| Szolgáltatás | Korlátozások |
|---------|-------------|
| [indexelők](search-indexer-overview.md) | Az indexelők nem érhetők el S3 HD felbontásban. |
| [Ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) | Nem érhető el az ingyenes szinten. |

## <a name="tiers-skus"></a>Rétegek (SKI-k)

A meghatározási szinteket a következők különböztetik meg:

+ Létrehozható indexek és indexelők mennyisége
+ A partíciók mérete és sebessége (fizikai tárolás)

A kiválasztott szint határozza meg a számlázható arányt. Az Alábbi képernyőkép az Azure Portalon az elérhető csomagokat mutatja, mínusz a díjszabás (amely megtalálható a portálon és a [díjszabási oldalon.](https://azure.microsoft.com/pricing/details/search/) **Az ingyenes**, **alapszintű**és **standard** a leggyakoribb szintek.

**A Free** korlátozott keresési szolgáltatást hoz létre egy fürtön, amelyet más előfizetőkkel osztanak meg. Kis projekteket is végrehajthat, beleértve a rövid útmutatókat és az oktatóanyagokat, de nem skálázhatja a szolgáltatást, és nem futtathat jelentős számítási feladatokat. **Alapszintű** és **standard** a leggyakrabban használt számlázható rétegek, **a Standard** az alapértelmezett.

![Az Azure Cognitive Search díjszabási szintjei](media/search-sku-tier/tiers.png "Az Azure Cognitive Search díjszabási szintjei")

Egyes szintek bizonyos típusú munkákhoz vannak optimalizálva. A **Standard 3 Nagy sűrűségű (S3 HD)** például az S3 üzemeltetési *módja,* ahol az alapul szolgáló hardver nagyszámú kisebb indexre van optimalizálva, és többdimenziós forgatókönyvekhez készült. Az S3 HD egységenkénti töltése megegyezik az S3-mal, de a hardver gyors fájlolvasásra van optimalizálva számos kisebb indexen.

**A tárolásra optimalizált** rétegek nagyobb tárolókapacitást kínálnak a Standard szinteknél alacsonyabb TB-áron. Az elsődleges kompromisszumot a magasabb lekérdezési késés, amelyet ellenőriznie kell az adott alkalmazás követelményeinek.  Ha többet szeretne megtudni a réteg teljesítményével kapcsolatos szempontokról, olvassa el a [Teljesítmény- és optimalizálási szempontok című témakört.](search-performance-optimization.md)

A különböző szintekről a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)olvashat bővebben, az Azure Cognitive Search cikk [szolgáltatáskorlátaiban,](search-limits-quotas-capacity.md) és a szolgáltatás kiépítésekor a portál oldalon.

## <a name="billable-events"></a>Számlázható események

Az Azure Cognitive Search szolgáltatásra épülő megoldás a következő módokon járhat költségekkel:

+ A szolgáltatás alapköltsége minimális konfigurációnál (szolgáltatás létrehozása)
+ Növekményes költség felskálázáskor (replikák vagy partíciók hozzáadása)
+ Sávszélesség-díjak (kimenő adatátvitel) 
+ Kognitív keresés (cognitive services csatolása a ai-dúsításhoz, Azure storage a tudástárolóhoz)

### <a name="service-costs"></a>Szolgáltatási költségek

A virtuális gépekkel vagy más erőforrásokkal ellentétben, amelyek "szüneteltethetők" a díjak elkerülése érdekében, az Azure Cognitive Search szolgáltatás mindig elérhető a kizárólagos használatra szánt hardveren. Így a szolgáltatás létrehozása egy számlázható esemény, amely a szolgáltatás létrehozásakor kezdődik, és a szolgáltatás törlésekor ér véget. 

A minimális díj az első keresési egység (egy replika x egy partíció) a számlázható sebességgel. Ez a minimum a szolgáltatás élettartama alatt van rögzítve, mert a szolgáltatás nem futtatható ennél kisebb konfiguráción. A minimális minimumon túl replikákat és partíciókat adhat hozzá egymástól függetlenül. A replikák és partíciók on-re növekményes kapacitásnövekedése növeli a számlát a következő képlet alapján: [(replikák x partíciók x sebesség)](#search-units), ahol a felszámított díj a kiválasztott tarifacsomagtól függ.

A keresési megoldás költségének becslésénél ne feledje, hogy az árképzés és a kapacitás nem lineáris. (A kapacitás megduplázása több mint megduplázza a költségeket.) A képlet működésének például a [replikák és partíciók lefoglalása című témakörben található.](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)

### <a name="bandwidth-charges"></a>Sávszélesség-díjak

Az [Azure Cognitive Search indexelők](search-indexer-overview.md) használata hatással lehet a számlázásra, a szolgáltatások helyétől függően. Az adatok kimenő díjait teljesen kiküszöbölheti, ha az Azure Cognitive Search szolgáltatást ugyanabban a régióban hozza létre, mint az adatokat. Íme néhány információ a [sávszélesség árképzési oldalon:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ A Microsoft nem számít fel díjat az Azure bármely szolgáltatására érkező adatokért, illetve az Azure Cognitive Search-ből érkező kimenő adatokért.
+ A többszolgáltatásos megoldásokban nem számít unk fel díjat a vezetéket átszelő adatokért, ha az összes szolgáltatás ugyanabban a régióban van.

A kimenő adatokért díjat kell fizetni, ha a szolgáltatások különböző régiókban találhatók. Ezek a díjak valójában nem részei az Azure Cognitive Search számlájának. Itt említiőket, mert ha adatokat vagy AI-dúsított indexelőket használ a különböző régiókból származó adatok lekérése érdekében, a költségek megjelennek a teljes számlában.

### <a name="ai-enrichment-with-cognitive-services"></a>AI gazdagodása a Cognitive Services szolgáltatással

[Ai-bővítés](cognitive-search-concept-intro.md)esetén meg kell [terveznie, hogy csatolja a számlázható Azure Cognitive Services-erőforrás](cognitive-search-attach-cognitive-services.md), ugyanabban a régióban, mint az Azure Cognitive Search, az S0 tarifacsomag pay-as-you-feldolgozás. Nincs rögzített költség társított cognitive services csatolása. Csak a szükséges feldolgozásért kell fizetnie.

| Művelet | Számlázási hatás |
|-----------|----------------|
| Dokumentum feltörése, szövegkinyerés | Ingyenes |
| Dokumentum repedés, kép kinyerése | A kiszámlázott képek száma szerint számlázva. Indexelő [konfigurációban](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)az **imageAction** az a paraméter, amely elindítja a kép kinyerését. Ha az **imageAction** beállítása "nincs" (ez az alapértelmezett), akkor nem kell fizetnie a kép kinyerése. A kép kinyerésének mértéke az Azure Cognitive Search [díjszabási részletek](https://azure.microsoft.com/pricing/details/search/) oldalán található.|
| [Beépített kognitív képességek](cognitive-search-predefined-skills.md) | A számlázás ugyanolyan ütemben történik, mintha a Cognitive Services közvetlen használatával hajtotta volna végre a feladatot. |
| Egyéni készségek | Az egyéni szakértelem az Ön által megadott funkciók. Az egyéni szakértelem használatának költsége teljes mértékben attól függ, hogy az egyéni kód más díjdíjas szolgáltatásokat hív-e. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Számlázási képlet (R x P = SU)

Az Azure Cognitive Search-műveletek legfontosabb számlázási koncepciója a *keresési egység* (SU). Mivel az Azure Cognitive Search az indexelés és a lekérdezések replikáktól és partícióktól is függ, nincs értelme csak az egyik vagy a másik által számlázni. Ehelyett a számlázás alapja a kompozit mindkettő.

Su a szolgáltatás által használt *replikák* és *partíciók* szorzata: **(R x P = SU)**.

Minden szolgáltatás egy SU-val kezdődik (egy replika szorozva egy partícióval) minimálisként. Minden szolgáltatás maximális an 36 SUs. Ez a maximum többféle módon érhető el: 6 partíció x 6 replikák, vagy 3 partícióx 12 replikák, például. Gyakori, hogy a teljes kapacitásnál kevesebbet használ (például egy 3-replika, 3 partíciós szolgáltatás 9 SUs-ként számlázott). Tekintse meg a [partíció és a replika kombinációk](search-capacity-planning.md#chart) diagram érvényes kombinációk.

A számlázási díj per SU óránként. Minden szint fokozatosan magasabb a díja. A magasabb szintek nagyobb és gyorsabb partíciókkal rendelkeznek, és ez hozzájárul az adott szint általánosmagasabb óradíjához. Az egyes szintek díjait az árképzés részletei oldalon [tekintheti](https://azure.microsoft.com/pricing/details/search/) meg.

A legtöbb ügyfél a teljes kapacitásnak csak egy részét hozza online, a többit tartalékban tartva. Számlázási, a partíciók és replikák száma, hogy hozza online, az SU képlet által számított, határozza meg, hogy mit fizet óránként.

## <a name="how-to-manage-costs"></a>A költségek kezelése

Az alábbi javaslatok segítségével minimális költségeket érhet el:

- A sávszélesség-díjak minimalizálása vagy kiküszöbölése érdekében hozza létre az összes erőforrást ugyanabban a régióban vagy a lehető legkevesebb régióban.

- Az összes szolgáltatást egyetlen erőforráscsoportba egyesítheti, például az Azure Cognitive Search, a Cognitive Services és a megoldásban használt egyéb Azure-szolgáltatások. Az Azure Portalon keresse meg az erőforráscsoportot, és használja a **Költségkezelési** parancsokat a tényleges és a tervezett kiadások megismeréséhez.

- Fontolja meg az Azure Web App az előtér-alkalmazás, hogy a kérelmek és válaszok az adatközpont határain belül maradnak.

- Skálázható az erőforrás-igényes műveletek, például az indexelés, és majd lefelé a rendszeres lekérdezési számítási feladatok. Kezdje az Azure Cognitive Search minimális konfigurációjával (egy partícióból és egy replikaból álló SU), majd figyelje a felhasználói tevékenységet a használati minták azonosításához, amelyek nagyobb kapacitást jelentenek. Ha van egy kiszámítható minta, előfordulhat, hogy képes szinkronizálni a skálát a tevékenységgel (akkor kell írni a kódot, hogy automatizálják ezt).

Ezenkívül a kiadásokhoz kapcsolódó beépített eszközökért és funkciókért látogasson el a [Számlázás és a költségkezelés](https://docs.microsoft.com/azure/billing/billing-getting-started) oldalra.

A keresési szolgáltatás ideiglenes leállítása nem lehetséges. A dedikált erőforrások mindig működőképesek, és kizárólagos használatra vannak kiosztva a szolgáltatás teljes élettartama alatt. A szolgáltatás törlése állandó, és törli a kapcsolódó adatokat is.

Ami magát a szolgáltatást illeti, a számla csökkentésének egyetlen módja, ha a replikákat és a partíciókat olyan szintre csökkenti, amely továbbra is elfogadható teljesítményt és [SLA-megfelelőséget](https://azure.microsoft.com/support/legal/sla/search/v1_0/)biztosít, vagy alacsonyabb szinten hoz létre szolgáltatást (az S1 óradíja alacsonyabb, mint az S2 vagy S3 díj). Feltéve, hogy a szolgáltatás kiépítése a terhelési előrejelzések alsó végén, ha kinövi a szolgáltatást, létrehozhat egy második nagyobb rétegzett szolgáltatást, újraépítheti az indexek a második szolgáltatás, és törölje az első.

## <a name="how-to-evaluate-capacity-requirements"></a>A kapacitáskövetelmények értékelése

Az Azure Cognitive Search kapacitás *a replikák* és *partíciók.*

+ A replikák a keresési szolgáltatás példányai. Minden replika egy-egy index terheléselosztásos példányát üzemelteti. Például egy szolgáltatás hat replikák hat példányban minden index betöltve a szolgáltatásban.

+ A partíciók indexeket tárolnak, és automatikusan felosztják a kereshető adatokat. Két partíció kettéosztotta az indexet, három partíció harmadára osztotta, és így tovább. A kapacitás szempontjából a *partíció mérete* az elsődleges megkülönböztető funkció a rétegek között.

> [!NOTE]
> Minden standard és tárolási optimalizált réteg támogatja [a replikák és partíciók rugalmas kombinációját,](search-capacity-planning.md#chart) így [optimalizálhatja a rendszert](search-performance-optimization.md) a sebességre vagy a tárolásra az egyenleg módosításával. Az alapszintű szint legfeljebb három replikát kínál a magas rendelkezésre álláshoz, de csak egy partícióval rendelkezik. Az ingyenes szintek nem biztosítanak dedikált erőforrásokat: a számítási erőforrásokat több előfizető osztja meg.


### <a name="evaluating-capacity"></a>A kapacitás értékelése

A kapacitás és a szolgáltatás működtetésének költségei kéz a kézben járnak. A szintek két szinten korlátozzák: a tárolást és az erőforrásokat. Mindkettőre gondolnod kell, mert bármelyik korlátot is éred el először, az a tényleges korlát.

Az üzleti követelmények általában meghatározzák a szükséges indexek számát. Előfordulhat például, hogy egy globális indexre van szüksége egy nagy dokumentumtárhoz. Vagy szükség lehet több indexek régió, alkalmazás vagy üzleti rést.

Az index méretének meghatározásához létre kell [készítenie egyet.](search-create-index-portal.md) Mérete az importált adatokon és az indexkonfiguráción alapul, például attól, hogy engedélyezi-e a javaslattevők, a szűrés és a rendezés. A méretre gyakorolt konfigurációs hatásról további információt az [Alapindex létrehozása című ](search-what-is-an-index.md)témakörben talál.

A teljes szöveges keresésnél az elsődleges adatstruktúra fordított [indexszerkezet,](https://en.wikipedia.org/wiki/Inverted_index) amely nek más jellemzői vannak, mint a forrásadatoknak. A fordított indexek esetében a méretet és az összetettséget a tartalom határozza meg, nem feltétlenül a betáplálandó adatok mennyisége. A nagy redundanciával rendelkező nagy adatforrás oka kisebb index lehet, mint egy kisebb adatkészlet, amely rendkívül változó tartalmat tartalmaz. Így ritkán lehet következtetni index mérete az eredeti adatkészlet mérete alapján.

> [!NOTE] 
> Annak ellenére, hogy az indexek és a tárolás jövőbeli igényeinek becslése találgatásnak tűnhet, érdemes megtenni. Ha egy szint kapacitása túl alacsonynak tűnik, új szolgáltatást kell üzembe létesítenie egy magasabb szinten, majd [újra be kell töltenie az indexeket.](search-howto-reindex.md) Az egyik termékváltozatról a másikra nem történik helyben frissítés.
>

### <a name="estimate-with-the-free-tier"></a>Becslés az ingyenes szinttel

A kapacitás becslésének egyik megközelítése az ingyenes szinttel való kezdés. Ne feledje, hogy az ingyenes szolgáltatás legfeljebb három indexet, 50 MB tárhelyet és 2 perc indexelési időt kínál. A tervezett indexméretet ezekkel a korlátokkal nehéz megbecsülni, de ezek a lépések:

+ [Hozzon létre egy ingyenes szolgáltatást](search-create-service-portal.md).
+ Készítsen elő egy kis, reprezentatív adatkészletet.
+ [Hozzon létre egy kezdeti indexet a portálon,](search-create-index-portal.md) és jegyezze fel annak méretét. A funkciók és attribútumok hatással vannak a tárhelyre. Például a javaslatjavasolók hozzáadása (typeahead) növeli a tárolási követelményeket. Ugyanazt az adatkészletet használva megpróbálhat több verziót létrehozni egy indexből, különböző attribútumokkal az egyes mezőkben, hogy lássa, hogyan változnak a tárolási követelmények. További információt az Alapindex létrehozása című [témakör "Tárolási vonatkozások" című témakörében](search-what-is-an-index.md#index-size)talál.

Egy durva becslés a kezében, előfordulhat, hogy a duplája a költségvetés két indexek (fejlesztés és termelés), majd válassza ki a szint ennek megfelelően.

### <a name="estimate-with-a-billable-tier"></a>Becslés számlázható szinttel

A dedikált erőforrások nagyobb mintavételi és feldolgozási időket is betudnak fogadni az indexmennyiség, a méret és a lekérdezési kötetek valósághűbb becsléséhez a fejlesztés során. Egyes ügyfelek azonnal beugranak egy számlázható szinttel, majd újraértékelik a fejlesztési projekt érlelődése után.

1. [Tekintse át az egyes szintekszolgáltatás-korlátokat,](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) és állapítsa meg, hogy az alacsonyabb szintek támogatják-e a szükséges indexek számát. Az alapszintű, s1 és S2 szintek között az indexkorlátok 15, 50 és 200, illetve 15. A tárolásra optimalizált szint legfeljebb 10 indexet, mert úgy tervezték, hogy támogassa a nagyon nagy indexek kis számú.

1. [Szolgáltatás létrehozása számlázható szinten:](search-create-service-portal.md)

    + Indítsa el az alacsony, alapszintű vagy S1, ha nem biztos a tervezett terhelés.
    + Indítsa el a magas, S2 vagy akár S3, ha tudod, hogy lesz nagy méretű indexelési és lekérdezési terhelések.
    + Ha nagy mennyiségű adatot indexel, akkor a tárolási optimalizált, L1 vagy L2 verzióval kezdje, ha nagy mennyiségű adatot indexel, és a lekérdezésterhelés viszonylag alacsony, mint egy belső üzleti alkalmazás esetén.

1. [Hozzon létre egy kezdeti indexet](search-create-index-portal.md) annak meghatározásához, hogy a forrásadatok hogyan viszonyuljanak az indexhez. Ez az egyetlen módja az index méretének becslésének.

1. [Figyelheti a tárhelyet, a szolgáltatáskorlátokat, a lekérdezési kötetet és a késést](search-monitor-usage.md) a portálon. A portál on-nként lekérdezések, szabályozott lekérdezések, és a keresési késés. Ezek az értékek segíthetnek eldönteni, hogy a megfelelő szintet választotta-e. 

Az index száma és mérete ugyanolyan fontos az elemzéshez. Ennek az az oka, hogy a maximális korlátok at a tároló (partíciók) teljes kihasználásával vagy az erőforrások (indexek, indexelők stb.) maximális korlátjával érik el, amelyik előbb következik be. A portál segítségével nyomon követheti mindkettőt, és az aktuális használatot és a maximális korlátokat egymás mellett jeleníti meg az Áttekintés oldalon.

> [!NOTE]
> A tárolási követelmények felfújhatók, ha a dokumentumok idegen adatokat tartalmaznak. Ideális esetben a dokumentumok csak azokat az adatokat tartalmazzák, amelyek a keresési élményhez szükségesek. A bináris adatok nem kereshetőek, és külön kell tárolni őket (esetleg egy Azure-táblában vagy blobstorage-ban). Ezután egy mezőt kell hozzáadni az indexhez, hogy a külső adatokra mutató URL-hivatkozás legyen. Az egyes dokumentumok maximális mérete 16 MB (vagy kevesebb, ha egy kérelemben több dokumentumot tölt fel tömegesen). További információ: [Service limits in Azure Cognitive Search](search-limits-quotas-capacity.md).
>

**Kötet lekérdezésével kapcsolatos szempontok**

Lekérdezések másodpercenként (QPS) egy fontos metrika a teljesítmény hangolása során, de ez általában csak egy réteg szempont, ha várhatóan magas lekérdezési kötet az elején.

A standard szintek biztosíthatja a replikák és partíciók egyensúlyát. Növelheti a lekérdezési átfutási idő a terheléselosztás replikák hozzáadásával vagy partíciók hozzáadása párhuzamos feldolgozáshoz. Ezután a szolgáltatás kiépítése után a teljesítményre hangolhat.

Ha a nagy tartós lekérdezési kötetek a kezdetektől fogva, érdemes magasabb standard rétegeket, nagyobb teljesítményű hardver rel támogatott. Ezután a partíciókat és a replikákat offline állapotba helyezheti, vagy akár alacsonyabb szintű szolgáltatásra is válthat, ha ezek a lekérdezési kötetek nem fordulnak elő. A lekérdezési átviteli teljesítmény kiszámításáról az [Azure Cognitive Search teljesítmény- és optimalizálási](search-performance-optimization.md)témakörben olvashat bővebben.

A tárolási optimalizált rétegek nagy adatszámítási feladatok hoz hasznosak, támogatva a több általános rendelkezésre álló index tároló, ha a lekérdezéskési követelmények kevésbé fontosak. Továbbra is további replikákat kell használnia a terheléselosztáshoz és további partíciókat a párhuzamos feldolgozáshoz. Ezután a szolgáltatás kiépítése után a teljesítményre hangolhat.

**Szolgáltatási szintű megállapodások**

Az ingyenes szint és az előzetes verzió funkció nem biztosít [szolgáltatásiszint-szerződéseket (SLOS).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Az összes számlázható réteg esetén az SLA-k akkor lépnek érvénybe, ha elegendő redundanciát biztosít a szolgáltatáshoz. Két vagy több replikát kell rendelkeznie a lekérdezési (olvasási) SLA-khoz. Három vagy több replikával kell rendelkeznie a lekérdezéshez és az indexeléshez (írási és írási) SL-ek. A partíciók száma nincs hatással az SL-ek.

## <a name="tips-for-tier-evaluation"></a>Tippek a szintek kiértékeléséhez

+ A metrikák köréépülhet a lekérdezések, és adatokat gyűjthet a használati minták (munkaidőben lekérdezések, indexelés csúcsidőn kívül) körül. Ezeket az adatokat a szolgáltatáskiépítési döntések tájékoztatásához használja. Bár ez nem praktikus óránkénti vagy napi ütemben, dinamikusan módosíthatja a partíciókat és az erőforrásokat a lekérdezési kötetek tervezett változásainak megfelelően. A nem tervezett, de tartós változásokat is befogadhatja, ha a szintek elég hosszúak ahhoz, hogy cselekvést indokoljanak.

+ Ne feledje, hogy az egyetlen hátránya az alulépítés, hogy előfordulhat, hogy le kell bontania egy szolgáltatást, ha a tényleges követelmények nagyobbak, mint az előrejelzések. A szolgáltatás kimaradásának elkerülése érdekében hozzon létre egy új szolgáltatást egy magasabb szinten, és futtassa egymás mellett, amíg az összes alkalmazás és kérés az új végpontot célozza.

## <a name="next-steps"></a>További lépések

Kezdje egy ingyenes szinttel, és hozzon létre egy kezdeti indexet az adatok egy részhalmazának használatával a jellemzők megértéséhez. Az Azure Cognitive Search adatstruktúrája egy fordított indexstruktúra. A fordított index méretét és összetettségét a tartalom határozza meg. Ne feledje, hogy a rendkívül redundáns tartalom általában kisebb indexet eredményez, mint a rendkívül szabálytalan tartalom. Így a tartalom jellemzői, nem pedig az adatkészlet mérete határozza meg az index tárolási követelményeit.

Miután rendelkezik az index méretének kezdeti becslésével, [egy számlázható szolgáltatást kell kiépítenie](search-create-service-portal.md) az ebben a cikkben tárgyalt rétegek egyikén: Alapszintű, Standard vagy Tárolási optimalizált. Lazítson az adatok méretezésével kapcsolatos mesterséges korlátokon, és [építse újra az indexet,](search-howto-reindex.md) hogy tartalmazza az összes olyan adatot, amelyet kereshetővé szeretne tenni.

[Partíciók és replikák szükség szerint lefoglalása](search-capacity-planning.md) a teljesítmény és a méretezés szükséges.

Ha a teljesítmény és a kapacitás rendben van, akkor kész. Ellenkező esetben hozzon létre újra egy keresési szolgáltatást egy másik szinten, amely jobban igazodik az igényeihez.

> [!NOTE]
> Ha kérdése van, írjon a [StackOverflow-nak,](https://stackoverflow.com/questions/tagged/azure-search) vagy [lépjen kapcsolatba az Azure ügyfélszolgálatával.](https://azure.microsoft.com/support/options/)
