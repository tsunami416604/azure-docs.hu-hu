---
title: Robomongo használata Azure Cosmos DB |} Microsoft Docs
description: 'Ismerje meg, hogyan használható az Azure Cosmos DB Robomongo: API-t a MongoDB-fiók'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: b6d64d7d7b30d4175fb8c8bf6c98127465427d4e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795030"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Egy Azure Cosmos DB Robomongo használni: API-t a MongoDB-fiók
Egy Azure Cosmos DB való kapcsolódáshoz: API használatával Robomongo MongoDB-fiók, meg kell:

* Töltse le és telepítse [Robomongo](https://robomongo.org/)
* Az Azure Cosmos DB rendelkezik: API-t a MongoDB-fiók [kapcsolati karakterlánc](connect-mongodb-account.md) információk

## <a name="connect-using-robomongo"></a>Csatlakozás Robomongo használatával
Az Azure Cosmos DB hozzáadása: a Robomongo MongoDB kapcsolatok fiók MongoDB API a következő lépésekkel.

1. Az Azure Cosmos DB beolvasása: API-t a MongoDB fiók kapcsolat adatait az utasításokat követve [Itt](connect-mongodb-account.md).

    ![A kapcsolati karakterlánc panel képernyőfelvétele](./media/mongodb-robomongo/connectionstringblade.png)
2. Futtatás *Robomongo.exe*

3. A kapcsolat gombra a **fájl** a kapcsolatok kezelésére. Kattintson a **létrehozása** a a **MongoDB kapcsolatok** ablakban nyílik meg, a **kapcsolatbeállítások** ablak.

4. Az a **kapcsolatbeállítások** ablakban válasszon egy nevet. Majd keresse meg a **állomás** és **Port** a kapcsolati információit az 1. lépés, és írja be őket **cím** és **Port**, illetve.

    ![Képernyőfelvétel a Robomongo kapcsolatok kezelése](./media/mongodb-robomongo/manageconnections.png)
5. Az a **hitelesítési** lapra, majd **végrehajtása hitelesítési**. Ezután írja be az adatbázis (alapértelmezett érték *Admin*), **felhasználónév** és **jelszó**.
Mindkét **felhasználónév** és **jelszó** a kapcsolatadatok, 1. lépésben található.

    ![A Robomongo hitelesítés lap](./media/mongodb-robomongo/authentication.png)
6. Az a **SSL** lapon jelölje **használja az SSL protokoll**, majd módosítsa a **hitelesítési módszer** való **önaláírt tanúsítvány**.

    ![A Robomongo SSL lap](./media/mongodb-robomongo/SSL.png)
7. Végezetül kattintson **teszt** ellenőrizze, hogy kapcsolódni tud, majd **mentése**.

## <a name="next-steps"></a>További lépések
* Ismerheti meg az Azure Cosmos DB: Mongodb-protokolltámogatással API [minták](mongodb-samples.md).
