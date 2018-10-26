---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091771"
---
### <a name="prerequisites"></a>Előfeltételek
* Egy [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) fiók  

Az FTP-fiók, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az FTP-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az FTP-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A logic app Designerben FTP, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *FTP* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![FTP-kapcsolat létrehozási lépés](./media/connectors-create-api-ftp/ftp-1.png)  
2. Ha még nem hozott létre előtt FTP-kapcsolatokat, első kéri a FTP hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a FTP-fiókhoz tartozó adatok eléréséhez:  
   ![FTP-kapcsolat létrehozási lépés](./media/connectors-create-api-ftp/ftp-2.png)  
3. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![FTP-kapcsolat létrehozási lépés](./media/connectors-create-api-ftp/ftp-3.png)  

