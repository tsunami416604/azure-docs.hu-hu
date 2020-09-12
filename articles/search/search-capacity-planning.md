---
title: A kapacitás módosítása a lekérdezési és indexelési feladatokhoz
titleSuffix: Azure Cognitive Search
description: A partíciós és a replika számítógép erőforrásainak módosítása az Azure Cognitive Searchban, ahol az egyes erőforrások számlázható keresési egységekben vannak.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 76084a9ddd6842194bb4c6b25d62e62c2ed2d4a8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660290"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás kapacitásának módosítása

A [keresési szolgáltatás üzembe helyezése](search-create-service-portal.md) és egy adott díjszabási szinten való zárolása előtt szánjon néhány percet, hogy megértse, hogyan működik a kapacitás, és hogyan módosíthatja a replikákat és a partíciókat a munkaterhelés ingadozásának kielégítése érdekében.

A kapacitás a [kiválasztott rétegek](search-sku-tier.md) függvénye (a szintek határozzák meg a hardver jellemzőit), valamint a tervezett munkaterhelésekhez szükséges replika és partíciós kombinációt. Egyenként növelheti vagy csökkentheti a replikák és a partíciók számát. A szintjétől és a beállítás méretétől függően a kapacitás hozzáadása vagy csökkentése akár 15 perctől akár több óráig is eltarthat.

A replikák és partíciók kiosztásának módosításakor javasoljuk, hogy használja a Azure Portal. A portál kényszeríti az olyan megengedett kombinációk korlátozásait, amelyek az adott szinten maximális korláton belül maradnak. Ha azonban parancsfájl-vagy programkód-alapú létesítési megközelítésre van szüksége, akkor a [Azure PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](/rest/api/searchmanagement/services) alternatív megoldás.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Fogalmak: keresési egységek, replikák, partíciók, szegmensek

A kapacitás olyan *keresési egységben* van kifejezve, amely *partíciók* és *replikák*kombinációi között foglalható le, a rugalmas konfigurációk támogatásához *egy mögöttes* horizontális Felskálázási mechanizmus használatával:

| Fogalom  | Definíció|
|----------|-----------|
|*Keresési egység* | A teljes rendelkezésre álló kapacitás (36 egység) egyetlen növekménye. Az Azure Cognitive Search szolgáltatás számlázási egysége is. A szolgáltatás futtatásához legalább egy egység szükséges.|
|*Replika* | A keresési szolgáltatás azon példányai, amelyek elsődlegesen a lekérdezési műveletek terheléselosztására szolgálnak. Minden replika egy adott index egy példányát tárolja. Ha három replikát oszt ki, akkor a lekérdezési kérések kiszolgálásához három példánya lesz elérhető.|
|*Partíció* | Fizikai tárterület és I/O írási/olvasási műveletekhez (például indexek újraépítésekor vagy frissítésekor). Minden partíció tartalmaz egy szeletet a teljes indexből. Ha három partíciót foglal le, az index a harmadikra van osztva. |
|*Szegmens* | Egy indexből álló tömb. Az Azure Cognitive Search felosztja az egyes indexeket a szegmensekre, így gyorsabbá teszi a partíciók hozzáadását (a szegmensek új keresési egységekre való áthelyezésével).|

Az alábbi ábrán a replikák, a partíciók, a szegmensek és a keresési egységek közötti kapcsolat látható. Ebből a példából kiderül, hogy az egyes indexek hogyan terjednek át egy szolgáltatás négy keresési egységére két replikával és két partícióval. A négy keresési egység az indexnek csak a felét tárolja. A bal oldali oszlopban található keresési egységek a szegmensek első felét tárolják, amely az első partíciót tartalmazza, míg a jobb oldali oszlopban lévők a második partíciót tartalmazó szegmensek második felét tárolják. Mivel két replika létezik, az egyes indexek szegmensének két példánya van. A felső sorban található keresési egységek egyetlen másolatból állnak, amelyek az első replikát alkotják, míg az alsó sorban lévők egy másik példányt tárolnak, amely a második replikát tartalmazza.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="A keresési indexek felosztva vannak a partíciók között.":::

A fenti diagram csak egy példát mutat be. A partíciók és a replikák számos kombinációja lehetséges, legfeljebb 36 teljes keresési egység.

Cognitive Search a szilánkok kezelése egy implementációs adat és nem konfigurálható, de az indexek horizontális felskálázása segít megérteni a rangsorolási és az automatikus kiegészítési viselkedésben előforduló esetenkénti rendellenességeket:

+ Rangsorolási rendellenességek: a keresési pontszámok először a szegmens szintjén vannak kiszámítva, majd egyetlen eredményhalmaz szerint összesítve. A szegmens tartalmának jellemzőitől függően előfordulhat, hogy az egyik szegmensből származó egyezések egy másiknál magasabbra vannak sorolva. Ha észreveheti, hogy a keresési eredmények nem intuitív rangsorban jelennek meg, akkor valószínűleg a horizontális skálázás hatása okozza, különösen, ha az indexek kicsik. A rangsorolási rendellenességek elkerülhetők, ha [a teljes indexben globálisan szeretné kiszámítani a pontszámokat](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), de ez a teljesítményre vonatkozó szankciót is eredményez.

+ Automatikus kiegészítési rendellenességek: automatikus lekérdezések, ahol a egyezések egy részben megadott kifejezés első több karakterén történnek, fogadjon egy olyan fuzzy paramétert, amely a helyesírási kis eltéréseket megbocsátja. Az automatikus kiegészítés esetében a zavaros egyezés az aktuális szegmensen belüli kifejezésekre van korlátozva. Ha például egy szegmens "Microsoft" kifejezést tartalmaz, és a "micor" részleges kifejezése van megadva, akkor a keresőmotor a "Microsoft" résznek felel meg a szegmensben, de nem az index fennmaradó részeit tároló más szegmensekben.

## <a name="when-to-add-nodes"></a>Mikor lehet csomópontokat hozzáadni

A szolgáltatás eredetileg egy partíciót és egy replikát tartalmazó minimális szintű erőforrásokat foglal le.

Egyetlen szolgáltatásnak elegendő erőforrással kell rendelkeznie az összes számítási feladat (indexelés és lekérdezések) kezeléséhez. Egyik munkaterhelés sem fut a háttérben. Az indexelést ütemezhet olyan időpontokra, amikor a lekérdezési kérések természetesen ritkábbak, de a szolgáltatás más módon nem rangsorolja az egyik feladatot. Emellett egy bizonyos mennyiségű redundancia is kisimítja a lekérdezési teljesítményt, ha a szolgáltatások vagy a csomópontok belsőleg frissülnek.

Általános szabályként a keresési alkalmazások általában több replikát igényelnek, mint a partíciók, különösen akkor, ha a szolgáltatási műveletek a lekérdezési munkaterhelések szempontjából elfogultak. A [magas rendelkezésre állásról](#HA) szóló szakasz ismerteti, hogy miért.

> [!NOTE]
> További replikák vagy partíciók hozzáadásával növelheti a szolgáltatás futtatásának költségeit, és az eredmények rendezésének módjában enyhe eltéréseket vezethet be. Ügyeljen arra, hogy ellenőrizze a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/) , és Ismerje meg a további csomópontok hozzáadásának számlázási következményeit. Az [alábbi diagram](#chart) segítséget nyújt az adott konfigurációhoz szükséges keresési egységek számának kereszthivatkozásához. További információ arról, hogyan befolyásolják a további replikák a lekérdezés feldolgozását: [megrendelés eredményei](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Replikák és partíciók lefoglalása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.

1. A **Beállítások**területen nyissa meg a **skálázás** lapot a replikák és partíciók módosításához. 

   Az alábbi képernyőfelvételen egy replikával és partícióval kiépített szabványos szolgáltatás látható. A lenti képlet azt jelzi, hogy hány keresési egység van használatban (1). Ha az egység ára $100 (nem valós díj), akkor a szolgáltatás futtatásának havi költsége átlagosan $100.

   ![Az aktuális értékeket mutató méretezési oldal](media/search-capacity-planning/1-initial-values.png "Az aktuális értékeket mutató méretezési oldal")

1. A csúszka használatával növelheti vagy csökkentheti a partíciók számát. A lenti képlet azt jelzi, hogy hány keresési egységet használ a rendszer.

   Ez a példa dupla kapacitást, két replikával és partícióval rendelkezik. Figyelje meg a keresési egységek darabszámát; most négy, mert a számlázási képlet a replikák szorozza a partíciókkal (2 x 2). A kapacitás megkettőzése több, mint megduplázza a szolgáltatás futtatásának költségeit. Ha a keresési egység díja $100 volt, az új havi számla mostantól $400 lesz.

   Az egyes szintek aktuális egységenkénti költségeiért látogasson el a [díjszabási oldalra](https://azure.microsoft.com/pricing/details/search/).

   ![Replikák és partíciók hozzáadása](media/search-capacity-planning/2-add-2-each.png "Replikák és partíciók hozzáadása")

1. A módosítások megerősítéséhez válassza a **Mentés** lehetőséget.

   ![A skálázás és a számlázás változásainak megerősítése](media/search-capacity-planning/3-save-confirm.png "A skálázás és a számlázás változásainak megerősítése")

   A kapacitás változásai több órát is igénybe vehetik. A folyamat elindítása után nem lehet megszakítani a műveletet, és nincs valós idejű figyelés a replika és a partíciók beállításaihoz. A következő üzenet azonban továbbra is látható, amíg megváltozik.

   ![Állapotjelző üzenet a portálon](media/search-capacity-planning/4-updating.png "Állapotjelző üzenet a portálon")

> [!NOTE]
> A szolgáltatás üzembe helyezését követően nem lehet magasabb szintre frissíteni. Létre kell hoznia egy keresési szolgáltatást az új szinten, és újra kell töltenie az indexeket. A szolgáltatás kiépítési támogatásához tekintse meg az [Azure Cognitive Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című témakört.
>
> Emellett a partíciókat és a replikákat a szolgáltatás kizárólag és belsőleg kezeli. A processzor affinitása nem áll rendelkezésre, vagy a számítási feladatok egy adott csomóponthoz rendelhetők hozzá.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció-és replika-kombinációk

Az alapszintű szolgáltatásoknak pontosan egy partíciójuk és legfeljebb három replikájuk lehet, legfeljebb három SUs esetén. Az egyetlen állítható erőforrás replika. A lekérdezésekben a magas rendelkezésre állás érdekében legalább két replikára van szükség.

A standard és a Storage-alapú optimalizált keresési szolgáltatások a 36-SU korlátnak megfelelő replikák és partíciók következő kombinációit vehetik igénybe. 

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíció** | **6 partíció** | **12 partíció** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replika** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replika** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replika** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 replika** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 replika** |12 SU |24 SU |36 SU |N.A. |N.A. |N.A. |

Az SUs, a díjszabás és a kapacitás részletes ismertetését az Azure webhelyén találja. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és partíciók száma egyenletesen oszlik el a 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek az az oka, hogy az Azure Cognitive Search az egyes indexeket 12 szegmensre osztja, így a partíciók között egyenlő arányban terjedhet. Ha például a szolgáltatás három partícióval rendelkezik, és létrehoz egy indexet, minden partíció az index négy szegmensét fogja tartalmazni. Az Azure Cognitive Search az indexek egy implementációs részletességgel rendelkeznek, amely a jövőbeli kiadásokban változhat. Bár a szám 12 ma, nem várható, hogy ez a szám mindig 12 lesz a jövőben.
>

<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás

Mivel a méretezés egyszerűen és viszonylag gyorsan megoldható, általában azt javasoljuk, hogy először egy partíciót és egy vagy két replikát válasszon, majd a skálázási folyamat létrehozásakor. A lekérdezési feladatok elsődlegesen a replikákat futtatják. Ha nagyobb átviteli sebességre vagy magas rendelkezésre állásra van szüksége, valószínűleg további replikákat kell megadnia.

A magas rendelkezésre állásra vonatkozó általános javaslatok a következők:

* Két replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)

* Három vagy több replika az olvasási/írási munkaterhelések magas rendelkezésre állásához (lekérdezések és az indexelés egyéni dokumentumok hozzáadása, frissítése vagy törlése)

Az Azure Cognitive Search szolgáltatói szerződések (SLA) a lekérdezési műveletekre és a dokumentumok hozzáadását, frissítését vagy törlését tartalmazó index-frissítésekre irányulnak.

Az alapszintű csomag egy partíción és három replikán található. Ha azt szeretné, hogy a rugalmasság azonnal reagáljon az indexelési és a lekérdezési átviteli sebesség igény szerinti ingadozására, vegye figyelembe az egyik standard szintet is.  Ha úgy találja, hogy a tárolási követelmények sokkal gyorsabban növekednek, mint a lekérdezési átviteli sebesség, vegye figyelembe a tárterület optimalizált szintjeinek egyikét.

## <a name="disaster-recovery"></a>Vészhelyreállítás

Jelenleg nincs beépített mechanizmus a vész-helyreállításhoz. Partíciók vagy replikák hozzáadása nem megfelelő stratégiát jelentene a vész-helyreállítási célkitűzések teljesítéséhez. A leggyakoribb módszer a redundancia a szolgáltatási szinten való hozzáadása egy másik régióban található második keresési szolgáltatás beállításával. Az indexek újraépítése során a rendelkezésre álláshoz hasonlóan az átirányítás vagy a feladatátvételi logikának a kódból kell származnia.

## <a name="estimate-replicas"></a>Replikák becslése

Üzemi szolgáltatás esetén három replikát kell kiosztania SLA-célokból. Ha lassú lekérdezési teljesítményt tapasztal, hozzáadhat replikákat, hogy az index további példányai online állapotba kerüljenek a nagyobb lekérdezési munkaterhelések támogatása és a kérelmek terheléselosztása a több replikán keresztül.

Nem biztosítunk útmutatást arra vonatkozóan, hogy hány replikára van szükség a lekérdezési terhelések fogadásához. A lekérdezési teljesítmény a lekérdezés és a versengő számítási feladatok összetettsége alapján változhat. Bár a replikák hozzáadása egyértelműen jobb teljesítményt eredményez, az eredmény nem feltétlenül lineáris: három replika hozzáadása nem garantálja a háromszoros átviteli sebességet.

A megoldás QPS becslésével kapcsolatos útmutatásért lásd: [Méretezés a teljesítményhez](search-performance-optimization.md)és a [figyelési lekérdezések](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Partíciók becslése

A [kiválasztott](search-sku-tier.md) keret határozza meg a partíció méretét és sebességét, az egyes szintek pedig a különböző forgatókönyvekhez illeszkedő jellemzők körén vannak optimalizálva. Ha magasabb szintű szintet választ, előfordulhat, hogy kevesebb partícióra van szüksége, mint ha S1-et használ. Az egyik kérdés, amelyet saját irányított teszteléssel kell megválaszolnia, hogy egy nagyobb és drágább partíció jobb teljesítményt biztosít-e, mint két olcsóbb partíció az alacsonyabb szinten kiépített szolgáltatásokban.

A közel valós idejű adatfrissítést igénylő alkalmazások kereséséhez a replikák több partícióra van szükségük. A partíciók hozzáadásával az írási/olvasási műveletek nagyobb számú számítási erőforráson keresztül terjednek ki. Emellett további lemezterületet biztosít a további indexek és dokumentumok tárolásához.

A nagyobb indexek lekérése hosszabb időt vesz igénybe. Ezért előfordulhat, hogy a partíciók növekményes növekedésének a replikák kisebb, de arányos növekedése szükséges. A lekérdezések és a lekérdezési kötetek összetettsége azt eredményezi, hogy milyen gyorsan történik a lekérdezés végrehajtása.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Válasszon árképzési szintet az Azure Cognitive Search](search-sku-tier.md)