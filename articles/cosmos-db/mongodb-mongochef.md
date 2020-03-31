---
title: A Studio 3T segítségével csatlakozhat az Azure Cosmos DB MongoDB-hoz való API-jához
description: Ismerje meg, hogyan csatlakozhat az Azure Cosmos DB MongoDB-hoz való API-jához a Studio 3T használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 9b8c3a36dbdd3a14d0bd325c22421033a1765df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063709"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Csatlakozás Azure Cosmos-fiókhoz a Studio 3T használatával

Ha csatlakozni szeretne egy Azure Cosmos DB MongoDB-hoz való API-jához a Studio 3T használatával, a következőket kell tennie:

* Töltse le és telepítse [a Studio 3T-t.](https://studio3t.com/)
* Az Azure Cosmos-fiók [kapcsolati](connect-mongodb-account.md) karakterlánc-adataival rendelkezik.

> [!NOTE]
> Jelenleg a Robo 3T v1.2 és az alacsonyabb verziók támogatottak a Cosmos DB MongoDB API-jával.

## <a name="create-the-connection-in-studio-3t"></a>A kapcsolat létrehozása a Studio 3T-ben

Az Azure Cosmos-fiók hozzáadása a Studio 3T kapcsolatkezelőhöz az alábbi lépésekkel:

1. Az Azure Cosmos DB MongoDB-fiókjához szükséges API-hoz való kapcsolódási információk lekérése a [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB cikkhez](connect-mongodb-account.md) című cikk utasításai alapján.

    ![Képernyőkép a kapcsolati karakterlánc lapról](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Kattintson a **Csatlakozás** gombra a Csatlakozáskezelő megnyitásához, majd az **Új kapcsolat gombra.**

    ![Képernyőkép a Studio 3T kapcsolatkezelőről](./media/mongodb-mongochef/ConnectionManager.png)
3. Az **Új kapcsolat** ablakban a **Kiszolgáló** lapon adja meg az Azure Cosmos-fiók és a PORT gazdagépét (FQDN).

    ![Képernyőkép a Studio 3T kapcsolatkezelő kiszolgálólapjáról](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az **Új kapcsolat** ablak **Hitelesítés** lapján válassza a Hitelesítési mód **alapszintű (MONGODB-CR vagy SCARM-SHA-1)** lehetőséget, és írja be a FELHASZNÁLÓNÉV és JELSZÓ parancsot.  Fogadja el az alapértelmezett hitelesítési adatbázist (rendszergazda), vagy adja meg saját értékét.

    ![Képernyőkép a Studio 3T kapcsolatkezelő hitelesítéslapjáról](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az **Új kapcsolat** ablak **SSL** lapján jelölje be az **SSL protokoll használata a csatlakozáshoz** jelölőnégyzetet és a **Kiszolgáló önaláírt SSL-tanúsítványainak elfogadása** választógombot.

    ![Képernyőkép a Studio 3T kapcsolatkezelő SSL lapjáról](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **Kapcsolat tesztelése** gombra a kapcsolatadatainak ellenőrzéséhez, kattintson az **OK** gombra az Új kapcsolat ablakba való visszatéréshez, majd kattintson a **Mentés gombra.**

    ![Képernyőkép a Studio 3T tesztkapcsolatablakáról](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Adatbázis, gyűjtemény és dokumentumok létrehozása a Studio 3T segítségével
Ha a Studio 3T használatával szeretne adatbázist, gyűjteményt és dokumentumokat létrehozni, hajtsa végre az alábbi lépéseket:

1. A **Csatlakozáskezelőben**jelölje ki a kapcsolatot, és kattintson a **Csatlakozás gombra.**

    ![Képernyőkép a Studio 3T kapcsolatkezelőről](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson a jobb gombbal az állomásra, és válassza **az Adatbázis hozzáadása parancsot.**  Adja meg az adatbázis nevét, és kattintson **az OK**gombra.

    ![Képernyőkép a Studio 3T Adatbázis hozzáadása beállításáról](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázisra, és válassza **a Gyűjtemény hozzáadása parancsot.**  Adja meg a gyűjtemény nevét, és kattintson **a Létrehozás gombra.**

    ![Képernyőkép a Studio 3T Gyűjtemény hozzáadása beállításáról](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **Gyűjtemény** menüelemre, majd a **Dokumentum hozzáadása**parancsra.

    ![Képernyőkép a Studio 3T Dokumentum hozzáadása menüeleméről](./media/mongodb-mongochef/AddDocument1.png)
5. A Dokumentum hozzáadása párbeszédpanelen illessze be a következőt, majd kattintson a **Dokumentum hozzáadása**gombra.

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
7. Mintalekérdezés végrehajtása. Például keressen "Andersen" vezetéknevű családokat, és adja vissza a szülők és az állam mezőket.

    ![Képernyőkép a Mongo Chef lekérdezéseredményeiről](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.
