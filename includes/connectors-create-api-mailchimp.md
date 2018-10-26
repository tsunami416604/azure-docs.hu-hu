---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 752c43604349a2361a8f5b26cd6d0bce7b516bc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134173"
---
### <a name="prerequisites"></a>Előfeltételek
* A [MailChimp](https://www.MailChimp.com/) fiók 

A MailChimp-fiókban, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a MailChimp-fiókban való kapcsolódáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a MailChimp-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. A Logic app Designerben MailChimp, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *MailChimp* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![A MailChimp 1. lépés.](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Ha még nem hozott létre, mielőtt MailChimp kapcsolatokat, első kéri a MailChimp hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a MailChimp-fiókban adatok elérésére:  
   ![A MailChimp 2. lépés.](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Adja meg a MailChimp felhasználónevet és jelszót, hogy a logikai alkalmazás engedélyezése:  
   ![A MailChimp 3. lépés.](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![A MailChimp 4. lépés.](./media/connectors-create-api-mailchimp/mailchimp-4.png)

