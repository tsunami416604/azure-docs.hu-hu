---
title: MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez
description: Megtudhatja, hogyan csatlakoztathat egy MongoDB-alkalmazást az Azure Cosmos DB-hoz az Azure Portal csatlakozási karakterláncának beszerzésével
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: e3ab6282a3c61e12dce5dd17bc0859c0d73a7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051719"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez

Megtudhatja, hogyan csatlakoztathatja a MongoDB-alkalmazását egy Azure Cosmos DB-adatbázishoz MongoDB kapcsolati sztring használatával. Ezt követően az Azure Cosmos-adatbázist használhatja a MongoDB-alkalmazása adattáraként.

Ez az oktatóanyag két módszert ismertet a kapcsolati sztringgel kapcsolatos információk lekéréséhez:

- [A gyorsindítási módszer](#get-the-mongodb-connection-string-by-using-the-quick-start).
- [Az egyéni kapcsolati karakterlánc metódusa](#get-the-mongodb-connection-string-to-customize), más illesztőprogramokkal való használatra

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik Azure-fiókkal, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) most.
- Egy Cosmos fiók. További információt a Webalkalmazás létrehozása az [Azure Cosmos DB MongoDB és .NET SDK API-jával (Build a web app for Azure Cosmos DB) (A.](create-mongodb-dotnet.md)

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>A MongoDB kapcsolati karakterlánc beszerezése a gyorsindítás sal

1. Egy internetböngészőben jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az **Azure Cosmos DB** panelen válassza ki az API-t.
3. A fiókpanel bal oldali ablaktáblájában kattintson a **Gyorsindítás**gombra.
4. Válassza ki a platformot (**.NET**, **Node.js**, **MongoDB Shell**, **Java,** **Python**). Ha nem látja az illesztőprogramot vagy az eszközt a listában, ne aggódjon – folyamatosan dokumentálunk több kapcsolatkódrészletet. Kérjük, véleményezze alább, hogy mit szeretne látni. Ha meg szeretné tudni, hogyan hozhatja létre saját kapcsolatát, olvassa el [A fiók kapcsolati karakterláncának adatainak beolvasása című részeket.](#get-the-mongodb-connection-string-to-customize)
5. Másolja és illessze be a kódrészletet a MongoDB alkalmazásba.

    ![Gyorsindítási panel](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>A MongoDB kapcsolati karakterlánc testreszabásának leválasztása

1. Egy internetböngészőben jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az **Azure Cosmos DB** panelen válassza ki az API-t.
3. A fiókpanel bal oldali ablaktáblájában kattintson a **Kapcsolati karakterlánc gombra.**
4. Megnyílik **a Kapcsolati karakterlánc** panel. A MongoDB illesztőprogramjának használatával a fiókhoz való csatlakozáshoz szükséges összes információt tartalmazza, beleértve egy előre konstruált kapcsolati karakterláncot is.

   [![Kapcsolati](./media/connect-mongodb-account/ConnectionStringBlade.png) karakterlánc panel](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Csatlakozási karakterlánc követelményei

> [!Important]
> Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Az Azure Cosmos DB-fiókok hitelesítést és biztonságos kommunikációt igényelnek *SSL-en*keresztül. 
>
>

Az Azure Cosmos DB támogatja a szabványos MongoDB kapcsolati karakterlánc URI-formátumot, néhány speciális követelménysel: Az Azure Cosmos DB-fiókok hitelesítést és biztonságos kommunikációt igényelnek SSL-en keresztül. Tehát a kapcsolati karakterlánc formátuma:

    mongodb://username:password@host:port/[database]?ssl=true

Ennek a karakterláncnak az értékei korábban látható **Kapcsolati karakterlánc** panelen érhetők el:

* Felhasználónév (kötelező): Cosmos-fiók neve.
* Jelszó (szükséges): Cosmos-fiók jelszava.
* Állomás (kötelező): A Cosmos-fiók fqdn-je.
* Port (kötelező): 10255.
* Adatbázis (nem kötelező): A kapcsolat által használt adatbázis. Ha nincs megadva adatbázis, az alapértelmezett adatbázis a "teszt" lesz.
* ssl=igaz (kötelező)

Vegyük például a **Kapcsolati karakterlánc** panelen látható fiókot. Az érvényes kapcsolati karakterlánc a következő:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.
