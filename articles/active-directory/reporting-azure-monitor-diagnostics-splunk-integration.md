---
title: Az Azure Monitor (előzetes verzió) használatával az Azure Active Directory-naplók integrálása Splunk |} A Microsoft Docs
description: Ismerje meg a Splunk Azure Monitor (előzetes verzió) használatával az Azure Active Directory naplóinak integrálása
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503842"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Az Azure AD-naplók integrálása Splunk az Azure Monitor (előzetes verzió) használatával

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD) naplókban Splunk integrálása az Azure Monitor használatával. A naplók átirányítása az Azure event hubhoz, és majd integrálása az event hubs-Splunk.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Azure event hub, amely tartalmazza az Azure Active Directory-naplók. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* A Splunk az Azure Monitor bővítményt. [Töltse le és konfigurálja a Splunk példány](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Oktatóanyag 

1. Nyissa meg a Splunk-példány, és válassza ki **adatok összegzése**.

    ![Az "Adatok összegzése" gomb](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Válassza ki a **Sourcetypes** lapra, és válassza ki **amal: aadal:audit**

    ![Az adatok összegzése Sourcetypes lapon](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Az Azure AD tevékenység naplók az alábbi ábrán láthatók:

    ![Tevékenységnaplók](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Ha kiegészítő szolgáltatást nem lehet telepíteni a Splunk-példány (például, ha proxyt használ, vagy a Splunk-felhőben futó), a Splunk HTTP esemény gyűjtőhöz is továbbíthatja, ezeket az eseményeket. Ehhez használja ezt [Azure-függvény](https://github.com/Microsoft/AzureFunctionforSplunkVS), amely az eseményközpontban lévő üzenetek által aktivált. 
>

## <a name="next-steps"></a>További lépések

* [Az Azure monitorban naplózási naplók séma értelmezése](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Az Azure monitorban bejelentkezési naplók séma értelmezése](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Gyakori kérdések és ismert hibák](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
