---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789653"
---
## <a name="prerequisites"></a>Előfeltételek

* [MailChimp-fiók](https://www.MailChimp.com/) 

Mielőtt használhatja a MailChimp-fiókot egy logikai alkalmazásban, engedélyeznie kell a Logic alkalmazást a MailChimp-fiókhoz való csatlakozáshoz. Szerencsére ezt egyszerűen megteheti az Azure Portalon lévő Logic alkalmazásból. 

Az alábbi lépésekkel engedélyezheti, hogy a Logic alkalmazás csatlakozzon a MailChimp-fiókhoz:

1. A MailChimp-rel való kapcsolat létrehozásához a Logikai alkalmazás tervezőjében válassza a **Microsoft által felügyelt API-k megjelenítése** lehetőséget a legördülő listában, majd írja be a *MailChimp* kifejezést a keresőmezőbe. Válassza ki a használni kívánt eseményindítót vagy műveletet:  
   ![MailChimp 1.](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Ha még nem hozott létre kapcsolatot a MailChimp-lel, a rendszer kéri a MailChimp hitelesítő adatainak megadását. Ezeket a hitelesítő adatokat arra használjuk, hogy engedélyezze a Logic alkalmazás számára, hogy csatlakozzon a MailChimp-fiók adataihoz, és hozzáférjen azokhoz:  
   ![MailChimp lépés 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Adja meg mailchimp felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  
   ![MailChimp lépés 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Figyelje meg, hogy létrejött a kapcsolat, és most már szabadon folytathatja a logikai alkalmazás további lépéseit:  
   ![MailChimp 4.](./media/connectors-create-api-mailchimp/mailchimp-4.png)

