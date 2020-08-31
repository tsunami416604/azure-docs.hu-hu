---
title: Az ikergráf lekérdezése
titleSuffix: Azure Digital Twins
description: 'További információ: az Azure Digital Twins Twin Graph lekérdezése.'
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e6236d9ed5ed75b6b5e10914e668de545c48fc2c
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055634"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Az Azure Digital Twins Twin gráf lekérdezése

Ez a cikk példákat és részletesebb információkat tartalmaz az [Azure digitális Twins lekérdezési nyelvének](concepts-query-language.md) használatáról, hogy lekérdezze a [Twin gráfot](concepts-twins-graph.md) . A Graph-lekérdezéseket az Azure Digital Twins [**lekérdezési API**](how-to-use-apis-sdks.md)-k használatával futtathatja.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

A cikk további része példákat tartalmaz a műveletek használatára.

## <a name="query-syntax"></a>Lekérdezési szintaxis

Ez a szakasz olyan példákat tartalmaz, amelyek a lekérdezési nyelvi struktúrát illusztrálják, és a lehetséges lekérdezési műveleteket hajtják végre a [digitális Twins](concepts-twins-graph.md)-on.

### <a name="select-top-items"></a>Legfelső elemek kijelölése

A lekérdezésben több "Top" elemet is kijelölhet a `Select TOP` záradék használatával.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
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

Azt is megteheti, **hogy egy bizonyos tulajdonság definiálva van-e**. Itt látható egy olyan lekérdezés, amely a megadott *Location* tulajdonsággal rendelkező ikreket tartalmaz:

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Ez segít az ikreknek a *címke* tulajdonságai alapján való beszerzésében, a [Címkék hozzáadása a digitális ikrekhez](how-to-use-tags.md)című témakörben leírtak szerint. Itt látható egy olyan lekérdezés, amely a *vörös színnel*jelölt összes ikreket lekéri:

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Az ikreket **egy tulajdonság típusa**alapján is elérheti. Íme egy lekérdezés, amely olyan ikreket kap, amelyek *hőmérsékleti* tulajdonsága egy szám:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Lekérdezés modell szerint

Az `IS_OF_MODEL` operátor használható a Twin [**modell**](concepts-models.md)alapján történő szűrésre. Támogatja az öröklést, és számos túlterhelési lehetőséggel rendelkezik.

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

A digitális ikrek **kapcsolatain**alapuló lekérdezések esetén az Azure digitális Twins lekérdezési nyelvének speciális szintaxisa van.

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

Hasonlóan ahhoz, ahogy a digitális ikrek rendelkeznek a DTDL-on keresztül leírt tulajdonságokkal, a kapcsolatok is rendelkezhetnek tulajdonságokkal. Az ikreket **a kapcsolataik tulajdonságai alapján**kérdezheti le.
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

Jelenleg előzetes verzióban érhető el, legfeljebb öt `JOIN` s támogatott egyetlen lekérdezésben. Ez lehetővé teszi, hogy egyszerre több kapcsolati szintet is bejárjon.

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

| Leírás | Lekérdezés |
| --- | --- |
| A 123-as *termetű* eszközökön a kezelő szerepkört kiszolgáló MxChip-eszközöket kell visszaadnia. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Olyan ikrek beszerzése, amelyekben egy nevű kapcsolat *található* egy másik, *ID1* azonosítóval | `SELECT Room`<br>`FROM DIGITIALTWINS Room`<br>`JOIN Thermostat ON Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| A *floor11* által foglalt Room-modell összes szobájának beolvasása | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="run-queries-with-an-api-call"></a>Lekérdezések futtatása API-hívással

Ha úgy döntött, hogy egy lekérdezési karakterláncot használ, a **lekérdezési API**meghívásával végrehajtja azt.
A következő kódrészlet az ügyfélalkalmazás ezt a hívást mutatja be:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Ez a hívás egy QueryResult objektum formájában adja vissza a lekérdezés eredményét. 

A lekérdezési hívások támogatják a lapozást. Íme egy teljes példa a hibakezelés és a lapozásra:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Lekérdezési korlátozások

A példányban történt módosítások a lekérdezésekben akár 10 másodperces késéssel is rendelkezhetnek. Ha például olyan műveletet hajt végre, mint az ikrek létrehozása vagy törlése a DigitalTwins API-val, akkor előfordulhat, hogy az eredmény nem jelenik meg azonnal a lekérdezési API-kérelmekben. A rövid ideig tartó várakozásnak elegendőnek kell lennie a feloldáshoz.

Az előzetes verzió használatakor további korlátozások is rendelkezésre állnak `JOIN` .
* Az utasítás nem támogatja az allekérdezéseket `FROM` .
* `OUTER JOIN` a szemantika nem támogatott, ami azt jelenti, hogy ha a kapcsolat nulla rangú, akkor a rendszer a teljes "sort" kizárja a kimeneti eredményhalmaz alapján.
* Az előzetes verzióban a Graph bejárási mélysége `JOIN` lekérdezési szinten legfeljebb öt szintre van korlátozva.
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
