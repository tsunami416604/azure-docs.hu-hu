---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 975d6625f46a6c10417793764b28d0a529a5c0cd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091779"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Project Online](https://products.office.com/Project/project-online-with-project-for-office-365) fiók 

A Project online-fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Project Online-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a Project Online-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. Hozzon létre egy kapcsolatot a Project Online, a Logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Project Online* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Project Online 1. lépés.](./media/connectors-create-api-projectonline/projectonline-1.png)
2. Ha még nem hozott létre a Project Online előtt kapcsolatokat, első kéri a Project online hitelesítő adatokat. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Project Online-fiókhoz tartozó adatok eléréséhez:  
   ![Project Online 2. lépés.](./media/connectors-create-api-projectonline/projectonline-2.png)
3. Adja meg a Project Online felhasználónevet és jelszót, hogy a logikai alkalmazás engedélyezése:  
   ![Project Online 3. lépés.](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Project Online 4. lépés.](./media/connectors-create-api-projectonline/projectonline-4.png)   

