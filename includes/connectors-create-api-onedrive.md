---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133947"
---
#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* A [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) fiók 

A OneDrive-fiókjába, a logikai alkalmazás használata előtt engedélyezze a logikai alkalmazás csatlakozni a OneDrive-fiókjába.  Ehhez egyszerűen az Azure Portalon, a logikai alkalmazásban. 

Engedélyezi a logikai alkalmazás csatlakozni a OneDrive-fiókjába az alábbi lépéseket követve:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps-tervezőben jelölje ki **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában, és írja be a "onedrive vállalati verzió" kifejezést a keresőmezőbe. Az eseményindítók és műveletek közül választhat:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Ha korábban még nem létrehozott kapcsolatok onedrive vállalati verzió, jelentkezzen be a onedrive vállalati verzió hitelesítő adatait kéri:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Válassza ki **jelentkezzen be a**, és adja meg a felhasználónevet és jelszót. Válassza ki **jelentkezzen be a**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ezeket a hitelesítő adatokat engedélyezése a logikai alkalmazás csatlakozhat, és a onedrive vállalati verzió fiókjában található adatok elérésére szolgálnak. 
4. Válassza ki **Igen** engedélyezése a logikai alkalmazás a OneDrive-fiók használata:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Figyelje meg, hogy a kapcsolat létrejött. Most folytassa a további lépések a logikai alkalmazásban:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

