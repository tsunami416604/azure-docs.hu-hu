---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789850"
---
## <a name="prerequisites"></a>Előfeltételek

* [Facebook](https://www.facebook.com/) -fiók 

Ahhoz, hogy használni tudja a Facebook-fiókját egy logikai alkalmazásban, engedélyeznie kell a logikai alkalmazásnak a Facebook-fiókjához való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül. 

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak a Facebook-fiókjához való kapcsolódást:

1. A Facebookhoz való kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *Facebook* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![Facebook 1. lépés](./media/connectors-create-api-facebook/facebook-1.png)
2. Ha korábban még nem hozott létre kapcsolatokat a Facebook-hoz, a rendszer kérni fogja a Facebook hitelesítő adatainak megadását. Ezek a hitelesítő adatok arra szolgálnak, hogy engedélyezzék a logikai alkalmazáshoz való kapcsolódást és a Facebook-fiók adatainak elérését:  
   ![Facebook 2. lépés](./media/connectors-create-api-facebook/facebook-2.png)
3. Adja meg a Facebook-felhasználónevét és-jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![Facebook 3. lépés](./media/connectors-create-api-facebook/facebook-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![Facebook 4. lépés](./media/connectors-create-api-facebook/facebook-4.png)   

