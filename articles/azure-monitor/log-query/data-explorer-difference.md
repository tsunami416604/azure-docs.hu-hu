---
title: Azure Monitor log lekérdezési nyelv különbségek |} A Microsoft Docs
description: Referenciainformációk az Azure Monitor által használt Kusto-lekérdezési nyelv. Az Azure Monitor további elemeket és elemeket nem támogatott az Azure Monitor log-lekérdezéseket tartalmaz.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 1185f3f96fd39f168d138d7dbf66e7780884b1fa
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266730"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor log lekérdezési nyelv különbségek

Amíg [naplók az Azure Monitor](log-query-overview.md) épül [Azure adatkezelő](/azure/data-explorer) látható, ugyanazokkal [Kusto-lekérdezés nyelvi](/azure/kusto/query), a nyelvi verzióját van néhány különbség. Ez a cikk elemek, amelyek különböznek az adatkezelőt, és az Azure Monitor log-lekérdezésekben használt verzió használt nyelv verzióját azonosítja.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Nem támogatott az Azure Monitor KQL elemek
A következő szakaszok ismertetik a Kusto-lekérdezés nyelvi elemei, amelyek az Azure Monitor által nem támogatott.

### <a name="statements-not-supported-in-azure-monitor"></a>Nem támogatott az Azure Monitor utasítások

* [Alias](/azure/kusto/query/aliasstatement)
* [Lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Az Azure monitorban nem támogatott funkciók

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Az Azure monitorban nejsou podporovány operátory

* [Kereszt-fürthöz való csatlakozás](/azure/kusto/query/joincrosscluster)
* [externaldata operátor](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Beépülő modulok nem támogatott az Azure monitorban

* [sql_request beépülő modul](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Az Azure monitorban kiegészítő operátorok
Az alábbi műveleteket támogatják a Azure Monitor, és nem érhetők el az Azure Monitor-en kívül.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>További lépések

- Referenciák beszerzése különböző [erőforrásokat az Azure Monitor írásához jelentkezzen lekérdezések](query-language.md).
- A teljes hozzáférési [Kusto-lekérdezés nyelvi referenciadokumentációjára](/azure/kusto/query/).
