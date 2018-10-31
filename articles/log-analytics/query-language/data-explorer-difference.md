---
title: Az Azure Monitor Log-Analytics nyelvi dokumentáció |} A Microsoft Docs
description: Referenciainformációk az adatkezelő lekérdezési nyelv a Log Analytics által használt. Log Analytics-további elemeket és elemeket nem támogatja a Log Analytics-lekérdezéseket tartalmaz.
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
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e6d097749dae49cf6f1d710bcf01cf99dcd98a4c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244121"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics lekérdezési nyelv különbségek

Amíg [Log Analytics](../log-analytics-queries.md) épül [Azure adatkezelő](/azure//data-explorer) , és használja a [azonos lekérdezési nyelvet](/azure/kusto/query), a nyelvi verzióját van néhány különbség. Ez a cikk azonosítja az elemeket, amelyeket az adatkezelő használt nyelv és a Log Analytics-lekérdezések használt verziója eltérő.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Nem támogatott a Log Analytics Data Explorer elemek
A következő szakaszok ismertetik az adatkezelő lekérdezési nyelv elemei, amelyek a Log Analytics által nem támogatott.

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
Az alábbi műveleteket támogatják a Log Analytics, és nem állnak rendelkezésre a Log Analyticsen kívül.

* [App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>További lépések

<<<<<<< HEAD:articles/log-analytics/query-language/data-explorer-difference.md
- Referenciák beszerzése különböző [erőforrásokhoz a Log Analytics-lekérdezések írására](kusto.md).
- A teljes hozzáférési [adatkezelő lekérdezési nyelv referenciadokumentációjára](/azure/kusto/query/).
=======
- További információ a lekérdezések [Log Analytics](../log-analytics-queries.md).
- A lecke az írás a végig egy [Log Analytics-lekérdezés](/log-analytics/query-language/get-started-queries.md).
- A teljes hozzáférési [Kusto referenciadokumentációjára](/azure/kusto/query/).
>>>>>>> 4bccab5ecb17c887658a4d2ed1bab6b22bf29ffd:articles/log-Analytics/Query-Language/kusto.md
