---
title: "Robomongo használata Azure Cosmos DB |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Cosmos DB Robomongo: API-t a MongoDB-fiók"
keywords: robomongo
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
ms.openlocfilehash: 8983594776a1bbe413a6d7cf2cd518f0e327648a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

## <a name="next-steps"></a>Következő lépések
* Ismerheti meg az Azure Cosmos DB: Mongodb-protokolltámogatással API [minták](mongodb-samples.md).
