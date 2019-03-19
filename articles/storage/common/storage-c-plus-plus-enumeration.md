---
title: A Storage ügyféloldali kódtár az Azure Storage-erőforrások listázása C++ |} A Microsoft Docs
description: Ismerje meg, a listaelem API-k használata a Microsoft Azure Storage ügyféloldali kódtára a C++ tárolók, blobok, üzenetsorok, táblák és entitások számbavétele.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.subservice: common
ms.openlocfilehash: 13ddb4d64908421e999174623003acd2fb24024d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014893"
---
# <a name="list-azure-storage-resources-in-c"></a>A C++ Azure Storage-erőforrások listázása
Listaelem műveletekre az Azure Storage számos fejlesztési forgatókönyvek kulcs. Ez a cikk ismerteti a leghatékonyabban számbavétele az objektumok az Azure Storage-ban a listaelem API-k a Microsoft Azure Storage ügyféloldali kódtára a c++ programnyelvhez.

> [!NOTE]
> Ez az útmutató célozza meg benne az Azure Storage ügyféloldali kódtára a C++-verzió keresztül érhető el 2.x [NuGet](https://www.nuget.org/packages/wastorage) vagy [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

A Storage ügyféloldali kódtára számos módszer az Azure Storage-lista vagy a lekérdezés objektumokhoz biztosít. Ez a cikk foglalkozik a következő esetekben:

* Egy fiók tárolóit listája
* Egy tárolót vagy virtuális blob könyvtárban lévő blobok listázása
* Egy fiók üzenetsorok listázása
* Egy fiók tábláira listája
* Egy tábla lekérdezési entitások

Mindkét módszerhez jelenik meg a különböző helyzetekhez különböző túlterheléssel használatával.

## <a name="asynchronous-versus-synchronous"></a>A szinkron és aszinkron
Mivel a használatra készült a Storage ügyféloldali kódtára a C++ a [C++ REST könyvtár](https://github.com/Microsoft/cpprestsdk), természetüknél fogva támogatjuk az aszinkron műveletek használatával [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Példa:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

A szinkron műveletek burkolhatja a megfelelő aszinkron műveletek:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Ha több szálkezelési alkalmazásokkal vagy szolgáltatásokkal dolgozik, azt javasoljuk, hogy, használja az async API-k közvetlenül a szál létrehozása helyett hívni az API-kat, amelynek jelentős hatással van a teljesítményre szinkronizálása.

## <a name="segmented-listing"></a>Szegmentált listázása
A méretezési csoport, felhőalapú tárolás szükséges szegmentált listázása. Például rendelkezhet egy millió blobokat egy Azure blob-tárolóba vagy keresztül egymilliárd entitások Azure-táblában. Ezek nem a elméleti számok, de a valós használati esetek.

Éppen ezért nem célszerű feltételezni, egy egyetlen válaszként összes objektumok listája. Ehelyett listázhatja az objektumok lapozást. Az eredményablakban API-k mindegyike rendelkezik egy *szegmentált* túlterhelni.

A válasz egy szegmentált listázási művelet tartalmazza:

* <i>_segment</i>, amely azokat a egyetlen a tőzsdei API-hívással adott vissza eredményt.
* *continuation_token*, amelyet átad az a következő hívást a következő lapra az eredmények lekérése érdekében. Ha nincs visszaadandó több találat, a folytatási kód értéke null.

Például egy tipikus hívás a tárolóban lévő összes BLOB listáját az alábbi kódrészlet nézhet. A kód megtalálható az [minták](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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

Vegye figyelembe, hogy a lap visszaadott eredmények számának a paraméter által szabályozható *max_results* az egyes API-t, például az egyik túlterhelése:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Ha nem adja meg a *max_results* paramétert, az alapértelmezett legfeljebb 5000 eredmények maximális értékét adja vissza egy oldalon.

Vegye figyelembe, hogy egy lekérdezést az Azure Table storage vissza egy rekord sem, vagy értékénél kevesebb bejegyzést a *max_results* paraméter, amely a megadott, még akkor is, ha a folytatási token nem üres. Egyik oka az lehet, hogy a lekérdezés nem hajtható végre, öt másodperc. Mindaddig, amíg a folytatási token nem üres, továbbra is a lekérdezést, és a kód nem kell feltételezi szegmens eredmények méretét.

A legtöbb esetben ajánlott kódolási minta szegmentált van, listázása, amely biztosítja, hogy tőzsdei vagy lekérdezéséhez és a szolgáltatás összes kérelemhez reakciója explicit előrehaladását. Különösen a c++ nyelven írt alkalmazások vagy szolgáltatások a listaelem folyamatban alacsonyabb szintű irányítását segíthet vezérlő – memória és teljesítmény.

## <a name="greedy-listing"></a>Mohó listázása
Korábbi verzióiban a Storage ügyféloldali kódtára a C++ (előzetes verziók 0.5.0-s és korábbi) része nem szegmentált eredményablakban API-k táblák és üzenetsorok, a következő példához hasonlóan:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Ezek a metódusok lettek megvalósítva burkolókat szegmentált API-k. Az összes szegmentált listaelem válaszról a kód hozzáfűzi az eredményeket egy vektoros, és minden találatot eredményezett, miután a teljes tárolók fájlcímkék.

Ez a megközelítés előfordulhat, hogy működnek, ha a storage-fiók vagy a tábla tartalmaz egy kis számú objektumok. Azonban objektumok számának növekedése, a memóriára van szükség megnövelheti korlátozás nélkül, mert az összes eredmény maradt a memóriában. Egy listázási művelet során, amely a hívó nem rendelkezett információkkal kapcsolatban a folyamat előrehaladását nagyon hosszú időt is igénybe vehet.

Ezek mohó listaelem az SDK API-k nem létezik a C#, Java vagy a JavaScript Node.js környezetben. Ezek mohó API-k használatával, a lehetséges problémák elkerülése érdekében eltávolítottuk őket verziójában 0.6.0 előzetes verzió.

Ha a kód hívja ezek mohó API-kat:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Ezután a kód a szegmentált listaelem API-k használatával kell módosítania:

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

Adja meg a *max_results* paramétert a szegmens, meg tudja osztani a kérések számát és a memóriahasználat vizsgálatával felel meg az alkalmazás teljesítményével kapcsolatos megfontolások között.

Ezenkívül ha szegmentált listaelem API-kat használ, de az adatok tárolása a helyi gyűjtemény egy "mohó" stílusban, is javasoljuk, hogy refaktorovat a kódját egy helyi gyűjtés gondosan ipari méretekben történő adattároláshoz kezelésére.

## <a name="lazy-listing"></a>Lusta listázása
Bár a mohó listaelem kiváltása a potenciális problémákat, célszerű a ha ott nem túl sok objektumot a tárolóban.

C#- vagy Oracle Java SDK-k is használja, ha ismeri a enumerálható programozási modellt, így az a Lusta stílusú listázása, ahol az adatokat egy bizonyos eltolástól csak olvas be szükség esetén kell lennie. A C++ a iterátor-alapú sablon is hasonló megközelítést biztosít.

Egy tipikus Lusta listaelem API-t, használatával **list_blobs** például kell kinéznie:

```cpp
list_blob_item_iterator list_blobs() const;
```

Egy tipikus kódrészlettel, amely a Lusta listaelem mintát használ, előfordulhat, hogy néznek ki:

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

Vegye figyelembe, hogy Lusta lista csak a szinkron módban érhető el.

Képest mohó listaelem a Lusta listaelem lekérdezi adatok csak szükség esetén. Valójában azt kéri le adatokat az Azure Storage csak akkor, ha a következő iterátor helyezi át a következő szegmens. Ezért egy korlátozott méretű memória használata vezérli, és a művelet gyors.

Lusta listaelem API-k megtalálhatók a Storage ügyféloldali kódtára a C++-2.2.0-ás verzió.

## <a name="conclusion"></a>Összegzés
Ebben a cikkben már volt szó az API-k listázása a különféle objektumokat, a Storage ügyféloldali kódtára a C++-osztálypéldányt. Összefoglalásképpen:

* Aszinkron API-k erősen ajánlott több szálkezelési forgatókönyv szerint.
* A legtöbb forgatókönyvhöz ajánlott szegmentált listázása.
* Lusta listázása egy kényelmes burkolót szinkron forgatókönyvekben verzióként az erőforrástárban.
* Mohó listaelem nem ajánlott, és el lett távolítva a könyvtárból.

## <a name="next-steps"></a>További lépések
További információ Azure Storage- és ügyféloldali kódtára a C++ a következő forrásanyagokban talál.

* [A c++ segítségével Blob Storage használata](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Hogyan használható a Table Storage, a c++ segítségével](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A c++ segítségével a Queue Storage használata](../storage-c-plus-plus-how-to-use-queues.md)
* [Az Azure Storage ügyféloldali kódtára a C++ API-dokumentáció esetén.](https://azure.github.io/azure-storage-cpp/)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)

