---
title: Használja a Studio 3T (MongoChef) rendelkező Azure Cosmos DB |} Microsoft Docs
description: Studio 3T használata Azure Cosmos DB MongoDB API-fiókkal
keywords: mongochef, studio 3T
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sngun
ms.openlocfilehash: 9d00f260252e31ab9186c8aab8f07db848d1af29
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794770"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Az Azure Cosmos DB: Használata Studio 3T MongoDB API-fiókkal

Csatlakozás Azure Cosmos DB MongoDB API-fiókkal, a következőket kell tennie:

* Töltse le és telepítse [Studio 3T](https://studio3t.com/) (korábbi nevén MongoChef)
* Az Azure Cosmos DB rendelkezik [kapcsolati karakterlánc](connect-mongodb-account.md) MongoDB-fiókja adatainak

## <a name="create-the-connection-in-studio-3t"></a>A Studio 3T a kapcsolat létrehozása
A Azure Cosmos DB-fiók hozzáadása a Studio 3T Csatlakozáskezelő, hajtsa végre az alábbi lépéseket:

1. A MongoDB API fiókja utasításai Azure Cosmos adatbázis-kapcsolati információkat lekérni a [a MongoDB alkalmazás az Azure Cosmos Adatbázishoz csatlakozni](connect-mongodb-account.md) cikk.

    ![Képernyőfelvétel a kapcsolati karakterlánc oldal](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kattintson a **Connect** a Csatlakozáskezelő megnyitásához kattintson a **új kapcsolat**

    ![A Studio 3T Csatlakozáskezelő képernyőfelvétele](./media/mongodb-mongochef/ConnectionManager.png)
3. Az a **új kapcsolat** ablakban, a **Server** lapra, adja meg a GAZDAGÉP (FQDN) az Azure Cosmos DB fiók és a PORTOT.

    ![A Studio 3T kapcsolatot kezelő kiszolgálóhoz lap](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az a **új kapcsolat** ablakban, a a **hitelesítési** lapra, majd a hitelesítési mód **(MONGODB-CR vagy SCARM-SHA-1) a Basic** , és írja be a FELHASZNÁLÓNEVET és JELSZÓT.  Fogadja el az alapértelmezett hitelesítési db (rendszergazda), vagy a saját értéket adjon meg.

    ![A Studio 3T kapcsolat manager hitelesítés lap](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az a **új kapcsolat** ablakban, a a **SSL** lapon jelölje a **való csatlakozáshoz használjon SSL-protokoll** jelölőnégyzetet és a **kiszolgáló önaláírt SSL-tanúsítványokat elfogadó** választógombot.

    ![A Studio 3T kapcsolat manager SSL lap](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **kapcsolat tesztelése** gombra kattintva ellenőrizze a kapcsolati adatokat, kattintson a **OK** az új kapcsolat ablakba, és kattintson **mentése**.

    ![A Studio 3T teszt kapcsolat ablak képernyőfelvétele](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Egy adatbázis, gyűjtemény és dokumentumok létrehozásához használja a Studio 3T
Egy adatbázis, gyűjtemény és dokumentumok Studio 3T létrehozásához hajtsa végre az alábbi lépéseket:

1. A **Csatlakozáskezelő**, jelölje ki a kapcsolat, majd kattintson **Connect**.

    ![A Studio 3T Csatlakozáskezelő képernyőfelvétele](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson a jobb gombbal a gazdagépre, és válassza a **adatbázis hozzáadása**.  Adja meg az adatbázis nevét, és kattintson a **OK**.

    ![Képernyőfelvétel a Studio 3T hozzáadása adatbázis-beállítás](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázist, és válassza a **gyűjtemény hozzáadása**.  Adjon meg egy gyűjtemény nevét, majd kattintson a **létrehozása**.

    ![Képernyőfelvétel a Studio 3T gyűjtemény hozzáadása lehetőséget](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **gyűjtemény** menü elemet, majd kattintson a **hozzáadása a dokumentumhoz**.

    ![Képernyőfelvétel a Studio 3T hozzáadása a dokumentumhoz menüpont](./media/mongodb-mongochef/AddDocument1.png)
5. A dokumentum felvétele párbeszédpanelen illessze be a következőt, és kattintson a **hozzáadása a dokumentumhoz**.

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
6. Egy másik dokumentum, az alábbi tartalommal most hozzáadása:

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
7. A minta lekérdezés végrehajtása. Például "Andersen" utolsó nevű családok kereshet, és térjen vissza a szülők és állapot mezőket.

    ![Képernyőfelvétel a Mongo Chef lekérdezés eredményei](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>További lépések
* Fedezze fel az Azure Cosmos DB MongoDB API [minták](mongodb-samples.md).
