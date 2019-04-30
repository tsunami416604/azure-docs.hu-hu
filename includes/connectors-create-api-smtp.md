---
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130012"
---
### <a name="prerequisites"></a>Előfeltételek
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) fiók  

Az SMTP-fiókja, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az SMTP-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az SMTP-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A logic app Designerben az SMTP-kapcsolat létrehozásához jelölje ki a **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *SMTP* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Ha még nem hozott létre, mielőtt SMTP kapcsolatokat, első kéri az SMTP hitelesítő adatait. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és az SMTP-fiókja adatainak eléréséhez:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

