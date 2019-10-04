---
title: Azure Storage-erőforrások listázása a Storage ügyféloldali függvénytárával C++ | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Microsoft Azure Storage ügyféloldali függvénytárában C++ lévő API-kat a tárolók, blobok, várólisták, táblák és entitások enumerálásához.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 3a87e39c9435ba02357b4b655e95e96666242b71
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721920"
---
# <a name="list-azure-storage-resources-in-c"></a>Azure Storage-erőforrások listázása itt:C++

A listázási műveletek kulcsfontosságúak az Azure Storage-ban számos fejlesztési forgatókönyvhöz. Ez a cikk azt ismerteti, hogyan lehet a leghatékonyabban enumerálni az Azure Storage-objektumokat a Microsoft Azure Storage ügyféloldali függvénytárában C++található List API-k használatával.

> [!NOTE]
> Ez az útmutató az Azure Storage ügyféloldali kódtárat C++ célozza meg a 2. x verzióhoz, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [githubon](https://github.com/Azure/azure-storage-cpp)keresztül érhető el.

A Storage ügyféloldali kódtár számos módszert biztosít az objektumok listázásához és lekérdezéséhez az Azure Storage-ban. Ez a cikk a következő forgatókönyveket tárgyalja:

* Egy fiókban található tárolók listázása
* Blobok listázása egy tárolóban vagy egy virtuális blob könyvtárában
* Fiókban lévő várólisták listázása
* Fiókban lévő táblák listázása
* Entitások lekérdezése egy táblában

Ezek a módszerek különböző túlterhelések használatával jelennek meg a különböző forgatókönyvek esetében.

## <a name="asynchronous-versus-synchronous"></a>Aszinkron és szinkron

Mivel a Storage ügyféloldali kódtára C++ a [ C++ Rest-könyvtárra](https://github.com/Microsoft/cpprestsdk)épül, a [pplx:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html)paranccsal az aszinkron műveleteket is támogatjuk. Példa:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

A szinkron műveletek a megfelelő aszinkron műveleteket takarják el:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Ha több többszálú alkalmazást vagy szolgáltatást használ, javasoljuk, hogy az aszinkron API-kat közvetlenül a szinkronizálási API-k meghívásához használja ahelyett, hogy a teljesítményre jelentős hatással lenne.

## <a name="segmented-listing"></a>Szegmentált lista

A felhőalapú tárolás méretezéséhez szegmentált lista szükséges. Például több mint egy millió blob található egy Azure Blob-tárolóban vagy egy milliárd entitásban egy Azure-táblában. Ezek nem elméleti számok, hanem valódi ügyfél-használati esetek.

Ezért nem célszerű egyetlen válaszban lévő összes objektumot listázni. Ehelyett a lapozást használó objektumokat is listázhat. A tőzsdei API-k mindegyike *szegmentált* túlterheléssel rendelkezik.

A szegmentált listázási műveletre adott válasz az alábbiakat tartalmazza:

* a *_segment*, amely a tőzsdei API-nak egyetlen hívása által visszaadott eredmények készletét tartalmazza.
* *continuation_token*, amelyet a rendszer a következő hívásnak továbbít a találatok következő oldalának beolvasása érdekében. Ha nincs több eredmény a visszaadáshoz, a folytatási jogkivonat null értékű.

A tárolóban lévő összes blob listázása például a következő kódrészlethez hasonló lehet. A kód a mintákban érhető [](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)el:

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

Vegye figyelembe, hogy a lapon visszaadott eredmények száma az egyes API-k túlterhelése *max_results* paraméterrel szabályozható, például:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Ha nem adja meg a *max_results* paramétert, a rendszer legfeljebb 5000 eredmény alapértelmezett maximális értékét adja vissza egyetlen lapon.

Azt is vegye figyelembe, hogy az Azure Table Storage-beli lekérdezés nem adhat vissza rekordokat, vagy kevesebb rekordot, mint a megadott *max_results* paraméter, még akkor is, ha a folytatási jogkivonat nem üres. Ennek egyik oka az lehet, hogy a lekérdezés öt másodpercen belül nem fejeződött be. Mindaddig, amíg a folytatási jogkivonat nem üres, a lekérdezésnek folytatódnia kell, és a kód nem feltételezheti a szegmens eredményeinek méretét.

A legtöbb forgatókönyv esetén ajánlott kódolási minta a szegmentált lista, amely explicit módon mutatja be a listázási és lekérdezési folyamatokat, valamint azt, hogy a szolgáltatás hogyan válaszol az egyes kérelmekre. Különösen C++ alkalmazások vagy szolgáltatások esetében a tőzsdei folyamat alacsonyabb szintű felügyelete segíthet a memória és a teljesítmény szabályozásában.

## <a name="greedy-listing"></a>Kapzsi felsorolás

A Storage ügyféloldali kódtár korábbi verziói C++ (0.5.0 előzetes verzió és korábbi verziók) a nem szegmentált listázási API-kat tartalmazzák a táblákhoz és várólistákhoz, ahogy az alábbi példában is látható:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Ezek a metódusok a szegmentált API-k burkolói lettek implementálva. A szegmentált listaelemekre adott válaszok esetében a kód hozzáfűzte az eredményeket egy vektorhoz, és az összes eredményt a teljes tárolók vizsgálata után adta vissza.

Ez a megközelítés akkor működhet, ha a Storage-fiók vagy-tábla kis számú objektumot tartalmaz. Az objektumok számának növekedésével azonban a szükséges memória korlát nélkül növekedhet, mert az összes eredmény a memóriában marad. Az egyik listázási művelet nagyon hosszú időt vehet igénybe, amelynek során a hívó nem kapott információt a folyamatáról.

Az SDK-ban a kapzsi listázási API-k C#nem léteznek, a Java vagy a JavaScript Node. js környezetben. A kapzsi API-k használatával kapcsolatos lehetséges problémák elkerülése érdekében a 0.6.0 előzetes verziójában eltávolította őket.

Ha a kód a következő kapzsi API-kat hívja meg:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Ezután módosítania kell a kódot a szegmentált listázási API-k használatára:

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

A szegmens *max_results* paraméterének megadásával összekapcsolhatja a kérelmek és a memóriahasználat közötti egyensúlyt az alkalmazás teljesítménybeli szempontjainak kielégítése érdekében.

Emellett, ha szegmentált listázási API-kat használ, de az adatokat egy "kapzsi" stílusban tárolja egy helyi gyűjteményben, erősen ajánlott a kód újrabontása, hogy az adatokat egy helyi gyűjteményben gondosan, nagy méretben kezeljék.

## <a name="lazy-listing"></a>Lusta lista

Bár a kapzsi felsorolás potenciális problémákat okoz, érdemes lehet, ha a tárolóban nincs túl sok objektum.

Ha a vagy az Oracle C# Java SDK-kat is használja, ismerkedjen meg a enumerable programozási modellel, amely egy lusta stílusú listát biztosít, ahol az adatok bizonyos eltolás esetén csak akkor lesznek beolvasva, ha szükségesek. A C++-ben az iteráció-alapú sablon hasonló megközelítést is biztosít.

Egy tipikus lusta tőzsdei API, a **list_blobs** használatával példaként a következőképpen néz ki:

```cpp
list_blob_item_iterator list_blobs() const;
```

A lusta listaelemet használó tipikus kódrészlet a következőhöz hasonló lehet:

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

Vegye figyelembe, hogy a lusta lista csak szinkron módban érhető el.

A kapzsi felsorolással összehasonlítva a lusta lista csak szükség esetén kéri le az adatokat. A borítók alatt az Azure Storage-ból származó adatok csak akkor kerülnek beolvasásra, ha a következő iteráció a következő szegmensbe kerül. Ezért a memóriahasználat határos mérettel van vezérelve, és a művelet gyors.

A "Lazy List" API-kat a rendszer a C++ következő verzióban tartalmazza a Storage ügyféloldali kódtáraban:.

## <a name="conclusion"></a>Összegzés

Ebben a cikkben különböző túlterheléseket beszéltünk a Storage ügyféloldali kódtár különböző objektumaihoz tartozó API-k listázásához C++ . Összefoglalás:

* Az aszinkron API-k használata kifejezetten ajánlott több szálon.
* A szegmentált lista a legtöbb esetben ajánlott.
* A lusta lista a könyvtárban kényelmes burkolóként van megadva a szinkron helyzetekben.
* A kapzsi felsorolás nem ajánlott, és el lett távolítva a könyvtárból.

## <a name="next-steps"></a>További lépések

További információ az Azure Storage-ról és az C++ügyféloldali kódtáraról: az alábbi források.

* [A Blob Storage használataC++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [A Table Storage használataC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A Queue Storage használataC++](../storage-c-plus-plus-how-to-use-queues.md)
* [Az Azure Storage ügyféloldali kódtára C++ az API dokumentációjában.](https://azure.github.io/azure-storage-cpp/)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
