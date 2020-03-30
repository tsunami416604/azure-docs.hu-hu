---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789663"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* [OneDrive-fiók](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Mielőtt egy logikai alkalmazásban használhatna OneDrive-fiókját, engedélyezze a logikai alkalmazásnak, hogy csatlakozzon a OneDrive-fiókjához.  Ezt egyszerűen megteheti a logikai alkalmazásában az Azure Portalon. 

Engedélyezze, hogy a logikai alkalmazás az alábbi lépésekkel csatlakozzon A OneDrive-fiókjához:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps tervezőjében válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be a "onedrive" kifejezést a keresőmezőbe. Válasszon egyet az eseményindítók vagy műveletek közül:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Ha korábban még nem hozott létre kapcsolatot a OneDrive-on, a rendszer kéri, hogy jelentkezzen be a OneDrive-hitelesítő adataival:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Válassza **a Bejelentkezés**lehetőséget, és adja meg a felhasználónevét és a jelszavát. Válassza **a Bejelentkezés**lehetőséget:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ezek a hitelesítő adatok arra szolgálnak, hogy engedélyezze a logikai alkalmazás számára, hogy csatlakozzon a OneDrive-fiókjához, és hozzáférjen azokhoz az adatokhoz. 
4. Válassza az **Igen** lehetőséget a logikai alkalmazás OneDrive-fiókjának használatához:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Figyelje meg, hogy létrejött a kapcsolat. Most folytassa a logikai alkalmazás további lépéseit:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

