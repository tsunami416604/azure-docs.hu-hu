---
title: "Az Azure felügyeleti csoportok - Azure-erőforrások rendszerezéséhez |} Microsoft Docs"
description: "További tudnivalók a felügyeleti csoportok és a használatukat."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Az Azure felügyeleti csoportok-erőforrások rendszerezése 

Ha több előfizetéssel rendelkezik, rendszerezheti őket "felügyeleti csoport" nevű tárolók való hozzáférést, a házirend, a költségek és a megfelelőség kezeléséhez az előfizetések között. Tegyük fel akkor házirendeket is alkalmazhat a felügyeleti csoport ezt a határértéket, milyen létrehozhatók.

> [!Note]
> Ez a funkció jelenleg magán előnézetben. [Itt regisztrálhat](https://aka.ms/MGPreviewSignup) kell rendelkeznie a csatlakozás az előzetes regisztrációját.   
 


Felügyeleti csoportok szerint csoportosíthatóak a hierarchiában. A bemutatott struktúra egy felügyeleti csoport hierarchia csak egy minta ábrázolása:


![Hierarchiafája](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Hogyan kapcsolódó felügyeleti csoport az Azure a nagyvállalati beléptetés

A felügyeleti csoportok bevezetése Ez a nagyobb út tér át lépés [vállalati portál](https://ea.azure.com) funkciói szorulnak a [Azure-portálon](https://portal.azure.com).

A felügyeleti csoport struktúra jön létre, a vállalati portál van definiálva. A teljes hierarchia regisztráció, a szervezeti egységek és a fiókok megfelelő felügyeleti csoportok vannak leképezve. 

Ez az aktuális EA struktúra hogyan felügyeleti csoport hierarchia rendeli. 

![Hierarchiafája](media/billing-enterprise-mgmt-groups/tree2.png)

Az alábbi táblázat mutatja, hogy a felhasználók a vállalati portálról a felügyeleti csoport hierarchia van leképezve.

|    EA szerepkör                                       |    A csatlakoztatott felügyeleti csoport csomóponton szerepkör    |    A felügyeleti csoport csomópontot engedélyek                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA rendszergazda                              |    Erőforrás házirend közreműködő                   |    Is megtekintheti a költségek, erőforrás házirend és a nézet hierarchiában, és a beléptetési csomópont alatt kezelése    |
|    Csak olvasható módban EA rendszergazda            |    Számlázási olvasó                                |    Olvashatja a költségek, és megtekintheti a hierarchiában, és a beléptetési csomópont alatt                              |
|    Szervezeti egység rendszergazdája                      |    Számlázási olvasó                                |    Költségek olvashatja és hierarchia megtekintése és a részleg csomópont alatt                                 |
|    Csak olvasható módban részleg rendszergazda    |    Számlázási olvasó                                |    Költségek olvashatja és hierarchia megtekintése és a részleg csomópont alatt                                 |
|    Fiók tulajdonosának                                 |    Erőforrás házirend közreműködő                   |    Is megtekintheti a költségek, erőforrás házirend és a nézet hierarchiában, és a fiók csomópont alatt kezelése       |




## <a name="view-management-groups-in-the-azure-portal"></a>Felügyeleti csoportok megtekintése az Azure-portálon

A beléptetési, részleg vagy egy fiók belül az előzetes megtekintéséhez jelentkezzen be az Azure-portálon a hivatkozás az üdvözlő e-mailben.   

![hierarchia](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Költségek megtekintése 
A felügyeleti csoportok részletei képernyőn tekintse meg az aktuális hónap dátumig költségeket. Ezek a költségek a felhasználás alapján, és nem derül ki előre összegek, többletfelhasználás, belefoglalt mennyiségek, módosításának és adók. A felügyeleti csoportnak, a beléptetési megfelelő, a költségek szakasz bemutatja, a fennmaradó előre.  

![regisztráció-részletek](media/billing-enterprise-mgmt-groups/enrollment.png)

 "A költségek külön számlázva" olyan havi felhalmozódásához külön módosításokat, például a piactér többletfelhasználás és egyéb költségek, amely nem nyissa meg a beléptetési kötelezettségvállalás ellen.  A költségek lebontása kapcsolatos további információkért tekintse meg a [vállalati portál](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Költségek való hozzáférés engedélyezése
Ha nem Ön költségek, tekintse meg a [kapcsolatos problémák elhárítása vállalati költség nézetek](https://aka.ms/enableazurecosts) dokumentum segítségét.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>A vállalati portál és az Azure-portálon közötti üzenetváltás miatti késésekre 
* Az előzetes változatban jelenik meg az Azure-portálon belül összegek előfordulhat, hogy később összehasonlított értékek a vállalati portálon. A probléma ideiglenes, és a rendszer éppen dolgozott.
* A vállalati portál frissített beállításokkal a frissítéseket az Azure-portálon jelennek meg több percig várnia kell. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Felügyeleti csoportok áll a címtár kapcsolatban   
Előfizetések, például a felügyeleti csoportok is az Azure ad-val megbízhatósági kapcsolatot. A felügyeleti csoport hierarchia megbízhatósági kapcsolatok egy egyetlen directory a felhasználók hitelesítéséhez. A felügyeleti csoport hierarchia társított összes rendszergazda ugyanabban a könyvtárban kell tartoznia. 

A beléptetési hierarchia hozzá van rendelve, a felügyeleti csoportokhoz, mivel egyetlen könyvtár megbízhatósági kapcsolat jön létre. Ahol lehetséges, a beléptetési felhasználói fiókok társított meglévő címtárhoz van kiválasztva. Bizonyos esetekben egy új könyvtár jön létre, és minden meglévő beléptetési felhasználók felkérik abba a könyvtárba. A regisztráció előfizetések társított könyvtárak, nem érintettek. Emiatt a hierarchia előfordulhat, hogy az előfizetések eltérő könyvtárban létrehozása. [További](billing-enterprise-mgmt-grp-find.md) arról, hogy ez a folyamat milyen hatással van a hierarchia és az előfizetések közötti navigálás élménye.

## <a name="administering-your-management-groups"></a>A felügyeleti csoportok felügyelete
Felügyeleti csoportok az Azure-portálon belül még csak előzetes verziójúak, és a kezdeti kiadásának csak olvashatók. Ahhoz, hogy minden olyan frissítés, nyissa meg a vállalati portálon. A frissítések automatikusan megjelennek az Azure-portálon. A vállalati portálon találja meg a súgó dokumentációjában találhat a módosításokat és kiegészítéseit.   

## <a name="policy-management"></a>Csoportházirend kezelése
A Resource Manager lehetővé teszi, hogy létrehozzon testreszabott házirendeket az erőforrások kezeléséhez. Felügyeleti csoportok, a házirendek rendelhetők a hierarchia bármely szinten és az erőforrások öröklése ezeket a szabályzatokat.  [További információ](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> A házirend nem kényszerítve van könyvtárak. 


