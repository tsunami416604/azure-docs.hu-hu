---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 48cd41d432c5969fc4128b055ca61fc86a57bdd2
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134334"
---
### <a name="prerequisites"></a>Előfeltételek
* A [OneDrive](http://OneDrive.com) fiók 

A onedrive vállalati verziójához tartozó fiókban, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a OneDrive-fiókkal való kapcsolódáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás csatlakozni a OneDrive vállalati verzióbeli fiók hitelesítéséhez lépései a következők:

1. Hozzon létre egy kapcsolatot a onedrive vállalati verzióba, a Logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *OneDrive vállalati verzióba* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Ha még nem hozott létre a onedrive vállalati verzióba előtt kapcsolatokat, a rendszer adja meg a onedrive-hoz tartozó hitelesítő adatait fogja kérni. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a OneDrive vállalati fiók adatainak eléréséhez:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Adja meg a OneDrive vállalati üzleti felhasználónevet és jelszót a logikai alkalmazás engedélyezése:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

