---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 8fe22f95c1dfc5171c41d6b3f0e00760fd9c9d69
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134404"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Facebook](https://www.facebook.com/) fiók 

A Facebook-fiókban, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Facebook-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a Facebook-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. A Logic app Designerben Facebook, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Facebook* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Facebook 1. lépés.](./media/connectors-create-api-facebook/facebook-1.png)
2. Ha még nem hozott létre a Facebookon előtt kapcsolatokat, első kéri a Facebook hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a Facebook-fiókban adatok elérésére:  
   ![Facebook 2. lépés.](./media/connectors-create-api-facebook/facebook-2.png)
3. Adja meg a Facebook felhasználói nevét és jelszavát, a logikai alkalmazás engedélyezése:  
   ![3 facebookon. lépés](./media/connectors-create-api-facebook/facebook-3.png)   
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Facebook 4. lépés.](./media/connectors-create-api-facebook/facebook-4.png)   

