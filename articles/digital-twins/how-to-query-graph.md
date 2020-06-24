---
title: Az ikergráf lekérdezése
titleSuffix: Azure Digital Twins
description: 'További információ: az Azure Digital Twins Twin Graph lekérdezése.'
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 8788dcc1ec09580aa0da7c68665433dfe4a1d563
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725584"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Az Azure Digital Twins Twin gráf lekérdezése

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Ez a cikk részletesen bemutatja, hogyan használhatja az [Azure Digital Twins Query Store nyelvet](concepts-query-language.md) , hogy lekérdezze a [Twin Graphot](concepts-twins-graph.md) az információkhoz. A Graph-lekérdezéseket az Azure Digital Twins [**lekérdezési API**](how-to-use-apis-sdks.md)-k használatával futtathatja.

## <a name="query-syntax"></a>Lekérdezési szintaxis

Íme néhány példa a lekérdezés nyelvi felépítését bemutató példákra, és végrehajthatja a lehetséges lekérdezési műveleteket.

[Digitális ikrek](concepts-twins-graph.md) beolvasása tulajdonságok alapján (beleértve az azonosítót és a metaadatokat):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

Digitális ikrek beszerzése [modell](concepts-models.md) szerint
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> A Digital Twin AZONOSÍTÓját a rendszer a metaadatok mező használatával kérdezi le `$dtId` .

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

## <a name="query-based-on-relationships"></a>Lekérdezés kapcsolatok alapján

A digitális ikrek kapcsolatain alapuló lekérdezések esetén az Azure Digital Twins Query Store nyelvének speciális szintaxisa van.

A kapcsolatok a záradék lekérdezési hatókörében vannak behúzva `FROM` . A "klasszikus" SQL-Type nyelvek egyik fontos különbsége, hogy az ebben a `FROM` záradékban szereplő kifejezések nem táblázatos jellegűek, a záradék pedig az `FROM` entitások közötti kapcsolat bejárását fejezi ki, és a (z) Azure digitális Twins-verziójával van írva `JOIN` . 

Ne felejtse el, hogy az Azure Digital Twins [modell](concepts-models.md) képességeivel a kapcsolatok nem léteznek az ikrektől függetlenül. Ez azt jelenti, hogy az Azure Digital Twins Query Store nyelve `JOIN` egy kicsit eltér az általános SQL `JOIN` -től, mivel itt nem lehet egymástól függetlenül lekérdezni a kapcsolatokat, és egy Twin-hez kell kötni.
A különbség befoglalásához a kulcsszó a `RELATED` `JOIN` kettős kapcsolatokra mutató hivatkozásokat használja a záradékban. 

A következő szakasz több példát mutat be, hogy ez hogyan néz ki.

> [!TIP]
> Ez a funkció elméletileg a CosmosDB dokumentum-központú funkcióit utánozza, ahol a `JOIN` dokumentumon belüli gyermekobjektumok is elvégezhetők. A CosmosDB a `IN` kulcsszó használatával jelzi, hogy a az `JOIN` aktuális környezeti dokumentumban lévő tömb elemeinek megismétlésére szolgál.

### <a name="relationship-based-query-examples"></a>Kapcsolaton alapuló lekérdezési példák

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

### <a name="query-the-properties-of-a-relationship"></a>Kapcsolat tulajdonságainak lekérdezése

Hasonlóan ahhoz, ahogy a digitális ikrek rendelkeznek a DTDL-on keresztül leírt tulajdonságokkal, a kapcsolatok is rendelkezhetnek tulajdonságokkal. Az Azure Digital Twins Query Store nyelve lehetővé teszi a kapcsolatok szűrését és kivetítését azáltal, hogy a záradékon belül egy aliast rendel hozzá a kapcsolathoz `JOIN` . 

Példaként vegyünk egy olyan *servicedBy* -kapcsolatot, amely *reportedCondition* tulajdonsággal rendelkezik. Az alábbi lekérdezésben ez a kapcsolat az "R" aliast kapja, hogy hivatkozzon a tulajdonságára.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

A fenti példában látható, hogy a *reportedCondition* a *servicedBy* -kapcsolat egyik tulajdonsága (nem pedig egy olyan digitális iker, amely *servicedBy* -kapcsolattal rendelkezik).

### <a name="query-limitations"></a>Lekérdezési korlátozások

A példányban történt módosítások a lekérdezésekben akár 10 másodperces késéssel is rendelkezhetnek. Ha például olyan műveletet hajt végre, mint az ikrek létrehozása vagy törlése a DigitalTwins API-val, akkor előfordulhat, hogy az eredmény nem jelenik meg azonnal a lekérdezési API-kérelmekben. A rövid ideig tartó várakozásnak elegendőnek kell lennie a feloldáshoz.

Az előzetes verzió használatakor további korlátozások is rendelkezésre állnak `JOIN` .
* Az utasítás nem támogatja az allekérdezéseket `FROM` .
* `OUTER JOIN`a szemantika nem támogatott, ami azt jelenti, hogy ha a kapcsolat nulla rangú, akkor a rendszer a teljes "sort" kizárja a kimeneti eredményhalmaz alapján.
* A nyilvános előzetes verzióban a Graph bejárási mélysége korlátozott: a `JOIN` lekérdezésekben csak egy engedélyezett.
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


## <a name="next-steps"></a>További lépések

Tudjon meg többet az [Azure Digital Twins API-król és SDK](how-to-use-apis-sdks.md)-król, beleértve a LEKÉRDEZÉSi API-t, amely a jelen cikkben található lekérdezések futtatására szolgál.
