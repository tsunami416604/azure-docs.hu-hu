---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789663"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -fiók 

A OneDrive-fiók logikai alkalmazásban való használata előtt engedélyezze a logikai alkalmazásnak a OneDrive-fiókhoz való kapcsolódást.  Ezt könnyedén elvégezheti a logikai alkalmazáson belül a Azure Portalon. 

A következő lépések végrehajtásával engedélyezheti a logikai alkalmazásnak a OneDrive-fiókhoz való kapcsolódást:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps Designerben válassza a legördülő listában a **Microsoft által felügyelt API** -k megjelenítése elemet, majd írja be a "onedrive" kifejezést a keresőmezőbe. Válasszon egy eseményindítót vagy műveletet:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Ha korábban még nem hozott létre kapcsolatot a OneDrive-hez, a rendszer felszólítja, hogy jelentkezzen be a OneDrive hitelesítő adataival:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Válassza a **Bejelentkezés**lehetőséget, és adja meg felhasználónevét és jelszavát. **Bejelentkezés**kiválasztása:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ezekkel a hitelesítő adatokkal engedélyezhető, hogy a logikai alkalmazás csatlakozhasson a szolgáltatáshoz, és hozzáférhessen a OneDrive-fiókban lévő adatokhoz. 
4. Válassza az **Igen** lehetőséget, ha engedélyezni szeretné a logikai alkalmazás számára a OneDrive-fiók használatát:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Figyelje meg, hogy létrejött a kapcsolatok. Most folytassa a logikai alkalmazás egyéb lépéseit:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

