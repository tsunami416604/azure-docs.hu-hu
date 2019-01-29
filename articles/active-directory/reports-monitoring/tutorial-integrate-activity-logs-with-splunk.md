---
title: Azure Active Directory-naplók az Azure Monitor (előzetes verzió) használatával Splunk Stream |} A Microsoft Docs
description: Ismerje meg a Splunk Azure Monitor (előzetes verzió) használatával az Azure Active Directory naplóinak integrálása
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7bc687d339a3089b8285078f7ad23c172452c5dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183441"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor-preview"></a>Az Azure AD-naplók integrálása az Azure Monitor (előzetes verzió) használatával Splunk

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD) naplókban Splunk integrálása az Azure Monitor használatával. A naplók átirányítása az Azure event hubhoz, és majd integrálása az event hubs-Splunk.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Azure event hub, amely tartalmazza az Azure Active Directory-naplók. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* A Splunk az Azure Monitor bővítményt. [Töltse le és konfigurálja a Splunk példány](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Oktatóanyag 

1. Nyissa meg a Splunk-példány, és válassza ki **adatok összegzése**.

    ![Az "Adatok összegzése" gomb](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Válassza ki a **Sourcetypes** lapra, és válassza ki **amal: aadal:audit**

    ![Az adatok összegzése Sourcetypes lapon](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Az Azure AD tevékenység naplók az alábbi ábrán láthatók:

    ![Tevékenységnaplók](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Ha kiegészítő szolgáltatást nem lehet telepíteni a Splunk-példány (például, ha proxyt használ, vagy a Splunk-felhőben futó), a Splunk HTTP esemény gyűjtőhöz is továbbíthatja, ezeket az eseményeket. Ehhez használja ezt [Azure-függvény](https://github.com/Microsoft/AzureFunctionforSplunkVS), amely az eseményközpontban lévő üzenetek által aktivált. 
>

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
