---
title: Az Azure Search .NET SDK 5-ös verzió frissítése |} Microsoft Docs
description: Az Azure Search .NET SDK 5-ös verzió frissítése
author: brjohnstmsft
manager: jlembicz
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 018388cd2bd85eb86ad7b62ee247bccd6329e9ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Az Azure Search .NET SDK 5-ös verzió frissítése
Ha szeretne megtudni a 4.0-preview vagy a régebbi verzióját a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít frissíteni az alkalmazást, 5-ös verzió használatára.

Az SDK, beleértve a példákat általános útmutatást lásd: [használata az Azure Search .NET-alkalmazás](search-howto-dotnet-sdk.md).

Az Azure Search .NET SDK 5-ös verzióját a korábbi verzióihoz képest végrehajtott egyes módosításokat tartalmazza. Ezek a legtöbbször kisebb, így a kód módosítása elvégzéséhez szükséges csak csatlakozhatnak. Lásd: [frissítésének lépései](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatára.

> [!NOTE]
> 2.0-preview vagy régebbi verzió használata, inkább 3-as verziójú először frissítsen, és majd frissítenie kell az 5-ös verzióját. Lásd: [frissítsen az Azure Search .NET SDK 3-as verziójú](search-dotnet-sdk-migration.md) utasításokat.
>
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja. A REST API használata esetén minden kérelemben keresztül az api-version paramétert meg kell adnia az API-verzió. A .NET SDK használatával, az SDK-t használ verziója határozza meg a megfelelő REST API-verzióra. Ha egy régebbi SDK használ, továbbra is futtassa ezt a kódot módosítások nélküli, még akkor is, ha a szolgáltatás frissítése újabb API-verzió támogatja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>5-ös verzió újdonságai
Az Azure Search .NET SDK 5-ös verzióját célozza a legújabb az Azure Search REST API, kifejezetten 2017-11-11 általánosan elérhető verziójának. Ez lehetővé teszi az Azure Search .NET-alkalmazás, beleértve a következő új szolgáltatások használatához:

* [Szinonimák](search-synonyms.md).
* Programozott módon érheti el az indexelő futtatási előzményei figyelmeztetések (lásd a `Warning` tulajdonsága `IndexerExecutionResult` a a [.NET hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) további részletekért).
* Támogatja a .NET Core 2.
* Új csomag struktúra támogatja a csak azon részei, amelyekre szüksége van az SDK-t (lásd: [Megtörje a módosításokat az 5-ös verzió](#ListOfChanges) részletekért).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítésének lépései
Először frissítse a NuGet referencia `Microsoft.Azure.Search` vagy a NuGet-Csomagkezelő konzol használatával vagy az csomagot jobb gombbal a projekt hivatkozásait, majd válassza a "Kezelése NuGet csomagjainak..." a Visual Studióban.

Miután NuGet töltött le az új csomagok és a függőségek, a projekt újraépítéséhez. Amely nincs az új GA SDK előzetes verziójú funkciók használata, ha sikeresen kell-e újraépítése (Ha nem, ossza meg velünk a [GitHub](https://github.com/azure/azure-sdk-for-net/issues)). Ha igen, most készen áll!

Ne feledje, hogy az Azure Search .NET SDK csomagolásának-módosítások miatt, hogy újból létre kell hoznia az alkalmazás használatához az 5-ös verzióját. Ezek a változások részletes leírást talál [Megtörje a módosításokat az 5-ös verzió](#ListOfChanges).

Elavult metódusokra vagy tulajdonságokra kapcsolatban további build figyelmeztetések jelenhetnek meg. A figyelmeztetésekkel kapcsolatos használata helyett az elavult funkció tartalmazza. Például, ha az alkalmazás által a `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` módszerrel kell megjelenik egy figyelmeztetés, amely szerint a "Ez a viselkedés most alapértelmezés szerint engedélyezve van, így a metódus hívása már nincs szükség."

Build figyelmeztetéseket megszüntetése után módosíthatja a új funkcióinak kihasználásához, ha az alkalmazáshoz. Az SDK-t az új funkciói részletes leírást talál [5-ös verzió újdonságai](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>5-ös verzió megtörje változásai
A legtöbb jelentős használhatatlanná tévő változást az 5-ös verzió, hogy a `Microsoft.Azure.Search` szerelvény és annak tartalmát rendelkezik lett osztva négy külön szerelvények most elosztott négy külön NuGet-csomag:

 - `Microsoft.Azure.Search`: A csomag egy metaadat-, amely tartalmazza az összes többi Azure Search csomagot szerint függőségeinek. Frissítés egy korábbi SDK, egyszerűen a csomag frissítése és újbóli összeállítása kell elegendő elindítani az új verziót használja.
 - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha egy .NET-alkalmazást az Azure Search kidolgozása, és csak lekérdezni, vagy az indexek dokumentumainak frissíteni kell. Ha is kell létrehozni vagy frissíteni az indexek, szinonima maps, vagy más szolgáltatásiszint-erőforrások, használja a `Microsoft.Azure.Search` csomag helyette.
 - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha az Azure Search-indexek, szinonima maps, indexelők, adatforrások vagy más szolgáltatásiszint-erőforrások kezeléséhez .NET automatikus. Ha csak kell lekérdezés vagy a frissítés dokumentumot az indexben, használja a `Microsoft.Azure.Search.Data` csomag helyette. Ha módosítania kell az Azure Search összes funkcióját, használja a `Microsoft.Azure.Search` csomag helyette.
- `Microsoft.Azure.Search.Common`: Az Azure Search .NET-kódtárakra igényli általános típusok. Nem kell használni a csomag közvetlenül az alkalmazás; Csak célja, hogy a függőség beállításához használható.
 
Ez a változás technikailag feltörésével, mivel számos különböző került szerelvények között. Ezért az alkalmazás újraépítése szükség ahhoz, hogy frissítsen az SDK 5-ös verzióját.

Van néhány egyéb jelentős változásokat az 5-ös verzió, amelyre szüksége lehet a kód mellett az alkalmazás újraépítése módosítja.

### <a name="removed-obsolete-members"></a>Eltávolított elavult tagok

Előfordulhat, hogy látja build metódusokra vagy tulajdonságokra volt korábbi verzióiban elavultként megjelölt ezt követően a és eltávolított 5-ös verzió kapcsolatos hibákat. Ha ilyen hibákba ütközik, ez megoldásával:

- Ha használta a `IndexingParametersExtensions.IndexStorageMetadataOnly` metódus használata `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` helyette.
- Ha használta a `IndexingParametersExtensions.SkipContent` metódus használata `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` helyette.

### <a name="removed-preview-features"></a>Az eltávolított előzetes verziójú funkciók

Ha verziója 4.0-Preview 5 verzióra frissíti, vegye figyelembe, hogy JSON-tömb és a fürt megosztott kötetei szolgáltatás támogatja a Blob indexelők elemzése el lett távolítva, mert ezek a funkciók még még csak előzetes verziójúak. Pontosabban, a következő módszerek egyikét a `IndexingParametersExtensions` osztály eltávolításra került:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás erős függőség az ezen szolgáltatásoktól, csak akkor térnek át az Azure Search .NET SDK 5-ös verzióját. Továbbra is 4.0-előzetes verzióját használja. Azonban adja a következőket kell figyelembe venni, hogy **megtekintés SDK-k éles környezetben nem javasoljuk**. Előzetes verziójú funkciók csak tesztelési és módosíthatja.

## <a name="conclusion"></a>Összegzés
Ha további információk az Azure Search .NET SDK van szüksége, tekintse meg a [.NET – útmutató](search-howto-dotnet-sdk.md).

Az SDK-val szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megkérdezi a Súgó a [Azure Search MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a problémát fájl is a [Azure .NET SDK GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címének és a "[az Azure Search]" előtag.

Köszönjük, hogy az Azure Search használatával!
