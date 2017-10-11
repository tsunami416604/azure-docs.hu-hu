---
title: "Azure Storage-erőforrások a Storage ügyféloldali kódtára a C++ listázása |} Microsoft Docs"
description: "Ismerje meg, a listaelem API-k használata a Microsoft Azure Storage ügyféloldali kódtára a C++ tárolók, blobok, várólisták, táblákat és entitásokat enumerálása."
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="list-azure-storage-resources-in-c"></a>A c++ Azure Storage-erőforrások felsorolása
Listaelem műveletek kulcsot az Azure Storage számos fejlesztési forgatókönyvek esetében. Ez a cikk ismerteti a leghatékonyabban az Azure Storage az átjáróra a listában megadott a Microsoft Azure Storage ügyféloldali kódtára a C++ API-k használata az objektumok számbavétele.

> [!NOTE]
> Ez az útmutató az Azure Storage ügyféloldali kódtára a C++ verzióját célozza 2.x, amelyik keresztül elérhető [NuGet](http://www.nuget.org/packages/wastorage) vagy [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

A Storage ügyféloldali kódtára számos módszer az Azure Storage lista vagy a lekérdezés objektumhoz biztosít. Ez a cikk foglalkozik a következő esetekben:

* Egy fiók tárolók listája
* A tároló vagy virtuális blob directory lista blobok
* Egy fiók lista várólisták
* Egy fiók listáját táblákat
* A tábla lekérdezési entitások

Mindkét módszerhez látható osztálypéldányt használatával különböző helyzetek kezelésére.

## <a name="asynchronous-versus-synchronous"></a>Aszinkron és szinkron
Mivel a Storage ügyféloldali kódtára a C++ épül, a a [C++ REST könyvtár](https://github.com/Microsoft/cpprestsdk), azt eleve támogatja az aszinkron műveletek használatával [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Példa:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Szinkron műveletek burkolja a megfelelő aszinkron műveletek:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Több szálkezelési alkalmazáshoz vagy szolgáltatáshoz dolgozik, azt javasoljuk, hogy az async API-k közvetlenül a szál létrehozása helyett hívására használt a szinkronizálás API-k, amely jelentős hatással van a teljesítményre.

## <a name="segmented-listing"></a>Szegmentált listázása
A skála felhőbeli tárolóhely szükséges szegmentált listázása. Lehet például egy millió blobot, amely egy Azure blob-tároló vagy egy Azure-tábla egymilliárd entitások keresztül. Ezek a a nem elméleti számokat, de a tényleges felhasználói használati esetekben.

Éppen ezért nem célszerű egyetlen válasz összes objektumok listája. Ehelyett az objektumok lapozás listázhatja. Az átjáróra a listában API-k mindegyike rendelkezik egy *szegmentált* túlterhelést.

A válasz szegmentált listázási művelet tartalmazza:

* <i>_segment</i>, amely azokat a egyetlen meghívása a listaelem API-t adott vissza eredményt.
* *continuation_token*, amely a következő alkalommal meghívja a következő oldalra, hogy átadott. Nincs több eredmény esetén, a folytatási kód értéke null.

Például minden, a tárolóban lévő blobok listázásához tipikus hívása a következő kódrészletet hasonlíthat. A kód is elérhető a [minták](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Vegye figyelembe, hogy a paraméter egy visszaadott eredmények száma szabályozhatók *max_results* az egyes API, például a túlterhelési:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Ha nem adja meg a *max_results* paraméter, az alapértelmezett legfeljebb 5000 eredmények maximális értékének eredmény abban az esetben egy oldalra.

Vegye figyelembe azt is, hogy egy Azure Table storage elleni vissza a lekérdezés egyetlen bejegyzést sem, vagy az értéknél kevesebb bejegyzést a *max_results* paraméter, amely a megadott, még akkor is, ha a folytatási kód nem üres. Egyik oka lehet, hogy a lekérdezés nem hajtható végre öt másodpercben. Mindaddig, amíg a folytatási kód nem üres, továbbra is a lekérdezés, és a kódot kell feltételezi azt szegmens eredmények méretét.

A legtöbb esetben ajánlott kódolási mintát szegmentált van, listázása, amely biztosítja, hogy listázása vagy kérdez le, és hogyan reagál a a szolgáltatás minden egyes kérelem explicit előrehaladását. Különösen a C++-alkalmazások vagy szolgáltatások a listaelem folyamatban alacsonyabb szintű irányítását segíthet vezérlő memória és teljesítmény.

## <a name="greedy-listing"></a>Mohó listázása
A Storage ügyféloldali kódtára a C++ korábbi verzióiban (előzetes verzió 0.5.0 és korábbi) része nem szegmentált átjáróra a listában API-k táblák és a várólisták, az alábbi példában látható módon:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Ezek a módszerek, szegmentált API-k burkolók volt megvalósítva. Minden válasz szegmentált listaelem a kódot az eredmények hozzáfűzi a vektor, és adott vissza az összes eredményt, után a teljes tárolók beolvasásakor.

Ez a megközelítés előfordulhat, hogy működnek, ha a tárfiók, vagy a tábla tartalmaz a kis számú objektum. Azonban objektumok számának növekedése, a szükséges memória nő korlátozás nélkül, mert a memóriában marad minden eredmény. Egy listázási művelet sok időt, amely alatt a hívó előrehaladásával kapcsolatos információ nem volt is igénybe vehet.

Ezek mohó listázása az SDK API-k nem léteznek a C#, Java, vagy a JavaScript Node.js környezetben. A lehetséges problémák ezen mohó API-k használatának elkerülése azt eltávolította azokat verziójában 0.6.0 előzetes verzió.

Ha a kód hívja meg ezek mohó API-kat:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Majd módosítania kell a kódot a szegmentált listaelem API-k használata:

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

Megadja a *max_results* szegmens paraméter, akkor is el tudnak osztani kérések számát és a memóriahasználat felel meg a teljesítménnyel kapcsolatos szempontok az alkalmazás között.

Emellett ha használ szegmentált listaelem API-kat, de az adatok tárolása a helyi gyűjtés "mohó" Style, is javasoljuk, hogy refactor-e a kód adatainak tárolásához egy helyi gyűjtemény gondosan léptékű kezelésére.

## <a name="lazy-listing"></a>Lusta listázása
Bár mohó listaelem lehetséges problémák kezeléséhez következik be, célszerű a Ha nem túl sok objektum a tárolóban.

Ha a C# vagy Oracle Java SDK-k is használ, a Számbavehető programozási modellel, a Lusta stílusú listázása, ahol az adatok egy bizonyos eltolásnál csak beolvassa szükség esetén kínál, amelyek tisztában kell lennie. A c++-ban a iterátor alapú sablont is hasonló megközelítését ismerteti.

A tipikus Lusta felsorolást API-t használó **list_blobs** például dolgozunk:

```cpp
list_blob_item_iterator list_blobs() const;
```

Egy tipikus kódrészletet, amely a Lusta listaelem mintát használ nézhet ki:

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

Vegye figyelembe, hogy Lusta átjáróra a listában csak a szinkron módban érhető el.

Mohó listaelem képest, Lusta listázása kér le adatokat, csak szükség esetén. A színfalak azt kér le adatokat az Azure Storage csak akkor, ha a következő iterátor áthelyezi a következő szegmens. Ezért kötött méretű memória használata vezérli, és a művelet gyors.

Lusta listaelem API-k szerepelnek a Storage ügyféloldali kódtára a C++ 2.2.0 verzióban.

## <a name="conclusion"></a>Összegzés
Ebben a cikkben tárgyalt API-k felsoroló különböző objektumoknál a Storage ügyféloldali kódtára a C++ osztálypéldányt. Összefoglalásképpen:

* Aszinkron API-kat is erősen ajánlott több szálkezelési forgatókönyv szerint.
* Legtöbb esetben ajánlott szegmentált listázása.
* Lusta listázása a könyvtár egy kényelmes burkoló szinkron forgatókönyvekben megadva.
* Mohó listaelem nem ajánlott, és a könyvtár el lett távolítva.

## <a name="next-steps"></a>Következő lépések
További információ Azure Storage- és ügyféloldali kódtára a C++ a következő forrásanyagokban talál.

* [A C++ Blob Storage használata](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [A C++ Table Storage használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A C++ Queue Storage használata](../storage-c-plus-plus-how-to-use-queues.md)
* [Az Azure Storage ügyféloldali kódtára a C++ API-JÁNAK dokumentációja esetén.](http://azure.github.io/azure-storage-cpp/)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)

