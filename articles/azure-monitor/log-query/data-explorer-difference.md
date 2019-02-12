---
title: Azure Monitor log lekérdezési nyelv különbségek |} A Microsoft Docs
description: Referenciainformációk az adatkezelő lekérdezési nyelvet használják az Azure Monitor. Az Azure Monitor további elemeket és elemeket nem támogatott az Azure Monitor log-lekérdezéseket tartalmaz.
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
ms.openlocfilehash: 9c58796fa19ffb6d38582c809f7bb6ca948bd92c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003626"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor log lekérdezési nyelv különbségek

Amíg [naplók az Azure Monitor](log-query-overview.md) épül [Azure adatkezelő](/azure/data-explorer) , és használja a [azonos lekérdezési nyelvet](/azure/kusto/query), a nyelvi verzióját van néhány különbség. Ez a cikk elemek, amelyek különböznek az adatkezelőt, és az Azure Monitor log-lekérdezésekben használt verzió használt nyelv verzióját azonosítja.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="data-explorer-elements-not-supported-in-azure-monitor"></a>Nem támogatott az Azure Monitor Explorer adatelem
A következő szakaszok ismertetik az adatkezelő lekérdezési nyelv elemei, amelyek az Azure Monitor által nem támogatott.

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
- A teljes hozzáférési [adatkezelő lekérdezési nyelv referenciadokumentációjára](/azure/kusto/query/).
