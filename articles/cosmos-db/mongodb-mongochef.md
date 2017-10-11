---
title: "MongoChef használata Azure Cosmos DB |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Cosmos DB MongoChef: API-t a MongoDB-fiók"
keywords: mongochef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Egy Azure Cosmos DB MongoChef használni: API-t a MongoDB-fiók

Egy Azure Cosmos DB való kapcsolódáshoz: API-t a MongoDB-fiókot kell:

* Töltse le és telepítse [MongoChef](http://3t.io/mongochef)
* Az Azure Cosmos DB rendelkezik: API-t a MongoDB-fiók [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="create-the-connection-in-mongochef"></a>Hozzon létre a kapcsolat MongoChef
Az Azure Cosmos DB hozzáadása: MongoChef Csatlakozáskezelő fiók MongoDB API a következő lépésekkel.

1. Az Azure Cosmos DB beolvasása: API-t a MongoDB kapcsolatadatok utasításai [Itt](connect-mongodb-account.md).

    ![A kapcsolati karakterlánc panel képernyőfelvétele](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kattintson a **Connect** a Csatlakozáskezelő megnyitásához kattintson a **új kapcsolat**

    ![Képernyőfelvétel a MongoChef Csatlakozáskezelő](./media/mongodb-mongochef/ConnectionManager.png)
3. Az a **új kapcsolat** ablakban, a a **Server** lapra, adja meg a GAZDAGÉP (FQDN) az Azure Cosmos DB: API-t a MongoDB-fiók és a PORT.

    ![A MongoChef kapcsolatot kezelő kiszolgálóhoz lap](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Az a **új kapcsolat** ablakban, a a **hitelesítési** lapra, majd a hitelesítési mód **szabvány (MONGODB-CR vagy SCARM-SHA-1)** , és írja be a FELHASZNÁLÓNEVET és JELSZÓT.  Fogadja el az alapértelmezett hitelesítési db (rendszergazda), vagy a saját értéket adjon meg.

    ![A MongoChef kapcsolat manager hitelesítés lap](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Az a **új kapcsolat** ablakban, a a **SSL** lapon jelölje a **való csatlakozáshoz használjon SSL-protokoll** jelölőnégyzetet és a **kiszolgáló önaláírt SSL-tanúsítványokat elfogadó** választógombot.

    ![A MongoChef kapcsolat manager SSL lap](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kattintson a **kapcsolat tesztelése** gombra kattintva ellenőrizze a kapcsolati adatokat, kattintson a **OK** az új kapcsolat ablakba, és kattintson **mentése**.

    ![A MongoChef teszt kapcsolat ablak képernyőfelvétele](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Egy adatbázis, gyűjtemény és dokumentumok létrehozásához használja a MongoChef
Egy adatbázis, gyűjtemény és dokumentumok MongoChef létrehozásához hajtsa végre az alábbi lépéseket.

1. A **Csatlakozáskezelő**, jelölje ki a kapcsolat, majd kattintson **Connect**.

    ![Képernyőfelvétel a MongoChef Csatlakozáskezelő](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kattintson jobb gombbal arra a gazdagépre, és válassza a **adatbázis hozzáadása**.  Adja meg az adatbázis nevét, és kattintson a **OK**.

    ![Képernyőfelvétel a MongoChef hozzáadása adatbázis-beállítás](./media/mongodb-mongochef/AddDatabase1.png)
3. Kattintson a jobb gombbal az adatbázist, és válassza a **gyűjtemény hozzáadása**.  Adjon meg egy gyűjtemény nevét, majd kattintson a **létrehozása**.

    ![Képernyőfelvétel a MongoChef gyűjtemény hozzáadása lehetőséget.](./media/mongodb-mongochef/AddCollection.png)
4. Kattintson a **gyűjtemény** menü elemet, majd kattintson a **hozzáadása a dokumentumhoz**.

    ![Képernyőfelvétel a MongoChef hozzáadása a dokumentumhoz menüpont](./media/mongodb-mongochef/AddDocument1.png)
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
6. Adjon hozzá egy másik dokumentum, ezúttal az alábbi tartalommal.

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

## <a name="next-steps"></a>Következő lépések
* Ismerheti meg az Azure Cosmos DB: Mongodb-protokolltámogatással API [minták](mongodb-samples.md).
