---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3a0cb48e7fe5f3eef101f644e4f72fcfa2689d6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789450"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy [OneDrive](https://OneDrive.com) -fiók 

Ahhoz, hogy a OneDrive vállalati fiókhoz használhassa a logikai alkalmazásokban, engedélyeznie kell a logikai alkalmazásnak, hogy kapcsolódjon a OneDrive for Business-fiókhoz. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül. 

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak, hogy csatlakozhasson a OneDrive for Business-fiókhoz:

1. A OneDrive for Business kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *OneDrive for Business* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Ha még nem hozott létre kapcsolatot a vállalati OneDrive, a rendszer kérni fogja, hogy adja meg a OneDrive az üzleti hitelesítő adatokhoz. Ezek a hitelesítő adatok arra szolgálnak, hogy engedélyezzék a logikai alkalmazás kapcsolódását, és hozzáférhessenek a OneDrive a vállalati fiók adataihoz:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. A logikai alkalmazás engedélyezéséhez adja meg a OneDrive for Business-felhasználónevet és-jelszót:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

