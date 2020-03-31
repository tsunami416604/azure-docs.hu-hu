---
title: Teljesítmény- és terheléstesztelés az Azure Application Insights szolgáltatással | Microsoft dokumentumok
description: Teljesítmény- és betöltési tesztek beállítása az Azure Application Insights segítségével
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669862"
---
# <a name="performance-testing"></a>Teljesítménytesztelés

> [!NOTE]
> A felhőalapú terheléstesztelési szolgáltatás elavult. Az eegesztésről, a szolgáltatás elérhetőségéről és az alternatív szolgáltatásokról itt talál további [információt.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)

Az Application Insights lehetővé teszi a webhelyek terhelési tesztjeinek generálását. A [rendelkezésre állási tesztekhez](monitor-web-app-availability.md)hasonlóan az Azure-tesztügynököktől világszerte küldhet alapszintű vagy [többlépéses kérelmeket.](availability-multistep.md) A teljesítménytesztek lehetővé teszik akár 20 000 egyidejű felhasználó szimulálását akár 60 percig.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

A teljesítményteszt létrehozásához először létre kell hoznia egy Application Insights-erőforrást. Ha már létrehozott egy erőforrást, folytassa a következő szakaszra.

Az Azure Portalon válassza az erőforrás > létrehozása**Developer Tools** > Application **Insights,** és hozzon létre egy Application Insights-erőforrás.**Application Insights**

## <a name="configure-performance-testing"></a>Teljesítménytesztelés konfigurálása

Ha ez az első alkalom, hogy teljesítménytesztet hoz létre, válassza a **Szervezet beállítása** lehetőséget, és válasszon egy Azure DevOps-szervezetet a teljesítménytesztek forrásának.

A **Konfigurálás**csoportban nyissa meg a **Teljesítménytesztelés menü** **Új** gombbal a teszt létrehozásához.

![Töltse ki legalább a webhelye URL-címét](./media/performance-testing/new-performance-test.png)

Alapvető teljesítményteszt létrehozásához válassza ki a **Kézi teszt** teszttípusát, és töltse ki a teszt kívánt beállításait.

|Beállítás| Maximális érték
|----------|------------|
| Felhasználói betöltés | 20000 |
| Időtartam (perc)  | 60 |  

A teszt létrehozása után kattintson a **Teszt futtatása**gombra.

Miután a teszt befejeződött, látni fogja, hogy az eredmények hasonlítanak az alábbi eredményekhez:

![Vizsgálati eredmények](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio webes tesztjének konfigurálása

Az Application Insights speciális teljesítménytesztelési képességei a Visual Studio teljesítmény- és betöltési tesztprojektjein alapulnak.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztek](availability-multistep.md)
* [Url ping tesztek](monitor-web-app-availability.md)
