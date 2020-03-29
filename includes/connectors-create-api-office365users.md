---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789518"
---
## <a name="prerequisites"></a>Előfeltételek

* [Office 365-felhasználók](https://office365.com) fiókja  

Mielőtt az Office 365-felhasználók fiókját egy logikai alkalmazásban használhatna, engedélyeznie kell a Logic alkalmazást az Office 365-felhasználók fiókhoz való csatlakozáshoz. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból.  

Az alábbi lépésekkel engedélyezheti a Logic alkalmazásnak az Office 365-felhasználók hoz való csatlakozást:  

1. Ha kapcsolatot szeretne létrehozni az Office 365-felhasználókkal, a Logikai alkalmazástervezőben válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be az Office *365-felhasználók kifejezést* a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![Az Office 365 Felhasználók kapcsolatának létrehozása lépés](./media/connectors-create-api-office365users/office365users-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot az Office 365-felhasználókkal, a rendszer felszólítja az Office 365-felhasználók hitelesítő adatainak megadására. Ezekkel a hitelesítő adatokkal engedélyezzük a Logikai alkalmazás nak, hogy csatlakozzon az Office 365-felhasználók fiókjához, és hozzáférjen azokhoz:  
   ![Az Office 365 Felhasználók kapcsolatának létrehozása lépés](./media/connectors-create-api-office365users/office365users-2.png)  
3. Adja meg az Office 365 Felhasználók felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![Az Office 365 Felhasználók kapcsolatának létrehozása lépés](./media/connectors-create-api-office365users/office365users-3.png)  
4. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![Az Office 365 Felhasználók kapcsolatának létrehozása lépés](./media/connectors-create-api-office365users/office365users-4.png)  

