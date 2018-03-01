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
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>Mi az az integráció fiókkal?

Integráció fiók lehetőséget biztosít a vállalati integrációs alkalmazások, kifejezetten logikai alkalmazások férhessen hozzá és felügyelhesse a B2B összetevők, például kereskedelmi partnerek, egyezmények, maps, sémák, tanúsítványok és stb. A hozzáférés biztosításához az integráció fiók összekötése a Logic Apps alkalmazást meg arról, hogy mindkét az integráció fiók és a logic app elvégzése után a *azonos Azure-beli hely*.

## <a name="create-an-integration-account"></a>Integrációs fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com "Azure Portal") 

2. Válassza a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![integráció-fiók létrehozása](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. Válassza ki a lap tetején **Hozzáadás**.

   ![Válasszon hozzáadása](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Az integráció fiók neve, és válassza ki a használni kívánt Azure-előfizetés. Létrehozhat egy új **erőforráscsoport** vagy válasszon ki egy meglévő erőforráscsoportot. Válassza ki a **hely** integrációs fiókja üzemeltetésére és egy **Tarifacsomagot**. Ha elkészült, válassza ki a **létrehozása**.

   ![Részletek a integrációs fiók megadása](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure látja el a integrációs fiók a kiválasztott helyen, amely egy percen belül kell végrehajtani.

5. Frissítse az oldalt. Megjelenik az új integrációs fiókját.

   ![Új integrációs fiókja jelenik meg.](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

A következő fiókját az integráció a Logic Apps alkalmazást létrehozott. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Az integráció-fiók összekötése a logikai alkalmazás

A B2B összetevők, például kereskedelmi partnerek, egyezmények, maps és integrációs fiókjában sémák a logic apps hozzáférésének a Logic Apps alkalmazást az integráció-fiók összekötése. 

1. Az Azure portálon válassza ki a Logic Apps alkalmazást, és ellenőrizze a logikai alkalmazás helyét.

   ![Válassza ki a Logic Apps alkalmazást, a hely ellenőrzése](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. A **beállítások**, jelölje be **integrációs fiók**.

   ![Válassza ki a "Integrációs fiók"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Az a **válassza ki a integrációs fiókot** listára, válassza ki a integrációs fióknevet, amelyet a logikai alkalmazás csatolása. Linking befejezéséhez válassza ki a **mentése**.

   ![Válassza ki a integrációs fiókját](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Bemutatja az integráció, fiók van összekapcsolva a Logic Apps alkalmazást, és, hogy az integrációs-fiókban lévő összes összetevők a logikai alkalmazás már elérhetők értesítést kap.

   ![A Logic Apps alkalmazást integrációs fiókja van csatolva.](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Most, hogy fiókja integráció a Logic Apps alkalmazást kapcsolódik, a Logic Apps alkalmazást is használhatja a B2B összekötők. Néhány gyakori B2B összekötők XML-érvényesítés, és egybesimított fájl kódolási/dekódolási tartalmazza.  

## <a name="delete-your-integration-account"></a>Az integráció fiók törlése

1. Válassza a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Válassza ki a törölni kívánt integrációs fiókot.

    ![Válassza ki az integráció fiók törlése](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. A menüben válassza a **törlése**.

    ![Válassza a "Delete"](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Erősítse meg, hogy az integrációs fiók törlése.

## <a name="move-your-integration-account"></a>Az integráció fiók áthelyezése

Integráció fiók áthelyezése egy másik Azure-előfizetés vagy az erőforrás-csoportok, kövesse az alábbi lépéseket. Az integráció fiók áthelyezése után győződjön meg arról, hogy az új erőforrás-azonosítók használata az összes parancsfájl frissíti.

1. Válassza a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Az integráció fiók](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Válassza ki a integrációs fiókot, amelyet át szeretné helyezni. A **beállítások**, válassza a **tulajdonságok**.

   ![Jelölje ki az integráció fiókot, helyezze át. A beállítások területen válassza a Tulajdonságok](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Erőforráscsoport vagy az Azure-integráció fiókjához tartozó előfizetés módosítása.

   ![Válassza ki a módosítás erőforráscsoportba vagy módosítás előfizetés](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>További lépések

* [További információ a megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  

