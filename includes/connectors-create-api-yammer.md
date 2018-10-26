---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 00358e5164e492030c3c58e1c544a2fee3f775e8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092139"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Yammer](https://www.yammer.com/) fiók 

A Yammer-fiókjába, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Yammer-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a Yammer-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. A Logic app Designerben a Yammeren, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Yammer* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-yammer/yammer-1.png)
2. Ha még nem hozott létre a Yammeren, mielőtt kapcsolatokat, első kéri a Yammer hitelesítő adatokat. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Yammer-fiókhoz tartozó adatok eléréséhez:  
   ![](./media/connectors-create-api-yammer/yammer-2.png)
3. Adja meg a Yammer felhasználónevet és jelszót, hogy a logikai alkalmazás engedélyezése:  
   ![](./media/connectors-create-api-yammer/yammer-3.png)   
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![](./media/connectors-create-api-yammer/yammer-4.png)   

