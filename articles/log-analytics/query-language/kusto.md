---
title: Az Azure Monitor Log-Analytics nyelvi dokumentáció |} A Microsoft Docs
description: Részletes információ a Log Analytics Kusto nyelvét. Log Analytics-további elemeket és elemeket nem támogatja a Log Analytics-lekérdezéseket tartalmaz.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451068"
---
# <a name="log-analytics-query-language-reference"></a>Log Analytics lekérdezési nyelv leírása
[Log Analytics-lekérdezések](../log-analytics-queries.md) az azonos lekérdezési nyelvet és a motor által használt [Azure adatkezelő](/azure/data-explorer/). Is elérheti a nyelvi referencia és egyéb nyelv részleteit a következő helyről: [Kusto nyelvi referencia](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>A Log Analytics nem támogatja a Kusto-elemek
A Log Analytics-lekérdezések használata Kusto megvalósítását, amíg vannak bizonyos Kusto-elemek nem támogatja a következő szakaszokban leírtak szerint.

### <a name="statements-not-supported-in-log-analytics"></a>Nem támogatott a Log Analytics utasítások

* [Alias](/kusto/query/aliasstatement)
* [Lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>A Log Analytics nem támogatott funkciók

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>A Log Analytics nejsou podporovány operátory

* [Kereszt-fürthöz való csatlakozás](/azure/kusto/query/joincrosscluster)
* [externaldata operátor](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Beépülő modulok nem támogatott a Log Analyticsben

* [sql_request beépülő modul](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Kiegészítő operátorok a Log Analyticsben
Annak érdekében, hogy támogatják a Log Analytics, a következő további Kusto-operátorok vannak, feltéve, hogy nem állnak rendelkezésre a Log Analyticsen kívül. 

* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>További lépések

- További információ a lekérdezések [Log Analytics](../log-analytics-queries.md).
- A lecke az írás a végig egy [Log Analytics-lekérdezés](/log-analytics/query-language/get-started-queries.md).
- A teljes hozzáférési [Kusto referenciadokumentációjára](/azure/kusto/query/).