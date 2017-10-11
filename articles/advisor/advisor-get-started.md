---
title: "Ismerkedés az Azure Advisor |} Microsoft Docs"
description: "Ismerkedés az Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Útmutató az Azure-portálon keresztül férnek hozzá az Advisor, javaslatok beszerzése, ajánlások megvalósításával, keresse meg a javaslatok, illetve a frissítést.

## <a name="get-advisor-recommendations"></a>Javaslatok az Advisor használatával kapcsolatban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali ablaktáblában **további szolgáltatások**.

3. A szolgáltatás menü ablaktáblán alatt **figyelés és felügyelet**, kattintson a **Azure Advisor**.  
 Az Advisor irányítópult jelenik meg.

   ![Hozzáférés az Azure Advisor az Azure portál használatával](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Az Advisor irányítópultra válassza ki a javaslatok fogadni kívánt előfizetést.  
Az Advisor irányítópulton megjelenített személyre szabott javaslatok a kiválasztott előfizetéshez. 

5. Ahhoz, hogy egy adott kategória javaslatok, kattintson a lapfülek egyikére: **magas rendelkezésre állású**, **biztonsági**, **teljesítmény**, vagy **költség**.
 
> [!NOTE]
> Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

  ![Az Azure Advisor irányítópult](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Az Advisor javaslat részletes, és egy megoldás bevezetésére

A **javaslat** panel az Advisor nyújt további információt a javaslat. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd indítsa el a [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Az a **Advisor-javaslatokra** irányítópultján kattintson **javaslatok beszerzése**.

3. A javaslatok listájának megtekintéséhez kattintson a azt ajánljuk, hogy meg szeretné tekinteni részletesen.  
A **javaslat** panel jelenik meg.

4. Az a **javaslatok** panelt, tekintse át adatokat azokról a műveletekről, hogy akkor is lehetséges probléma megoldása érdekében hajtsa végre, vagy egy költségkímélő lehetőség előnyeit. 
  
  ![Az Advisor indexjavaslat panelen](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Keresés az Advisor-javaslatokra

Kereshet egy adott előfizetés vagy az erőforrás csoport javaslatok. Is kereshet javaslatok állapot szerint.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd indítsa el a [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Az előfizetések, erőforráscsoport-sablonok és javaslat állapot szűréssel javaslatokra keresési (**aktív** vagy **Snoozed**).

3. A keresés-szűrési feltételeknek megfelelő Advisor-javaslatokra listájának megjelenítéséhez kattintson **javaslatok beszerzése**.

  ![Az Advisor keresési-szűrési feltételeket](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Emlékeztet, vagy hagyja figyelmen kívül az Advisor-javaslatokra

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd indítsa el a [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **javaslatok beszerzése**, és a javaslatok listájának megtekintéséhez kattintson egy javaslat.

3. Az a **javaslat** panelen kattintson a **emlékeztet**.  

   ![Az Advisor javasolt művelet – példa](./media/advisor-get-started/advisor-snooze.png)

4. Adjon meg egy emlékeztető időszakra vonatkozóan, vagy válasszon **soha** elvetni a javaslat.


## <a name="next-steps"></a>Következő lépések

Az Advisor kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)
-  [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
