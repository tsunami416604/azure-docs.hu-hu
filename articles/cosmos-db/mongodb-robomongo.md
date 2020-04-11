---
title: Az Azure Cosmos DB-hoz való csatlakozás a Robo 3T segítségével
description: Ismerje meg, hogyan csatlakozhat az Azure Cosmos DB-hez a Robo 3T és az Azure Cosmos DB MongoDB-hoz való API-ja használatával
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114191"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Robo 3T használata a MongoDB-hez készült Azure Cosmos DB API-hoz

A Cosmos-fiókhoz a Robo 3T használatával való csatlakozáshoz a következőket kell tennie:

* A [Robo 3T](https://robomongo.org/) letöltése és telepítése
* A Cosmos DB [kapcsolati karakterlánc](connect-mongodb-account.md) adatainak

> [!NOTE]
> Jelenleg a Robo 3T v1.2 és az alacsonyabb verziók támogatottak a Cosmos DB MongoDB API-jával.

## <a name="connect-using-robo-3t"></a>Csatlakozás a Robo 3T használatával

Ha hozzá szeretné adni cosmos-fiókját a Robo 3T kapcsolatkezelőhöz, hajtsa végre az alábbi lépéseket:

1. Az [itt](connect-mongodb-account.md)található utasítások alapján olvassa be az Azure Cosmos DB API-jával konfigurált Cosmos-fiók kapcsolatadatait.

    ![Képernyőkép a kapcsolati karakterlánc panelről](./media/mongodb-robomongo/connectionstringblade.png)
2. Futtassa a *Robomongo* alkalmazást.

3. A kapcsolatok kezeléséhez kattintson a **Fájl csoport csatlakozási** gombjára. Ezután kattintson a **Létrehozás gombra** a **MongoDB kapcsolatok** ablakban, amely megnyitja a **Kapcsolatbeállítások** ablakot.

4. A **Kapcsolat beállításai** ablakban válasszon nevet. Ezután keresse meg a **gazdatestet** és **a portot** a **Port**kapcsolati adatok között az 1. **Address**

    ![Képernyőkép a Robomongo Kapcsolatok kezelése](./media/mongodb-robomongo/manageconnections.png)
5. A **Hitelesítés** lapon kattintson a **Hitelesítés végrehajtása gombra.** Ezután adja meg az adatbázist (alapértelmezés szerint *rendszergazda),* **felhasználónév** és **jelszó**.
Mind **a felhasználónév,** mind a **jelszó** megtalálható a kapcsolati adatokban az 1.

    ![Képernyőkép a Robomongo hitelesítés lapról](./media/mongodb-robomongo/authentication.png)
6. Az **SSL** lapon jelölje be **az SSL protokoll használata**jelölőnégyzetet, majd módosítsa a **hitelesítési módszert** **önaláírt tanúsítványra.**

    ![Képernyőkép a Robomongo SSL lapról](./media/mongodb-robomongo/SSL.png)
7. Végül kattintson a **Teszt** gombra, és ellenőrizze, hogy tud-e csatlakozni, majd a **Mentés gombra.**

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.
