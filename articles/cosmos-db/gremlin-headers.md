---
title: Az Azure Cosmos DB Gremlin válaszfejlécei
description: A kiszolgálóválasz metaadatainak referenciadokumentációja, amely további hibaelhárítást tesz lehetővé
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755082"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin kiszolgálóválasz-fejlécek
Ez a cikk azokat a fejléceket ismerteti, amelyeket a Cosmos DB Gremlin-kiszolgáló visszaküld a hívónak a kérelmek végrehajtásakor. Ezek a fejlécek segítenek a kérelmek teljesítményének hibaelhárításában, a Cosmos DB szolgáltatással natív módon integrálható alkalmazások létrehozásában és az ügyfélszolgálat egyszerűsítésében.

Ne feledje, hogy a fejlécek függőségének figyelembe vétele korlátozza az alkalmazás hordozhatóságát más Gremlin implementációkra. Cserébe szorosabb integrációt kap a Cosmos DB Gremlinnel. Ezek a fejlécek nem TinkerPop szabvány.

## <a name="headers"></a>Fejlécek

| Fejléc | Típus | Mintaértéke | Ha tartalmazza | Magyarázat |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Sikeres és sikertelen művelet-végrehajtási trendek | A részleges válaszüzenet [kérésegységeiben (RU/s vagy VRu-kban)](request-units.md) felhasznált begyűjtési vagy adatbázis-átviteli sebesség mennyisége. Ez a fejléc minden folytatási kérelmek, amelyek több adattömbök. Ez tükrözi a díjat egy adott válasz darab. Csak az egyetlen választömbből álló kérelmek esetében ez a fejléc megegyezik a bejárás teljes költségével. Az összetett bejárások többségénél azonban ez az érték részleges költséget jelent. |
| **x-ms-total-request-charge** | double | 423.987 | Sikeres és sikertelen művelet-végrehajtási trendek | A [kérelemegységekben (RU/s vagy VRu-kban)](request-units.md) felhasznált begyűjtési vagy adatbázis-átviteli érték mennyisége a teljes kérelemre vonatkozóan. Ez a fejléc minden folytatási kérelmek, amelyek több adattömbök. A kérelem kezdete óta halmozott díjat jelöl. A fejléc értéke az utolsó adattömbben a teljes kérelemköltség-terhelést jelzi. |
| **x-ms-szerver-time-ms** | double | 13.75 | Sikeres és sikertelen művelet-végrehajtási trendek | Ez a fejléc a késés hibaelhárítási célokat szolgál. Azt jelzi, hogy a Cosmos DB Gremlin kiszolgáló mennyi időt töltött ezredmásodpercben, és részleges válaszüzenetet hozott létre. A fejléc értékének használata és összehasonlítása a teljes kérelem késési alkalmazásokkal kiszámíthatja a hálózati késés terhelését. |
| **x-ms-total-server-time-ms** | double | 130.512 | Sikeres és sikertelen művelet-végrehajtási trendek | A Cosmos DB Gremlin kiszolgáló által a teljes bejárás végrehajtásához szükséges teljes idő ezredmásodpercben. Ez a fejléc minden részleges válaszban szerepel. A kérelem kezdete óta halmozott végrehajtási időt jelöl. Az utolsó válasz a teljes végrehajtási időt jelzi. Ez a fejléc az ügyfél és a kiszolgáló késésforrásként való megkülönböztetéséhez hasznos. Összehasonlíthatja az ügyfél bejárási végrehajtási idejét a fejléc értékével. |
| **x-ms-status-code** | long | 200 | Sikeres és sikertelen művelet-végrehajtási trendek | A fejléc a kérelem befejezésének vagy befejezésének belső okát jelzi. Az alkalmazásnak azt tanácsoljuk, hogy nézze meg a fejléc értékét, és tegyen korrekciós intézkedéseket. |
| **x-ms-alstátusz-kód** | long | 1003 | Csak a hiba | A Cosmos DB egy többmodelles adatbázis, amely az egyesített tárolási rétegre épül. Ez a fejléc további elemzési adatokat tartalmaz a hiba oka, ha hiba történik az alacsonyabb rétegek magas rendelkezésre állású verem. Az alkalmazás nak azt tanácsoljuk, hogy tárolja ezt a fejlécet, és használja azt, amikor kapcsolatba lép a Cosmos DB ügyfélszolgálatával. A fejléc értéke hasznos cosmos DB mérnök gyors hibaelhárítás. |
| **x-ms-retry-after-ms** | karakterlánc (TimeSpan) | "00:00:03.9500000" | Csak a hiba | Ez a fejléc .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) típusú karakterlánc-ábrázolás. Ez az érték csak akkor lesz benne a kérelmekben sikertelen ített kiépített átviteli kimerülés. A kérelemnek az utasításra adott idő elteltével újra be kell nyújtania a bejárást. |
| **x-ms-activity-id** | karakterlánc (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Sikeres és sikertelen művelet-végrehajtási trendek | A fejléc egy kérelem egyedi kiszolgálóoldali azonosítóját tartalmazza. A kiszolgáló minden kéréshez egyedi azonosítót rendel nyomon követés céljából. Az alkalmazásoknak naplózniuk kell a kiszolgáló által visszaadott tevékenységazonosítókat azon kérések esetén, amelyekről az ügyfelek kapcsolatba kívánnak lépni az ügyfélszolgálattal. A Cosmos DB támogatási személyzete ezek az azonosítók által adott kérelmeket találhat a Cosmos DB szolgáltatás telemetriában. |

## <a name="status-codes"></a>Állapotkódok

A kiszolgáló által visszaadott leggyakoribb állapotkódok az alábbiakban találhatók.

| status | Magyarázat |
| --- | --- |
| **401** | Hibaüzenet `"Unauthorized: Invalid credentials provided"` jelenik meg, ha a hitelesítési jelszó nem egyezik a Cosmos DB-fiók kulcsával. Keresse meg a Cosmos DB Gremlin-fiókját az Azure Portalon, és ellenőrizze, hogy a kulcs helyes-e.|
| **404** | Egyidejű műveletek, amelyek egyszerre próbálják törölni és frissíteni ugyanazt a szegélyt vagy csúcspontot. Az `"Owner resource does not exist"` (Tulajdonos-erőforrás nem létezik) hibaüzenet azt jelzi, hogy a kapcsolati paraméterekben `/dbs/<database name>/colls/<collection or graph name>` formátumban megadott adatbázis vagy gyűjtemény helytelen.|
| **408** | `"Server timeout"`azt jelzi, hogy a bejárás több mint **30 másodpercet** vett igénybe, és a kiszolgáló megszakította. Optimalizálja a bejárásokat, hogy gyorsan fusson a csúcsok vagy élek szűrésével a bejárás minden ugrásán a keresési hatókör szűkítéséhez.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Ez általában akkor fordul elő, ha a gráfban már létezik egy adott azonosítójú csúcspont vagy él.| 
| **412** | Az állapotkódot hibaüzenet `"PreconditionFailedException": One of the specified pre-condition is not met`egészíti ki. Ez a hiba azt jelzi, hogy egy él vagy csúcspont olvasása és a módosítás után visszaírás a tárolóba optimista egyidejűség-vezérlési megsértése van. A hiba leggyakoribb előfordulása például `g.V('identifier').property('name','value')`a tulajdonság módosítása. A Gremlin motor elolvasta a csúcspontot, módosította, és visszaírta. Ha egy másik, párhuzamosan futó bejárás ugyanazon csúcspontot vagy élet próbálja meg írni, az egyik ezt a hibaüzenetet kapja. Az alkalmazásnak újra be kell nyújtania a bejárást a kiszolgálónak.| 
| **429** | A kérelem szabályozása volt, és az **x-ms-retry-after-ms** érték után újra meg kell próbálni| 
| **500** | Ha egy hibaüzenetben megtalálható a(z) `"NotFoundException: Entity with the specified id does not exist in the system."`, az azt jelzi, hogy egy adatbázis és/vagy gyűjtemény újra létre lett hozva ugyanazzal a névvel. Ez a hiba 5 percen belül eltűnik, mivel a változás propagálja és érvényteleníti a különböző Cosmos DB-összetevők gyorsítótárait. A hiba elkerüléséhez minden alkalommal egyedi adatbázis- és gyűjteményneveket használjon.| 
| **1000** | Ez az állapotkód akkor jelenik meg, ha a kiszolgáló sikeresen elemezt egy üzenetet, de nem sikerült végrehajtani. Ez általában a lekérdezéssel kapcsolatos problémára utal.| 
| **1001** | Ezt a kódot a rendszer akkor adja vissza, amikor a kiszolgáló befejezi a sokszögelésvégrehajtását, de nem szerializálja a választ az ügyfélnek. Ez a hiba akkor fordulhat elő, ha a bejárás összetett eredményt hoz létre, amely túl nagy, vagy nem felel meg a TinkerPop protokoll specifikációjának. Az alkalmazásnak egyszerűsítenie kell a bejárást, amikor ezt a hibát észleli. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`ha a bejárás túllépi az engedélyezett memóriakorlátot, a rendszer visszaadja. A memóriakorlát bejárásonként **2 GB.**| 
| **1004** | Ez az állapotkód hibásan formázott gráfkérelmet jelez. A kérelem hibásan formázható, ha nem sikerül deszerializálása, nem értéktípus deszerializálása értéktípusként vagy nem támogatott gremlin művelet kérésként. Az alkalmazás ne próbálkozzon újra a kéréssel, mert az nem lesz sikeres. | 
| **1007** | Ezt az állapotkódot általában `"Could not process request. Underlying connection has been closed."`hibaüzenet teljében adja vissza. Ez a helyzet akkor fordulhat elő, ha az ügyfél-illesztőprogram olyan kapcsolatot próbál használni, amelyet a kiszolgáló bezár. Az alkalmazásnak újra meg kell próbálnia a bejárást egy másik kapcsolaton.
| **1008** | Cosmos DB Gremlin kiszolgáló megszüntetheti a kapcsolatokat a fürt forgalmának egyensúlyának helyreállítása érdekében. Az ügyfél-illesztőprogramok kezelni ük kell ezt a helyzetet, és csak élő kapcsolatokat kell használniuk a kérelmek kiszolgálóra küldéséhez. Előfordulhat, hogy az ügyfél-illesztőprogramok nem észlelik, hogy a kapcsolat megszakadt. Ha az alkalmazás hibát `"Connection is too busy. Please retry after sometime or open more connections."` észlel, újra meg kell próbálnia a bejárást egy másik kapcsolaton.

## <a name="samples"></a>Példák

Egy Gremlin.Net alapuló mintaügyfél-alkalmazás, amely egy állapotattribútumot olvas be:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Egy példa, amely bemutatja, hogyan kell olvasni status attribútum Gremlin java kliens:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>További lépések
* [AZ Azure Cosmos DB HTTP-állapotkódjai](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Gyakori Azure Cosmos DB REST válaszfejlécek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [A TinkerPop Graph illesztőprogram-szolgáltatóinak követelményei]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
