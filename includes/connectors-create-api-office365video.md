---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c0dcba2dd003dfebdd9ce67bf4d78082c32824ba
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789485"
---
## <a name="prerequisites"></a>Előfeltételek

* [Office 365-videó](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6) fiók  

Ahhoz, hogy használhassa az Office 365-es videós fiókját egy logikai alkalmazásban, engedélyeznie kell a logikai alkalmazásnak az Office 365-fiókhoz való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül.  

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak az Office 365-videó fiókhoz való kapcsolódást:  

1. Az Office 365 videóval létesített kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft által felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be az *Office 365 videó* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![Office 365 video-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365video/office365video-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot az Office 365-videóval, a rendszer kérni fogja, hogy adja meg az Office 365-videó hitelesítő adatait. Ezek a hitelesítő adatok arra szolgálnak, hogy engedélyezzék a logikai alkalmazásnak a kapcsolódást, és hozzáférhessen az Office 365 video-fiókja adataihoz:  
   ![Office 365 – videó-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365video/office365video-2.png)  
3. Adja meg hitelesítő adatait az Office 365 videóhoz való kapcsolódáshoz:  
   ![Office 365 – videó-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365video/office365video-3.png)  
4. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![Office 365 – videó-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365video/office365video-4.png)  

