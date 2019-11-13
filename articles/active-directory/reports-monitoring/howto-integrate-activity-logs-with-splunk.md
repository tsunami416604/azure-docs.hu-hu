---
title: Splunk integrálása a Azure Monitor használatával | Microsoft Docs
description: Ismerje meg, hogyan integrálhatja Azure Active Directory-naplókat a SumoLogic a Azure Monitor használatával
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
ms.openlocfilehash: 7da3a545847382f8fed192a6ec4fe2ac75bb8b35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014410"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Útmutató: Azure Active Directory-naplók integrálása a splunk a Azure Monitor használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Active Directory-(Azure AD-) naplókat a splunk a Azure Monitor használatával. Először az Azure Event hub-ba irányítja a naplókat, majd az Event hub-t integrálja a splunk.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Az Azure AD-tevékenységek naplóit tartalmazó Azure Event hub. Megtudhatja, hogyan [továbbíthatja a tevékenység naplóit egy Event hubhoz](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* A splunk Azure Monitor-bővítménye. [Töltse le és konfigurálja a splunk-példányt](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory naplók integrálása 

1. Nyissa meg a splunk-példányt, és válassza **az adatösszegzés**lehetőséget.

    ![Az "adatösszegzés" gomb](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Válassza a **Sourcetypes** lapot, majd válassza az **Adorján: Aadal: audit** lehetőséget.

    ![Az adatösszegzés Sourcetypes lapja](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Az Azure AD-tevékenységek naplói az alábbi ábrán láthatók:

    ![Tevékenységnaplók](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Ha nem telepíthet bővítményt a splunk-példányban (például ha proxyt használ, vagy a splunk-felhőben fut), továbbíthatja ezeket az eseményeket a splunk HTTP-esemény gyűjtője számára. Ehhez használja ezt az Azure- [függvényt](https://github.com/Microsoft/AzureFunctionforSplunkVS), amelyet az Event hub új üzenetei indítanak. 
>

## <a name="next-steps"></a>Következő lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)