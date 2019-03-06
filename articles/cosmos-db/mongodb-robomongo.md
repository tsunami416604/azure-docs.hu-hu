---
title: Csatlakozás az Azure Cosmos DB Robo 3T használata
description: Ismerje meg, hogyan csatlakozhat az Azure Cosmos DB használatával Robo 3T és az Azure Cosmos DB API a mongodb-hez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443970"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API a mongodb-hez Robo 3T használata

Ha csatlakozni szeretne Robo 3T Cosmos-fiókot, tegye a következőket:

* Töltse le és telepítse [Robo 3T](https://robomongo.org/)
* A Cosmos DB rendelkezik [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="connect-using-robo-3t"></a>Csatlakozás a Robo 3T használata
A Cosmos-fiók hozzáadása a Robo 3T Csatlakozáskezelő, hajtsa végre az alábbi lépéseket:

1. Az Azure Cosmos DB API a MongoDB utasításokat követve konfigurált Cosmos-fiók kapcsolódási adatainak lekérése [Itt](connect-mongodb-account.md).

    ![A kapcsolati karakterlánc panelen – képernyőfelvétel](./media/mongodb-robomongo/connectionstringblade.png)
2. Futtatás *Robomongo.exe*

3. Kattintson a csatlakozási gomb alatt **fájl** a kapcsolatok kezelésére. Kattintson a **létrehozása** a a **MongoDB kapcsolatok** ablakban nyílik meg a **kapcsolatbeállítások** ablak.

4. Az a **kapcsolatbeállítások** ablakban válasszon egy nevet. Majd keresse meg a **gazdagép** és **Port** 1. lépés a kapcsolat adatait, és írja be őket **cím** és **Port**, illetve.

    ![A Robomongo képernyőkép kapcsolatok kezelése](./media/mongodb-robomongo/manageconnections.png)
5. Az a **hitelesítési** lapra, majd **állítása hitelesítési**. Ezt követően adja meg az adatbázishoz (alapértelmezés szerint *rendszergazdai*), **felhasználónév** és **jelszó**.
Mindkét **felhasználónév** és **jelszó** a kapcsolatadatokat az 1. lépésben található.

    ![A Robomongo hitelesítés lap képernyőképe](./media/mongodb-robomongo/authentication.png)
6. Az a **SSL** lapon jelölje **használja az SSL protokoll**, majd módosítsa a **hitelesítési módszer** való **önaláírt tanúsítvány**.

    ![A Robomongo SSL lap képernyőképe](./media/mongodb-robomongo/SSL.png)
7. Végül kattintson **teszt** , ellenőrizze, hogy képes csatlakozni, majd **mentése**.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.
