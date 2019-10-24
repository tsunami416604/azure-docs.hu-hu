---
title: MongoDB-alkalmazás összekötése Azure Cosmos DB
description: Ismerje meg, hogyan csatlakoztatható a MongoDB-alkalmazás a Azure Cosmos DBhoz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.reviewer: sngun
ms.openlocfilehash: 30b37b86bbe0e91887932423fa1884d8bd49333c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757007"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>MongoDB-alkalmazás összekötése Azure Cosmos DB
Megtudhatja, hogyan csatlakoztathatja a MongoDB-alkalmazást egy Azure Cosmos DBhoz egy MongoDB kapcsolati sztring használatával. Ezután egy Azure Cosmos-adatbázist is használhat a MongoDB-alkalmazás adattárában. 

Ez az oktatóanyag két módszert biztosít a kapcsolódási karakterlánc adatainak lekérésére:

- [A](#QuickstartConnection)gyors üzembe helyezési módszer .net-, Node. js-, MongoDB-rendszerhéj-, Java-és Python-illesztőprogramokkal való használatra
- [Az egyéni kapcsolatok karakterláncának módszere](#GetCustomConnection), a más illesztőprogramokkal való használathoz

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik Azure-fiókkal, hozzon létre most egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) . 
- Egy Cosmos-fiók. Útmutatásért lásd: [Webalkalmazás létrehozása a MongoDB és a .net SDK-hoz készült Azure Cosmos db API-](create-mongodb-dotnet.md)val.

## <a id="QuickstartConnection"></a>A MongoDB-kapcsolatok karakterláncának beolvasása a gyors üzembe helyezés használatával
1. Egy böngészőben jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. A **Azure Cosmos db** panelen válassza ki az API-t. 
3. A fiók panel bal oldali paneljén kattintson a **gyors üzembe helyezés**elemre. 
4. Válassza ki a platformot ( **.net**, **Node. js**, **MongoDB Shell**, **Java**, **Python**). Ha nem látja a felsorolt illesztőprogramot vagy eszközt, ne aggódjon – a további kapcsolatok kódrészleteit folyamatosan dokumentáljuk. Kérjük, írja le alább, hogy mit szeretne látni. Ha szeretné megismerni a saját kapcsolatok kiépítésének módját, olvassa el [a fiók kapcsolódási karakterláncával kapcsolatos információkat](#GetCustomConnection).
5. Másolja és illessze be a kódrészletet a MongoDB alkalmazásba.

    ![Gyors üzembe helyezés panel](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>A MongoDB-kapcsolatok karakterláncának beolvasása a testreszabáshoz
1. Egy böngészőben jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. A **Azure Cosmos db** panelen válassza ki az API-t. 
3. A fiók panel bal oldali ablaktábláján kattintson a **kapcsolatok karakterlánc**elemre. 
4. Megnyílik a **kapcsolatok karakterlánca** panel. Minden olyan információt tartalmaz, amely a fiókhoz való kapcsolódáshoz szükséges a MongoDB illesztőprogramjának használatával, beleértve az előépített kapcsolati karakterláncot is.

    ![Kapcsolati sztring panel](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>A kapcsolatok karakterláncának követelményei
> [!Important]
> Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Azure Cosmos DB fiókoknak hitelesítésre és biztonságos kommunikációra van szükségük *SSL*-n keresztül. 
>
>

A Azure Cosmos DB támogatja a szabványos MongoDB-kapcsolati karakterlánc URI-formátumát, amely több konkrét követelménynek is eleget tesz: az Azure Cosmos DB fiókoknak hitelesítésre és biztonságos kommunikációra van szükségük az SSL Tehát a kapcsolatok karakterláncának formátuma a következőket teszi:

    mongodb://username:password@host:port/[database]?ssl=true

A karakterlánc értékei a korábban látható **kapcsolatok karakterlánc** paneljén érhetők el:

* Username (kötelező): Cosmos-fiók neve.
* Password (kötelező): Cosmos-fiók jelszava.
* Gazdagép (kötelező): a Cosmos-fiók teljes tartományneve.
* Port (kötelező): 10255.
* Adatbázis (nem kötelező): a-adatbázis, amelyet a kapcsolatok használ. Ha nincs megadva adatbázis, az alapértelmezett adatbázis a "test".
* SSL = True (kötelező)

Tegyük fel például, hogy a fiók megjelenik a **kapcsolatok karakterlánca** panelen. Érvényes a kapcsolatok karakterlánca:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
