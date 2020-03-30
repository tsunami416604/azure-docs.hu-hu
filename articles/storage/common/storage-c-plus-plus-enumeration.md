---
title: Az Azure Storage-erőforrások listázása C++ ügyféltárral
description: Megtudhatja, hogy a C++ verzióhoz való microsoft Azure Storage ügyfélkódtárban található listázási API-k használatával felsorolhat tárolókat, blobokat, várólistákat, táblákat és entitásokat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74269450"
---
# <a name="list-azure-storage-resources-in-c"></a>Az Azure Storage-erőforrások listázása C++ nyelven

A listázási műveletek számos fejlesztési forgatókönyv kulcsai az Azure Storage-szal. Ez a cikk azt ismerteti, hogyan lehet a leghatékonyabban számba venni az objektumokat az Azure Storage-ban a Microsoft Azure Storage C++ ügyfélkódtárjában megadott listázási API-k használatával.

> [!NOTE]
> Ez az útmutató az Azure Storage Client Library for C++ 2.x verzióját célozza meg, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [GitHub](https://github.com/Azure/azure-storage-cpp)rendszeren keresztül érhető el.

A storage-ügyfélkódtár számos módszert biztosít az Azure Storage objektumainak listázásához vagy lekérdezéséhez. Ez a cikk a következő eseteket ismerteti:

* Tárolók listázása fiókban
* Blobok listázása tárolóban vagy virtuális blobkönyvtárban
* Várólisták listázása egy fiókban
* Táblák listázása egy fiókban
* Tábla entitások lekérdezése

Ezek a módszerek mindegyike különböző túlterhelések használatával jelenik meg a különböző forgatókönyvekhez.

## <a name="asynchronous-versus-synchronous"></a>Aszinkron és szinkron

Mivel a C++ tár tárolóügyfél-könyvtára a [C++ REST könyvtárra](https://github.com/Microsoft/cpprestsdk)épül, a [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html)használatával eredendően támogatjuk az aszinkron műveleteket. Példa:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

A szinkron műveletek a megfelelő aszinkron műveleteket lezárják:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Ha több szálas alkalmazással vagy szolgáltatással dolgozik, azt javasoljuk, hogy az async API-kat közvetlenül használja ahelyett, hogy egy szálat hozna létre a szinkronizálási API-k hívásához, ami jelentősen befolyásolja a teljesítményt.

## <a name="segmented-listing"></a>Szegmentált lista

A felhőalapú tárolás méretezése szegmentált adatsorba van szükség. Például több mint egymillió blobot egy Azure blob tárolóban vagy több mint egy milliárd entitásegy Azure-táblázatban rendelkezhet. Ezek nem elméleti számok, hanem valós ügyfélhasználati esetek.

Ezért nem célszerű az összes objektumot egyetlen válaszban felsorolni. Ehelyett az objektumokat lapozással is felsorolhatja. A listaelem API-k mindegyike *szegmentált túlterheléssel* rendelkezik.

A szegmentált tőzsdei műveletre adott válasz a következőket tartalmazza:

* *_segment*, amely a lista-API egyetlen hívásához visszaadott eredményekkészletét tartalmazza.
* *continuation_token*, amely et a következő híváshoz továbbítja, hogy megkapja az eredmények következő oldalát. Ha nincs több visszaadandó eredmény, a folytatási jogkivonat null értékű.

Például egy tipikus hívás, hogy egy tárolóban az összes blobok listázása a következő kódrészlethez hasonlóan nézhet ki. A kód elérhető a [minták:](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Vegye figyelembe, hogy az oldalon visszaadott eredmények számát az egyes API-k túlterhelésében *max_results* paraméter szabályozhatja, például:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Ha nem adja meg a *max_results* paramétert, az alapértelmezett maximális érték legfeljebb 5000 találat egyetlen oldalon jelenik meg.

Azt is vegye figyelembe, hogy az Azure Table storage lekérdezése nem adhat vissza rekordokat, vagy kevesebb rekordot, mint a megadott *max_results* paraméter értéke, még akkor is, ha a folytatási jogkivonat nem üres. Ennek egyik oka az lehet, hogy a lekérdezés nem fejeződött be öt másodperc alatt. Mindaddig, amíg a folytatási jogkivonat nem üres, a lekérdezésnek folytatódnia kell, és a kód nem kell feltételeznie a szegmens eredmények méretét.

Az ajánlott kódolási minta a legtöbb esetben a szegmentált lista, amely kifejezett előrehaladást biztosít a listázás vagy lekérdezés, és hogyan válaszol a szolgáltatás az egyes kérelmekre. Különösen a C++ alkalmazások vagy szolgáltatások esetében a listaelem előrehaladásának alacsonyabb szintű szabályozása segíthet a memória és a teljesítmény szabályozásában.

## <a name="greedy-listing"></a>Kapzsi lista

A C++ tár (0.5.0-s előzetes verziójú és korábbi verziók) korábbi verziói nem szegmentált listázási API-kat tartalmaztak a táblákhoz és várólistákhoz, ahogy az a következő példában is látható:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Ezeket a módszereket szegmentált API-k burkolójaként vezették be. A szegmentált listaelem minden egyes válaszához a kód hozzáfűzte az eredményeket egy vektorhoz, és a teljes tárolók beolvasása után minden eredményt visszaadott.

Ez a megközelítés akkor működhet, ha a tárfiók vagy tábla kis számú objektumot tartalmaz. Az objektumok számának növekedésével azonban a szükséges memória korlátozás nélkül növekedhet, mivel minden eredmény a memóriában maradt. Egy listaművelet nagyon hosszú időt vehet igénybe, amelynek során a hívó nak nem volt információja a folyamatelőrehaladásáról.

Ezek a kapzsi listázási API-k az SDK-ban nem léteznek a C#, java vagy a JavaScript Node.js környezetben. A kapzsi API-k használatával kapcsolatos esetleges problémák elkerülése érdekében eltávolítottuk őket a 0.6.0-s verziójú előzetes verzióban.

Ha a kód ezeket a kapzsi API-kat hívja:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Ezután módosítania kell a kódot a szegmentált tőzsdei API-k használatához:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

A szegmens *max_results* paraméterének megadásával egyensúlyba hozhatja a kérelmek számát és a memóriahasználatot, hogy megfeleljen az alkalmazás teljesítményével kapcsolatos szempontoknak.

Továbbá, ha szegmentált tőzsdei API-kat használ, de az adatokat egy helyi gyűjteményben "kapzsi" stílusban tárolja, azt is javasoljuk, hogy a kódot újrafaktorálja az adatok helyi gyűjteményben való tárolásának kezeléséhez, gondosan, nagy méretekben.

## <a name="lazy-listing"></a>Lusta lista

Bár a kapzsi lista felvetette a potenciális problémákat, célszerű, ha nincs túl sok objektum a tárolóban.

Ha C# vagy Oracle Java SDK-kat is használ, ismernie kell az Enumerable programozási modellt, amely lusta stílusú listát kínál, ahol az adatok egy bizonyos eltolásnál csak akkor lesznek behívva, ha szükséges. A C++ nyelvben az iterátor alapú sablon is hasonló megközelítést biztosít.

Egy tipikus lusta listázási API, amely **például list_blobs** használja, így néz ki:

```cpp
list_blob_item_iterator list_blobs() const;
```

Egy tipikus kódrészlet, amely a lusta listamintát használja, így nézhet ki:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Vegye figyelembe, hogy a lusta lista lista csak szinkron módban érhető el.

Összehasonlítva a kapzsi tőzsdei, lusta lista fetches adatok csak akkor, ha szükséges. A borítók alatt csak akkor olvassa be az adatokat az Azure Storage-ból, ha a következő iterátor a következő szegmensbe kerül. Ezért a memóriahasználat határolt mérettel van szabályozva, és a művelet gyors.

A lusta listázási API-k a C++ tárolóügyfél-könyvtárban találhatók a 2.2.0-s verzióban.

## <a name="conclusion"></a>Összegzés

Ebben a cikkben a C++ tárolóügyfél-könyvtár különböző objektumainak API-k listázásához szükséges különböző túlterheléseket vitattuk meg. Összegezve:

* Az Async API-k használata erősen ajánlott több szálas ítás esetén.
* A szegmentált listaelem a legtöbb esetben ajánlott.
* A lusta lista a könyvtárban kényelmes burkolóként érhető el szinkron helyzetekben.
* A kapzsi lista nem ajánlott, ezért eltávolították a könyvtárból.

## <a name="next-steps"></a>További lépések

Az Azure Storage és a C++ ügyfélkódtárról az alábbi forrásokban talál további információt.

* [A Blob Storage használata C++ oldalról](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [A C++ táblatároló használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A Queue Storage használata C++-szal](../storage-c-plus-plus-how-to-use-queues.md)
* [Az Azure Storage Ügyféltár C++ API dokumentációjához.](https://azure.github.io/azure-storage-cpp/)
* [Az Azure Storage-csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)
