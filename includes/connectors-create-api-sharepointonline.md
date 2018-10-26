---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 0cabc58d856c09accd9b1924fe63d6518b1cb9ef
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093501"
---
Használata esetén csatlakozhassanak az **SharePoint online-hoz**, meg kell adnia az identitás (felhasználónév és jelszó, intelligens kártya hitelesítő adatait, stb.) a SharePoint online-hoz. Ön már megtörtént a hitelesítést követően folytathatja a SharePoint Online-összekötő használatára a logikai alkalmazásban. 

A tervezőben a logikai alkalmazás létrehozásához jelentkezzen be a SharePoint alábbi lépéseket követve a **kapcsolat** a logikai alkalmazásban használható:

1. A keresőmezőbe írja be a SharePoint, és várjon, amíg az összes eseményindítók és a SharePoint online-hoz kapcsolódó művelet keresése:   
   ![A SharePoint konfigurálása][1]  
2. Válassza ki a **a SharePoint online-ban – amikor létrejön egy fájl** eseményindító  
3. Válassza ki **bejelentkezés a SharePoint online-bA**:   
   ![A SharePoint konfigurálása][2]    
4. Jelentkezzen be a hitelesítést és a SharePoint, a SharePoint hitelesítő adatok megadása   
   ![A SharePoint konfigurálása][3]     
5. A hitelesítés befejezése után átirányítjuk a logikai alkalmazáshoz. Ennyi az egész, a kapcsolat létrejött. Figyelje meg, amely azt jelzi, hogy most már csatlakozott a SharePoint alján az üzenet.  
   ![A SharePoint konfigurálása][4]  
6. Ezután hozzáadhatja a más eseményindítókat és műveleteket, amelyeket a logikai alkalmazás végrehajtásához szükséges.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
