---
ms.openlocfilehash: d1db175ecc2cf61de0debc15d198d6367aae8bdd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119975"
---
### <a name="prerequisites"></a>Előfeltételek
* A [box](http://box.com) fiók  

A box-fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a box-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A box-fiók kapcsolódni a logikai alkalmazás engedélyezése lépései a következők:  

1. A Logic app Designerben box, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *box* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Box kapcsolat létrehozási lépés](./media/connectors-create-api-box/box-1.png)  
2. Ha még nem hozott létre, mielőtt mezőben kapcsolatokat, első kéri a box hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a box-fiók adatok elérésére:  
   ![Box kapcsolat létrehozási lépés](./media/connectors-create-api-box/box-2.png)  
3. Adja meg a box felhasználói nevét és jelszavát, a logikai alkalmazás engedélyezése:  
   ![Box kapcsolat létrehozási lépés](./media/connectors-create-api-box/box-3.png)  
4. Csatlakozhat a boxhoz elindíthatjuk:  
   ![Box kapcsolat létrehozási lépés](./media/connectors-create-api-box/box-4.png)  
5. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Box kapcsolat létrehozási lépés](./media/connectors-create-api-box/box-5.png)  

