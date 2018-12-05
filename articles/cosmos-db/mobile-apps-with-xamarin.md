---
title: Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával
description: Oktatóanyag egy Xamarin iOS-, Android- vagy Forms-alkalmazás létrehozásához az Azure Cosmos DB használatával. Az Azure Cosmos DB egy gyors, világméretű, felhőalapú adatbázis mobilalkalmazásokhoz.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: d5c17325bfccef2c97378cb54d8843290cc98635
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878006"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

A legtöbb mobilalkalmazásnak felhőbeli adattárolásra van szüksége, az Azure Cosmos DB pedig egy felhőbeli adatbázis mobilalkalmazásokhoz. Tartalmaz mindent, amire egy mobilalkalmazás-fejlesztőnek szüksége lehet. Ez egy teljes körűen felügyelt, szolgáltatásként nyújtott adatbázis, amely igény szerint skálázható. Képes transzparens módon eljuttatni az adatokat az alkalmazáshoz, függetlenül attól, hogy a felhasználók a világ mely pontján tartózkodnak. Az [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) használatával lehetővé teszi, hogy a xamarinos mobilalkalmazások közvetlenül, középső réteg nélkül kommunikáljanak az Azure Cosmos DB-vel.

Ez a cikk egy oktatóanyagot tartalmaz mobilalkalmazások létrehozásához a Xamarin és az Azure Cosmos DB használatával. Az oktatóanyag teljes forráskódját megtalálja a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) oldalon, beleértve a felhasználók és engedélyek kezelését.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Az Azure Cosmos DB képességei mobilalkalmazásokhoz
Az Azure Cosmos DB az alábbi fő lehetőségeket nyújtja a mobilalkalmazás-fejlesztőknek:

![Az Azure Cosmos DB képességei mobilalkalmazásokhoz](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Séma nélküli adatok részletes lekérdezése. Az Azure Cosmos DB az adatokat séma nélküli JSON-dokumentumok formájában tárolja, heterogén gyűjteményekben. [Részletes és gyors lekérdezéseket](how-to-sql-query.md) nyújt, amelyekhez nem kell sémákkal vagy indexekkel foglalkoznia.
* Gyors adatátvitel. Az Azure Cosmos DB mindössze néhány ezredmásodperc alatt végzi a dokumentumok írását és olvasását. A fejlesztők megadhatják a kívánt adatátviteli sebességet, amelyre vonatkozóan az Azure Cosmos DB 99,99%-os rendelkezésre állású SLA-t biztosít minden enyhén korlátozott konzisztenciájú egyrégiós és többrégiós fiókhoz, valamint 99,999%-os olvasási rendelkezésre állást minden többrégiós adatbázisfiókhoz.
* Korlátlan skálázhatóság. Azure Cosmos DB-gyűjteménye [az alkalmazással együtt növekszik](partition-data.md). Kezdetben a kis adatmennyiségek és a másodpercenkénti néhány száz kérés lehet a jellemző. A gyűjtemények vagy adatbázisok olyan mértékig növekedhetnek, hogy több petabájtnyi adatot tartalmazhatnak, és hatalmas mennyiségű, akár másodpercenként több százmillió kérést szolgálhatnak ki.
* Globálisan elosztott. A mobilalkalmazások felhasználói általában úton vannak, gyakran a világ legtávolabbi pontjai között. Az Azure Cosmos DB egy [globálisan elosztott adatbázis](distribute-data-globally.md). Kattintson a térképre, és tegye elérhetővé adatait a felhasználói számára.
* Beépített részletes hitelesítés. Az Azure Cosmos DB-vel könnyedén, összetett egyéni engedélyezési kódok használata nélkül implementálhat olyan népszerű mintákat, mint a [felhasználónkénti adatok](https://aka.ms/documentdb-xamarin-todouser) vagy a többfelhasználós megosztott adatok.
* Térinformatikai lekérdezések. Napjainkban számos mobilalkalmazás nyújt földrajzi helyhez kötött funkciókat. A [térinformatikai típusok](geospatial.md) kimagasló támogatásával az Azure Cosmos DB nagyban megkönnyíti az efféle élmények megteremtését.
* Bináris mellékletek. Az alkalmazásadatok gyakran tartalmaznak bináris blobokat. A mellékletek natív támogatásának köszönhetően az Azure Cosmos DB könnyebben használható teljes körűen az alkalmazásadatokhoz.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB és Xamarin oktatóanyag
A következő oktatóanyag ismerteti, hogyan lehet létrehozni egy mobilalkalmazást a Xamarin és az Azure Cosmos DB használatával. Az oktatóanyag teljes forráskódját megtalálja a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) oldalon.

### <a name="get-started"></a>Bevezetés
Az Azure Cosmos DB használatát könnyű elsajátítani. Lépjen az Azure Portalra, és hozzon létre egy új Azure Cosmos DB-fiókot. Kattintson az **Első lépések** lapra. Töltse le a Xamarin Forms feladatlista mintáját, amely már csatlakoztatva van az Azure Cosmos DB-fiókjához. 

![Rövid útmutató: Az Azure Cosmos DB és mobilalkalmazások](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Vagy ha már rendelkezik Xamarin-alkalmazással, hozzáadhatja az [Azure Cosmos DB NuGet-csomagot](sql-api-sdk-dotnet-core.md). Az Azure Cosmos DB támogatja a Xamarin.iOS, a Xamarin.Android és a Xamarin Forms közös kódtárakat.

### <a name="work-with-data"></a>Adatok használata
Az Azure Cosmos DB az adatokat séma nélküli JSON-dokumentumokban tárolja, heterogén gyűjteményekben. Ugyanabban a gyűjteményben különböző struktúrájú dokumentumok is tárolhatók:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

A Xamarin-projektekben nyelvintegrált lekérdezéseket futtathat a séma nélküli adatokra vonatkozóan:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Felhasználók hozzáadása
Sok bevezető mintához hasonlóan a letöltött Azure Cosmos DB-minta hitelesítése a szolgáltatásban egy olyan fő kulccsal történik, amely az alkalmazás kódjába van beleírva. Az alapértelmezett eljárás használata nem célszerű, ha az alkalmazást a helyi emulátoron kívül bárhol máshol is futtatni kívánja. Ha egy illetéktelen felhasználó hozzájut a fő kulcshoz, az Azure Cosmos DB-fiókjában található összes adathoz hozzáférhet. Ehelyett az ajánlott módszer az, ha az alkalmazás csak a bejelentkezett felhasználó rekordjaihoz fér hozzá. Az Azure Cosmos DB lehetővé teszi a fejlesztőknek, hogy az alkalmazásoknak olvasási vagy olvasási/írási engedélyeket adjanak egy gyűjteményre, egy partíciókulcs szerint csoportosított dokumentumkészletre vagy egy adott dokumentumra. 

Az alábbi lépésekkel módosítható a teendőlista alkalmazás egy többfelhasználós teendőlista alkalmazássá: 

  1. Az alkalmazáshoz hozzáadhat a Facebookon, Active Directoryn vagy bármely egyéb szolgáltatón keresztül történő bejelentkezési lehetőséget is.

  2. Hozzon létre egy Azure Cosmos DB UserItems gyűjteményt, amelynek partíciókulcsa **/felhasználóazonosító**. A gyűjtemény partíciókulcsának megadásával az Azure Cosmos DB-t végtelenül lehet skálázni az alkalmazás felhasználószámának növekedésével párhuzamosan, miközben továbbra is gyorsak maradnak a lekérdezések.

  3. Adja hozzá az Azure Cosmos DB erőforrásjogkivonat-közvetítőjét. Ez az egyszerű webes API hitelesíti a felhasználókat, és rövid élettartamú jogkivonatokat bocsát ki a bejelentkezett felhasználóknak, akik csak a saját partíciójukon belül tárolt dokumentumokhoz férhetnek hozzá. Ebben a példában az erőforrásjogkivonat-közvetítőt az App Services üzemelteti.

  4. Módosítsa az alkalmazást úgy, hogy az a Facebookon keresztül végezze az erőforrásjogkivonat-közvetítőbe való hitelesítést, és a bejelentkezett Facebook-felhasználók számára kérje le az erőforrás-jogkivonatokat. Ezt követően hozzáférhet a felhasználók adataihoz a UserItems gyűjteményben.  

A minta teljes mintakódját megtalálja az [Erőforrásjogkivonat-közvetítő a GitHubon](https://aka.ms/documentdb-xamarin-todouser) oldalon. Az alábbi ábra illusztrálja a megoldást:

![Az Azure Cosmos DB felhasználó- és engedélyközvetítője](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Ha azt szeretné, hogy a két felhasználó hozzáférjen ugyanahhoz a teendőlistához, hozzáadhat további engedélyeket a hozzáférési jogkivonathoz az erőforrásjogkivonat-közvetítőben.

### <a name="scale-on-demand"></a>Igény szerinti méretezés
Az Azure Cosmos DB egy felügyelt, szolgáltatásként nyújtott adatbázis. Ahogy a felhasználóbázis növekszik, nem kell aggódnia a virtuális gépek üzembe helyezése vagy a magok számának növelése miatt. Mindössze meg kell adnia az Azure Cosmos DB-nek, hogy az alkalmazásának másodpercenként hány műveletre (mekkora átviteli sebességre) van szüksége. Az átviteli sebességet a **Skálázás** lapon adhatja meg, az alapján, hogy másodpercenként hány úgynevezett kérelemegységre van szüksége. Például egy 1 KB méretű dokumentum olvasási művelete igényel 1 kérelemegységet. Emellett hozzáadhat riasztásokat az **átviteli** metrikához az adatforgalom növekedésének monitorozására, és a riasztások életbe lépésekor programozott módon módosíthatja az átviteli sebességet.

![Az Azure Cosmos DB átviteli sebességének igény szerinti skálázása](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Váltson világszintre
Ahogy alkalmazása népszerűbbé válik, a világ minden tájáról szerezhet új felhasználókat. Vagy az is lehet, hogy csak fel szeretne készülni a váratlan eseményekre. Lépjen az Azure Portalra, és nyissa meg az Azure Cosmos DB-fiókját. Kattintson a térképre, ha szeretné, hogy adatai folyamatosan replikálva legyenek a világ bármely régiójában. Ez a funkció elérhetővé teszi az adatokat, függetlenül attól, hogy a felhasználók hol tartózkodnak. A váratlan helyzetekre való felkészültség érdekében hozzáadhat feladatátvételi szabályzatokat is.

![Az Azure Cosmos DB skálázása több földrajzi régióra](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Gratulálunk! Elkészítette a megoldást és a mobilalkalmazást a Xamarin és az Azure Cosmos DB segítségével. Hasonló lépésekkel hozhat létre Cordova-alkalmazásokat az Azure Cosmos DB JavaScript SDK-val, illetve natív iOS-/Android-alkalmazásokat az Azure Cosmos DB REST API-kkal.

## <a name="next-steps"></a>További lépések
* Forráskód megtekintése a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) oldalon.
* Az [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) letöltése.
* További kódminták [.NET-alkalmazásokhoz](sql-api-dotnet-samples.md).
* Az [Azure Cosmos DB részletes lekérdezési képességeinek](how-to-sql-query.md) ismertetése.
* Az [Azure Cosmos DB térinformatikai támogatásának](geospatial.md) ismertetése.



