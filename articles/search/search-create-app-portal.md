---
title: Keresési alkalmazás létrehozása Azure Portal
titleSuffix: Azure Cognitive Search
description: Futtassa az alkalmazás létrehozása (előzetes verzió) varázslót, amellyel HTML-lapokat és parancsfájlokat hozhat létre egy operatív webalkalmazáshoz. Az oldalon található egy keresősáv, az eredmények, az oldalsáv és a typeahead támogatása.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 9ed62ea6fdfc0732128ed340cc0bc48481eb73ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841080"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Gyors útmutató: keresési alkalmazás létrehozása a portálon (Azure Cognitive Search)

A portálon a **keresési alkalmazás létrehozása** varázslóval létrehozhat egy letölthető, "localhost" stílusú webalkalmazást, amely böngészőben fut. A konfigurációtól függően a létrehozott alkalmazás az első használat során működik, és élő kapcsolattal csatlakozik egy távoli indexhez. Egy alapértelmezett alkalmazás tartalmazhatja a keresősáv, az eredmények, a Sidebar-szűrők és a typeahead támogatását.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

Most frissítenie kell a [Microsoft Edge legújabb verziójára](https://www.microsoft.com/edge) , vagy a Google Chrome böngészőjét kell használnia ehhez a rövid útmutatóhoz.

[Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

[Hozzon létre egy indexet](search-create-index-portal.md) , amelyet az alkalmazás alapjaként kíván használni. 

Ez a rövid útmutató a beépített ingatlan-mintaadatok és-indexet használja, mivel miniatűr képekkel rendelkezik. Az ebben a gyakorlatban használt index létrehozásához futtassa az **adat importálása** varázslót, és válassza az *Realestate-US-Sample* adatforrást.

![adatforrások lapja mintaadatok esetén](media/search-create-app-portal/import-data-realestate.png)

Ha az index használatra kész, lépjen tovább a következő lépésre.

## <a name="start-the-wizard"></a>A varázsló elindítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és [Keresse meg a keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Az Áttekintés oldalon, a lap közepén található hivatkozások közül válassza az **indexek**lehetőséget. 

1. Válassza a *Realestate-US-Sample-index* elemet a meglévő indexek listájából.

1. A varázsló elindításához az index lap tetején válassza a **Search alkalmazás létrehozása (előzetes verzió)** lehetőséget.

1. A varázsló első lapján jelölje be a **több eredetű erőforrás-megosztás (CORS) engedélyezése** lehetőséget az CORS támogatásának hozzáadásához az index definíciójában. Ez a lépés nem kötelező, de a helyi webalkalmazás nem csatlakozik a távoli indexhez.

## <a name="configure-search-results"></a>Keresési eredmények konfigurálása

A varázsló alapvető elrendezést biztosít a megjelenített keresési eredmények számára, amely tartalmazza a miniatűr képek, a cím és a Leírás területét. Az egyes elemek biztonsági mentése az index egyik mezője, amely az adatokat biztosítja. 

1. A miniatűr mezőben válassza ki a *miniatűr* mezőt a *Realestate-US-Sample* indexben. Ez a minta egy *miniatűr*nevű mezőben tárolt, URL-címekkel elválasztott lemezképek formájában tartalmaz képminiatűröket. Ha az index nem rendelkezik rendszerképekkel, hagyja üresen ezt a mezőt.

1. A title (cím) mezőben válasszon ki egy mezőt, amely az egyes dokumentumok egyediségét közvetíti. Ebben a példában a tőzsdei azonosító egy ésszerű kijelölés.

1. A Description (Leírás) mezőben válasszon ki egy olyan mezőt, amely segítséget nyújthat valakinek annak eldöntésében, hogy az adott dokumentumra kattint-e.

![adatforrások lapja mintaadatok esetén](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Oldalsáv hozzáadása

A Search szolgáltatás támogatja a csiszolt navigációt, amely gyakran oldalsávként jelenik meg. Az aspektusok szűrhető és sokrétű mezőkön alapulnak, az index sémában kifejezett módon.

Az Azure Cognitive Searchban a csiszolatlan navigáció egy halmozott szűrési élmény. Egy kategórián belül több szűrő kiválasztásával kibővíti az eredményeket (például kiválasztja a Seattle és a Bellevue lehetőséget a városon belül). A kategóriák között több szűrő kiválasztásával szűkíti az eredményeket.

> [!TIP]
> A teljes index séma a portálon tekinthető meg. Keresse meg az **index Definition (JSON)** hivatkozást az egyes indexek áttekintő oldalán. A csiszolatlan navigálásra jogosult mezők "szűrhetők: true" és "sokrétű: true" attribútumokkal rendelkeznek.

A dimenziók aktuális kijelölésének elfogadása, és a következő lapra való továbblépés.


## <a name="add-typeahead"></a>Typeahead hozzáadása

A typeahead funkció automatikus kiegészítés és lekérdezési javaslatok formájában érhető el. A varázsló támogatja a lekérdezési javaslatokat. A felhasználó által megadott billentyűleütés-bemenetek alapján a Search szolgáltatás a "befejezett" lekérdezési karakterláncok listáját adja vissza, amelyek bemenetként választhatók ki.

A javaslatok meghatározott mező-definíciókban engedélyezettek. A varázsló lehetővé teszi, hogy beállítsa, hogy mennyi információ szerepeljen a javaslatban. 

Az alábbi képernyőfelvételen a varázsló azon beállításai láthatók, amelyek az alkalmazás megjelenített oldalával vannak összefoglalva. Megtekintheti a mezők kiválasztásának módját, valamint azt, hogy a rendszer hogyan használja a "mezőnév megjelenítése" kifejezést a javaslaton belüli címkézés befoglalására vagy kizárására.

![Lekérdezési javaslat konfigurálása](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Létrehozás, letöltés és végrehajtás

1. A HTML-fájl létrehozásához válassza a **keresési alkalmazás létrehozása** lehetőséget.

1. Ha a rendszer kéri, válassza az **alkalmazás letöltése** lehetőséget a fájl letöltéséhez.

1. Nyissa meg a fájlt. Az alábbi képernyőképhez hasonló oldalnak kell megjelennie. Adjon meg egy kifejezést, és használjon szűrőket a szűkített eredményekhez. 

Az alapul szolgáló index olyan fiktív, generált adatokból áll, amelyek a dokumentumok között duplikálva vannak, és a leírások időnként nem egyeznek meg a képpel. Az alkalmazások saját indexek alapján történő létrehozásakor több összetartó élményre számíthat.

![Az alkalmazás futtatása](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Habár az alapértelmezett alkalmazás a kezdeti feltárás és a kis feladatok esetében hasznos, az API-k korán történő áttekintése segít megérteni a fogalmakat és a munkafolyamatot mélyebb szinten:

> [!div class="nextstepaction"]
> [Index létrehozása a .NET SDK használatával](https://docs.microsoft.com/azure/search/search-create-index-dotnet)