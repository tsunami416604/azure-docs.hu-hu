---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789768"
---
1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást. 

2. A Logic Apps Designer, írja be a "github", mint a szűrő. 

3. Válassza ki a GitHub-összekötőt és a használni kívánt eseményindítót.

   ![Válassza ki a GitHub-összekötőt és egy eseményindítót](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Minden logikai alkalmazás-munkafolyamatnak egy eseményindítóval kell kezdődnie. Csak akkor választhat ki műveleteket, ha a logikai munkafolyamat már egy eseményindítóval kezdődik. 

4. Ha korábban nem hozlétre kapcsolatot, válassza **a Bejelentkezés** lehetőséget, hogy megadhassa a GitHub-hitelesítő adatait, amikor a rendszer kéri.  

   ![Bejelentkezés a GitHub-hitelesítő adatokkal](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A logikai alkalmazás ezeket a hitelesítő adatokat használja a GitHub-fiók adatainak csatlakoztatásához és eléréséhez. 

5. Adja meg a GitHub-felhasználónevét és jelszavát, majd erősítse meg az engedélyezést.

   ![Hitelesítő adatok megadása és engedélyezés megerősítése](./media/connectors-create-api-github/github-connector-authorize.png)   

   A kapcsolat most már létre az Azure Portalon, és készen áll a használatra.

6. Folytassa a logikai alkalmazás munkafolyamatának definiálását.

   ![További műveletek hozzáadása a logikai alkalmazás munkafolyamatához](./media/connectors-create-api-github/github-connector-logic-app.png)

