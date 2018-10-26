---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133813"
---
#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [Office 365](https://office365.com) fiók  

Az Office 365-fiókjával, a logikai alkalmazás használatához engedélyezze a logikai alkalmazás az Office 365-fiókhoz való csatlakozáshoz. Ehhez egyszerűen az Azure Portalon, a logikai alkalmazásban.  

Engedélyezi a logikai alkalmazás az Office 365-fiók az alábbi lépéseket követve csatlakozhat:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps-tervezőben válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában, majd adja meg az "office 365" kifejezést a keresőmezőbe. Az eseményindítók és műveletek közül választhat:  
    ![Az Office 365 kapcsolat létrehozási lépés](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Ha korábban még nem létrehozott Office 365 fennálló kapcsolatokat, az Office 365-höz használt hitelesítő adataival bejelentkezni kéri:  
    ![Az Office 365 kapcsolat létrehozási lépés](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Válassza ki **jelentkezzen be a**, és adja meg a felhasználónevet és jelszót. Válassza ki **jelentkezzen be a**:  
    ![Az Office 365 kapcsolat létrehozási lépés](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Ezeket a hitelesítő adatokat segítségével csatlakozhat és az Office 365-fiókkal érheti el a logikai alkalmazás engedélyezése. 
4. Figyelje meg, hogy a kapcsolat létrejött. Most folytassa a további lépések a logikai alkalmazásban:   
    ![Az Office 365 kapcsolat létrehozási lépés](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

