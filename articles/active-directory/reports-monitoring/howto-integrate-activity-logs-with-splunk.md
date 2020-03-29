---
title: Splunk integrálása az Azure Monitor használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként integrálható az Azure Active Directory-naplók a SumoLogic szolgáltatással az Azure Monitor használatával
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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968712"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Útmutató: Az Azure Active Directory-naplók integrálása a Splunk szolgáltatással az Azure Monitor használatával

Ebben a cikkben megtudhatja, hogyan integrálhatja az Azure Active Directory (Azure AD) naplók splunk az Azure Monitor használatával. Először átirányítja a naplókat egy Azure-eseményközpontba, majd integrálja az eseményközpontot a Splunk-kal.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

- Az Azure-eseményközpont, amely tartalmazza az Azure AD-tevékenységnaplók. Megtudhatja, hogyan [streamelheti a tevékenységnaplókat egy eseményközpontba.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 

-  A [Microsoft Azure add on for Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Az Azure Active Directory-naplók integrálása 

1. Nyissa meg a Splunk példányt, és válassza **az Adatok összegzése**lehetőséget.

    ![Az "Adatok összegzése" gomb](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Válassza a **Forrástípusok** lapot, majd az **aadal:audit lehetőséget.**

    ![Az Adatösszegzet forrástípusai lap](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Az Azure AD-tevékenységnaplók az alábbi ábrán láthatók:

    ![Tevékenységnaplók](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Ha nem tud telepíteni egy bővítményt a Splunk példányban (például ha proxyt használ, vagy a Splunk Cloud-on fut), továbbíthatja ezeket az eseményeket a Splunk HTTP Event Collector-nek. Ehhez használja ezt az [Azure-függvényt,](https://github.com/Microsoft/AzureFunctionforSplunkVS)amelyet az eseményközpontban az új üzenetek váltanak ki. 
>

## <a name="next-steps"></a>További lépések

* [Naplónaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)