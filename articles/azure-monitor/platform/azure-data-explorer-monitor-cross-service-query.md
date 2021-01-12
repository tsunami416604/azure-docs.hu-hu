---
title: Azure Monitor és az Azure Adatkezelő (előzetes verzió) közötti több szolgáltatás lekérdezése
description: Az Azure Adatkezelő az Azure-Log Analytics eszközein keresztül kérdezheti le az Azure-t, hogy az összes adatait egy helyen egyesítse és elemezze.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: e60f77495cdb822a0c50be936c2b0d3ac31348f3
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116709"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Több szolgáltatás lekérdezése – Azure Monitor és az Azure Adatkezelő (előzetes verzió)
Az [Azure adatkezelő](https://docs.microsoft.com/azure/data-explorer/), a [Application Insights](/azure/azure-monitor/app/app-insights-overview)és a [log Analytics](/azure/azure-monitor/platform/data-platform-logs)közötti szolgáltatások közötti lekérdezések létrehozása.
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor és az Azure Adatkezelő szolgáltatások közötti lekérdezés
Ez a funkció lehetővé teszi, hogy az [azure adatkezelő és Azure monitor között több szolgáltatásból származó lekérdezéseket](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) hozzon létre, és [Azure monitor és az Azure adatkezelő között több szolgáltatásbeli lekérdezést hozzon létre](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

Például: (Azure Adatkezelő lekérdezése a Log Analyticsból):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Ahol a külső lekérdezés a munkaterületen található táblázatot kérdezi le, majd egy új "ADX ()" függvény használatával csatlakozik egy Azure Adatkezelő-fürt egy másik táblájához (ebben az esetben a clustername = help, databasename = Samples), például hogyan teheti ugyanezt egy másik munkaterület lekérdezésére a lekérdezés szövege alapján.

> [!NOTE]
> * Az Azure-Adatkezelőokból Azure Monitor adatok lekérdezése közvetlenül az Azure Adatkezelő-eszközökről, vagy közvetve, egy Azure Adatkezelő-fürtön futtatott lekérdezéssel, előzetes módban van.
> * Forduljon a [Cross Service lekérdezési](mailto:adxproxy@microsoft.com) csapatához bármilyen kérdéssel.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Az Azure Blob Storage-fiókból exportált Log Analytics adatok lekérdezése

Az adatok a Azure Monitorból egy Azure Storage-fiókba való exportálása lehetővé teszi az alacsony költségeket és a naplók más régiókban való újrafoglalását.

Az Azure Adatkezelő segítségével lekérdezheti az Log Analytics-munkaterületekről exportált adatok lekérdezését. A konfigurálást követően a munkaterületekről egy Azure Storage-fiókba küldendő támogatott táblák az Azure Adatkezelő adatforrásként lesznek elérhetők. [Azure monitor exportált adatok lekérdezése az Azure adatkezelő (előzetes verzió) használatával](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Azure Adatkezelő lekérdezés a Storage flow-ból.":::

>[!tip] 
> * Az Log Analytics munkaterületről származó összes adatok Azure Storage-fiókba vagy Event hub-ba való exportálásához használja Azure Monitor naplók Log Analytics munkaterület adatexportálási szolgáltatását. [Lásd: log Analytics munkaterület-adatexportálás Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Következő lépések
További információk:
* [hozzon létre több szolgáltatást az Azure adatkezelő és Azure monitor között](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Azure Monitor adatok lekérdezése az Azure-ból Adatkezelő
* [hozzon létre több szolgáltatást Azure monitor és az Azure adatkezelő között](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Azure-Adatkezelő adatainak lekérdezése Azure Monitor
* [Log Analytics munkaterület-adatexportálás Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Az Azure Blob Storage-fiók összekapcsolása és lekérdezése Log Analytics exportált adattal.
