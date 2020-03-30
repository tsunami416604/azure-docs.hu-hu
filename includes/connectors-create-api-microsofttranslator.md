---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 94f18b20d84a1929368053c44b4f07793e385b38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789570"
---
## <a name="prerequisites"></a>Előfeltételek

* [MicrosoftTranslator-fiók](https://www.microsoft.com/translator)  

Mielőtt a MicrosoftTranslator-fiók egy logikai alkalmazásban, engedélyeznie kell a Logic alkalmazás a MicrosoftTranslator-fiókhoz való csatlakozáshoz. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból.  

Az alábbi lépésekkel engedélyezheti a Logic alkalmazásnak a MicrosoftTranslator-fiókhoz való csatlakozást:  

1. A MicrosoftTranslator szolgáltatással való kapcsolat létrehozásához a Logikai alkalmazástervezőben válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be a *MicrosoftTranslator* kifejezést a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![MicrosoftTranslator kapcsolat létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-1.png)  
2. Ha még nem hozott létre kapcsolatot a MicrosoftTranslator előtt, a rendszer kéri, hogy adja meg a MicrosoftTranslator hitelesítő adatait. Ezeket a hitelesítő adatokat arra használjuk, hogy engedélyezze a Logic alkalmazás számára, hogy csatlakozzon a MicrosoftTranslator-fiók adataihoz, és hozzáférjen azokhoz:  
   ![MicrosoftTranslator kapcsolat létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-2.png)  
3. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![MicrosoftTranslator kapcsolat létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-3.png)  

