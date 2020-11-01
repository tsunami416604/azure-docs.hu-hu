---
title: Az ikergráf lekérdezése
titleSuffix: Azure Digital Twins
description: 'További információ: az Azure Digital Twins Twin Graph lekérdezése.'
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8aad0d9fde30a235903364d57a73c1c53f08ecce
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145786"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Az Azure Digital Twins Twin gráf lekérdezése

Ez a cikk példákat és részletesebb információkat tartalmaz az [Azure digitális Twins lekérdezési nyelvének](concepts-query-language.md) használatáról, hogy lekérdezze a [Twin gráfot](concepts-twins-graph.md) . A Graph-lekérdezéseket az Azure Digital Twins [**lekérdezési API**](/rest/api/digital-twins/dataplane/query)-k használatával futtathatja.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

A cikk további része példákat tartalmaz a műveletek használatára.

## <a name="query-syntax"></a>Lekérdezési szintaxis

Ez a szakasz olyan példákat tartalmaz, amelyek a lekérdezési nyelvi struktúrát illusztrálják, és a lehetséges lekérdezési műveleteket hajtják végre a [digitális Twins](concepts-twins-graph.md)-on.

### <a name="show-all-existing-digital-twins"></a>Az összes meglévő digitális ikrek megjelenítése

Itt látható az alapszintű lekérdezés, amely a példányban található összes digitális ikrek listáját fogja visszaadni:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Legfelső elemek kijelölése

A lekérdezésben több "Top" elemet is kijelölhet a `Select TOP` záradék használatával.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Elemek darabszáma

Az eredményhalmaz elemeinek számát a záradék használatával számolhatja el `Select COUNT` :

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Adjon hozzá egy `WHERE` záradékot az adott feltételeknek megfelelő elemek számának megszámlálásához. Íme néhány példa az alkalmazott szűrővel való számlálásra az iker modell típusa alapján (a szintaxissal kapcsolatban bővebben lásd az alábbi [*modell szerinti lekérdezést*](#query-by-model) ):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Emellett `COUNT` a záradékkal együtt is használható `JOIN` . Itt látható egy olyan lekérdezés, amely az 1. és 2. szoba világos panelén található összes villanykörtét számlálja:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="specify-return-set-with-projections"></a>Visszatérési készlet megadása vetítésekkel

A kivetítések használatával kiválaszthatja, hogy a lekérdezés mely oszlopokat adja vissza.

>[!NOTE]
>Jelenleg az összetett tulajdonságok nem támogatottak. Annak ellenőrzéséhez, hogy a leképezési tulajdonságok érvényesek-e, kombinálja a kivetítéseket egy `IS_PRIMITIVE` ellenőrzéssel.

Íme egy példa egy olyan lekérdezésre, amely kivetítést használ az ikrek és a kapcsolatok visszaküldéséhez. A következő lekérdezés a *fogyasztót* , a *gyárat* és az *Edge* -t olyan forgatókönyv alapján tervezi meg, ahol az *ABC* azonosítóval rendelkező *gyár* a *Factory. Customer* kapcsolaton keresztül kapcsolódik a *fogyasztóhoz* , és ez a kapcsolat a *peremhálózat* .

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

A kivetítés használatával a Twin tulajdonságot is visszaállíthatja. A következő lekérdezés a Gyárhoz kapcsolódó *felhasználók* *név* TULAJDONSÁGát az *ABC* azonosítóval *együtt a* *Factory. Customer* kapcsolaton keresztül.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

A kivetítés használatával egy kapcsolat tulajdonságát is visszaadhatja. Ahogy az előző példában is látható, a következő lekérdezés a Gyárhoz kapcsolódó *felhasználók* *Name (név* ) tulajdonságát az *ABC* azonosítójával együtt a *Factory. Customer* kapcsolaton keresztül. *Factory* Most azonban a kapcsolat két tulajdonságát is visszaadja, a *prop1* és a *prop2* . Ez a kapcsolati *Szegély* elnevezésével és a tulajdonságainak összegyűjtésével végezhető el.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Aliasokat is használhat a kivetítéssel való lekérdezések egyszerűsítéséhez.

A következő lekérdezés ugyanazokat a műveleteket végzi el, mint az előző példában, de az a, a, a és a tulajdonság nevét aliasként nevezi el `consumerName` `first` `second` `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)"
```

Íme egy hasonló lekérdezés, amely ugyanazt a készletet kérdezi le, de a projektek csak a *Consumer.name* tulajdonságot `consumerName` , a teljes *gyárat* pedig Twin-ként tervezik.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

### <a name="query-by-property"></a>Lekérdezés tulajdonság szerint

Digitális ikrek beolvasása **Tulajdonságok** alapján (beleértve az azonosítót és a metaadatokat):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> A Digital Twin AZONOSÍTÓját a rendszer a metaadatok mező használatával kérdezi le `$dtId` .

Azt is megteheti, **hogy egy bizonyos tulajdonság definiálva van-e** . Itt látható egy olyan lekérdezés, amely a megadott *Location* tulajdonsággal rendelkező ikreket tartalmaz:

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Ez segít az ikreknek a *címke* tulajdonságai alapján való beszerzésében, a [Címkék hozzáadása a digitális ikrekhez](how-to-use-tags.md)című témakörben leírtak szerint. Itt látható egy olyan lekérdezés, amely a *vörös színnel* jelölt összes ikreket lekéri:

```sql
select * from digitaltwins where is_defined(tags.red)
```

Az ikreket **egy tulajdonság típusa** alapján is elérheti. Íme egy lekérdezés, amely olyan ikreket kap, amelyek *hőmérsékleti* tulajdonsága egy szám:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Lekérdezés modell szerint

Az `IS_OF_MODEL` operátor használható a Twin [**modell**](concepts-models.md)alapján történő szűrésre.

Figyelembe veszi az [öröklési](concepts-models.md#model-inheritance) és a [verziószám-sorrendet](how-to-manage-model.md#update-models) , és kiértékeli az **igaz** értéket egy adott Twin esetében, ha a Twin megfelel a következő feltételek egyikének:

* A Twin közvetlenül megvalósítja a által biztosított modellt `IS_OF_MODEL()` , a modell verziószáma pedig *nagyobb vagy egyenlő, mint* a megadott modell verziószáma.
* A Twin olyan modellt valósít meg, amely *kibővíti* a által biztosított modellt `IS_OF_MODEL()` , és a Twin modell verziószáma *nagyobb vagy egyenlő, mint* a megadott modell verziószáma.

Ez a metódus több túlterhelési lehetőséggel rendelkezik.

A legegyszerűbb használata `IS_OF_MODEL` csak a (z `twinTypeName` ) paramétert veszi igénybe: `IS_OF_MODEL(twinTypeName)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét továbbítja:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Ha egynél több (például a használatban lévő) értéknél szeretne keresni egy dupla gyűjteményt `JOIN` , adja hozzá a következő `twinCollection` paramétert: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét adja meg:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

A pontos egyezéshez adja hozzá a következő `exact` paramétert: `IS_OF_MODEL(twinTypeName, exact)` .
Az alábbi példa egy olyan lekérdezési példát mutat be, amely a paraméter értékét adja meg:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Mindhárom argumentumot együtt is át lehet adni: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Íme egy lekérdezési példa, amely mindhárom paraméter értékét megadja:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Lekérdezés kapcsolatok alapján

A digitális ikrek **kapcsolatain** alapuló lekérdezések esetén az Azure digitális Twins lekérdezési nyelvének speciális szintaxisa van.

A kapcsolatok a záradék lekérdezési hatókörében vannak behúzva `FROM` . A "klasszikus" SQL-Type nyelvek egyik fontos különbsége, hogy az ebben a `FROM` záradékban szereplő kifejezések nem táblázatos jellegűek, a záradék pedig az `FROM` entitások közötti kapcsolat bejárását fejezi ki, és a (z) Azure digitális Twins-verziójával van írva `JOIN` .

Ne felejtse el, hogy az Azure Digital Twins [modell](concepts-models.md) képességeivel a kapcsolatok nem léteznek az ikrektől függetlenül. Ez azt jelenti, hogy az Azure Digital Twins lekérdezési nyelve `JOIN` egy kicsit eltér az általános SQL `JOIN` -től, mivel itt nem lehet egymástól függetlenül lekérdezni a kapcsolatokat, és egy Twin-hez kell kötni.
A különbség befoglalásához a kulcsszó a `RELATED` `JOIN` kettős kapcsolatokra mutató hivatkozásokat használja a záradékban.

A következő szakasz több példát mutat be, hogy ez hogyan néz ki.

> [!TIP]
> Ez a funkció elméletileg a CosmosDB dokumentum-központú funkcióit utánozza, ahol a `JOIN` dokumentumon belüli gyermekobjektumok is elvégezhetők. A CosmosDB a `IN` kulcsszó használatával jelzi, hogy a az `JOIN` aktuális környezeti dokumentumban lévő tömb elemeinek megismétlésére szolgál.

#### <a name="relationship-based-query-examples"></a>Kapcsolaton alapuló lekérdezési példák

Ha olyan adatkészletet szeretne beolvasni, amely kapcsolatokat tartalmaz, használjon egyetlen `FROM` utasítást, amelyet N utasítások követnek `JOIN` , ahol a `JOIN` utasítások egy korábbi vagy egy utasítás eredményéhez kapcsolódnak `FROM` `JOIN` .

Példa a kapcsolaton alapuló lekérdezésre. Ez a kódrészlet kiválasztja az összes olyan digitális ikreket, amelynek *ID* tulajdonsága "ABC", és az ezen digitális ikrekhez kapcsolódó összes digitális ikrek a következő kapcsolaton keresztül *szerepelnek* .

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

>[!NOTE]
> A fejlesztőnek nem kell `JOIN` összekapcsolnia ezt a záradékban található kulcs értékével `WHERE` (vagy a definícióban szereplő kulcs értékének megadásával `JOIN` ). Ezt a korrelációt a rendszer automatikusan kiszámítja, mivel a kapcsolat tulajdonságai maguk határozzák meg a célként megadott entitást.

#### <a name="query-the-properties-of-a-relationship"></a>Kapcsolat tulajdonságainak lekérdezése

Hasonlóan ahhoz, ahogy a digitális ikrek rendelkeznek a DTDL-on keresztül leírt tulajdonságokkal, a kapcsolatok is rendelkezhetnek tulajdonságokkal. Az ikreket **a kapcsolataik tulajdonságai alapján** kérdezheti le.
Az Azure digitális Twins lekérdezési nyelve lehetővé teszi a kapcsolatok szűrését és kivetítését azáltal, hogy a záradékon belül egy aliast rendel a kapcsolathoz `JOIN` .

Példaként vegyünk egy olyan *servicedBy* -kapcsolatot, amely *reportedCondition* tulajdonsággal rendelkezik. Az alábbi lekérdezésben ez a kapcsolat az "R" aliast kapja, hogy hivatkozzon a tulajdonságára.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

A fenti példában látható, hogy a *reportedCondition* a *servicedBy* -kapcsolat egyik tulajdonsága (nem pedig egy olyan digitális iker, amely *servicedBy* -kapcsolattal rendelkezik).

### <a name="query-with-multiple-joins"></a>Lekérdezés több illesztéssel

Legfeljebb öt `JOIN` s támogatott egyetlen lekérdezésben. Ez lehetővé teszi, hogy egyszerre több kapcsolati szintet is bejárjon.

Íme egy példa egy többcsatlakozásos lekérdezésre, amely az 1. és 2. helyiségekben található világos panelek összes izzóját lekéri.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="other-compound-query-examples"></a>Egyéb összetett lekérdezési példák

A fenti típusú lekérdezéseket **kombinálhatja** a kombinált operátorok használatával, így több részletet is megadhat egyetlen lekérdezésben. Íme néhány további példa az összetett lekérdezésekre, amelyek egyszerre több típusú Twin-leírót kérdeznek le.

| Description | Lekérdezés |
| --- | --- |
| A 123-as *termetű* eszközökön a kezelő szerepkört kiszolgáló MxChip-eszközöket kell visszaadnia. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Olyan ikrek beszerzése, amelyekben egy nevű kapcsolat *található* egy másik, *ID1* azonosítóval | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| A *floor11* által foglalt Room-modell összes szobájának beolvasása | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Hivatkozás: kifejezések és kikötések

Ez a szakasz az Azure Digital Twins-lekérdezések írásakor elérhető operátorokra és funkciókra vonatkozó referenciákat tartalmaz.

### <a name="operators"></a>Operátorok

A következő operátorok támogatottak:

| Family (Család) | Operátorok |
| --- | --- |
| Logikai |ÉS, VAGY NEM |
| Összehasonlítás |=,! =, <, >, <=, >= |
| Contains | , NIN |

### <a name="functions"></a>Függvények

A következő típusú ellenőrzési és öntési függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| IS_DEFINED | Egy logikai értéket ad vissza, amely azt jelzi, hogy a tulajdonsághoz hozzá van-e rendelve érték. Ez csak akkor támogatott, ha az érték egy egyszerű típus. Az egyszerű típusok például a következők: string, Boolean, numerikus vagy `null` . A DateTime, az Objektumtípusok és a tömbök nem támogatottak. |
| IS_OF_MODEL | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott iker megfelel-e a megadott modell típusának. |
| IS_BOOL | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa logikai. |
| IS_NUMBER | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa szám-e. |
| IS_STRING | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa sztring-e. |
| IS_NULL | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa NULL-e. |
| IS_PRIMITIVE | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa primitív (karakterlánc, logikai, numerikus vagy `null` ). |
| IS_OBJECT | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa JSON-objektum-e. |

A következő karakterlánc-függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| STARTSWITH (x, y) | Olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikval kezdődik-e. |
| ENDSWITH (x, y) | Egy olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc kifejezése a másodperctel végződik-e |

## <a name="run-queries-with-an-api-call"></a>Lekérdezések futtatása API-hívással

Ha úgy döntött, hogy egy lekérdezési karakterláncot használ, a **lekérdezési API** meghívásával végrehajtja azt.
A következő kódrészlet az ügyfélalkalmazás ezt a hívást mutatja be:

```csharp

var adtInstanceEndpoint = new Uri(your-Azure-Digital-Twins-instance-URL>);
var tokenCredential = new DefaultAzureCredential();

var client = new DigitalTwinsClient(adtInstanceEndpoint, tokenCredential);

string query = "SELECT * FROM digitaltwins";
AsyncPageable<string> result = await client.QueryAsync<string>(query);
```

Ez a hívás egy karakterlánc-objektum formájában adja vissza a lekérdezés eredményét.

A lekérdezési hívások támogatják a lapozást. Íme egy teljes példa a `BasicDigitalTwin` lekérdezési eredmény típusaként a hibakezelés és a lapozás használatával:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<BasicDigitalTwin> qresult = client.QueryAsync<BasicDigitalTwin>(query);
    await foreach (BasicDigitalTwin item in qresult)
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="query-limitations"></a>Lekérdezési korlátozások

A példányban történt módosítások a lekérdezésekben akár 10 másodperces késéssel is rendelkezhetnek. Ha például olyan műveletet hajt végre, mint az ikrek létrehozása vagy törlése a DigitalTwins API-val, akkor előfordulhat, hogy az eredmény nem jelenik meg azonnal a lekérdezési API-kérelmekben. A rövid ideig tartó várakozásnak elegendőnek kell lennie a feloldáshoz.

A használatának további korlátai vannak `JOIN` .

* Az utasítás nem támogatja az allekérdezéseket `FROM` .
* `OUTER JOIN` a szemantika nem támogatott, ami azt jelenti, hogy ha a kapcsolat nulla rangú, akkor a rendszer a teljes "sort" kizárja a kimeneti eredményhalmaz alapján.
* A gráf bejárási mélysége `JOIN` lekérdezési szintenként legfeljebb öt szintre van korlátozva.
* A műveletek forrása `JOIN` korlátozott: a lekérdezésnek deklarálnia kell azokat az ikreket, amelyeken a lekérdezés elindul.

## <a name="query-best-practices"></a>Ajánlott eljárások lekérdezésekhez

Az alábbiakban néhány tippet talál az Azure digitális Twins lekérdezéséhez.

* A modell tervezési fázisában vegye figyelembe a lekérdezési mintát. Győződjön meg arról, hogy az egyetlen lekérdezésben megválaszolni kívánt kapcsolatok egy egyszintű kapcsolatként vannak modellezve.
* Tervezze meg a tulajdonságokat úgy, hogy elkerülje a diagram bejárásának nagy eredményeinek elkerülését.
* Jelentősen csökkentheti a szükséges lekérdezések számát az ikrek tömbjét kiépítve és az operátorral történő lekérdezéssel `IN` . Vegyünk például egy olyan forgatókönyvet, amelyben *Buildings* az épületek *padlót* és *padlót* *tartalmaznak.* A gyakran használt épületben található szobák kereséséhez a következőket teheti:

    1. Az épületben található emeletek keresése `contains` kapcsolat alapján

        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ```

    2. A szobák megkereséséhez ahelyett, hogy a padlót egy-egy, és egy lekérdezést futtasson, `JOIN` hogy megkeresse a szobák mindegyikét, lekérdezheti a padlók gyűjteményét (a lenti lekérdezésben a *padló* nevet).

        Az ügyfélalkalmazás alkalmazásban:

        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```

        A lekérdezésben:

        ```sql

        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')

        ```

* A tulajdonságok nevei és értékei megkülönböztetik a kis-és nagybetűket, ezért ügyeljen a modellekben definiált pontos nevek használatára. Ha a tulajdonságok neve hibásan van megadva vagy helytelenül van megadva, az eredményhalmaz üres, és nem ad vissza hibát.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet az [Azure Digital Twins API-król és SDK](how-to-use-apis-sdks.md)-król, beleértve a LEKÉRDEZÉSi API-t, amely a jelen cikkben található lekérdezések futtatására szolgál.
