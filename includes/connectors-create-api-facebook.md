---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789850"
---
## <a name="prerequisites"></a>Előfeltételek

* [A Facebook](https://www.facebook.com/) számla 

Mielőtt használhatna Facebook-fiókját egy logikai alkalmazásban, engedélyeznie kell a Logikai alkalmazást a Facebook-fiókjához való csatlakozáshoz. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból. 

Az alábbi lépésekkel engedélyezheti a Logikai alkalmazásnak, hogy csatlakozzon a Facebook-fiókjához:

1. A Facebookkal való kapcsolat létrehozásához a Logikai alkalmazás tervezőjében válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be a *Facebookot* a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![facebook lépés 1](./media/connectors-create-api-facebook/facebook-1.png)
2. Ha korábban még nem hoztál létre kapcsolatot a Facebookkal, a rendszer kérni fogja a Facebook-hitelesítő adataid megadását. Ezeket a hitelesítő adatokat arra használjuk, hogy engedélyezd a Logikai alkalmazásod számára, hogy csatlakozzon a Facebook-fiókod adataihoz, és hozzáférjen azokhoz:  
   ![facebook lépés 2](./media/connectors-create-api-facebook/facebook-2.png)
3. Adja meg facebookos felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![facebook lépés 3](./media/connectors-create-api-facebook/facebook-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![facebook lépés 4](./media/connectors-create-api-facebook/facebook-4.png)   

