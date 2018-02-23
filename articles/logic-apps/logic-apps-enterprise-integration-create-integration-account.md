---
title: "Hozzon létre, csatolja, törlése vagy egy integrációs fiók áthelyezése az Azure logic apps |} Microsoft Docs"
description: "Integráció-fiók létrehozása, és csatolja a logic Apps alkalmazások"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: f7ec63810fe78b38c574ec39369d5926f80e595e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-an-integration-account"></a>Mi az az integráció fiókkal?

Integráció fiók lehetővé teszi, hogy vállalati integrációs alkalmazások összetevők, beleértve a sémák, térképeket, tanúsítványok, partnerek és megállapodások kezeléséhez. Minden integrációs alkalmazást hoz létre egy integrációs fiók használatával hozzáférni a sémák, maps, tanúsítványokat, és így tovább.

## <a name="create-an-integration-account"></a>Integrációs fiók létrehozása

1.  Jelentkezzen be az [Azure Portalra](http://portal.azure.com "Azure Portal") A bal oldali menüben válassza ki a **minden szolgáltatás**.

    ![Jelölje be "az összes szolgáltatás"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. A keresőmezőbe írja be a "integrációt" a szűrőhöz. Az eredmények listájában válassza **integrációs fiókok**.

    ![A "integrációt" szűrheti, válassza ki a "Integrációs fiókok"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Válassza ki a lap tetején **Hozzáadás**.

    ![Válasszon hozzáadása](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Az integráció fiók neve, és válassza ki a használni kívánt Azure-előfizetés. Létrehozhat egy új **erőforráscsoport** vagy válasszon ki egy meglévő erőforráscsoportot. Válassza ki a **hely** integrációs fiókja üzemeltetésére és egy **árképzési szintjében**. 

    Ha elkészült, válassza ki a **létrehozása**.

    ![Részletek a integrációs fiók megadása](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure látja el a kijelölt helyre, amely 1 percen belül kell végrehajtani a integrációs fiókba.

5. Frissítse az oldalt. Megjelenik az új integrációs fiókját.

    ![Új integrációs fiókja jelenik meg.](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

A következő fiókját az integráció a Logic Apps alkalmazást létrehozott. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Az integráció-fiók összekötése a logikai alkalmazás

A hozzáférést a logic apps maps, sémákat, szerződések és más összetevők, az integráció fiókban, a Logic Apps alkalmazást az integráció-fiók összekötése.

### <a name="prerequisites"></a>Előfeltételek

* Integráció fiók
* A logikai alkalmazás

> [!NOTE] 
> Győződjön meg arról, hogy az integrációs fiók és a logic app szerepelnek a *azonos Azure-beli hely* megkezdése előtt.


1. Az Azure portálon válassza ki a Logic Apps alkalmazást, és ellenőrizze a logikai alkalmazás helyét.

    ![Válassza ki a Logic Apps alkalmazást, a hely ellenőrzése](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. A **beállítások**, jelölje be **integrációs fiók**.

    ![Válassza ki a "Integrációs fiók"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Az a **válassza ki a integrációs fiókot** listára, válassza ki a integrációs fióknevet, amelyet a logikai alkalmazás csatolása. Linking befejezéséhez válassza ki a **mentése**.

    ![Válassza ki a integrációs fiókját](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    Bemutatja az integráció, fiók van összekapcsolva a Logic Apps alkalmazást, és, hogy az integrációs-fiókban lévő összes összetevők a logikai alkalmazás már elérhetők értesítést kap.

    ![A Logic Apps alkalmazást integrációs fiókja van csatolva.](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Most, hogy fiókja integráció a Logic Apps alkalmazást kapcsolódik, a B2B összekötők a logic apps segítségével is. Néhány gyakori B2B összekötők XML-érvényesítés, és egybesimított fájl kódolási/dekódolási tartalmazza.  

## <a name="delete-your-integration-account"></a>Az integráció fiók törlése

1. Válassza ki **minden szolgáltatás**.

    ![Jelölje be "az összes szolgáltatás"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. A keresőmezőbe írja be a "integrációt" a szűrőhöz. Az eredmények listájában válassza **integrációs fiókok**.

    ![A "integrációt" szűrheti, válassza ki a "Integrációs fiókok"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Válassza ki a törölni kívánt integrációs fiókot.

    ![Válassza ki az integráció fiók törlése](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. A menüben válassza a **törlése**.

    ![Válassza a "Delete"](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. Erősítse meg, hogy az integrációs fiók törlése.

## <a name="move-your-integration-account"></a>Az integráció fiók áthelyezése

Integráció fiók áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok, kövesse az alábbi lépéseket.

> [!IMPORTANT]
> Minden parancsfájl használata az új erőforrás-azonosítók integrációs fiók áthelyezése után frissítenie kell.

1. Válassza ki **minden szolgáltatás**.

    ![Jelölje be "az összes szolgáltatás"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. A keresőmezőbe írja be a "integrációt" a szűrőhöz. Az eredmények listájában válassza **integrációs fiókok**.

    ![A "integrációt" szűrheti, válassza ki a "Integrációs fiókok"](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. Válassza ki a integrációs fiókot, amelyet át szeretné helyezni. A **beállítások**, válassza a **tulajdonságok**.

    ![Jelölje ki az integráció fiókot, helyezze át. A beállítások területen válassza a Tulajdonságok](./media/logic-apps-enterprise-integration-accounts/move.png)

5. Erőforráscsoport vagy az Azure-integráció fiókjához tartozó előfizetés módosítása.

    ![Válassza ki a módosítás erőforráscsoportba vagy módosítás előfizetés](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>További lépések
* [További információ a megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  

