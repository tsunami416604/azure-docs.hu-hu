---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 96943405f3fce02b8a07158f797dd204eb4bb8e2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789707"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy [GoogleDrive](https://www.google.com/drive/) -fiók  

Ahhoz, hogy GoogleDrive-fiókját a logikai alkalmazásban használhassa, engedélyeznie kell a logikai alkalmazásnak a GoogleDrive-fiókhoz való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül.  

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak a GoogleDrive-fiókhoz való kapcsolódást:  

1. A GoogleDrive-kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *GoogleDrive* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot a GoogleDrive, a rendszer kérni fogja, hogy adja meg a GoogleDrive hitelesítő adatait. Ezek a hitelesítő adatok a logikai alkalmazáshoz való kapcsolódáshoz és a GoogleDrive-fiók adatainak eléréséhez lesznek használva:  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. Adja meg a GoogleDrive e-mail-címét:  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. Adja meg a GoogleDrive jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-4.png)
5. GoogleDrive való kapcsolódás engedélyezése  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![GoogleDrive-kapcsolatok létrehozási lépése](./media/connectors-create-api-googledrive/googledrive-6.png)  

