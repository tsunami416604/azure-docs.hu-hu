---
title: A Studio 3T használata Azure Cosmos DB API-MongoDB való kapcsolódáshoz
description: Megtudhatja, hogyan csatlakozhat a MongoDB Azure Cosmos DB API-hoz a Studio 3T használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548824"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Kapcsolódás Azure Cosmos-fiókhoz a Studio 3T használatával

Ha a Studio 3T használatával szeretne csatlakozni egy Azure Cosmos DB API-hoz a MongoDB-hez, a következőket kell tennie:

* Töltse le és telepítse a [Studio 3T](https://studio3t.com/).
* Adja meg az Azure Cosmos-fiókja a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információkat.

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolatok létrehozása a Studio 3T

Az alábbi lépéseket követve adhatja hozzá az Azure Cosmos-fiókot a Studio 3T-Csatlakozáskezelőhöz:

1. Kérje le a Azure Cosmos DB API-MongoDB-fiókjának kapcsolati információit a MongoDB- [alkalmazás csatlakoztatása a Azure Cosmos db](connect-mongodb-account.md) cikk utasításait követve.

    ![Képernyőfelvétel a kapcsolatok karakterláncának oldaláról](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Kattintson a **Kapcsolódás** gombra a Csatlakozáskezelő megnyitásához, majd kattintson az **új kapcsolat** elemre.

    ![A Studio 3T-Csatlakozáskezelő képernyőképe](./media/mongodb-mongochef/ConnectionManager.png)
3. Az **új csatlakozás** ablak **kiszolgáló** lapján adja meg az Azure Cosmos-fiók gazdagépét (FQDN) és a portot.

    ![A Studio 3T Csatlakozáskezelő kiszolgáló lapjának képernyőképe](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az **új kapcsolatok** ablakban a **hitelesítés** lapon válassza az **alapszintű hitelesítési mód (MONGODB-CR vagy SCARM-SHA-1)** lehetőséget, és adja meg a felhasználónevet és a jelszót.  Fogadja el az alapértelmezett hitelesítési adatbázist (rendszergazda), vagy adja meg a saját értékét.

    ![A Studio 3T Csatlakozáskezelő hitelesítés lapjának képernyőképe](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az **új kapcsolat** ablak **SSL** LAPJÁN jelölje be az **SSL protokoll használata a kapcsolódáshoz** jelölőnégyzetet, valamint a **kiszolgáló önaláírt SSL-tanúsítványok fogadása** választógombot.

    ![Képernyőfelvétel a Studio 3T-Csatlakozáskezelő SSL-lapjáról](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **Kapcsolódás tesztelése** gombra a kapcsolódási adatok érvényesítéséhez, kattintson az **OK** gombra az új kapcsolódási ablakhoz való visszatéréshez, majd kattintson a **Mentés**gombra.

    ![Képernyőfelvétel a Studio 3T-tesztelési kapcsolatok ablakáról](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Adatbázis, gyűjtemény és dokumentumok létrehozása a Studio 3T használatával
Adatbázis, gyűjtemény és dokumentumok létrehozásához a Studio 3T használatával hajtsa végre a következő lépéseket:

1. A **Csatlakozáskezelő**területen jelölje ki a kapcsolatot, majd kattintson a **Csatlakoztatás**gombra.

    ![A Studio 3T-Csatlakozáskezelő képernyőképe](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson a jobb gombbal a gazdagépre, majd válassza az **adatbázis hozzáadása**elemet.  Adja meg az adatbázis nevét, majd kattintson **az OK gombra**.

    ![Képernyőfelvétel a Studio 3T adatbázis hozzáadása lehetőségéről](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázisra, és válassza a **gyűjtemény hozzáadása**elemet.  Adja meg a gyűjtemény nevét, és kattintson a **Létrehozás**gombra.

    ![Képernyőfelvétel a Studio 3T-gyűjtemény hozzáadása lehetőségről](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **gyűjtemény** menüpontra, majd a **dokumentum hozzáadása**elemre.

    ![Képernyőfelvétel a Studio 3T hozzáadása dokumentum menüelem](./media/mongodb-mongochef/AddDocument1.png)
5. A dokumentum hozzáadása párbeszédpanelen illessze be a következőt, majd kattintson a **dokumentum hozzáadása**lehetőségre.

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
6. Adjon hozzá egy másik dokumentumot, ezúttal a következő tartalommal:

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
7. Minta lekérdezés végrehajtása. Keressen például egy "Andersen" vezetéknevű családot, és adja vissza a szülők és az állam mezőket.

    ![Képernyőkép a Mongo Chef lekérdezési eredményeiről](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
