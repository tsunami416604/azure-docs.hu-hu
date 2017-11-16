---
title: "Mobil a Xamarinnal és Azure Cosmos DB használó alkalmazások |} Microsoft Docs"
description: "Egy oktatóanyag, amely a Xamarin iOS, Android vagy űrlapok alkalmazás Azure Cosmos DB használatával. Azure Cosmos DB egy gyors, a bolygónk méretezés, a felhő adatbázis mobilalkalmazásokhoz."
services: cosmos-db
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 5ab36a16ca87ae7a3e7b26571fc6aadd108841c9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Mobil a Xamarinnal és Azure Cosmos DB használó alkalmazások
Szükség van a felhőben tárolt adatok a legtöbb mobileszköz-alkalmazások, és Azure Cosmos DB mobilalkalmazások felhő adatbázisába. Minden mobileszköz fejlesztőnek rendelkezik. Az igény szerinti méretezi szolgáltatásként is teljes körűen felügyelt adatbázis. Azt is kerüljön adatait az alkalmazás átlátható módon, a felhasználók a világ minden táján helyétől. Használatával a [Azure Cosmos DB .NET Core SDK](documentdb-sdk-dotnet-core.md), engedélyezheti a Xamarin mobilalkalmazások együttműködhet közvetlenül az Azure Cosmos DB, a középső réteg nélkül.

Ez a cikk nyújt segítséget a Xamarinnal és Azure Cosmos DB mobilalkalmazások létrehozása. Az oktatóanyag: a teljes forráskód található [Xamarinnal és Azure Cosmos DB a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), beleértve a mobileszközök kezelése a felhasználókat és engedélyeket.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Az Azure Cosmos DB képességet biztosít a mobile apps szolgáltatásban
Azure Cosmos-adatbázis az alábbi főbb képességeket biztosít a mobilalkalmazás-fejlesztőknek:

![Az Azure Cosmos DB képességet biztosít a mobile apps szolgáltatásban](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* A lekérdezések gazdag séma nélküli adatokat. Azure Cosmos-adatbázis a JSON-dokumentumokként séma nélküli heterogén gyűjteményekben tárolja az adatokat. Kínál [gazdag és gyors lekérdezéseket](documentdb-sql-query.md) nem kell aggódnia sémák vagy az indexek.
* Gyors teljesítmény. Írási és olvasási Azure Cosmos DB-dokumentumok csak néhány ezredmásodperc vesz igénybe. A fejlesztők megadhatja az átviteli sebesség van szükségük, és Azure Cosmos DB eleget tegyen azt az összes egyetlen régión és az összes több területi fiókok rendelkezésre állás 99,99 % SLA laza konzisztencia és 99.999 %, olvassa el az összes fiókot a több területi adatbázis rendelkezésre állásának .
* Korlátlan skála. Az Azure Cosmos DB gyűjtemények [nő, ahogy az alkalmazás forgalmához igazítható](partition-data.md). Kisebb adatméret és átviteli kérések száma másodpercenként több száz indítható. A gyűjtemények a petabájtokig tetszőlegesen nagy átviteli sebesség és a kérések száma másodpercenként több millió száz növelhető.
* Globálisan elosztott. Mobilalkalmazás felhasználók vannak útközben, gyakran keresztül történik. Az Azure Cosmos DB van egy [globálisan elosztott adatbázis](distribute-data-globally.md). Kattintson a térkép, hogy a felhasználók számára az adatok.
* Beépített gazdag engedélyezési. Az Azure Cosmos DB, könnyen Megvalósíthat például népszerű minták [felhasználói adatok](https://aka.ms/documentdb-xamarin-todouser) vagy többfelhasználós megosztott adatok összetett egyéni engedélyezési kód nélkül.
* A földrajzi lekérdezések. Számos ma ajánlatot földrajzi környezetfüggő lép. A kiváló támogatást nyújt az [földrajzi típusok](geospatial.md), ezek a tapasztalatok elvégzéséhez egyszerűen létrehozása Azure Cosmos DB elérhetővé válnak.
* Bináris mellékleteket. Az alkalmazásadatok gyakran bináris blobot tartalmaz. Támogatja a mellékleteket megkönnyíti az Azure Cosmos DB használják a lehetőségekkel a az alkalmazás adatokat.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB és Xamarin útmutató
A következő oktatóanyag bemutatja, hogyan hozhat létre mobilalkalmazás Xamarinnal és Azure Cosmos DB használatával. Az oktatóanyag: a teljes forráskód található [Xamarinnal és Azure Cosmos DB a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Bevezetés
Ismerkedés az Azure Cosmos DB könnyen. Nyissa meg az Azure portálra, és hozzon létre egy új Azure Cosmos DB fiókot. Kattintson a **gyors üzembe helyezési** fülre. Töltse le a Xamarin Forms tennivalók listája minta Azure Cosmos DB fiókjához már csatlakoztatva van. 

![A mobilalkalmazások Azure Cosmos DB – első lépések](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Ha egy meglévő Xamarin-alkalmazás, adhat hozzá vagy a [Azure Cosmos DB NuGet-csomag](documentdb-sdk-dotnet-core.md). Azure Cosmos DB Xamarin.IOS, Xamarin.Android, támogatja, és Xamarin Forms megosztott szalagtárak.

### <a name="work-with-data"></a>Adatok használata
A rekordok Azure Cosmos DB JSON-dokumentumokként séma nélküli heterogén gyűjteményekben vannak tárolva. Ugyanaz a gyűjtemény másik struktúrák dokumentumok tárolhat:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

A Xamarin projektekben nyelvintegrált lekérdezéseket séma nélküli adatok használhatja:

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
Például számos első lépések minták, a letöltött Azure Cosmos DB minta hitelesíti a szolgáltatás számára a fő oszlopkulcs szoftveresen kötött a az alkalmazás. Az alapértelmezett érték nem célszerű a helyi emulátor kivéve bárhol futtatni kívánt alkalmazás. Ha egy jogosulatlan felhasználó, a fő oszlopkulcs kapott, minden az adatok így vannak elrendezve Azure Cosmos DB fiókja sérülhet. Ehelyett azt szeretné, az alkalmazás csak a bejelentkezett felhasználó rekordok eléréséhez. Azure Cosmos-adatbázis lehetővé teszi, hogy a fejlesztők számára, hogy adjon alkalmazás olvasási vagy olvasási/írási engedéllyel a gyűjtemény, a partíciókulcs szerint csoportosítva dokumentumok, vagy egy adott dokumentumot. 

Kövesse az alábbi lépéseket az tennivalók listája alkalmazásnak, hogy egy többfelhasználós tennivaló alkalmazás módosításához: 

  1. Bejelentkezés Facebook-on, az Active Directory vagy bármely egyéb szolgáltató használatával adja hozzá az alkalmazást.

  2. Hozzon létre egy Azure Cosmos DB UserItems gyűjteményt a **/UserId** partíciókulcsnak. A gyűjteményhez a partíciós kulcs megadása lehetővé teszi, hogy Azure Cosmos DB méretezési végtelenül növekedésével a felhasználók száma, miközben továbbra is kínálnak gyors lekérdezéseket.

  3. Adja hozzá az Azure Cosmos DB erőforrás Token Broker. Az egyszerű webes API hitelesíti a felhasználókat és a problémák rövid élettartamú tokeneket-bejelentkezett felhasználók csak a partíción belül a dokumentumokhoz való hozzáférést. Ebben a példában erőforrás Token Broker az App Service szolgáltatásban tárolja.

  4. Módosíthat való hitelesítésre szolgáló erőforrás Token Broker Facebook alkalmazást, és a bejelentkezés Facebook-felhasználók számára a erőforrás jogkivonatok igényelhetnek. Az adatok a UserItems gyűjtemény érheti el.  

Ebben a mintában a teljes kód mintát található [erőforrás Token Broker a Githubon](http://aka.ms/documentdb-xamarin-todouser). Az ábra azt mutatja, hogy a megoldás:

![Az Azure Cosmos DB felhasználókat és engedélyeket broker](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Ha azt szeretné, hogy a két felhasználók hozzáférjenek a ugyanazt a tennivalók listájára, adhat hozzá további engedélyeket az erőforrás-jogkivonat Broker a hozzáférési jogkivonat.

### <a name="scale-on-demand"></a>Az igény szerinti méretezése
Azure Cosmos-adatbázis egy olyan felügyelt adatbázis szolgáltatásként. A felhasználói bázis növekedésével nem kell aggódnia a virtuális gépek kiépítése, vagy magok növelését. Állapítható meg, hogy Azure Cosmos DB szüksége (teljesítmény) másodpercenként hány művelet a kell. Az átviteli sebesség keresztül is megadhat a **méretezési** lap használatával az átviteli kérelem egységek (RUs) másodpercenként nevű mérték. Például egy 1 KB-os dokumentum olvasási művelet szükséges 1 RU. Azt is megteheti a riasztásokat a **átviteli** metrika az adatforgalom növekedésének figyelésére, és a teljesítményt, riasztást küld tűz módosításához.

![Az igény szerinti Azure Cosmos DB méretezési átviteli](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Nyissa meg bolygónk méretezési
Az alkalmazás növekedését időben népszerűvé vált, mert előfordulhat, hogy révén a felhasználók világszerte. Vagy lehet, hogy elő kell készíteni a előre nem látható eseményeket. Nyissa meg az Azure portálra, és nyissa meg az Azure Cosmos DB fiókját. Kattintson a térkép legyen az adatok folyamatos replikálása régiók tetszőleges számú keresztül történik. Ez a funkció elérhetővé teszi az adatok bárhol a felhasználók is. Elő kell készíteni a függő tételek feladatátvételi házirendeket is hozzáadhat.

![Az Azure Cosmos DB méretezési földrajzi régiók között](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Gratulálunk! A megoldás befejeződött, és rendelkezik a mobilalkalmazások a Xamarinnal és Azure Cosmos DB. Hasonló lépésekkel alkalmazásokat lehet készíteni Cordova segítségével az Azure Cosmos DB JavaScript SDK-t és a natív iOS vagy Android-alkalmazások Azure Cosmos DB REST API-k használatával.

## <a name="next-steps"></a>Következő lépések
* A forráskódját [Xamarinnal és Azure Cosmos DB a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Töltse le a [Azure Cosmos DB .NET Core SDK](documentdb-sdk-dotnet-core.md).
* A további mintakódok található [.NET-alkalmazások](documentdb-dotnet-samples.md).
* További tudnivalók [Azure Cosmos DB gazdag lekérdezési képességek](documentdb-sql-query.md).
* További tudnivalók [földrajzi támogatása az Azure Cosmos DB](geospatial.md).



