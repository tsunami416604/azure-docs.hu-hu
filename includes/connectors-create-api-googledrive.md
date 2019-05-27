---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: fe726986b3f93ab3bb447b8973727a658ac1c706
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149308"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Google Drive-hoz](https://www.google.com/drive/) fiók  

A Google Drive-hoz fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a Google Drive-hoz-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazást a Google Drive-hoz-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A Logic app Designerben Google Drive-hoz, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Google Drive-hoz* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. Ha még nem hozott létre a googledrive-ba, mielőtt kapcsolatokat, első kéri a Google Drive-hoz hitelesítő adatokat. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Google Drive-hoz fiókhoz tartozó adatok eléréséhez:  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. Adja meg a Google Drive-hoz e-mail-címét:  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. Adja meg a Google Drive-hoz jelszavát, hogy a logikai alkalmazás engedélyezése:  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-4.png)
5. A Google Drive-hoz való csatlakozás engedélyezése  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Kapcsolat-létrehozási lépés Google Drive-hoz](./media/connectors-create-api-googledrive/googledrive-6.png)  

