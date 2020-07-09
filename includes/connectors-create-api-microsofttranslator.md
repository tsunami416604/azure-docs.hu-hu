---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 94f18b20d84a1929368053c44b4f07793e385b38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789570"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy [MicrosoftTranslator](https://www.microsoft.com/translator) -fiók  

Ahhoz, hogy MicrosoftTranslator-fiókját a logikai alkalmazásban használhassa, engedélyeznie kell a logikai alkalmazásnak a MicrosoftTranslator-fiókhoz való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül.  

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak a MicrosoftTranslator-fiókhoz való kapcsolódást:  

1. A MicrosoftTranslator-kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *MicrosoftTranslator* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![MicrosoftTranslator-kapcsolatok létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-1.png)  
2. Ha korábban még nem hozott létre kapcsolatot a MicrosoftTranslator, a rendszer kérni fogja, hogy adja meg a MicrosoftTranslator hitelesítő adatait. Ezek a hitelesítő adatok a logikai alkalmazáshoz való kapcsolódáshoz és a MicrosoftTranslator-fiók adatainak eléréséhez lesznek használva:  
   ![MicrosoftTranslator-kapcsolatok létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-2.png)  
3. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![MicrosoftTranslator-kapcsolatok létrehozási lépése](./media/connectors-create-api-microsofttranslator/microsofttranslator-3.png)  

