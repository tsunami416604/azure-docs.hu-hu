---
title: Splunk integrálása a Azure Monitor használatával | Microsoft Docs
description: Ismerje meg, hogyan integrálhat Azure Active Directory naplókat a splunk Azure Monitor használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351669453a5ce6930d3eb912e95e530d14febf61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335850"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Útmutató: Azure Active Directory-naplók integrálása a splunk a Azure Monitor használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Active Directory-(Azure AD-) naplókat a splunk a Azure Monitor használatával. Először az Azure Event hub-ba irányítja a naplókat, majd az Event hub-t integrálja a splunk.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

- Az Azure AD-tevékenységek naplóit tartalmazó Azure Event hub. Megtudhatja, hogyan [továbbíthatja a tevékenység naplóit egy Event hubhoz](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  A [Microsoft Azure add on splunk](https://splunkbase.splunk.com/app/3757/). 

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

* [A naplózási naplók sémájának értelmezése Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)