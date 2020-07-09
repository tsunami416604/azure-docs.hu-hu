---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789653"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy [MailChimp](https://www.MailChimp.com/) -fiók 

Ahhoz, hogy MailChimp-fiókját a logikai alkalmazásban használhassa, engedélyeznie kell a logikai alkalmazásnak a MailChimp-fiókhoz való kapcsolódást. Szerencsére ezt könnyedén elvégezheti az Azure Portalon a logikai alkalmazáson belül. 

Az alábbi lépésekkel engedélyezheti a logikai alkalmazásnak a MailChimp-fiókhoz való kapcsolódást:

1. A MailChimp-kapcsolat létrehozásához a Logic app Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *MailChimp* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  
   ![MailChimp 1. lépés](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Ha korábban még nem hozott létre kapcsolatot a MailChimp, a rendszer kérni fogja, hogy adja meg a MailChimp hitelesítő adatait. Ezek a hitelesítő adatok a logikai alkalmazáshoz való kapcsolódáshoz és a MailChimp-fiók adatainak eléréséhez lesznek használva:  
   ![MailChimp 2. lépés](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Adja meg a MailChimp felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![MailChimp 3. lépés](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolódás, és most már ingyenesen folytathatja a logikai alkalmazás egyéb lépéseit:  
   ![4. lépés MailChimp](./media/connectors-create-api-mailchimp/mailchimp-4.png)

