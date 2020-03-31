---
title: Az Azure Monitor naplólekérdezési nyelvi különbségei | Microsoft dokumentumok
description: Az Azure Monitor által használt Kusto lekérdezési nyelv referenciainformációi. Az Azure Monitorra jellemző további elemeket és az Azure Monitor naplólekérdezései által nem támogatott elemeket tartalmaz.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: b4601968a318388086a60ef98e4359ae01f652ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662102"
---
# <a name="azure-monitor-log-query-language-differences"></a>Az Azure Monitor naplólekérdezési nyelvének különbségei

Míg [az Azure Monitorban lévő naplók](log-query-overview.md) az Azure Data [Explorerre](/azure/data-explorer) épülnek, és ugyanazt a [Kusto lekérdezési nyelvet](/azure/kusto/query)használja, a nyelv verziója némi különbséget tartalmaz. Ez a cikk azokat az elemeket azonosítja, amelyek eltérnek az Adatkezelő ben használt nyelv és az Azure Monitor naplólekérdezéseihez használt verzió között.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Az Azure Monitor nem támogatja a KQL-elemeket
A következő szakaszok ismertetik a Kusto lekérdezési nyelv, amely nem támogatja az Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Az Azure Monitor nem támogatja a nyilatkozatokat

* [Alias](/azure/kusto/query/aliasstatement)
* [Lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Az Azure Monitor nem támogatja a funkciókat

* [fürt()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [adatbázis()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Az Azure Monitor nem támogatja az operátorokat

* [Többfürtillesztés](/azure/kusto/query/joincrosscluster)
* [külső adatok operátora](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Az Azure Monitor nem támogatja a bővítményeket

* [Python beépülő modul](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>További operátorok az Azure Monitorban
A következő operátorok támogatják az Azure Monitor adott funkcióit, és nem érhetők el az Azure Monitoron kívül.

* [app()](app-expression.md)
* [munkaterület()](workspace-expression.md)

## <a name="next-steps"></a>További lépések

- Az [Azure Monitor naplólekérdezéseinek írásához](query-language.md)különböző erőforrásokra mutató hivatkozások beszerzése.
- A [Kusto lekérdezési nyelvének teljes referenciadokumentációjának](/azure/kusto/query/)elérése.
