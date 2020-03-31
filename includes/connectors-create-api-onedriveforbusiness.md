---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3a0cb48e7fe5f3eef101f644e4f72fcfa2689d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789450"
---
## <a name="prerequisites"></a>Előfeltételek

* [OneDrive-fiók](https://OneDrive.com) 

Mielőtt használhatna OneDrive Vállalati verziós fiókját egy logikai alkalmazásban, engedélyeznie kell a Logikai alkalmazást, hogy csatlakozzon a OneDrive Vállalati verziós fiókjához. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból. 

Az alábbi lépésekkel engedélyezheti, hogy a Logikai alkalmazás csatlakozzon a OneDrive Vállalati verziós fiókhoz:

1. Ha kapcsolatot szeretne létrehozni a OneDrive Vállalati verzióval, a Logikai alkalmazástervezőben válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be a *OneDrive Vállalati verzió* kifejezést a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Ha korábban még nem hozott létre kapcsolatot a OneDrive Vállalati verzióval, a rendszer kérni fogja a OneDrive Vállalati verzió hitelesítő adatainak megadását. Ezekkel a hitelesítő adatokkal engedélyezzük a Logikai alkalmazás számára, hogy csatlakozzon a OneDrive Vállalati verziós fiókjához, és hozzáférjen azokhoz:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Adja meg a OneDrive Vállalati verzió felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

