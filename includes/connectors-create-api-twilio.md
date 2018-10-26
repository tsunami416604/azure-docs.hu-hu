---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 845b27b8efd66de87ec08e0b5b81bcc332dffdfb
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094767"
---
### <a name="prerequisites"></a>Előfeltételek
* Twilio-fiókja
* Egy ellenőrzött Twilio telefonszám, amely fogadhatnak SMS
* Egy ellenőrzött Twilio telefonszám, amely lehet SMS küldése

> [!NOTE]
> Próbaverziós Twilio-fiók használatakor, elküldheti az SMS-t csak **ellenőrzött** ügyfélszolgálatának telefonszámai.  
> 
> 

A Twilio-fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Twilio-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A Twilio-fiók kapcsolódni a logikai alkalmazás engedélyezése lépései a következők:

1. Hozzon létre egy kapcsolatot a Twilio, a Logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Twilio* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Ha még nem hozott létre a Twilióhoz előtt kapcsolatokat, első kéri a Twilio hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a Twilio-fiók adatok elérésére:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Szüksége lesz a **Twilio-fiókazonosító** és **Twilio hozzáférési jogkivonat** Twilio az irányítópultról, jelentkezzen be a Twilio-fiók most ezeket kétféle információra megnyitása:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio és a Logic apps különböző neveket használatával azonosíthatja ezeket kétféle információra. Itt látható, hogyan kell társítani őket, a Logic apps párbeszédpanel: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Válassza ki a **hozzon létre kapcsolatot** gombra:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

