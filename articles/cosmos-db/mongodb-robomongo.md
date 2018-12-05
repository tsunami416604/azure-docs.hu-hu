---
title: A Robomongo használata az Azure Cosmos DB
description: 'A Robomongo használata egy Azure Cosmos DB-vel: API a MongoDB-fiókhoz'
keywords: a robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863805"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>A Robomongo használata egy Azure Cosmos DB: API a MongoDB-fiókhoz
Szeretne csatlakozni egy Azure Cosmos DB: MongoDB-fiókok a Robomongo használata API-t, akkor kell:

* Töltse le és telepítse [Robomongo](https://robomongo.org/)
* Rendelkezik az Azure Cosmos DB: MongoDB-fiókok API [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="connect-using-robomongo"></a>Csatlakozás a Robomongo használatával
Az Azure Cosmos DB hozzáadása: API a MongoDB-fiókhoz a Robomongo MongoDB-kapcsolatokra az alábbi lépésekkel.

1. Az Azure Cosmos DB lekéréséhez: API a MongoDB kapcsolati fiókadatok utasításokat követve [Itt](connect-mongodb-account.md).

    ![Képernyőfelvétel a kapcsolati karakterlánc paneljén](./media/mongodb-robomongo/connectionstringblade.png)
2. Futtatás *Robomongo.exe*

3. Kattintson a csatlakozási gomb alatt **fájl** a kapcsolatok kezelésére. Kattintson a **létrehozása** a a **MongoDB kapcsolatok** ablakban nyílik meg a **kapcsolatbeállítások** ablak.

4. Az a **kapcsolatbeállítások** ablakban válasszon egy nevet. Majd keresse meg a **gazdagép** és **Port** 1. lépés a kapcsolat adatait, és írja be őket **cím** és **Port**, illetve.

    ![Képernyőfelvétel a Robomongo kapcsolatok kezelése](./media/mongodb-robomongo/manageconnections.png)
5. Az a **hitelesítési** lapra, majd **állítása hitelesítési**. Ezt követően adja meg az adatbázishoz (alapértelmezés szerint *rendszergazdai*), **felhasználónév** és **jelszó**.
Mindkét **felhasználónév** és **jelszó** a kapcsolatadatokat az 1. lépésben található.

    ![A Robomongo hitelesítés lap](./media/mongodb-robomongo/authentication.png)
6. Az a **SSL** lapon jelölje **használja az SSL protokoll**, majd módosítsa a **hitelesítési módszer** való **önaláírt tanúsítvány**.

    ![A Robomongo SSL lap](./media/mongodb-robomongo/SSL.png)
7. Végül kattintson **teszt** , ellenőrizze, hogy képes csatlakozni, majd **mentése**.

## <a name="next-steps"></a>További lépések
* Ismerkedés az Azure Cosmos DB: MongoDB API- [minták](mongodb-samples.md).
