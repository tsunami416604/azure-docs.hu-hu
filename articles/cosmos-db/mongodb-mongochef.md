---
title: A Studio 3T használata (MongoChef) az Azure Cosmos DB |} A Microsoft Docs
description: A Studio 3T használata egy Azure Cosmos DB MongoDB API-fiókkal
keywords: a mongochef, a studio 3T
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sclyon
ms.openlocfilehash: af66f413055c01d9a4c3e94c7d3b9c5f1d5917b0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42059459"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Az Azure Cosmos DB: Használja a Studio 3T MongoDB API-fiókkal

Szeretne csatlakozni egy Azure Cosmos DB MongoDB API-fiókot, tegye a következőket:

* Töltse le és telepítse [a Studio 3T](https://studio3t.com/) (korábbi nevén MongoChef)
* Az Azure Cosmos DB rendelkezik [kapcsolati karakterlánc](connect-mongodb-account.md) a MongoDB-fiókhoz tartozó adatok

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolat létrehozása ezen a Studio 3T
Az Azure Cosmos DB-fiók hozzáadása a Studio 3T Csatlakozáskezelő, hajtsa végre az alábbi lépéseket:

1. Lekéri az Azure Cosmos DB kapcsolati információkat, a MongoDB API-fiókot a következő témakör utasításait a [egy Azure Cosmos DB MongoDB-alkalmazás csatlakoztatása](connect-mongodb-account.md) cikk.

    ![Képernyőfelvétel a kapcsolati karakterlánc oldaláról](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kattintson a **Connect** a kapcsolat-kezelő megnyitásához kattintson a **új kapcsolat**

    ![A Studio 3T kezelő képernyőfelvétele](./media/mongodb-mongochef/ConnectionManager.png)
3. Az a **új kapcsolat** ablakban, a a **kiszolgáló** lapra, adja meg a GAZDAGÉP (FQDN) az Azure Cosmos DB-fiók és a PORTOT.

    ![A Studio 3T kapcsolatot kezelő kiszolgáló lap](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az a **új kapcsolat** ablakban, a a **hitelesítési** adja meg a hitelesítési mód **alapszintű (MONGODB-CR vagy SCARM-SHA-1)** , és adja meg a FELHASZNÁLÓNEVET és JELSZÓT.  Fogadja el az alapértelmezett hitelesítési db (rendszergazdai), vagy adjon meg egy saját értéket.

    ![A Studio 3T kapcsolat manager hitelesítés lap](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az a **új kapcsolat** ablakban, a a **SSL** lapon jelölje a **való csatlakozáshoz használja az SSL protokoll** jelölőnégyzetet, és a **fogadja el a kiszolgáló önaláírt SSL-tanúsítványok**  választógombot.

    ![A Studio 3T kapcsolat manager SSL lap](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **kapcsolat tesztelése** gombra kattintva ellenőrizze a kapcsolati adatokat, kattintson a **OK** térjen vissza az új kapcsolat ablakába, majd **mentése**.

    ![A Studio 3T teszt kapcsolat ablak képernyőképe](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>A Studio 3T használata egy adatbázis, gyűjtemény és dokumentumok létrehozása
Egy adatbázis, gyűjtemény és a Studio 3T használata dokumentumok létrehozásához hajtsa végre az alábbi lépéseket:

1. A **Csatlakozáskezelő**, jelölje ki a kapcsolat, és kattintson a **Connect**.

    ![A Studio 3T kezelő képernyőfelvétele](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson a jobb gombbal a gazdagépre, és válassza a **adatbázis hozzáadása**.  Adjon meg egy adatbázisnevet, és kattintson a **OK**.

    ![Képernyőfelvétel a Studio 3T adatbázis hozzáadása lehetőség](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázist, és válassza a **gyűjtemény hozzáadása**.  Adja meg a gyűjtemény nevét, és kattintson a **létrehozás**.

    ![Képernyőfelvétel a Studio 3T gyűjtemény hozzáadása lehetőség](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **gyűjtemény** menüben, majd kattintson a **dokumentum felvétele**.

    ![Képernyőfelvétel a Studio 3T dokumentum hozzáadása menüpont](./media/mongodb-mongochef/AddDocument1.png)
5. A dokumentum felvétele párbeszédpanelen illessze be a következőt, és kattintson a **dokumentum felvétele**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adjon hozzá egy másik dokumentumot, ezúttal az alábbi tartalommal:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Egy mintául szolgáló lekérdezés végrehajtása. Például keresse meg a család, a Vezetéknév "Andersen", és a szülők és az állam mezők adja vissza.

    ![Képernyőfelvétel a Mongo Chef-lekérdezés eredményei](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>További lépések
* Ismerkedés az Azure Cosmos DB MongoDB API [minták](mongodb-samples.md).
