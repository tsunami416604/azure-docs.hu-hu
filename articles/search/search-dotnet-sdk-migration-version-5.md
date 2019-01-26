---
title: Az Azure Search .NET SDK 5 – Azure Search verzió frissítése
description: Kód áttelepítése az Azure Search .NET SDK 5-ös verzió régebbi verzióit. Megtudhatja, Miben változott, és melyik programkód módosítása.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7684aa79ac9f58c2a047b01a6d9f5263795221d
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912050"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Az Azure Search .NET SDK 5-ös verzió frissítése
Ha a 4.0-preview jelű verziót vagy a régebbi verziót használja a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít az alkalmazás használhatja a 5-ös verzió frissítése.

Az SDK-t, beleértve a példákat általános bemutatóért lásd: [használata az Azure Search .NET-alkalmazásból](search-howto-dotnet-sdk.md).

5-ös verzióját az Azure Search .NET SDK tartalmaz néhány módosítást korábbi verzióit. Ezek a leginkább kis, így a kód módosítása csak minimális erőfeszítéssel elvégzéséhez szükséges. Lásd: [frissítésére lépéseket](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatához.

> [!NOTE]
> 2.0 – előzetes verzió vagy annál régebbi verzióját használja, ha meg kell először frissítse a 3-as verziójú, majd utána frissítse a 5-ös verzió. Lásd: [az Azure Search .NET SDK 3-as verziójú frissítését](search-dotnet-sdk-migration.md) útmutatást.
>
> Az Azure Search-szolgáltatáspéldányhoz több REST API-verziók, többek között a legújabb buildszám támogatja. Továbbra is verzióját használja, amikor már nem a legújabb buildszám, de azt javasoljuk, hogy a legújabb verzió használatához kódok migrálása. A REST API használata esetén az api-version paraméter használatával minden kérésben meg kell adnia az API-verziót. A .NET SDK használatával, az SDK-t használ a verzióját határozza meg a megfelelő REST API-verzió. Ha egy régebbi SDK-t használja, továbbra is futtassa, hogy a kód módosítása nélkül, akkor is, ha a szolgáltatás frissítése egy újabb API-verzió támogatja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>5-ös verzió újdonságai
Az Azure Search .NET SDK 5-ös verzióját a legújabb célozza meg benne az Azure Search REST API, kifejezetten 2017-11-11 általánosan elérhető verzióját. Ez lehetővé teszi az Azure Search .NET-alkalmazásból, többek között az alábbi új szolgáltatások használatához:

* [A szinonimák](search-synonyms.md).
* Programozott módon érhetők el az indexelő futtatási előzményei figyelmeztetések (lásd a `Warning` tulajdonsága `IndexerExecutionResult` a a [.NET-referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) további részletekért).
* A .NET Core 2 támogatása.
* Új csomag struktúra támogatja a szükséges az SDK-t csak azon részei (lásd: [parancsban történt használhatatlanná tévő az 5-ös verzió](#ListOfChanges) részletekért).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Először frissítse a NuGet referenciája `Microsoft.Azure.Search` vagy a NuGet Package Manager konzol segítségével vagy a jobb gombbal a projekt hivatkozásait a és a Visual Studióban válassza a "Kezelése NuGet Packages …".

Miután NuGet töltött le az új csomagok és azok függőségeit, a projekt újraépítéséhez. Attól függően, hogyan épül fel a kódot akkor előfordulhat, hogy építse újra sikeresen megtörtént. Ha igen, akkor készen!

A build sikertelen lesz, ha egy build hiba történt a következőhöz hasonlóan kell megjelennie:

    The name 'SuggesterSearchMode' does not exist in the current context

A következő lépés, hogy javítsa a build-hibát. Lásd: [parancsban történt használhatatlanná tévő az 5-ös verzió](#ListOfChanges) mi okozza a hibát, és annak megoldásáról.

Vegye figyelembe, hogy az Azure Search .NET SDK csomagolásának változtatások miatt, 5-ös verzió használatához az alkalmazás újból létre kell hoznia. Ezek a változások részleteit [parancsban történt használhatatlanná tévő az 5-ös verzió](#ListOfChanges).

Elavult módszerek és a Tulajdonságok kapcsolatban további build figyelmeztetések jelenhetnek meg. A figyelmeztetésekkel kapcsolatos helyett az elavult funkció tartalmazza. Például, ha az alkalmazás használja a `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` módszerrel kell egy figyelmeztetés fog megjelenni arról, hogy a "Ez a viselkedés mostantól alapértelmezés szerint engedélyezve van, így a metódus meghívásának hatására már nem szükséges."

Miután már kijavított bármely fordítási hibákat vagy figyelmeztetéseket, alkalmazását az új funkciók előnyeinek kihasználása, ha módosításokat végezheti el. Az SDK-t az új funkciók részletes leírásuk [5-ös verzió újdonságai](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Az 5-ös verzió használhatatlanná tévő változásai

### <a name="new-package-structure"></a>Új csomag szerkezete

A legtöbb jelentős használhatatlanná tévő változást az 5-ös verzió, hogy a `Microsoft.Azure.Search` szerelvény és annak tartalma rendelkezik mint négy külön NuGet-csomagok most elosztott négy külön szerelvények felosztva:

 - `Microsoft.Azure.Search`: A csomag egy metaadat-, amely tartalmazza az összes többi Azure Search csomagot függőségekként. Az SDK újabb verzióra történő frissítés, csak ez a csomag frissítése, és újra létrehozni kell elegendő az új verzió használatának megkezdéséhez.
 - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha használja az Azure Search .NET-alkalmazást fejleszt, és csak szeretne lekérdezni vagy frissíteni az indexek a dokumentumok. Ha is kell létrehozni vagy frissíteni az indexek, szinonimatérképet, vagy más szolgáltatásiszint-erőforrások, használja a `Microsoft.Azure.Search` csomag helyette.
 - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha a .NET-keretrendszerben Azure Search-indexek, szinonimatérképet, az indexelők, adatforrásokat vagy más szolgáltatásiszint-erőforrások kezelése automation fejleszt. Ha csak kell lekérdezés vagy a frissítés dokumentum az indexben, használja a `Microsoft.Azure.Search.Data` csomag helyette. Ha a funkciókat az Azure Search van szüksége, használja a `Microsoft.Azure.Search` csomag helyette.
 - `Microsoft.Azure.Search.Common`: Az Azure Search .NET-kódtárakra által igényelt gyakori típust. Nem kell saját alkalmazásában; ez a csomag használatára Csak hivatott függőségként használható.
 
Ez a változás technikailag megsérti számos különböző szerelvények között lett áthelyezve, mert. Ezért az alkalmazás újraépítését szükség ahhoz, hogy frissítsen az SDK 5-ös verzióját.

Nincs más kompatibilitástörő változásokat az 5-ös verzió, amely lehet szükség a kód kis számú mellett az alkalmazás újraépítését változik.

### <a name="change-to-suggesters"></a>Váltson a Javaslattevők 

A `Suggester` konstruktor már nem rendelkezik egy `enum` paramétere `SuggesterSearchMode`. Ez az enumerálás csak egyetlen értékkel rendelkezik, és ezért redundáns volt. Ha ez miatt kialakult hibákat, a egyszerűen a mutató hivatkozások eltávolítása a `SuggesterSearchMode` paraméter.

### <a name="removed-obsolete-members"></a>Elavult a tagok eltávolítása

Látni hozhat létre módszerek és a korábbi verziókban elavultként megjelölt, és ezt követően távolítva az 5-ös verzió tulajdonságok kapcsolódó hibák. Ha ilyen hibákat észlel, a következő azok megoldását:

- Ha használta a `IndexingParametersExtensions.IndexStorageMetadataOnly` metódus használata `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` helyette.
- Ha használta a `IndexingParametersExtensions.SkipContent` metódus használata `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` helyette.

### <a name="removed-preview-features"></a>Eltávolított előzetes verziójú funkciók

Ha frissít a 4.0-s verzióját. dátumú előzetes sémaverzióra 5-ös verzió, vegye figyelembe, hogy JSON-tömböt, és a CSV-elemzési Blob-indexelők támogatása el lett távolítva, mivel ezek a szolgáltatások egyelőre előzetes verzióként. Pontosabban, a következő módszerek egyikét a `IndexingParametersExtensions` osztály el lett távolítva:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás egy rögzített függőségi ezekről a funkciókról, nem tudja frissíteni az 5-ös verzióját az Azure Search .NET SDK. Továbbra is használhatja a 4.0-s verzióját. dátumú előzetes sémaverzióra. Ugyanakkor Kérjük vegye figyelembe, hogy **éles üzemi alkalmazások pedig az SDK-k előzetes verzió használata nem ajánlott**. Előzetes verziójú funkciók csak tesztelési és változhat.

## <a name="conclusion"></a>Összegzés
Ha részletesebb tájékoztatást az Azure Search .NET SDK van szüksége, tekintse meg a [.NET útmutató](search-howto-dotnet-sdk.md).

Szívesen fogadjuk a visszajelzéseket az SDK-val. Ha problémákat tapasztal, nyugodtan megszüntetését a Súgó a [Azure Search MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a megkereséseit is fájl a [Azure .NET SDK GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címe és az "[az Azure Search]" előtagot.

Köszönjük, hogy az Azure Search használatával!
