---
title: "Egy Azure Cosmos DB fiók MongoDB kapcsolati karakterlánc |} Microsoft Docs"
description: "Útmutató a MongoDB alkalmazás csatlakoztatása az Azure Cosmos DB fiók MongoDB kapcsolati karakterlánc használatával."
keywords: "mongodb-kapcsolati karakterlánc"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: anhoh
ms.openlocfilehash: 2ad6308903d1c3615392eadedfd7da3cfad4096d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Csatlakozás a MongoDB alkalmazás az Azure Cosmos-Adatbázishoz
Útmutató a MongoDB alkalmazás csatlakoztatása az Azure Cosmos DB fiók MongoDB kapcsolati karakterlánc használatával. A Data ezután használhatja az Azure Cosmos DB adatbázis áruházban a MongoDB-alkalmazásra. 

Ez az oktatóanyag megszerezni a kapcsolati karakterlánc adatait két lehetőséget biztosít:

- [A gyors üzembe helyezési módszer](#QuickstartConnection), .NET, Node.js, MongoDB rendszerhéj, Java és Python illesztőprogramok való használatra
- [Az egyéni kapcsolati karakterlánc metódus](#GetCustomConnection), más illesztőprogramok való használatra

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha az Azure-fiók nem rendelkezik, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) most. 
- Azure Cosmos DB-fiók. Útmutatásért lásd: [.NET és az Azure-portálon MongoDB API webalkalmazás összeállítása](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>A gyors üzembe helyezési segítségével könnyebben nyerhet a MongoDB-kapcsolati karakterlánc
1. Egy webböngészőben, jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API a MongoDB-fiók. 
3. A fiók panel bal oldali ablaktáblában kattintson **gyors üzembe helyezési**. 
4. Válassza ki a platformot (**.NET**, **Node.js**, **MongoDB rendszerhéj**, **Java**, **Python**). Ha nem látja az illesztőprogram vagy eszköz szerepel, ne aggódjon--azt folyamatosan a dokumentum több kapcsolat kódrészleteket. Várjuk a megjegyzéseket alatt a mi kíváncsi rá. Útmutató a saját kapcsolat létrehozható, olvassa el [a fiók kapcsolati karakterlánc adatainak megszerzése](#GetCustomConnection).
5. Másolja és illessze be a kódrészletet a MongoDB-alkalmazásba.

    ![Gyors indítás panel](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>A MongoDB kapcsolati karakterláncot testreszabása
1. Egy webböngészőben, jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Az a **Azure Cosmos DB** panelen válassza ki az API a MongoDB-fiók. 
3. A fiók panel bal oldali ablaktáblában kattintson **kapcsolati karakterlánc**. 
4. A **kapcsolati karakterlánc** panel nyílik meg. Minden illesztőprogram a mongodb, beleértve a preconstructed kapcsolati karakterláncot a fiókhoz való csatlakozáshoz szükséges információt tartalmaz.

    ![Kapcsolati karakterlánc panel](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Kapcsolati karakterlánc követelmények
> [!Important]
> Azure Cosmos DB szigorú biztonsági követelmények és szabványok rendelkezik. Azure Cosmos DB fiókok szükséges hitelesítés és a biztonságos kommunikáció keresztül *SSL*. 
>
>

Az Azure Cosmos DB támogatja a szabványos MongoDB kapcsolati karakterlánc URI-formátum, az adott igények szerint néhány: Azure Cosmos DB fiókok hitelesítési és a biztonságos kommunikációhoz SSL-en keresztüli van szükség. Igen a kapcsolati karakterlánc-formátum van:

    mongodb://username:password@host:port/[database]?ssl=true

Ez a karakterlánc a értékei érhető el a **kapcsolati karakterlánc** korábban bemutatott panel:

* Felhasználónév (kötelező): Azure Cosmos DB fióknevet.
* Jelszó (szükséges): Azure Cosmos DB fiók jelszavát.
* Állomás (kötelező): az Azure Cosmos DB fiók teljes Tartományneve.
* Port (kötelező): 10255.
* Adatbázis (nem kötelező): az adatbázis, a kapcsolat által használt. Ha nincs adatbázis, az alapértelmezett adatbázis-e a "teszt".
* SSL = true (kötelező)

Vegyük példaként a fiók látható a **kapcsolati karakterlánc** panelen. Egy érvényes kapcsolati karakterlánca:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [MongoChef használja](mongodb-mongochef.md) egy Azure Cosmos DB API-t a MongoDB-fiók.
* Fedezze fel az Azure Cosmos DB API mongodb megtekintésével [minták](mongodb-samples.md).
