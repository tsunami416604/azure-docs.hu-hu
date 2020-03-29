---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c0dcba2dd003dfebdd9ce67bf4d78082c32824ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789485"
---
## <a name="prerequisites"></a>Előfeltételek

* [Office 365 Videó](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6) fiók  

Mielőtt az Office 365 Videó-fiókját használhatna egy logikai alkalmazásban, engedélyeznie kell a Logic alkalmazást az Office 365 Videó-fiókhoz való csatlakozáshoz. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból.  

Az alábbi lépésekkel engedélyezheti a Logic alkalmazásnak az Office 365 Videó-fiókhoz való csatlakozást:  

1. Ha kapcsolatot szeretne létrehozni az Office 365 Videóval, a Logikai alkalmazástervezőben válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be az Office *365 Videó* kifejezést a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![Az Office 365 Videókapcsolat létrehozása lépés](./media/connectors-create-api-office365video/office365video-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot az Office 365 Videóval, a rendszer felszólítja az Office 365 Videó hitelesítő adatainak megadására. Ezekkel a hitelesítő adatokkal engedélyezzük a Logic alkalmazás nak, hogy csatlakozzon az Office 365 Videó-fiókjához, és hozzáférjen azokhoz:  
   ![Az Office 365 Videókapcsolat létrehozása lépés](./media/connectors-create-api-office365video/office365video-2.png)  
3. Adja meg hitelesítő adatait az Office 365 Videóhoz való csatlakozáshoz:  
   ![Az Office 365 Videókapcsolat létrehozása lépés](./media/connectors-create-api-office365video/office365video-3.png)  
4. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![Az Office 365 Videókapcsolat létrehozása lépés](./media/connectors-create-api-office365video/office365video-4.png)  

