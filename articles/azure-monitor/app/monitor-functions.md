---
title: Azure Functions-on futó alkalmazások figyelése Application Insights-Azure Monitorsal | Microsoft Docs
description: A Azure Monitor zökkenőmentesen integrálható a Azure Functionson futó alkalmazással, és lehetővé teszi a teljesítmény figyelését és a problémák azonnali megadását az alkalmazásokkal.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 093448ad0280ada039f1d4e5abd0e83e4be19b03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482105"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Functions figyelése Azure Monitor Application Insights

A [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) az Azure Application Insights beépített integrációját kínálja a függvények figyelésére. 

Application Insights gyűjti a napló-, a teljesítmény-és a hibák adatait, és automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. A Application Insights hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében. Ha betekintést nyújt az alkalmazás adataiba, folyamatosan fejlesztheti a teljesítményt és a használhatóságot. Application Insights is használhatja a helyi funkció alkalmazás-projekt fejlesztése során. 

A szükséges Application Insights rendszerállapot-kialakítás be van építve Azure Functionsba. A Function app Application Insights erőforráshoz való összekapcsolásához egyetlen szükséges dolog egy érvényes kialakítási kulcs. Ha a Function app-erőforrást az Azure-ban hozza létre, a kialakítási kulcsot hozzá kell adni az alkalmazás beállításaihoz. Ha a Function alkalmazás még nem rendelkezik ezzel a kulccsal, manuálisan is beállíthatja. További információért olvassa el a [Azure functions figyelését](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd)ismertető témakört.

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>A Java-alkalmazások elosztott nyomkövetése Windows rendszeren (nyilvános előzetes verzió)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el a Java-Azure Functions Windows rendszeren, a Java-Azure Functions elosztott nyomkövetése nem támogatott a Linux rendszeren. A fogyasztási csomaghoz a 8-9 másodperces használatú.

Ha alkalmazásai Java nyelven íródtak, megtekintheti a függvények alkalmazásaiból származó gazdagabb adatokat, többek között a kérelmeket, a függőségeket, a naplókat és a metrikákat is. A további adatokat is megtekintheti és diagnosztizálhatja a végpontok közötti tranzakciókat, és megtekintheti az alkalmazás-hozzárendelést, amely összegzi számos tranzakciót, hogy a rendszer hogyan befolyásolja a rendszerek működését, és hogy az átlagos teljesítmény és a hibák milyen arányban jelennek meg.

A végpontok közötti diagnosztika és az alkalmazás-hozzárendelés egyetlen tranzakció/kérelem számára biztosít láthatóságot. Ez a két funkció együttesen hasznos a megbízhatósági problémák kiváltó okának és a teljesítménnyel kapcsolatos szűk keresztmetszetek megkereséséhez.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Hogyan engedélyezhető a Java Function apps elosztott nyomkövetése?

Lépjen a functions alkalmazás áttekintés paneljére, és válassza a konfigurációk menüpontot. Az Alkalmazásbeállítások területen kattintson az "+ új alkalmazás beállítása" elemre. Adja hozzá az alábbi két Alkalmazásbeállítások értékét az alábbi értékek közül, majd kattintson a bal felső sarokban található Mentés gombra. TENNI!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Következő lépések

* További útmutatás és információk a figyelés [figyeléséről Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Az [elosztott nyomkövetés](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) áttekintése
* Megtudhatja, mit tehet az [alkalmazás-hozzárendelés](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) a vállalata számára
* További információ a [Java-alkalmazások kéréseiről és függőségeiről](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* További információ a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) és a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)