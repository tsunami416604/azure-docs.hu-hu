---
title: Teljesítmény-és terheléses tesztelés az Azure Application Insightskal | Microsoft Docs
description: Teljesítmény-és terhelési tesztek beállítása az Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669862"
---
# <a name="performance-testing"></a>Teljesítménytesztelés

> [!NOTE]
> A felhőalapú terhelés-tesztelési szolgáltatás elavult. Az elavultság, a szolgáltatás rendelkezésre állása és az alternatív szolgáltatások további információit [itt](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)találja.

Application Insights lehetővé teszi, hogy terhelési teszteket készítsen a webhelyekhez. A [rendelkezésre állási tesztekhez](monitor-web-app-availability.md)hasonlóan az Azure-beli tesztelési ügynököktől származó alapszintű kérelmek vagy [több lépésből álló kérések](availability-multistep.md) is küldhetők. A teljesítménytesztek lehetővé teszik akár 20 000 egyidejű felhasználó szimulálása akár 60 percig.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Teljesítményteszt létrehozásához először létre kell hoznia egy Application Insights erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakasszal.

A Azure Portal válassza az **erőforrás létrehozása** > **fejlesztői eszközök** > **Application Insights** lehetőséget, és hozzon létre egy Application Insights-erőforrást.

## <a name="configure-performance-testing"></a>Teljesítményteszt konfigurálása

Ha első alkalommal hoz létre teljesítménytesztet, válassza a **szervezet beállítása** lehetőséget, és válasszon egy Azure DevOps-szervezetet a teljesítménytesztek forrásaként.

A **Konfigurálás**területen válassza a **teljesítmény tesztelése** lehetőséget, majd kattintson az **új** elemre a teszt létrehozásához.

![Töltse ki legalább a webhelye URL-címét](./media/performance-testing/new-performance-test.png)

Alapszintű teljesítményteszt létrehozásához válassza ki a **manuális tesztelési** teszt típusát, és töltse ki a teszt kívánt beállításait.

|Beállítás| Maximális érték
|----------|------------|
| Felhasználói terhelés | 20,000 |
| Időtartam (perc)  | 60 |  

A teszt létrehozása után kattintson a **teszt futtatása**gombra.

A teszt befejezése után az alábbi eredményekhez hasonló eredmények jelennek meg:

![A teszt eredményei](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>A Visual Studio webes teszt konfigurálása

Application Insights a fejlett teljesítmény-tesztelési képességek a Visual Studio teljesítményére és terheléses tesztelési projektjeire épülnek.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Következő lépések

* [Több lépésből álló webes tesztek](availability-multistep.md)
* [URL-ping tesztek](monitor-web-app-availability.md)
