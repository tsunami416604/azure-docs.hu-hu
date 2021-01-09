---
title: Az ikergráf lekérdezése
titleSuffix: Azure Digital Twins
description: 'További információ: az Azure Digital Twins Twin Graph lekérdezése.'
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 47883c742d77a88adb662e8dded0723f0e105385
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044186"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Az Azure Digital Twins Twin gráf lekérdezése

Ez a cikk lekérdezési példákat és részletes útmutatást nyújt az **Azure digitális Twins lekérdezési nyelvének** használatához, hogy lekérdezze a [Twin Graphot](concepts-twins-graph.md) az információkhoz. (A lekérdezés nyelvének bevezetéséhez és a funkcióinak teljes listájáért lásd: [*fogalmak: lekérdezés nyelve*](concepts-query-language.md).)

Ez a cikk a lekérdezés nyelvi felépítését és a digitális ikrek gyakori lekérdezési műveleteit bemutató példákkal kezdődik. Ezután leírja, hogyan futtathat lekérdezéseket az Azure Digital Twins [query API](/rest/api/digital-twins/dataplane/query) vagy egy [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)használatával.

> [!TIP]
> Ha az alábbi lekérdezéseket API-vagy SDK-hívással futtatja, a lekérdezés szövegét egyetlen sorba kell bontania.

## <a name="show-all-digital-twins"></a>Az összes digitális ikrek megjelenítése

Itt látható az alapszintű lekérdezés, amely a példányban található összes digitális ikrek listáját fogja visszaadni:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Lekérdezés tulajdonság szerint

Digitális ikrek beolvasása **Tulajdonságok** alapján (beleértve az azonosítót és a metaadatokat):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

> [!TIP]
> A Digital Twin AZONOSÍTÓját a rendszer a metaadatok mező használatával kérdezi le `$dtId` .

Azt is megteheti, **hogy egy bizonyos tulajdonság definiálva van-e**. Itt látható egy olyan lekérdezés, amely a megadott *Location* tulajdonsággal rendelkező ikreket tartalmaz:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Ez segít az ikreknek a *címke* tulajdonságai alapján való beszerzésében, a [Címkék hozzáadása a digitális ikrekhez](how-to-use-tags.md)című témakörben leírtak szerint. Itt látható egy olyan lekérdezés, amely a *vörös színnel* jelölt összes ikreket lekéri:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Az ikreket **egy tulajdonság típusa** alapján is elérheti. Íme egy lekérdezés, amely olyan ikreket kap, amelyek *hőmérsékleti* tulajdonsága egy szám:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

## <a name="query-by-model"></a>Lekérdezés modell szerint

Az `IS_OF_MODEL` operátor használható a Twin [**modell**](concepts-models.md)alapján történő szűrésre.

Figyelembe veszi az [öröklést](concepts-models.md#model-inheritance) és a modell [verziószámozását](how-to-manage-model.md#update-models), és **igaz** értékre értékeli az adott Twin esetében, ha a Twin a következő feltételek valamelyikét teljesíti:

* A Twin közvetlenül megvalósítja a által biztosított modellt `IS_OF_MODEL()` , a modell verziószáma pedig *nagyobb vagy egyenlő, mint* a megadott modell verziószáma.
* A Twin olyan modellt valósít meg, amely *kibővíti* a által biztosított modellt `IS_OF_MODEL()` , és a Twin modell verziószáma *nagyobb vagy egyenlő, mint* a megadott modell verziószáma.

Így például, ha a modellhez tartozó ikreket kérdezi le `dtmi:example:widget;4` , a lekérdezés minden olyan ikreket visszaad, amely a **4-es vagy újabb verzión** alapul, valamint **a** **4-es vagy újabb** verzióra épülő, a **widgettől örökölt modellek** esetében is.

`IS_OF_MODEL` több különböző paramétert is igénybe vehet, a szakasz többi része pedig a különböző túlterhelési lehetőségekre van kijelölve.

A legegyszerűbb használata `IS_OF_MODEL` csak a (z `twinTypeName` ) paramétert veszi igénybe: `IS_OF_MODEL(twinTypeName)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét továbbítja:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Ha egynél több (például a használatban lévő) értéknél szeretne keresni egy dupla gyűjteményt `JOIN` , adja hozzá a következő `twinCollection` paramétert: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét adja meg:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

A pontos egyezéshez adja hozzá a következő `exact` paramétert: `IS_OF_MODEL(twinTypeName, exact)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét adja meg:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Mindhárom argumentumot együtt is át lehet adni: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Íme egy lekérdezési példa, amely mindhárom paraméter értékét megadja:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Lekérdezés kapcsolat alapján

A digitális ikrek **kapcsolatain** alapuló lekérdezések esetén az Azure digitális Twins lekérdezési nyelvének speciális szintaxisa van.

A kapcsolatok a záradék lekérdezési hatókörében vannak behúzva `FROM` . A "klasszikus" SQL-Type nyelvek egyik fontos különbsége, hogy az ebben a `FROM` záradékban szereplő kifejezések nem táblázatos jellegűek, a záradék pedig az `FROM` entitások közötti kapcsolat bejárását fejezi ki, és a (z) Azure digitális Twins-verziójával van írva `JOIN` .

Ne felejtse el, hogy az Azure Digital Twins [modell](concepts-models.md) képességeivel a kapcsolatok nem léteznek az ikrektől függetlenül. Ez azt jelenti, hogy az Azure Digital Twins lekérdezési nyelve `JOIN` egy kicsit eltér az általános SQL `JOIN` -től, mivel itt nem lehet egymástól függetlenül lekérdezni a kapcsolatokat, és egy Twin-hez kell kötni.
A különbség befoglalásához a kulcsszó a `RELATED` `JOIN` kettős kapcsolatokra mutató hivatkozásokat használja a záradékban.

A következő szakasz több példát mutat be, hogy ez hogyan néz ki.

> [!TIP]
> Ez a funkció elméletileg a CosmosDB dokumentum-központú funkcióit utánozza, ahol a `JOIN` dokumentumon belüli gyermekobjektumok is elvégezhetők. A CosmosDB a `IN` kulcsszó használatával jelzi, hogy a az `JOIN` aktuális környezeti dokumentumban lévő tömb elemeinek megismétlésére szolgál.

### <a name="relationship-based-query-examples"></a>Kapcsolaton alapuló lekérdezési példák

Ha olyan adatkészletet szeretne beolvasni, amely kapcsolatokat tartalmaz, használjon egyetlen `FROM` utasítást, amelyet N utasítások követnek `JOIN` , ahol a `JOIN` utasítások egy korábbi vagy egy utasítás eredményéhez kapcsolódnak `FROM` `JOIN` .

Példa a kapcsolaton alapuló lekérdezésre. Ez a kódrészlet kiválasztja az összes olyan digitális ikreket, amelynek *ID* tulajdonsága "ABC", és az ezen digitális ikrekhez kapcsolódó összes digitális ikrek a következő kapcsolaton keresztül *szerepelnek* .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> A fejlesztőnek nem kell `JOIN` összekapcsolnia ezt a záradékban található kulcs értékével `WHERE` (vagy a definícióban szereplő kulcs értékének megadásával `JOIN` ). Ezt a korrelációt a rendszer automatikusan kiszámítja, mivel a kapcsolat tulajdonságai maguk határozzák meg a célként megadott entitást.

### <a name="query-the-properties-of-a-relationship"></a>Kapcsolat tulajdonságainak lekérdezése

Hasonlóan ahhoz, ahogy a digitális ikrek rendelkeznek a DTDL-on keresztül leírt tulajdonságokkal, a kapcsolatok is rendelkezhetnek tulajdonságokkal. Az ikreket **a kapcsolataik tulajdonságai alapján** kérdezheti le.
Az Azure digitális Twins lekérdezési nyelve lehetővé teszi a kapcsolatok szűrését és kivetítését azáltal, hogy a záradékon belül egy aliast rendel a kapcsolathoz `JOIN` .

Példaként vegyünk egy olyan *servicedBy* -kapcsolatot, amely *reportedCondition* tulajdonsággal rendelkezik. Az alábbi lekérdezésben ez a kapcsolat az "R" aliast kapja, hogy hivatkozzon a tulajdonságára.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

A fenti példában látható, hogy a *reportedCondition* a *servicedBy* -kapcsolat egyik tulajdonsága (nem pedig egy olyan digitális iker, amely *servicedBy* -kapcsolattal rendelkezik).

### <a name="query-with-multiple-joins"></a>Lekérdezés több illesztéssel

Legfeljebb öt `JOIN` s támogatott egyetlen lekérdezésben. Ez lehetővé teszi, hogy egyszerre több kapcsolati szintet is bejárjon.

Íme egy példa egy többcsatlakozásos lekérdezésre, amely az 1. és 2. helyiségekben található világos panelek összes izzóját lekéri.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Elemek darabszáma

Az eredményhalmaz elemeinek számát a záradék használatával számolhatja el `Select COUNT` :

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Adjon hozzá egy `WHERE` záradékot az adott feltételeknek megfelelő elemek számának megszámlálásához. Íme néhány példa az alkalmazott szűrővel való számlálásra az iker modell típusa alapján (a szintaxissal kapcsolatban bővebben lásd az alábbi [*modell szerinti lekérdezést*](#query-by-model) ):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

Emellett `COUNT` a záradékkal együtt is használható `JOIN` . Itt látható egy olyan lekérdezés, amely az 1. és 2. szoba világos panelén található összes villanykörtét számlálja:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Eredmények szűrése: válassza ki a legfelső elemeket

A lekérdezésben több "Top" elemet is kijelölhet a `Select TOP` záradék használatával.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Szűrők eredményei: a visszatérési készlet megadása vetítésekkel

Az utasításban kivetítések használatával `SELECT` kiválaszthatja, hogy a lekérdezés mely oszlopokat adja vissza.

>[!NOTE]
>Jelenleg az összetett tulajdonságok nem támogatottak. Annak ellenőrzéséhez, hogy a leképezési tulajdonságok érvényesek-e, kombinálja a kivetítéseket egy `IS_PRIMITIVE` ellenőrzéssel.

Íme egy példa egy olyan lekérdezésre, amely kivetítést használ az ikrek és a kapcsolatok visszaküldéséhez. A következő lekérdezés a *fogyasztót*, a *gyárat* és az *Edge* -t olyan forgatókönyv alapján tervezi meg, ahol az *ABC* azonosítóval rendelkező *gyár* a *Factory. Customer* kapcsolaton keresztül kapcsolódik a *fogyasztóhoz* , és ez a kapcsolat a *peremhálózat*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

A kivetítés használatával a Twin tulajdonságot is visszaállíthatja. A következő lekérdezés a Gyárhoz kapcsolódó *felhasználók* *név* TULAJDONSÁGát az *ABC* azonosítóval *együtt a* *Factory. Customer* kapcsolaton keresztül.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

A kivetítés használatával egy kapcsolat tulajdonságát is visszaadhatja. Ahogy az előző példában is látható, a következő lekérdezés a Gyárhoz kapcsolódó *felhasználók* *Name (név* ) tulajdonságát az *ABC* azonosítójával együtt a *Factory. Customer* kapcsolaton keresztül.  Most azonban a kapcsolat két tulajdonságát is visszaadja, a *prop1* és a *prop2*. Ez a kapcsolati *Szegély* elnevezésével és a tulajdonságainak összegyűjtésével végezhető el.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Aliasokat is használhat a kivetítéssel való lekérdezések egyszerűsítéséhez.

A következő lekérdezés ugyanazokat a műveleteket végzi el, mint az előző példában, de az a, a, a és a tulajdonság nevét aliasként nevezi el `consumerName` `first` `second` `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Íme egy hasonló lekérdezés, amely ugyanazt a készletet kérdezi le, de a projektek csak a *Consumer.name* tulajdonságot `consumerName` , a teljes *gyárat* pedig Twin-ként tervezik.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Hatékony lekérdezések készítése az IN operátorral

Jelentősen csökkentheti a szükséges lekérdezések számát az ikrek tömbjét kiépítve és az operátorral történő lekérdezéssel `IN` . 

Vegyünk például egy olyan forgatókönyvet, amelyben  az épületek *padlót* és *padlót* *tartalmaznak.* Ha egy olyan épületen belül szeretne keresni, amely forró, az egyik módszer az alábbi lépések követése.

1. A kapcsolat alapján megkeresheti az épületben található szinteket `contains` .

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. A szobák megkereséséhez ahelyett, hogy a padlót egy-egy, és egy lekérdezést futtasson, `JOIN` hogy megkeresse a szobák mindegyikét, lekérdezheti a padlók gyűjteményét (a lenti lekérdezésben a *padló* nevet).

    Az ügyfélalkalmazás alkalmazásban:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    A lekérdezésben:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Egyéb összetett lekérdezési példák

A fenti típusú lekérdezéseket **kombinálhatja** a kombinált operátorok használatával, így több részletet is megadhat egyetlen lekérdezésben. Íme néhány további példa az összetett lekérdezésekre, amelyek egyszerre több típusú Twin-leírót kérdeznek le.

* A 123-as *termetű* eszközökön a kezelő szerepkört kiszolgáló MxChip-eszközöket kell visszaadnia.
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Olyan ikrek beszerzése, amelyekben egy nevű kapcsolat *található* egy másik, *ID1* azonosítóval
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* A *floor11* által foglalt Room-modell összes szobájának beolvasása
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Lekérdezések futtatása az API-val

Ha úgy döntött, hogy egy lekérdezési karakterláncot használ, a [**lekérdezési API**](/rest/api/digital-twins/dataplane/query)meghívásával végrehajtja azt.

Az API-t közvetlenül is meghívhatja, vagy használhatja az Azure Digital Twins-hoz elérhető [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) -kat.

A következő kódrészlet az ügyfélalkalmazás [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) -hívását mutatja be:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Ez a hívás egy [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) objektum formájában adja vissza a lekérdezés eredményét.

A lekérdezési hívások támogatják a lapozást. Íme egy teljes példa a `BasicDigitalTwin` lekérdezési eredmény típusaként a hibakezelés és a lapozás használatával:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>További lépések

További információ az [Azure Digital Twins API-kkal és SDK](how-to-use-apis-sdks.md)-kkal kapcsolatban, beleértve a cikkből származó lekérdezések futtatásához használt LEKÉRDEZÉSi API-t.
