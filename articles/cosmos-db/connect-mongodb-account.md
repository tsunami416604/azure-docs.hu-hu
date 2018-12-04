---
title: Az Azure Cosmos DB-fiók MongoDB-kapcsolati sztring
description: Ismerje meg, a MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-fiók MongoDB-kapcsolati karakterlánc használatával.
keywords: mongodb kapcsolati sztring
services: cosmos-db
author: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: a78a77e16e9a810c0be03656aa48b02cc8e6e5e6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849258"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása
Ismerje meg, a MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-fiók MongoDB-kapcsolati karakterlánc használatával. Ezután használhatja az Azure Cosmos DB-adatbázis az adatokat, a MongoDB-alkalmazást az áruházból. 

Ebben az oktatóanyagban a kapcsolati sztring adatainak lekéréséhez két lehetőséget biztosít:

- [A gyors üzembe helyezési módszer](#QuickstartConnection), .NET, Node.js, a MongoDB Shell, Java és Python-illesztőprogramok való használatra
- [Az egyéni kapcsolati karakterlánc mód](#GetCustomConnection), más illesztőprogramok való használatra

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik Azure-fiókkal, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) most. 
- Azure Cosmos DB-fiók. Útmutatásért lásd: [-MongoDB API webalkalmazás létrehozása .NET-tel és az Azure Portalon](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>A gyors üzembe helyezési használatával a MongoDB-kapcsolati sztring lekérése
1. Egy böngészőből, jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API a MongoDB-fiókhoz. 
3. A fiók panel bal oldali panelen kattintson a **gyors üzembe helyezési**. 
4. Válassza ki a platformot (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Ha nem látja, hogy az illesztőprogram vagy eszköz szerepel a listán, ne aggódjon – a Microsoft folyamatosan a dokumentum további kapcsolat-kódtöredékek. Várjuk a megjegyzéseket alább az Önnek tetszőt megtekintéséhez. Ismerje meg, hogyan írhat saját kapcsolatot, olvassa el [a fiók kapcsolati sztringek adatainak lekérése](#GetCustomConnection).
5. Másolja és illessze be a kódrészletet a MongoDB-alkalmazást.

    ![Gyors üzembe helyezési panel](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Testreszabhatja a MongoDB kapcsolati sztring lekérése
1. Egy böngészőből, jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API a MongoDB-fiókhoz. 
3. A fiók panel bal oldali panelen kattintson a **kapcsolati karakterlánc**. 
4. A **kapcsolati karakterlánc** panel nyílik meg. Egy illesztőprogram a mongodb-hez, beleértve a preconstructed kapcsolati karakterlánc használatával kapcsolódni a fiókjához szükséges összes információt tartalmaz.

    ![Kapcsolati sztring panel](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Kapcsolati karakterlánc követelmények
> [!Important]
> Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Az Azure Cosmos DB-fiókokhoz hitelesítés és a biztonságos kommunikáció keresztül szükséges *SSL*. 
>
>

Az Azure Cosmos DB támogatja a standard szintű MongoDB kapcsolati URI karakterláncként, van azonban néhány konkrét követelmények: Azure Cosmos DB-fiókokhoz hitelesítés és a biztonságos kommunikáció SSL-en keresztül van szükség. Így a kapcsolati karakterlánc formátuma a következő:

    mongodb://username:password@host:port/[database]?ssl=true

Ez a karakterlánc értékét érhetők el a **kapcsolati karakterlánc** korábban bemutatott panelen:

* Username (kötelező): az Azure Cosmos DB-fiók nevét.
* Jelszó (kötelező): az Azure Cosmos DB-fiók jelszavát.
* A gazdagép (kötelező): az Azure Cosmos DB-fiók teljes Tartománynevét.
* Port (kötelező): 10255.
* Adatbázis (nem kötelező): az adatbázis, amely a kapcsolatot használ. Ha nincs adatbázis van megadva, az alapértelmezett adatbázis-e a "teszt".
* az SSL = true (kötelező)

Vegyük példaként a fiók látható a **kapcsolati karakterlánc** panelen. Egy érvényes kapcsolati karakterláncot a következő:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [a Studio 3T használata (MongoChef)](mongodb-mongochef.md) együtt egy Azure Cosmos DB API a MongoDB-fiókhoz.
* Fedezze fel az Azure Cosmos DB API a mongodb-hez megtekintésével [minták](mongodb-samples.md).
