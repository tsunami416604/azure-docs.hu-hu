---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524207"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -fiók 

A OneDrive-fiók logikai alkalmazásban való használata előtt engedélyezze a logikai alkalmazásnak a OneDrive-fiókhoz való kapcsolódást.  Ezt könnyedén elvégezheti a logikai alkalmazáson belül a Azure Portalon. 

A következő lépések végrehajtásával engedélyezheti a logikai alkalmazásnak a OneDrive-fiókhoz való kapcsolódást:

1. Hozzon létre egy logikai alkalmazást. A Logic Apps Designerben válassza a legördülő listában a **Microsoft által felügyelt API** -k megjelenítése elemet, majd írja be a "onedrive" kifejezést a keresőmezőbe. Válasszon egy eseményindítót vagy műveletet:  
   ![A "Microsoft Managed API-k megjelenítése" nevű párbeszédpanel egy "onedrive" nevű keresőmezőt tartalmaz. Itt látható a négy eseményindító listája. A listán elsőként a "OneDrive-when a fájl létrehozása" lehetőség van kiválasztva.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Ha korábban még nem hozott létre kapcsolatot a OneDrive-hez, a rendszer felszólítja, hogy jelentkezzen be a OneDrive hitelesítő adataival:  
   ![Egy "OneDrive-fájl létrehozásakor" nevű párbeszédablakban megjelenik a "Bejelentkezés" feliratú gomb.](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Válassza a **Bejelentkezés**lehetőséget, és adja meg felhasználónevét és jelszavát. **Bejelentkezés**kiválasztása:  
   ![A "Bejelentkezés" nevű párbeszédpanel arra utasítja a Microsoft-fiók használatát. Két szövegmezőt tartalmaz "e-mail-cím" és "password" címkével, és a "Bejelentkezés megtartása" címkével is rendelkezik.](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ezekkel a hitelesítő adatokkal engedélyezhető, hogy a logikai alkalmazás csatlakozhasson a szolgáltatáshoz, és hozzáférhessen a OneDrive-fiókban lévő adatokhoz. 
4. Válassza az **Igen** lehetőséget, ha engedélyezni szeretné a logikai alkalmazás számára a OneDrive-fiók használatát:  
   ![Egy párbeszédpanel, amelynek címe: "az alkalmazás hozzáférhet az adatokhoz?" engedélyt kér a következő négy dolog elvégzésére: 1.) "Bejelentkezés automatikusan", 2) "az e-mail-címek elérése", 3) "az adatok elérése bármikor", és 4) "hozzáférés OneDrive-fájlokhoz". Van egy "igen" gomb, amely engedélyt ad, és egy "nem" gombot a megtagadásához. Az alkalmazás engedélyeinek módosításához hivatkozás található.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Figyelje meg, hogy létrejött a kapcsolatok. Most folytassa a logikai alkalmazás egyéb lépéseit:  
   ![A "fájl létrehozásakor" nevű párbeszédpanel egy "mappa" nevű szövegmezőt tartalmaz a társított Tallózás gombbal.](./media/connectors-create-api-onedrive/onedrive-5.png)

