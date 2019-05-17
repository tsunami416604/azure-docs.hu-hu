---
title: Azure Active Directory-naplók integrálása az Azure Monitor használatával Splunk |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor használatával SumoLogic'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f30e19a351f7b25f995a85cfd566bcba091ac27
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597821"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor használatával Splunk

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD) naplókban Splunk integrálása az Azure Monitor használatával. A naplók átirányítása az Azure event hubhoz, és majd integrálása az event hubs-Splunk.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Azure event hub, amely tartalmazza az Azure Active Directory-naplók. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* A Splunk az Azure Monitor bővítményt. [Töltse le és konfigurálja a Splunk példány](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory-naplók integrálása 

1. Nyissa meg a Splunk-példány, és válassza ki **adatok összegzése**.

    ![Az "Adatok összegzése" gomb](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Válassza ki a **Sourcetypes** lapra, és válassza ki **amal: aadal:audit**

    ![Az adatok összegzése Sourcetypes lapon](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Az Azure AD tevékenység naplók az alábbi ábrán láthatók:

    ![Tevékenységnaplók](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Ha kiegészítő szolgáltatást nem lehet telepíteni a Splunk-példány (például, ha proxyt használ, vagy a Splunk-felhőben futó), a Splunk HTTP esemény gyűjtőhöz is továbbíthatja, ezeket az eseményeket. Ehhez használja ezt [Azure-függvény](https://github.com/Microsoft/AzureFunctionforSplunkVS), amely az eseményközpontban lévő üzenetek által aktivált. 
>

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)