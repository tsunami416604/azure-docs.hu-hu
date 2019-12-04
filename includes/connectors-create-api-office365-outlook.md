---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789598"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* [Office 365](https://office365.com) -fiók  

Mielőtt az Office 365-fiókját egy logikai alkalmazásban használja, engedélyezze a logikai alkalmazásnak az Office 365-fiókhoz való kapcsolódást. Ezt könnyedén elvégezheti a logikai alkalmazáson belül a Azure Portalon.  

Engedélyezze a logikai alkalmazásnak az Office 365-fiókhoz való kapcsolódást az alábbi lépések végrehajtásával:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps Designerben válassza a legördülő listában a **Microsoft által felügyelt API** -k megjelenítése elemet, majd a keresőmezőbe írja be az "Office 365" kifejezést. Válasszon egy eseményindítót vagy műveletet:  
    ![Office 365-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Ha korábban még nem hozott létre kapcsolatokat az Office 365-hez, a rendszer felszólítja, hogy jelentkezzen be az Office 365-beli hitelesítő adataival:  
    ![Office 365-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Válassza a **Bejelentkezés**lehetőséget, és adja meg felhasználónevét és jelszavát. **Bejelentkezés**kiválasztása:  
    ![Office 365-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Ezekkel a hitelesítő adatokkal engedélyezheti a logikai alkalmazásnak a kapcsolódást és az Office 365-fiók elérését. 
4. Figyelje meg, hogy létrejött a kapcsolatok. Most folytassa a logikai alkalmazás egyéb lépéseit:   
    ![Office 365-kapcsolatok létrehozási lépése](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

