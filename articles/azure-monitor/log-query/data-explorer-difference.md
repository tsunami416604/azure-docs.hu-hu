---
title: Azure Monitor log lekérdezés nyelvi eltérései | Microsoft Docs
description: Az Azure Monitor által használt Kusto-lekérdezési nyelvre vonatkozó információk. A Azure Monitor és a Azure Monitor napló lekérdezésekben nem támogatott elemek további elemeit tartalmazza.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: b4601968a318388086a60ef98e4359ae01f652ed
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662102"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor a naplózási lekérdezés nyelvi különbségeit

A [Azure monitorban lévő naplók](log-query-overview.md) az [Azure Adatkezelőra](/azure/data-explorer) épülnek, és ugyanazt a [Kusto-lekérdezési nyelvet](/azure/kusto/query)használják, a nyelv verziója némileg eltér. Ez a cikk azokat az elemeket azonosítja, amelyek különböznek a Adatkezelő használt nyelv verziója és a Azure Monitor naplózási lekérdezésekhez használt verzió között.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>A Azure Monitorban nem támogatott KQL elemek
A következő szakaszok ismertetik a Kusto lekérdezési nyelvének azon elemeit, amelyeket a Azure Monitor nem támogat.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor nem támogatott utasítások

* [Alias](/azure/kusto/query/aliasstatement)
* [Lekérdezési paraméterek](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>A függvények nem támogatottak Azure Monitor

* [fürt ()](/azure/kusto/query/clusterfunction)
* [cursor_after ()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at ()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [adatbázis ()](/azure/kusto/query/databasefunction)
* [current_principal ()](/azure/kusto/query/current-principalfunction)
* [extent_id ()](/azure/kusto/query/extentidfunction)
* [extent_tags ()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor nem támogatott operátorok

* [Fürtök közötti csatlakozás](/azure/kusto/query/joincrosscluster)
* [externaldata operátor](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>A beépülő modulok Azure Monitor nem támogatottak

* [Python beépülő modul](/azure/kusto/query/pythonplugin)
* [sql_request beépülő modul](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>További operátorok a Azure Monitor
A következő operátorok bizonyos Azure Monitor szolgáltatásokat támogatnak, és nem érhetők el Azure Monitoron kívül.

* [alkalmazás ()](app-expression.md)
* [munkaterület ()](workspace-expression.md)

## <a name="next-steps"></a>Következő lépések

- A különböző erőforrásokra mutató hivatkozások beszerzése [Azure monitor napló lekérdezésének írásához](query-language.md).
- A [Kusto lekérdezési nyelvének teljes dokumentációjának](/azure/kusto/query/)elérése.
