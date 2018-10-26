---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 20729d1e73dd0ff9010201a1c6597b616b364a09
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134433"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Office 365 videó](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6) fiók  

Az Office 365 videó-fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az Office 365 videó-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az Office 365 videó-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. Hozzon létre egy kapcsolatot az Office 365 videó, a Logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Office 365 videó* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Az Office 365 videó kapcsolat létrehozási lépés](./media/connectors-create-api-office365video/office365video-1.png)  
2. Ha még nem hozott létre az Office 365 videó előtt kapcsolatokat, első kéri, adja meg az Office 365 videó hitelesítő adatait. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Office 365 videó fiókhoz tartozó adatok eléréséhez:  
   ![Az Office 365 videó kapcsolat létrehozási lépés](./media/connectors-create-api-office365video/office365video-2.png)  
3. Az Office 365 videóról való kapcsolódáshoz hitelesítő adatok megadása:  
   ![Az Office 365 videó kapcsolat létrehozási lépés](./media/connectors-create-api-office365video/office365video-3.png)  
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Az Office 365 videó kapcsolat létrehozási lépés](./media/connectors-create-api-office365video/office365video-4.png)  

