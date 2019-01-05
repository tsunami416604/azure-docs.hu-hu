---
title: Csatlakozás az Azure Cosmos DB Robo 3T használata
description: Ismerje meg, hogyan csatlakozhat az Azure Cosmos DB használatával Robo 3T és az Azure Cosmos DB API a mongodb-hez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5696c376ad64df01d7f9d43ff59c87402c334c52
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034811"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez Robo 3T használata

Ha csatlakozni szeretne Robo 3T Cosmos-fiókot, tegye a következőket:

* Töltse le és telepítse [Robo 3T](https://robomongo.org/)
* A Cosmos DB rendelkezik [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="connect-using-robo-3t"></a>Csatlakozás a Robo 3T használata
A Cosmos-fiók hozzáadása a Robo 3T Csatlakozáskezelő, hajtsa végre az alábbi lépéseket:

1. Az Azure Cosmos DB API a MongoDB utasításokat követve konfigurált Cosmos-fiók kapcsolódási adatainak lekérése [Itt](connect-mongodb-account.md).

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

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.
