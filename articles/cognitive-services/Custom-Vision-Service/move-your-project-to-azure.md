---
title: A korlátozott kísérleti projekt áthelyezése az Azure-bA
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan helyezze át egy korlátozott próbaverzió-projektet az Azure-bA.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821303"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>A korlátozott próbaverzió projekt áthelyezése az Azure-ban a CustomVision.ai hely


Mivel a Custom Vision Service most már a [Azure-on Előzetesben](https://azure.microsoft.com/services/preview/), támogatása korlátozott próbaverzió projektek Azure-on kívül lezárta. Ez a dokumentum megtanítja, hogyan használható a [Custom Vision webhely](https://customvision.ai) a korlátozott próbaverzió projekt egy Azure-erőforrás társítani kell áthelyezni. 

> [!NOTE]
> A Custom Vision projektek helyez át egy Azure-erőforrás, amikor azok az öröklési alapul szolgáló [engedélyek]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) adott Azure-erőforrás. Ha a szervezet más felhasználói az Azure-projektjéhez az erőforrás tulajdonosa, lesz elérni a projekthez a a [Custom Vision webhely](https://customvision.ai). Hasonlóképpen az erőforrások törlése eltávolítja a projekteket.  


Az előfizetésekre és erőforrásokra vonatkozó Azure fogalmakat szeretné megismerni, tekintse meg a [Azure fejlesztői útmutatójából.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Előfeltételek

A Microsoft-fiók vagy az Azure Active Directory (AAD) fiók segítségével jelentkezzen be tartozó érvényes Azure-előfizetést kell a [Custom Vision webhely](https://customvision.ai). 

Ha nem rendelkezik Azure-fiók [hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Egyéni vizuális erőforrások létrehozása az Azure Portalon
A Custom Vision Service használata az Azure-ban, szüksége lesz a Custom Vision betanítási és Predikciós erőforrások létrehozása a [az Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Ez a projekt áthelyezése [Custom Vision webhely](https://customvision.ai) tapasztalatokat, létre kell hoznia az erőforrások az az USA déli középső régiójában, mivel minden korlátozott próbaverzió projekt futnak, USA déli középső Régiójában. 

Egyetlen erőforrást több projektet társíthatók. További részleteket tartalmaz [díjszabás és korlátok](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) érhető el. Továbbra is ingyenesen használhatja a Custom Vision Service, válassza ki a F0 szintre az Azure Portalon. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Helyezze át a korlátozott próbaverzió projekt egy Azure-erőforrás

1.  A böngészőben navigáljon a [Custom Vision webhely](https://customvision.ai) válassza __jelentkezzen be a__. Nyissa meg a projekt egy Azure-fiókba migrálni szeretne. 
2.  A képernyő felső – jobb-oldali sarokban lévő fogaskerék ikonra kattintva nyissa meg a projekthez tartozó beállítások lap. 

    ![Projektbeállítások a lap felső sarkában lévő fogaskerék ikonra.](./media/move-your-project-to-azure/settings-icon.png)


3. Kattintson a __áthelyezése az Azure-bA__.

    ![A Projektbeállítások lap bal alsó van áthelyezése az Azure-ban gombra.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Lévő legördülő menüből a __áthelyezése az Azure-bA__ gombra, válassza ki a projekthez, hogy áthelyezi az Azure-erőforrás. Kattintson a __áthelyezése__. 

5. Ha nem látja a Custom Vision Service korábban létrehozott Azure-erőforrás, akkor valószínűleg egy másik címtárban. A projekthez egy másik címtárban erőforrás át, kövesse az alábbi utasításokat. 

    ![Projekt áttelepítési ablakot.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>Projekt áthelyezése egy másik Azure-címtárhoz 

> [!NOTE]
> Az Azure portál és CustomVision.ai válassza ki a címtárat a képernyő jobb felső sarkában lévő legördülő felhasználói menüből.   


1. Melyik címtárban szerepel az Azure-erőforrás azonosítja. A könyvtár a felhasználónevére a jobb felső sarkában az Azure portál menüsávjában alatt felsorolt találja. 

    ![A címtár a felhasználónevére a jobb felső sarkában az Azure portál menüsávjában területen látható. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Keresse meg az egyéni vizuális képzési erőforrás erőforrás-Azonosítóját. Megtalálhatja a az Azure Portalon nyissa meg az egyéni vizuális képzési erőforrást, majd válassza a "Tulajdonságok" a "Erőforrás-kezelés" szakaszban. Az erőforrás-azonosító nem lesz. 

    ![Keresse meg az erőforrás-azonosító az Azure Portalon nyissa meg az egyéni vizuális képzési erőforrást, majd válassza a "Tulajdonságok" a "Erőforrás-kezelés" szakaszban.](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Másik lehetőségként találhatja meg a Custom Vision erőforrás erőforrás-Azonosítójára közvetlenül a Custom Vision webhely [beállítások lapon]( https://www.customvision.ai/projects#/settings). Váltson át az Azure-erőforrás szerepel ugyanabban a címtárban kell.

    ![Az erőforrás-azonosító szerepel a listán az egyes erőforrások a beállítások oldalon a Custom Vision webhelyen.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Most, hogy az erőforrás-azonosító, térjen vissza a Custom Vision projekt egy Azure-erőforrás áthelyezése egy korlátozott próbaverzióra szeretne. Emlékeztető, szükség lehet váltson vissza az eredeti címtárat is megkeresheti. A megjelenő utasításokat követve [fent](#move-your-limited-trial-project-to-an-azure-resource) a Projektbeállítások lap megnyitásához, és válassza ki __áthelyezése az Azure-bA__. 


5. Az Azure-ablak a Váltás jelölje be az "Egy másik Azure-címtárhoz való áthelyezéshez?". Válassza ki azt a könyvtárat a projekthez, hogy helyezze át, és adja meg az erőforrás helyez át a projekthez, hogy az erőforrás-azonosítója. Kattintson a __áthelyezése__. 



5. Ne feledje, hogy a projekt már egy másik címtárban. A projekt megkereséséhez kell váltani a Custom Vision, amely a projektet a webes portálon ugyanabban a címtárban. Az Azure Portalon, és a [Custom Vision webhely](https://customvision.ai), kiválaszthatja a címtárban a fiókot a legördülő menüből a képernyő jobb felső sarkában. 

## <a name="next-steps"></a>További lépések

A projekt már át lett helyezve az Azure-erőforrás. A betanítási és Predikciós kulcsokat írt alkalmazások frissíteni kell.
