---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093322"
---
#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* A [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) fiók 

A logikai alkalmazás a Dynamics-fiók használata előtt engedélyezze a logikai alkalmazás a CRM Online-fiókhoz való csatlakozáshoz. Ehhez egyszerűen az Azure Portalon, a logikai alkalmazásban. 

Engedélyezi a CRM Online-fiókját az alábbi lépéseket követve csatlakozhat a logikai alkalmazás:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps-tervezőben válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában, és írja be a "dynamics" kifejezést a keresőmezőbe. Az eseményindítók és műveletek közül választhat:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Ha korábban még nem létrehozott Dynamics fennálló kapcsolatokat, jelentkezzen be a Dynamics hitelesítő adatait kéri:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Válassza ki **jelentkezzen be a**, és adja meg a felhasználónevet és jelszót. Válassza ki **jelentkezzen be a**. 
   
    Ezek a hitelesítő adatok segítségével csatlakozhat, és a Dynamics-fiókban lévő adatok eléréséhez a logikai alkalmazás engedélyezése. 
4. Figyelje meg, hogy a kapcsolat létrejött. Most folytassa a további lépések a logikai alkalmazásban:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

