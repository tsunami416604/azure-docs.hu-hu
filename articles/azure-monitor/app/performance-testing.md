---
title: Teljesítmény- és terheléses tesztelési az Azure Application insights segítségével |} A Microsoft Docs
description: Teljesítmény- és terhelési tesztjeinek előnyeit az Azure Application Insights beállítása
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305119"
---
# <a name="performance-testing"></a>Teljesítménytesztelés

> [!NOTE]
> A felhőalapú terheléses tesztelési szolgáltatás elavult. Az elavulással kapcsolatos, a szolgáltatás rendelkezésre állása és az alternatív szolgáltatásokkal kapcsolatos további információ található [Itt](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Az Application Insights segítségével készítése a terheléses tesztelést webhelyei számára. Például [rendelkezésre állási tesztek](monitor-web-app-availability.md), vagy alapszintű kéréseket küldhet vagy [több lépésből álló kéréseket](availability-multistep.md) az Azure-ból tesztügynökök szerte a világon. A teljesítménytesztek lehetővé teszi legfeljebb 20 000 egyidejű felhasználók szimulálására szolgál, akár 60 percre.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Teljesítményteszt létrehozásához, először hozzon létre egy Application Insights-erőforrást. Ha már létrehozott egy erőforrás folytassa a következő szakaszban.

Az Azure Portalon, válassza ki a **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights** , és hozzon létre egy Application Insights az erőforrás.

## <a name="configure-performance-testing"></a>Teljesítménytesztelés konfigurálása

Ez-e az első alkalommal létrehozása teljesítmény teszt válassza **beállítása szervezet** , és válassza ki az Azure DevOps szervezetek a teljesítménytesztek forrása lehet.

A **konfigurálása**, lépjen a **Teljesítménytesztelés** kattintson **új** teszt létrehozásához.

![Töltse ki legalább a webhelye URL-címét](./media/performance-testing/new-performance-test.png)

Egy alapszintű teljesítményteszt létrehozásához válassza a teszt típusát **manuális tesztelése** , és töltse ki a kívánt beállításokat a teszteléshez.

|Beállítás| A maximális érték
|----------|------------|
| Felhasználói terhelés | 20,000 |
| Időtartam (perc)  | 60 |  

A teszt létrehozása után kattintson a **teszt futtatása**.

A teszt befejeződése után jelenik meg, amelyek hasonlóak az eredmények az alábbi eredmények:

![A vizsgálati eredmények](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>A Visual Studio-webteszt konfigurálása

Az Application Insights a Visual Studio teljesítmény és terhelés speciális teljesítményfigyelési tesztelési funkciók épülő projektek teszteléséhez.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztek](availability-multistep.md)
* [URL-ping tesztek](monitor-web-app-availability.md)