---
title: Vizsgálja meg a felhasználók, az alkalmazásfelhasználók elemzését, az Azure-Sentinel-Előzetesében |} A Microsoft Docs
description: Ismerje meg a felhasználók, az alkalmazásfelhasználók elemzését, az Azure-Sentinel kivizsgálása.
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b9944764345f97c561f3f82f9cce9d37e204f134
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992977"
---
# <a name="investigate-users-with-user-analytics"></a>Vizsgálja meg a felhasználók, az alkalmazásfelhasználók elemzését

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az alaposan figyelendő kívánt entitásokat, a felhasználók. Könnyebben nyerhet betekintést a felhasználók, az Azure-Sentinel zökkenőmentesen integrálható az Azure komplex veszélyforrások elleni védelem. Ez az integráció lehetővé teszi a felhasználói viselkedés elemzéséhez, és mely felhasználók kell vizsgálni – először Azure Sentinel-és a Microsoft 365 riasztások, és a gyanús tevékenység minták alapján rangsorolja.

Az Azure Sentinel bővíti a felhasználói adatok Analytics engedélyezése minden felhasználó számára az Azure Active Directoryban analytics és a kockázati elemzés átfogó felhasználói a Microsoft 365-ből. 

## <a name="how-it-works"></a>Működés

1. Alapján az analitikus szabályok, ha egyezést észlel, a riasztásokat az Azure-Sentinel küld az Azure ATP biztonsági.
1. Az Azure ATP ellenőrzi, hogy mely felhasználói entitások kapcsolatos riasztások, és kiszámítja a vizsgálat prioritás azoknak a felhasználóknak.
3. Az Azure ATP újraszámítja a pontszám a felhasználók adatait az analytics-szabályok az Azure-Sentinel bővített után.


## <a name="prerequisites"></a>Előfeltételek

- Az Azure komplex veszélyforrások elleni védelem licence 
- A funkció engedélyezéséhez, melyen telepítette az Azure-Sentinel a bérlő globális rendszergazdai jogosultságokkal kell

> [!NOTE]
> - Minden egyes Azure ATP-bérlő csatlakoztathatja egy Azure Sentinel-példány.
> - Felhasználói analytics jelenleg csak az Azure Active Directory-felhasználók számára. 

## <a name="enable-user-analytics"></a>Felhasználói analytics engedélyezése

1. Ahhoz, hogy az a portálon, lépjen a Sentinel-Azure, az alkalmazásfelhasználók elemzését a **felhasználói analytics** lapon, és kattintson a **engedélyezése**. Ez az Azure ATP információt küld adatokat.

1. Ezt követően vesz igénybe, az Azure ATP. Alatt **biztonsági bővítmények** a a **a Microsoft Azure Sentinel-** lapra, majd a **+ plusz** hozzáadása, és válassza ki a munkaterületet. 
1. Kattintson a **Connect** (Csatlakozás) gombra.

## <a name="investigate-a-user"></a>A felhasználók vizsgálata

1. Az Azure-Sentinel menü alatt **felhasználói analytics**, tekintse át a kiválasztott felhasználók vizsgálat prioritásuk szerint. A pontszám Azure Sentinel-riasztások és értesítések a Microsoft 365 alapul.

2. Keressen rá a vizsgálni kívánt felhasználó. Kattintson a felhasználót, hogy a felhasználó a lap. Tekintse át a felhasználói tevékenységek és a riasztások időbeli alakulása, az eseményeket egy idővonalon megjelenítve. Láthatja, hogy a Microsoft 365 és az Azure-Sentinel érkező tevékenységek. A felhasználói oldalon is elérheti azokat a felhasználókhoz egy esetet részletezésével.
      
    ![Felhasználók](./media/user-analytics/user-investigation.png)

 
3. Ez megfelelően működjön, meg kell gondoskodnia a [egyéni riasztási szabály](tutorial-detect-threats.md) a megfelelő felhasználói azonosítók való leképezéséhez a **fiók** entitás.

    - Windows-eseményt, térkép **fiók** , a **biztonsági azonosítója**
    - (Ez található számos naplók, beleértve az Azure-tevékenység) az Azure AD-adatok vagy az Office 365-adatokhoz, képezze le a **fiók** tulajdonságot a **GUID**, vagy a **egyszerű felhasználónév**. 

   ![Lekérdezés](./media/user-analytics/query-window.png)



Példa: 
> [!NOTE]
> A Tevékenységnaplók az Azure-tevékenység a hívó entitás tartalmazza a az egyszerű Felhasználónevet.

1. Ez a lekérdezés keres, a rendellenes száma erőforrásokhoz vagy az Azure-tevékenységnapló telepítési műveletek létrehozásához.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Az egyéni riasztási szabály leképezése a **fiók** tulajdonságot **hívó**.
   
   ![Lekérdezés](./media/user-analytics/query-window.png)

3. Vizsgálja meg a felhasználó a felhasználói vizsgálat ablakban. Vizsgálja meg a felhasználók kapcsolatos tanácsadás, lásd: [oktatóanyag: Vizsgálja meg a felhasználó](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett a Fenyegetésfelderítés szolgáltató csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket.

- Ismerkedés az Azure Sentinel-, Microsoft Azure-előfizetéssel kell. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [előkészíteni az adatok Azure Sentinel-](quickstart-onboard.md), és [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
