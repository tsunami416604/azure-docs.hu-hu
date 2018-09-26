---
title: Az Azure Monitor Log Analytics és a Kusto nyelvi különbségek |} A Microsoft Docs
description: A Log Analytics-lekérdezések és az alapvető Kusto nyelvi közötti különbségeket ismerteti.
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
ms.openlocfilehash: 109ffa6abb34dad6a00210a5c2c726bdfdde094f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184837"
---
# <a name="log-analytics-and-kusto-language-differences"></a>A log Analytics és a Kusto nyelvi különbségek
[Log Analytics-lekérdezések](../log-analytics-queries.md) az írt a [Kusto nyelvi](/azure/kusto/query). Néhány különbségek vannak a standard szintű nyelvet és a Log Analytics végrehajtása, ha ebben a cikkben leírtak szerint.


## <a name="statements-not-supported-in-log-analytics"></a>Nem támogatott a Log Analytics utasítások
Az alábbi utasítások nem támogatottak a Log Analyticsben.

* [Alias](/kusto/query/aliasstatement)
* [Lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement)

## <a name="functions-not-supported-in-log-analytics"></a>A Log Analytics nem támogatott funkciók
A következő funkciók nem támogatottak a Log Analyticsben.

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

## <a name="operators-not-supported-in-log-analytics"></a>A Log Analytics nejsou podporovány operátory
Az alábbi operátorok nem támogatottak a Log Analyticsben.

* [Kereszt-fürthöz való csatlakozás](/azure/kusto/query/joincrosscluster)
* [externaldata operátor](/azure/kusto/query/externaldata-operator)

## <a name="plugins-not-supported-in-log-analytics"></a>Beépülő modulok nem támogatott a Log Analyticsben
A következő beépülő modulok nem támogatottak a Log Analyticsben.
* [sql_request beépülő modul](/azure/kusto/query/sqlrequestplugin)


## <a name="log-analytics-specific-operators"></a>Log Analytics adott operátorok
* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>További lépések

- További információ a lekérdezések [Log Analytics](../log-analytics-queries.md).
- A lecke az írás a végig egy [Log Analytics-lekérdezés](/log-analytics/query-language/get-started-queries.md).
- A teljes hozzáférési [Kusto referenciadokumentációjára](/azure/kusto/query/).