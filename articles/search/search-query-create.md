---
title: Alapszintű lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhat létre lekérdezési kéréseket a Cognitive Searchban, mely eszközök és API-k használhatók a teszteléshez és a kódoláshoz, és hogy a lekérdezési döntések hogyan kezdődnek az indexek
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371174"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Alapszintű lekérdezés létrehozása az Azure Cognitive Search

Ez a cikk a lekérdezés-fejlesztés lépéseit ismerteti. TÖBBek között a következőkben találhatók a karakterláncok másolása **a** portálon, vagy interaktív lekérdezéseket készíthet a Poster vagy a Visual Studio Code használatával. A jelen cikkben szereplő példákhoz a Cognitive Search bármely szintjét vagy verzióját használhatja.

## <a name="choose-a-tool-or-api"></a>Eszköz vagy API kiválasztása

A következő eszközök és API-k közül választhat a tesztelési vagy éles számítási feladatokhoz tartozó lekérdezések létrehozásához.

| Módszertan | Leírás |
|-------------|-------------|
| Portál| A [Search Explorer (portál)](search-explorer.md) az index és az API-verzió kiválasztására szolgáló keresési sávot és lehetőségeket biztosít. Az eredményeket JSON-dokumentumként adja vissza a rendszer. Korai vizsgálathoz, teszteléshez és érvényesítéshez ajánlott. <br/>[Részletek](search-explorer.md) |
| Webes tesztelési eszközök| A [Poster vagy a Visual Studio Code](search-get-started-rest.md) erős választás a [keresési dokumentumok](/rest/api/searchservice/search-documents) Rest-hívásainak megfogalmazásához. A REST API támogatja az Azure Cognitive Search minden programozott műveletét, így a kéréseket interaktív módon is kiállíthatja, hogy megtudja, hogyan működik a programkódba való befektetés előtt.  |
| Azure SDK | A [SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) a keresési indexek lekérdezésére használható a C#-ban.  [Részletek](search-howto-dotnet-sdk.md) <br/><br/>A [SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) használatával lekérdezhető a Pythonban egy keresési index. [Részletek](search-get-started-python.md) <br/><br/> A [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) használatával lekérdezhető a keresési index a JavaScriptben. [Részletek](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Keresési ügyfél beállítása

A keresési ügyfél hitelesíti a keresési szolgáltatást, küldi a kérelmeket, és kezeli a válaszokat. A lekérdezések mindig egyetlen index dokumentumainak gyűjteményére vannak irányítva. Az indexek nem csatlakoztathatók, és nem hozhatnak létre egyéni vagy ideiglenes adatstruktúrákat lekérdezési célként.

### <a name="in-the-portal"></a>A portálon

A keresési Explorer és egyéb portál eszközök beépített ügyfélkapcsolatot biztosítanak a szolgáltatáshoz, közvetlen hozzáférési indexekkel és más objektumokkal a portál oldalain. Az eszközökhöz, a varázslókhoz és az objektumokhoz való hozzáférés feltételezi, hogy rendszergazdai jogosultságokkal rendelkezik a szolgáltatásban. A Search Explorerben a keresési karakterlánc és más paraméterek megadására koncentrálhat. 

### <a name="using-rest"></a>A REST használata

REST-hívások esetén a [Poster vagy hasonló eszközöket](search-get-started-rest.md) használhatja az ügyfélként a [keresési dokumentumok](/rest/api/searchservice/search-documents) kérésének megadásához. Minden kérelem önálló, ezért meg kell adnia a végpontot (a szolgáltatás URL-címét) és egy rendszergazdai vagy lekérdezési API-kulcsot a hozzáféréshez. A kérelemtől függően előfordulhat, hogy az URL-cím tartalmazza az index nevét, a dokumentumok gyűjteményét és egyéb tulajdonságokat is. A kérelem fejlécében néhány tulajdonságot, például a Content-Type és az API-kulcsot adja át a rendszer. Az URL-címen vagy a kérelem törzsében más paraméterek adhatók át. Minden REST-híváshoz API-kulcs szükséges a hitelesítéshez és egy API-verzióhoz.

### <a name="using-azure-sdks"></a>Azure SDK-k használata

Az Azure SDK-k olyan keresési ügyfeleket biztosítanak, amelyek megtartják az állapotot, és lehetővé teszik a kapcsolatok ismételt A lekérdezési műveletek esetében egy SearchClient hoz létre, és a következő tulajdonságok értékeit adja meg: Endpoint, Key, index. Ezután meghívhatja a keresési módszert a lekérdezési karakterlánc megadására. 

| Nyelv | Ügyfél | Példa |
|----------|--------|---------|
| C# és .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Az első keresési lekérdezés küldése a C-ben #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Az első keresési lekérdezés küldése a Pythonban](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Az első keresési lekérdezés elküldése Java-ban](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Az első keresési lekérdezés küldése a JavaScriptben](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Válasszon elemzőt: Simple | teljes

Az Azure Cognitive Search két lekérdezési elemző közül választhat a tipikus és a speciális lekérdezések kezeléséhez. Az egyszerű elemzőt használó kérelmek általában teljes szöveges keresési lekérdezések, amelyek az [egyszerű lekérdezési szintaxis](query-simple-syntax.md)alapján lettek kiválasztva, és a sebesség és a hatékonyság az ingyenes szöveges lekérdezésekben. Ez a szintaxis számos gyakori keresési operátort támogat, többek között a következőt: és, vagy, nem, kifejezés, utótag és elsőbbségi operátor.

A [teljes Lucene lekérdezési szintaxis](query-Lucene-syntax.md#bkmk_syntax), amely a kérelemhez való hozzáadáskor engedélyezve van `queryType=full` , az [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)részeként kifejlesztett, széles körben elfogadott és kifejező lekérdezési nyelvet jeleníti meg. A teljes szintaxis kibővíti az egyszerű szintaxist. Az egyszerű szintaxishoz írt összes lekérdezés a teljes Lucene-elemző alatt fut. 

Az alábbi példák szemléltetik a pontot: ugyanaz a lekérdezés, de különböző **`queryType`** beállításokkal, amelyek különböző eredményeket eredményeznek. Az első lekérdezésben a `^3` rendszer a `historic` keresési kifejezés részeként kezeli a következőt:. A lekérdezés legfelső szintű eredménye a "Marquis Plaza & Suites", amely az *óceánt* is tartalmazta a leírásában.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

Ugyanaz a lekérdezés, amely a teljes Lucene elemzőt használja, az értelmezést `^3` a mezőre való emlékeztetőként. A váltási elemzők a rangot módosítják, és az olyan eredmények, amelyek a korábbi, a *régire* való áttérés kifejezését tartalmazzák.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Lekérdezési viselkedés engedélyezése egy indexben

Az index tervezési és lekérdezési kialakítása szorosan összekapcsolható az Azure Cognitive Searchban. Az *index séma*, amely az egyes mezők attribútumait határozza meg, meghatározza, hogy milyen típusú lekérdezést hozhat létre.

Egy mező indexelése az engedélyezett műveletekkel – azt határozza meg, hogy egy mező *kereshető* -e az indexben, lekérhető az *eredmények között,* *rendezhető*, *szűrhető* és így tovább. A példában szereplő lekérdezésekben `"$orderby": "Rating desc"` csak a működik, mert a minősítési mező az index sémában *rendezhető* van megjelölve.

![A szállodai minta index-definíciója](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta index-definíciója")

A fenti képernyőkép a [Hotels Sample index](search-get-started-portal.md)index-attribútumainak részleges listája. A teljes index sémát a portálon lehet létrehozni és megtekinteni. További információ az index attribútumairól: [create index (REST API)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette a kérés kialakításának módját, próbálja ki a példákat az egyszerű és a teljes szintaxis használatával.

+ [Példák egyszerű lekérdezésre](search-query-simple-examples.md)
+ [Példák a speciális lekérdezések kiépítési Lucene](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)