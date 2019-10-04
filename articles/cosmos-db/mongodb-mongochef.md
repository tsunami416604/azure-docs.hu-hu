---
title: Kapcsolódás Azure Cosmos DB MongoDB API-hoz a Studio 3T használatával
titleSuffix: Azure Cosmos DB
description: Megtudhatja, hogyan csatlakozhat a Cosmos DBhoz a Studio 3T és a Azure Cosmos DB API-MongoDB használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 46cac6720bbef133d6b3f7a269cf97a81cddd0c5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062117"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Kapcsolódás a Cosmos-fiókhoz a Studio 3T használatával

Ha a MongoDB-hez Azure Cosmos DB API-val szeretne csatlakozni egy Cosmos-fiókhoz, a következőket kell tennie:

* A [Studio 3T](https://studio3t.com/) letöltése és telepítése
* A Cosmos DB a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információk

> [!NOTE]
> Jelenleg a Robo 3T v 1.2-es és alacsonyabb verziója támogatott a Cosmos DB API-MongoDB. 

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolat létrehozása ezen a Studio 3T
Az alábbi lépéseket követve veheti fel a Cosmos-fiókját a Studio 3T-Csatlakozáskezelőbe:

1. A MongoDB Azure Cosmos DB API-jával konfigurált Cosmos-fiókjához tartozó kapcsolati információk beolvasása a [MongoDB-alkalmazás csatlakoztatása a Azure Cosmos db](connect-mongodb-account.md) cikkhez című cikkben leírtak alapján.

    ![Képernyőfelvétel a kapcsolatok karakterláncának oldaláról](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kattintson a **Connect** a kapcsolat-kezelő megnyitásához kattintson a **új kapcsolat**

    ![A Studio 3T-Csatlakozáskezelő képernyőképe](./media/mongodb-mongochef/ConnectionManager.png)
3. Az a **új kapcsolat** ablakban, a a **kiszolgáló** lapra, adja meg a GAZDAGÉP (FQDN) az Azure Cosmos DB-fiók és a PORTOT.

    ![A Studio 3T Csatlakozáskezelő kiszolgáló lapjának képernyőképe](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az a **új kapcsolat** ablakban, a a **hitelesítési** adja meg a hitelesítési mód **alapszintű (MONGODB-CR vagy SCARM-SHA-1)** , és adja meg a FELHASZNÁLÓNEVET és JELSZÓT.  Fogadja el az alapértelmezett hitelesítési db (rendszergazdai), vagy adjon meg egy saját értéket.

    ![A Studio 3T Csatlakozáskezelő hitelesítés lapjának képernyőképe](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az a **új kapcsolat** ablakban, a a **SSL** lapon jelölje a **való csatlakozáshoz használja az SSL protokoll** jelölőnégyzetet, és a **fogadja el a kiszolgáló önaláírt SSL-tanúsítványok**  választógombot.

    ![Képernyőfelvétel a Studio 3T-Csatlakozáskezelő SSL-lapjáról](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **kapcsolat tesztelése** gombra kattintva ellenőrizze a kapcsolati adatokat, kattintson a **OK** térjen vissza az új kapcsolat ablakába, majd **mentése**.

    ![Képernyőfelvétel a Studio 3T-tesztelési kapcsolatok ablakáról](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>A Studio 3T használata egy adatbázis, gyűjtemény és dokumentumok létrehozása
Egy adatbázis, gyűjtemény és a Studio 3T használata dokumentumok létrehozásához hajtsa végre az alábbi lépéseket:

1. A **Csatlakozáskezelő**, jelölje ki a kapcsolat, és kattintson a **Connect**.

    ![A Studio 3T-Csatlakozáskezelő képernyőképe](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson a jobb gombbal a gazdagépre, és válassza a **adatbázis hozzáadása**.  Adjon meg egy adatbázisnevet, és kattintson a **OK**.

    ![Képernyőfelvétel a Studio 3T adatbázis hozzáadása lehetőségéről](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázist, és válassza a **gyűjtemény hozzáadása**.  Adja meg a gyűjtemény nevét, és kattintson a **létrehozás**.

    ![Képernyőfelvétel a Studio 3T-gyűjtemény hozzáadása lehetőségről](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **gyűjtemény** menüben, majd kattintson a **dokumentum felvétele**.

    ![Képernyőfelvétel a Studio 3T hozzáadása dokumentum menüelem](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Képernyőkép a Mongo Chef lekérdezési eredményeiről](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen [](mongodb-samples.md) meg a MongoDB-mintákkal Azure Cosmos db API-val a MongoDB.
