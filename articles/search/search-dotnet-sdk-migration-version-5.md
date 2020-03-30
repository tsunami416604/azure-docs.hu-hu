---
title: Frissítés az Azure Search .NET SDK 5-ös verziójára
titleSuffix: Azure Cognitive Search
description: A kódot az Azure Search .NET SDK 5-ös verziójába telepítheti át a régebbi verziókból. Ismerje meg, hogy mi az új, és milyen kódmódosításokra van szükség.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793025"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Frissítés az Azure Search .NET SDK 5-ös verziójára

Ha az [Azure Search .NET SDK](https://aka.ms/search-sdk)4.0-s vagy régebbi verzióját használja, ez a cikk segít az alkalmazás 5-ös verzióhasználatára való frissítésében.

Az SDK általánosabb forgatókönyvét példákkal együtt az [Azure Search használata .NET alkalmazásból](search-howto-dotnet-sdk.md)című témakörben talál.

Az Azure Search .NET SDK 5-ös verziója tartalmaz néhány módosítást a korábbi verziókhoz. Ezek többnyire kisebb, így a kód módosítása csak minimális erőfeszítést igényel. Az új SDK-verzió használatához a [frissítés lépései](#UpgradeSteps) című témakörben talál útmutatást a kód módosításához.

> [!NOTE]
> Ha a 2.0-s vagy újabb verziót használja, először frissítsen a 3-as verzióra, majd frissítsen az 5-ös verzióra. További információt [az Azure Search .NET SDK 3-as verziójára való frissítés](search-dotnet-sdk-migration.md) című témakörben talál.
>
> Az Azure Search szolgáltatáspéldány a REST API számos verzióját támogatja, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de azt javasoljuk, hogy telepítse át a kódot a legújabb verzió használatára. A REST API használatakor meg kell adnia az API-verziót minden kérelemben az api-version paraméteren keresztül. A .NET SDK használatakor a használt SDK-verzió határozza meg a REST API megfelelő verzióját. Ha egy régebbi SDK-t használ, továbbra is futtathatja a kódot módosítások nélkül, még akkor is, ha a szolgáltatás egy újabb API-verzió támogatásához lett frissítve.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Az 5-ös verzió újdonságai
Az Azure Search .NET SDK 5-ös verziója az Azure Search REST API legújabb, általánosan elérhető verzióját célozza meg, különösen a 2017-11-11.5. Ez lehetővé teszi az Azure Search új funkcióinak használatát egy .NET alkalmazásból, beleértve a következőket:

* [Szinonimák](search-synonyms.md).
* Most már programozott módon hozzáférhet az indexelők `Warning` végrehajtási `IndexerExecutionResult` előzményeihez (további részletekért tekintse meg a [.NET referencia](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) tulajdonságát).
* A .NET Core 2 támogatása.
* Az új csomagstruktúra csak az SDK azon részeinek használatát támogatja, amelyekre szüksége van (a részletekért [lásd: Változások megbontása az 5-ös verzióban).](#ListOfChanges)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse NuGet-referenciáját a NuGet Csomagkezelő konzol `Microsoft.Azure.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. Attól függően, hogy a kód épül, előfordulhat, hogy sikeresen újraépül. Ha igen, akkor készen áll az indulásra!

Ha a build sikertelen, a következőhez hasonló buildhibát kell látnia:

    The name 'SuggesterSearchMode' does not exist in the current context

A következő lépés a buildhiba kijavítása. Az [5-ös verzió módosításai című témakörben](#ListOfChanges) részletesen tudni szeretné, hogy mi okozza a hibát, és hogyan javíthatja ki.

Kérjük, vegye figyelembe, hogy az Azure Search .NET SDK csomagolásának változásai miatt újra kell építenie az alkalmazást az 5-ös verzió használatához. Ezek a változások részletesen [Breaking változások 5-ös verzió](#ListOfChanges).

Az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további buildfigyelmeztetések jelenhetnek meg. A figyelmeztetések utasításokat tartalmaznak arra vonatkozóan, hogy mit használjanak az elavult szolgáltatás helyett. Ha például az alkalmazás `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` a módszert használja, akkor a következő figyelmeztetést kell kapnia, hogy "Ez a viselkedés alapértelmezés szerint engedélyezve van, így a metódus meghívása már nem szükséges."

Miután kijavította a buildelési hibákat vagy figyelmeztetéseket, módosíthatja az alkalmazást, hogy kihasználhassa az új funkciók előnyeit, ha szeretné. Az SDK új funkcióit az [5-ös verzió újdonságai](#WhatsNew)részletezik.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Változások megtörése az 5-ös verzióban

### <a name="new-package-structure"></a>Új csomagstruktúra

Az 5-ös verzió legjelentősebb változása az, hogy a `Microsoft.Azure.Search` szerelvényt és annak tartalmát négy különálló egységre osztották, amelyeket most négy különálló NuGet csomagként terjesztenek:

 - `Microsoft.Azure.Search`: Ez egy metacsomag, amely tartalmazza az összes többi Azure Search-csomagot függőségként. Ha az SDK egy korábbi verziójáról frissít, egyszerűen frissítse ezt a csomagot, és az újraépítés elegendő legyen az új verzió használatának megkezdéséhez.
 - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha az Azure Search használatával .NET alkalmazást fejleszt, és csak dokumentumokat kell lekérdeznie vagy frissítenie az indexekben. Ha indexeket, szinonimátleképezéseket vagy más szolgáltatásszintű erőforrásokat `Microsoft.Azure.Search` is létre kell hoznia vagy frissítenie, használja inkább a csomagot.
 - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha a .NET automatizálási fejlesztése során azure Search-indexeket, szinonimát leképezőket, indexelőket, adatforrásokat vagy más szolgáltatásszintű erőforrásokat kezelhet. Ha csak az indexekben lévő dokumentumokat kell lekérdeznie vagy frissítenie, használja inkább a `Microsoft.Azure.Search.Data` csomagot. Ha az Azure Search összes funkciójára `Microsoft.Azure.Search` szüksége van, használja inkább a csomagot.
 - `Microsoft.Azure.Search.Common`: Az Azure Search .NET-kódtárai hoz szükséges gyakori típusok. Ezt a csomagot nem kell közvetlenül az alkalmazásban használnia; Ez csak azt jelentette, hogy kell használni, mint a függőség.
 
Ez a változás technikailag megszakad, mivel sok típus tették át a szerelvények között. Ezért van szükség az alkalmazás újraépítésére az SDK 5-ös verziójára való frissítéshez.

Van egy kis számú egyéb törés változások 5-ös verzió, amely szükség lehet a kód módosítása mellett az alkalmazás újjáépítése.

### <a name="change-to-suggesters"></a>Váltás javaslatjavasolókra 

A `Suggester` konstruktor már nem rendelkezik paraméterrel `enum` a hoz. `SuggesterSearchMode` Ennek a felsorításnak csak egy értéke volt, ezért felesleges volt. Ha ennek eredményeként buildhibákat lát, egyszerűen távolítsa `SuggesterSearchMode` el a paraméterre mutató hivatkozásokat.

### <a name="removed-obsolete-members"></a>Elavult tagok eltávolítása

Előfordulhat, hogy a korábbi verziókban elavultként megjelölt, majd az 5-ös verzióban eltávolított metódusokhoz vagy tulajdonságokhoz kapcsolódó buildhibák jelennek meg. Ha ilyen hibákkal találkozik, a következőképpen oldhatja meg őket:

- Ha a `IndexingParametersExtensions.IndexStorageMetadataOnly` módszert használta, `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` használja helyette.
- Ha a `IndexingParametersExtensions.SkipContent` módszert használta, `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` használja helyette.

### <a name="removed-preview-features"></a>Az előzetes verzió funkcióinak eltávolítása

Ha a 4.0-s verziójú előzetes verzióról az 5-ös verzióra frissít, vegye figyelembe, hogy a JSON-tömb és a Blob Indexelők CSV-elemzési támogatása megszűnt, mivel ezek a szolgáltatások még előzetes verzióban vannak. Pontosabban, az `IndexingParametersExtensions` osztály következő módszereit eltávolították:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás szigorú függőséget gyakorol ezektől a funkcióktól, nem fog tudni frissíteni az Azure Search .NET SDK 5-ös verziójára. Továbbra is használhatja a 4.0-s verziójú előzetes verziót. Kérjük azonban, vegye figyelembe, hogy **nem javasoljuk az előzetes SDK-k használatát az éles alkalmazásokban.** Az előnézeti funkciók csak kiértékelésre szolgálnak, és változhatnak.

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége az Azure Search .NET SDK használatával kapcsolatos részletekről, olvassa el a [.NET útmutatóját.](search-howto-dotnet-sdk.md)

Örömmel fogadjuk az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, nyugodtan kérjen tőlünk segítséget [stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, az [Azure .NET SDK GitHub tárházban](https://github.com/Azure/azure-sdk-for-net/issues)nyújthat be problémát. Győződjön meg arról, hogy előtag a probléma címe a "[Azure Search]".

Köszönjük, hogy az Azure Search-et használja!
