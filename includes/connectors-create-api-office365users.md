---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789518"
---
## <a name="prerequisites"></a>Előfeltételek

* [Office 365 felhasználói](https://office365.com) fiók  

Ahhoz, hogy használhassa az Office 365-felhasználói fiókját egy logikai alkalmazásban, engedélyeznie kell a logikai alkalmazásnak az Office 365-felhasználói fiókhoz való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül.  

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak, hogy kapcsolódjon az Office 365 felhasználói fiókjához:  

1. Ha az Office 365-felhasználók kapcsolatát szeretné létrehozni, a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be az *Office 365-felhasználók* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![Office 365 felhasználói kapcsolatok létrehozási lépése](./media/connectors-create-api-office365users/office365users-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot az Office 365-felhasználók számára, a rendszer felszólítja az Office 365-felhasználók hitelesítő adatainak megadására. Ezeket a hitelesítő adatokat fogja használni a logikai alkalmazáshoz való kapcsolódáshoz és az Office 365 felhasználói fiók adatainak eléréséhez:  
   ![Office 365 felhasználói kapcsolatok létrehozási lépése](./media/connectors-create-api-office365users/office365users-2.png)  
3. Adja meg az Office 365 felhasználói felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![Office 365 felhasználói kapcsolatok létrehozási lépése](./media/connectors-create-api-office365users/office365users-3.png)  
4. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![Office 365 felhasználói kapcsolatok létrehozási lépése](./media/connectors-create-api-office365users/office365users-4.png)  

