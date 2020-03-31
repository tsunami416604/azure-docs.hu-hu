---
title: Keresési alkalmazás létrehozása az Azure Portalon
titleSuffix: Azure Cognitive Search
description: Az Alkalmazás létrehozása (előzetes verzió) varázsló futtatásával html-lapokat és parancsfájlt hozhat létre egy működő webalkalmazáshoz. A lap tartalmaz egy keresősávot, az eredményterületet, az oldalsávot és a typeahead támogatást.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369714"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Rövid útmutató: Keresési alkalmazás létrehozása a portálon (Azure Cognitive Search)

A portál **Keresés létrehozása varázslójával** egy böngészőben futó, letölthető, "localhost" stílusú webalkalmazást hozhat létre. A konfigurációtól függően a létrehozott alkalmazás első használatkor működik, és élő kapcsolatban áll egy távoli indexszel. Az alapértelmezett alkalmazás tartalmazhat keresősávot, találatterületet, oldalsávszűrőket és typeahead-támogatást.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

Frissítsen a [Microsoft Edge legújabb verziójára,](https://www.microsoft.com/edge) vagy használja a Google Chrome böngészőjét ehhez a rövid útmutatóhoz.

[Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat. 

[Hozzon létre egy indexet,](search-create-index-portal.md) amelyet az alkalmazás alapjaként használhat. 

Ez a rövid útmutató a beépített Ingatlan mintaadatokat és indexet használja, mivel miniatűr képekkel rendelkezik (a varázsló támogatja a képek hozzáadását az eredménylaphoz). Az ebben a gyakorlatban használt index létrehozásához futtassa az **Adatok importálása** varázslót, és válassza ki az *ingatlan-us-minta* adatforrást.

![adatforráslap mintaadatokhoz](media/search-create-app-portal/import-data-realestate.png)

Amikor az index készen áll a használatra, lépjen tovább a következő lépésre.

## <a name="start-the-wizard"></a>A varázsló indítása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és [keresse meg a keresési szolgáltatást.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. Az Áttekintés lap a lap közepén található hivatkozások között válassza az **Indexek**lehetőséget. 

1. Válassza ki *az ingatlan-us-minta-indexet* a meglévő indexek listájából.

1. A varázsló elindításához válassza a **keresési alkalmazás létrehozása (előnézet) választógombot** az indexlap tetején.

1. Az első varázslólapon válassza a CorS-támogatás indexdefinícióhoz való hozzáadásához válassza a CorS-támogatás engedélyezéséhez válassza a **CORS-támogatás engedélyezése (CORS)** lehetőséget. Ez a lépés nem kötelező, de a helyi webalkalmazás nem csatlakozik a távoli index nélkül.

## <a name="configure-search-results"></a>Keresési eredmények konfigurálása

A varázsló a megjelenített keresési eredmények alapelrendezését biztosítja, amely helyet biztosít a miniatűr képek, a cím és a leírás számára. Ezen elemek mindegyikének hátlapja az index egy olyan mezője, amely az adatokat szolgáltatja. 

1. A Miniatűrben válassza ki a *miniatűr* mezőt az *ingatlan-us-minta* indexben. Ez a minta történetesen tartalmazza a kép bélyegképeit URL-címzésű képek formájában, amelyeket egy *miniatűr*nek nevezett mezőben tárolnak. Ha az indexben nincsenek képek, hagyja üresen ezt a mezőt.

1. A Cím mezőben válasszon egy olyan mezőt, amely az egyes dokumentumok egyediségét közvetíti. Ebben a mintában a listaazonosító ésszerű választás.

1. A Leírás mezőben válasszon olyan mezőt, amely olyan részleteket tartalmaz, amelyek segíthetnek valakinek eldönteni, hogy átkattintanak-e az adott dokumentumra.

![adatforráslap mintaadatokhoz](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Oldalsáv hozzáadása

A keresési szolgáltatás támogatja a gyakran oldalsávként megjelenített felületalapú navigációt. A lapadatok szűrhető és átlaptáblamezőkön alapulnak, az indexsémában kifejezettmezőkalapján.

Az Azure Cognitive Search, a sokoldalú navigáció egy kumulatív szűrési élmény. Egy kategórián belül a több szűrő kijelölése kibővíti az eredményeket (például a Seattle és a Bellevue lehetőséget választva a városon belül). A kategóriák között a több szűrő kijelölése szűkíti az eredményeket.

> [!TIP]
> Megtekintheti a teljes indexsémát a portálon. Keresse meg az **Index definíció (JSON)** hivatkozást az egyes indexek áttekintő lapján. A jellemzőalapú navigációra jogosult mezők "szűrhető: igaz" és "facetable: true" attribútumokkal rendelkeznek.

Fogadja el a laptatok aktuális kiválasztását, és folytassa a következő oldalra.


## <a name="add-typeahead"></a>Typeahead hozzáadása

A Typeahead funkció automatikus kiegészítés és lekérdezési javaslatok formájában érhető el. A varázsló támogatja a lekérdezési javaslatokat. A felhasználó által megadott billentyűleütés-bemenetek alapján a keresési szolgáltatás a bemenetként kijelölhető "befejezett" lekérdezési karakterláncok listáját adja vissza.

A javaslatok engedélyezettek bizonyos meződefiníciókban. A varázsló lehetőséget ad annak konfigurálására, hogy mennyi információ szerepel a javaslatban. 

A következő képernyőképen a varázsló beállításai láthatók, amelyek et egy megjelenített oldallal együtt jelenítmeg az alkalmazásban. Megtekintheti a mezőbeállítások használatának módját, valamint azt, hogy a "Mezőnév megjelenítése" hogyan használható a címkézés nek a javaslatba való felvételére vagy kizárására.

![Lekérdezési javaslat konfigurációja](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Létrehozás, letöltés és végrehajtás

1. A HTML-fájl létrehozásához válassza a **Keresési alkalmazás létrehozása** lehetőséget.

1. Amikor a rendszer kéri, válassza **az Alkalmazás letöltése** lehetőséget a fájl letöltéséhez.

1. Nyissa meg a fájlt. Az alábbi képernyőképhez hasonló oldalt kell látnia. Írjon be egy kifejezést, és szűrők használatával szűkítse az eredményeket. 

Az alapul szolgáló index fiktív, generált adatokból áll, amelyek et dokumentumok között duplikáltak, és a leírások néha nem egyeznek meg a képpel. Egy alkalmazás saját indexealapján történő létrehozásakor még egyigazságosabb élményre számíthat.

![Az alkalmazás futtatása](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Bár az alapértelmezett alkalmazás hasznos a kezdeti feltáráshoz és a kisebb feladatokhoz, az API-k korai áttekintése segít a fogalmak és a munkafolyamat mélyebb szinten való megértésében:

> [!div class="nextstepaction"]
> [Index létrehozása a .NET SDK használatával](https://docs.microsoft.com/azure/search/search-create-index-dotnet)