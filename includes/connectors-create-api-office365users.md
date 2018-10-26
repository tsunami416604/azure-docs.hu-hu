---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: ebae0fac5edc0bb79e6a19d8bdc741960f0b0e20
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134040"
---
### <a name="prerequisites"></a>Előfeltételek
* Egy [Office 365-felhasználók](https://office365.com) fiók  

Az Office 365 felhasználói fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az Office 365 felhasználói fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az Office 365 felhasználói fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A Logic app Designerben az Office 365 Users-kapcsolat létrehozásához jelölje ki a **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Office 365-felhasználók* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Az Office 365 felhasználók kapcsolódási létrehozási lépés](./media/connectors-create-api-office365users/office365users-1.png)  
2. Ha még nem hozott létre az Office 365-felhasználók előtt a kapcsolatokat, első kéri, adja meg az Office 365-felhasználók hitelesítő adatait. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Office 365 felhasználói fiókhoz tartozó adatok eléréséhez:  
   ![Az Office 365 felhasználók kapcsolódási létrehozási lépés](./media/connectors-create-api-office365users/office365users-2.png)  
3. Adja meg a Office 365-felhasználók felhasználónevét és jelszavát a logikai alkalmazás engedélyezése:  
   ![Az Office 365 felhasználók kapcsolódási létrehozási lépés](./media/connectors-create-api-office365users/office365users-3.png)  
4. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Az Office 365 felhasználók kapcsolódási létrehozási lépés](./media/connectors-create-api-office365users/office365users-4.png)  

