---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: ec5b3ca9ccd139cbdf17768056eb1d835336e7a7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
1. Az a [Azure-portálon](https://portal.azure.com), egy üres logikai alkalmazás létrehozása. 

2. A Logic Apps tervezőben írja be a "github" szűrőként. 

3. Válassza ki a GitHub-összekötő és a használni kívánt eseményindító.

   ![Válassza ki a GitHub-összekötő, és egy eseményindító](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Minden logic app munkafolyamat eseményindítót kell kezdődnie. Műveletek csak akkor, ha az eseményindító már elindul a logikai munkafolyamat választhatja ki. 

4. Ha korábban a kapcsolat nem hozott létre, válassza a **bejelentkezés** , megadhatja a Githubon hitelesítő adatokat.  

   ![Jelentkezzen be a GitHub-fiókjával](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A Logic Apps alkalmazást engedélyezése a kapcsolódás, illetve a GitHub-fiókjában az adatok elérése ezeket a hitelesítő adatokat használ. 

5. Adja meg a GitHub-felhasználónevét és jelszavát, majd erősítse meg az engedélyt.

   ![Adjon meg hitelesítő adatokat, majd erősítse meg az engedélyt](./media/connectors-create-api-github/github-connector-authorize.png)   

   A kapcsolat az Azure portálon megtörtént, és használatra kész.

6. Továbbra is a logic app munkafolyamat meghatározása.

   ![Adjon hozzá további műveleteket a logic app munkafolyamat](./media/connectors-create-api-github/github-connector-logic-app.png)

